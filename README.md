# WebSocket plugin for CoppeliaSim

### Compiling

1. Install required packages for [libPlugin](https://github.com/CoppeliaRobotics/libPlugin): see libPlugin's README
2. Checkout and compile
```sh
$ git clone --recursive https://github.com/CoppeliaRobotics/simExtWS.git
$ cd simExtWS
$ mkdir build && cd build
$ cmake ..
$ cmake --build .
$ cmake --install .
```

### Usage

Use `simWS.start` to start listening on the specified port, `simWS.setMessageHandler` to set an handler for incoming messages, and `simWS.send` to send messages on the specified connection:

```lua
-- Simple echo server

function onMessage(server,connection,data)
    simWS.send(server,connection,data)
end

function sysCall_init()
    server=simWS.start(9000)
    simWS.setMessageHandler(server,'onMessage')
end
```

It is possible to broadcast a message to all connected clients by tracking active connections via open/close events:

```lua
-- Simple broadcaster

function onOpen(server,connection)
    clients[server]=clients[server] or {}
    clients[server][connection]=1
end

function onClose(server,connection)
    clients[server][connection]=nil
end

function broadcast(server,data)
    for connection,_ in pairs(clients[server] or {}) do
        simWS.send(server,connection,data)
    end
end

function sysCall_init()
    clients={}
    server=simWS.start(9000)
    simWS.setOpenHandler(server,'onOpen')
    simWS.setCloseHandler(server,'onClose')
end
```

See also the examples in the [`examples`](examples) subdirectory.
