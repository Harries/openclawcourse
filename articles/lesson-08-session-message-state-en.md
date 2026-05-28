# How Sessions, Messages, Context, and Task State Are Organized

![English cover](../assets/day-12-session-message-state-en.png)

When people first learn OpenClaw, four concepts often blur together:

```text
sessions
messages
context
task state
```

They all seem related to "chat history".

In OpenClaw, they are not the same thing.

If you mix them up, many behaviors become hard to explain:

- Why does a group chat have different context from a direct chat?
- Why does `/context list` not show the entire transcript?
- Why does a new message become steering while an agent is already running?
- Why can a tool result exist in the transcript but not fully appear in the next model window?
- Why can a long task keep running while the chat only shows a few status lines?

This lesson separates the four layers.

The short version is:

```text
Session defines the boundary.
Message defines input and output.
Context defines what the model sees in this run.
Task State defines where the current run is.
```

Together, these layers make OpenClaw's state model stable.

## The Core Idea: Do Not Call Everything "History"

In a simple chat product, history often means the visible message list.

An agent runtime needs more structure.

OpenClaw manages at least four kinds of state:

```text
Session
  Whose continuing conversation is this? How long does it live? Where is the transcript?

Message
  Where did this input come from? Where should output go? Is it a reply, follow-up, group message, or system message?

Context
  What exactly did the model receive in this run: system prompt, history, tool schemas, skill metadata, files?

Task State
  Is the current run queued, running, waiting for a tool, waiting for approval, streaming, retrying, or done?
```

The relationship looks like this:

```text
external message
  ↓
Gateway normalizes it as a Message
  ↓
Message routes to a Session
  ↓
Session provides history and boundaries
  ↓
Runtime builds this run's Context
  ↓
Agent Run produces Task State
  ↓
outbound Message is written back to the Session
```

Session is not context.

Message is not task state.

Transcript is not the same thing as what the model saw.

These distinctions are basic, but they unlock much of OpenClaw.

## Layer 1: Session Is the Conversation Boundary

The OpenClaw documentation explains that conversations are organized into sessions, and each message is routed to a session based on where it came from.

That means a session is a boundary.

It answers:

```text
Which history should this message continue?
Which run does this request belong to?
Which later messages should affect the same task?
When should this conversation reset, expire, or be maintained?
```

Different entry points can map to different sessions:

```text
direct messages       → shared by default or isolated by sender
groups / channels     → usually isolated per group, channel, or room
webhooks              → usually isolated per hook
cron jobs             → often a fresh session per run
dashboard             → reads the gateway-backed session transcript
```

That is why the same sentence behaves differently in different places.

If you type this in the CLI:

```text
Continue the previous fix.
```

OpenClaw may connect it to the local workspace, previous commands, prior edits, and the active transcript.

If the same sentence appears in a group chat mapped to another session, it will not automatically inherit the CLI history.

OpenClaw did not forget.

The session boundary did its job.

## Where Session State Lives

Session state is owned by the Gateway. Clients query the Gateway for session data.

You can think of it in two parts:

```text
session store
  session rows, lifecycle timestamps, routing metadata

transcript
  messages, tool calls, replies, and run traces for one session
```

The docs describe typical paths like:

```text
~/.openclaw/agents/<agentId>/sessions/sessions.json
~/.openclaw/agents/<agentId>/sessions/<sessionId>.jsonl
```

The chat window is not the source of truth.

The Gateway's session store and transcript are.

So when debugging "why did it remember this?" or "why did it forget that?", start with:

```text
Which session did the message route to?
What is in that session's transcript?
Is this entry point really using the same session?
```

## Layer 2: Message Is the Normalized Input/Output Object

Session defines the boundary. Message defines input and output.

External platforms have very different message shapes.

A Telegram message, Slack thread reply, enterprise chat message, and HTTP webhook do not look alike.

OpenClaw normalizes them into messages that can be routed, queued, sent, and traced.

A normalized message needs to answer:

```text
direction: inbound or outbound
channel: which transport
account: which account
target: direct, group, channel, or thread
sender: who sent it
body: model-facing text
command body: raw text for command/directive parsing
attachments: files, images, audio
relation: reply, follow-up, broadcast, system message
origin: user, platform, OpenClaw system output
```

That is why the message a user sees and the text the model receives are not always identical.

In a group chat, the raw platform text may be:

```text
@bot summarize this file
```

OpenClaw may add sender labels, pending group history, or untrusted-context wrappers so the model knows that this is group conversation material, not a higher-priority system instruction.

At the same time, command parsing may use a separate command body.

That keeps `/queue`, `/model`, `/status`, and other control semantics from polluting the model-facing task text.

## BodyForAgent, CommandBody, and History Wrappers

The Messages documentation makes an important distinction:

```text
BodyForAgent: primary model-facing text for the current message
Body: compatibility prompt fallback, sometimes with wrapped history
CommandBody: raw user text for command and directive parsing
RawBody: legacy alias
```

OpenClaw is not just forwarding raw platform text to the model.

It separates:

```text
task text the model should understand
control syntax the Gateway should parse
history supplied by the channel
attachments and platform metadata
```

This also matters for safety.

Group history, forwarded content, quotes, and platform metadata should be treated as untrusted context, not as instructions with system-level authority.

## Layer 3: Context Is What the Model Actually Sees

Context is often mistaken for "session history".

The official definition is more precise: context is everything OpenClaw sends to the model for a run, bounded by the model's context window.

It can include:

```text
system prompt
conversation history
tool calls and tool results
tool schemas
skill metadata
workspace injected files
attachments
compaction summaries
runtime metadata
channel context
```

Context is not the entire session.

A session may have a long transcript.

The model only receives what fits and what policy chooses to include.

