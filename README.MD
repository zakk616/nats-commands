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
nats-server -js -a 127.0.0.1 -p 4222

// Run NATS-Server using configuration
nats-server -js -c nats.conf
```

```properties
# nats.conf
listen:172.0.0.1:4222
```

## Intermediate
```javascript
// List streams
nats str ls -a 

// list streams of specific server
nats str ls -s "nats://127.0.0.1:4222"

// List stream report
nats str report -s "nats://127.0.0.1:4222"

// Remove streams
nats str rm -s "nats://127.0.0.1:4222"

// Purge streams
nats str purge -s "nats://127.0.0.1:4222"

// Subscribe to streams
nats sub "subject" -s "nats://127.0.0.1:4222"

// Publish message to streams
nats pub "subject" "this is message" -s "nats://127.0.0.1:4222"
```

// View messages from streams
nats -s "nats://127.0.0.1:4222" str view <StreamName>

// View individual message from streams
nats -s "nats://127.0.0.1:4222" str get <StreamName> <Sequence#>

// Remove individual message from streams
nats -s "nats://127.0.0.1:4222" str rmm <StreamName> <Sequence#>

```javascript
// list streams of specific server
nats str ls -s "nats://user:pass@127.0.0.1:4222"

// List stream report
nats str report -s "nats://user:pass@127.0.0.1:4222"

// Remove streams
nats str rm -s "nats://user:pass@127.0.0.1:4222"

// Subscribe to streams
nats sub "subject" -s "nats://user:pass@127.0.0.1:4222"

// Publish message to streams
nats pub "subject" "this is message" -s "nats://user:pass@127.0.0.1:4222"

// Check subjects on nats stream
nats str subjects -s "nats://user:pass@127:0.0.1:4222"

// Ping nats server
nats -s "nats://user:pass@127.0.0.1:4222" rtt
```

## Advance

```javascript
// install nats server as windows service
SC.EXE create nats-server1 binPath="C:\nats-server.exe -js"

// install nats server as windows service with arguments
SC.EXE create nats-server2 binPath="C:\nats-server.exe -js -a 127.0.0.1 -p 4222"

// install nats server as windows service with config
SC.EXE create nats-server3 binPath="C:\nats-server.exe -js -c C:\nats.conf"

// install nats server as windows service with config and event logging
SC.EXE create nats-server3 binPath="C:\nats-server.exe -js -D -c C:\nats.conf"

// install nats server as windows service with config, event+message logging and auto start
SC.EXE create nats-server4 binPath="C:\nats-server.exe -js -DV -c C:\nats.conf" start=delayed-auto

//create context
nats context save contextName -s "nats://user:passwd@IP:Port" --description "this is description of this context"

//create context
nats context save contextName --server "nats://IP:Port" --user "username" --password "password" --description "this is description of this context"

//create show list
nats context ls

//create select a context
nats context select

//create delete a context
nats context rm contextName
```

```properties
# nats.conf
server_name: Node1
listen: 127.0.0.1:4222

jetstream {
   store_dir: C:/nats-server/storage/node1
   max_memory_store: 5368709120
   max_file_store: 322122547200
}

system_account: SYS

logfile_size_limit: 50MB
log_file: "C:/nats-logs/node1.log"
```

## Cluster

### Server 1 (host_a)

```
server_name=n1-c1
listen=4222

accounts {
  $SYS {
    users = [
      { user: "admin",
        pass: "$2a$11$DRh4C0KNbNnD8K/hb/buWe1zPxEHrLEiDmuq1Mi0rRJiH/W25Qidm"
      }
    ]
  }
}

jetstream {
   store_dir=/nats/storage
}

cluster {
  name: C1
  listen: 0.0.0.0:6222
  routes: [
    nats://host_b:6222
    nats://host_c:6222
  ]
}
```

### Server 2 (host_b)
```
server_name=n2-c1
listen=4222

accounts {
  $SYS {
    users = [
      { user: "admin",
        pass: "$2a$11$DRh4C0KNbNnD8K/hb/buWe1zPxEHrLEiDmuq1Mi0rRJiH/W25Qidm"
      }
    ]
  }
}

jetstream {
   store_dir=/nats/storage
}

cluster {
  name: C1
  listen: 0.0.0.0:6222
  routes: [
    nats://host_a:6222
    nats://host_c:6222
  ]
}
```
### Server 3 (host_c)
```
server_name=n3-c1
listen=4222

accounts {
  $SYS {
    users = [
      { user: "admin",
        pass: "$2a$11$DRh4C0KNbNnD8K/hb/buWe1zPxEHrLEiDmuq1Mi0rRJiH/W25Qidm"
      }
    ]
  }
}

jetstream {
   store_dir=/nats/storage
}

cluster {
  name: C1
  listen: 0.0.0.0:6222
  routes: [
    nats://host_a:6222
    nats://host_b:6222
  ]
}
```

```javascript
// ls servers of specific cluster
nats server ls -s "nats://sysuser:pass@127.0.0.1:4222"
```
