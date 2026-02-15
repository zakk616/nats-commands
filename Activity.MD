# NATS JetStream CLI Reference Guide

This guide covers stream creation and the three primary ways to consume messages: WorkQueue (Load Balanced), Broadcast (Fan-out), and Pull Consumers.

---

## 1. Stream Setup

Before creating consumers, define the stream and populate it with data.

### Commands

```bash
nats stream add GREETINGS --subjects "MORNING" --storage file --retention work --discard old
```

**Purpose:** Creates a WorkQueue stream. Messages are automatically deleted from the stream once they are acknowledged by a consumer.

```bash
nats pub MORNING "test"
```

**Purpose:** Publishes a message to the `MORNING` subject to be captured by the stream.

---

## 2. Push Consumer: WorkQueue (Load Balanced)

Use this setup when you have multiple workers and want to distribute load between them. Only one worker in the group will receive each message.

### Create Consumer

```bash
nats consumer add GREETINGS worker_1 --target MORNING_push --filter MORNING --deliver all --deliver-group GROUP --ack explicit --replay instant --max-pending 1000 --defaults
```

**Purpose:** Registers a consumer that "pushes" messages to a specific target subject while assigning them to a deliver-group.

### Subscribe (The Worker)

```bash
nats sub MORNING_push --queue GROUP --ack
```

**Purpose:** Joins the queue group `GROUP`. If you run this in multiple terminals, NATS will load-balance messages between them.

---

## 3. Push Consumer: Broadcast (Fan-out)

Use this setup when you want each instance of an application to receive its own copy of every message.

### Create Consumer

```bash
nats consumer add GREETINGS worker_1 --target MORNING_push --filter MORNING --deliver all --ack explicit --replay instant --max-pending 1000 --defaults
```

**Purpose:** Creates a push consumer without a deliver-group. It sends messages to the `MORNING_push` subject.

### Subscribe (The Listener)

```bash
nats sub MORNING_push --ack
```

**Purpose:** Listens to the target subject. Note that without a `--queue` flag, running multiple instances may cause them to interfere with each other's acknowledgments in a WorkQueue stream.

---

## 4. Pull Consumer

The most scalable and reliable method. The client "asks" for messages only when it is ready to process them.

### Create Consumer

```bash
nats consumer add GREETINGS worker_1 --filter MORNING --deliver all --ack explicit --replay instant --max-pending 1000 --defaults
```

**Purpose:** Creates a consumer with no target. This tells NATS to wait for the client to request data.

### Consume Message

```bash
nats consumer next GREETINGS worker_1
```

**Purpose:** Explicitly pulls the next available message from the stream. Ideal for batch processing or workers with varying processing times.

---

## Key Reminders

- **Retention Policy:** Because the stream uses `--retention work`, messages vanish as soon as they are acknowledged.
- **Order of Operations:** For Push consumers, always start your `nats sub` listeners before publishing if you want to see messages arrive in real-time.