Old history may be compacted.

Large tool results may be trimmed.

Workspace files may be injected only partially.

Tool schemas count toward context even when you do not see them as normal text.

A useful mental model:

```text
Session = archive
Transcript = ledger
Context = packet brought into the meeting room for this run
```

The model can only work from that packet.

If the packet lacks the key fact, the model may behave as if it forgot, even though the transcript contains it somewhere.

## Layer 4: Task State Is Where the Current Run Is

Session and context explain what the model can know.

Task state explains what is happening right now.

An OpenClaw run may move through states such as:

```text
accepted
queued
running
model_call
tool_call
waiting_approval
streaming
compacting
retrying
ending
done / error / timeout / aborted
```

Not every state becomes a chat message.

Some states are only for scheduling.

Some are emitted as tool events.

Some are visible in the dashboard or CLI.

Some are written only to transcript or metadata.

That is why a final reply alone is not enough to judge a run.

The agent may have completed several tool calls while the final reply is still pending.

The model may have produced text, but channel delivery may have failed.

The run may be waiting for approval, not stuck in model inference.

## How the Four Layers Work Together

Suppose a user writes in a group:

```text
@OpenClaw check yesterday's refund anomalies and only send a summary.
```

The internal flow can be separated like this:

```text
Message layer
  identify channel, group, sender, message id, body, reply target

Session layer
  route the group message to the corresponding group session

Task State layer
  if idle, create a new run
  if busy, apply queue mode: steer / followup / collect / interrupt

Context layer
  assemble system prompt, group context, history, tool schemas, skill metadata

Agent loop
  model decides whether tools are needed; OpenClaw executes tools and returns observations

Message layer
  deliver the final summary with channel chunking, threading, or reply behavior

Session layer
  persist transcript, timestamps, and state for the next turn
```

No single layer completes the task by itself.

Their boundaries are the design.

## Why Steering Is Task State, Not Ordinary History

The previous lesson introduced steering.

Here is the more precise view: steering is not just the next chat message in history.

When a session already has an active run and a normal prompt arrives, OpenClaw may deliver it to the active runtime at the next model boundary.

The Steering Queue docs explain that steering does not interrupt a tool call already running. It waits until the tool batch finishes, then appends queued steering messages before the next model call.

In plain terms:

```text
tool is running
  ↓
user adds a correction
  ↓
message enters task state's steering queue
  ↓
tool result returns
  ↓
next model call receives the steering text
```

This is not the same as simply appending a message to transcript for a future turn.

It is active run state.

That explains why a correction may not affect the current tool action immediately, but can affect the next decision.

## Context, Memory, and Transcript

These three words are also easy to confuse.

Use this split:

```text
Transcript
  persisted session ledger of messages, tool calls, and results

Memory
  durable facts, preferences, or commitments that may be retrieved or injected

Context
  the actual packet sent to the model in this run
```

Transcript can contribute to context.

Memory can be injected into context.

But neither is automatically identical to context.

If you ask:

```text
Why did the model not remember my preference from last month?
```

Check:

```text
Was the preference stored as memory?
Can this session access it?
Was it injected into this run's context?
Was it trimmed, compacted, or omitted because of policy?
```

That is much more useful than saying "the model forgot."

## Common Misunderstandings

### Misunderstanding 1: Session Equals Chat Window

No.

A chat window is a surface.

A session is the Gateway-managed boundary for history and state.

One surface can switch sessions.

Multiple surfaces can map to one session.

### Misunderstanding 2: Everything in Transcript Is Visible to the Model

No.

Transcript is durable record.

Context is what this model call receives.

Between them there may be trimming, compaction, summarization, filtering, and tool-result cleanup.

### Misunderstanding 3: User Messages Are Always Sent Raw to the Model

No.

The Gateway may parse commands, directives, channel context, group history, and attachments before building model-facing text.

### Misunderstanding 4: A Stuck Task Means a Stuck Model

Not necessarily.

The run may be queued, waiting on a tool, waiting for approval, waiting on channel delivery, waiting for a session write lock, or compacting context.

Look at task state, not only the final reply.

## Final Summary

The four-layer model is:

```text
Session: boundary of the continuing conversation
Message: normalized input and output object
Context: model-visible packet for this run
Task State: runtime state of the current run
```

OpenClaw stays stable because these layers have separate jobs.

Session keeps history organized.

Message makes multi-channel I/O controllable.

Context gives the model the right material.

Task State makes long-running work, tools, steering, and interruption manageable.

Once you can separate these layers, system prompts, tool calls, streaming, and debugging become much easier to reason about.

## Lesson Homework

1. Pick one OpenClaw entry point you use and describe how its session is probably scoped.
2. Take one group message and split it into Message, Session, Context, and Task State concerns.
3. Inspect a run with `/context list` or a similar command and compare the context with the transcript.
4. If a user sends a correction while a tool is running, decide whether it should steer, follow up, collect, or interrupt.
5. Write a debugging checklist for "the model forgot", including session, transcript, memory, and context.

## Next Lesson Preview

Next we will cover:

```text
the priority between system prompt, developer instructions, and user input
```

That answers a sensitive question: when system rules, project rules, skill instructions, channel history, and user commands conflict, which one should OpenClaw follow?

## References

- OpenClaw Docs: [Session management](https://docs.openclaw.ai/concepts/session)
- OpenClaw Docs: [Messages](https://docs.openclaw.ai/concepts/messages)
- OpenClaw Docs: [Context](https://docs.openclaw.ai/concepts/context)
- OpenClaw Docs: [Steering queue](https://docs.openclaw.ai/concepts/queue-steering)
- OpenClaw Docs: [Message lifecycle refactor](https://docs.openclaw.ai/concepts/message-lifecycle-refactor)
