# NATS Commands

## Basic
```javascript
// Check NATS-Cli version
nats --version

// Check NATS-Server version
nats-server --version

// Run NATS-Server
nats-server
```

```javascript
// Run NATS-Server with jetstream
nats-server -js

// Run NATS-Server on specific IP:Port
nats-server -js -a 127.0.0.1 -p 4221

// Run NATS-Server using configuration
nats-server -js -c nats.conf
```

> nats.conf
```properties
listen:172.0.0.1:4221
```

## Intermediate
```javascript
// List streams
nats str ls -a 

// list streams of specific server
nats str ls -s "nats://127.0.0.1:4221"

// List stream report
nats str report -s "nats://127.0.0.1:4221"

// Remove streams
nats str rm -s "nats://127.0.0.1:4221"

// Subscribe to streams
nats sub "subject" -s "nats://127.0.0.1:4221"

// Publish message to streams
nats pub "subject" "this is message" -s "nats://127.0.0.1:4221"
```

```javascript
// list streams of specific server
nats str ls -s "nats://user:pass@127.0.0.1:4221"

// List stream report
nats str report -s "nats://user:pass@127.0.0.1:4221"

// Remove streams
nats str rm -s "nats://user:pass@127.0.0.1:4221"

// Subscribe to streams
nats sub "subject" -s "nats://user:pass@127.0.0.1:4221"

// Publish message to streams
nats pub "subject" "this is message" -s "nats://user:pass@127.0.0.1:4221"

// Check subjects on nats stream
nats str subjects -s "nats://user:pass@127:0.0.1:4221"

// Ping nats server
nats -s "nats://user:pass@127.0.0.1:4221" rtt
```

## Advance

```javascript
// install nats server as windows service
SC.EXE create nats-server1 binPath="C:\nats-server.exe -js"

// install nats server as windows service with arguments
SC.EXE create nats-server2 binPath="C:\nats-server.exe -js -a 127.0.0.1 -p 4221"

// install nats server as windows service with config
SC.EXE create nats-server3 binPath="C:\nats-server.exe -js -c C:\nats.conf"

// install nats server as windows service with config and logging
SC.EXE create nats-server3 binPath="C:\nats-server.exe -js -D -c C:\nats.conf"

// install nats server as windows service with config, logging and auto start
SC.EXE create nats-server4 binPath="C:\nats-server.exe -js -D -c C:\nats.conf" start=delayed-auto

```
> nats.conf
```properties
listen: 127.0.0.1:4222
server_name: Node1
jetstream {
   max_memory_store: 5368709120
   max_file_store: 322122547200
}
```
