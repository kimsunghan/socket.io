
# socket.io

[![Build Status](https://secure.travis-ci.org/Automattic/socket.io.svg)](http://travis-ci.org/Automattic/socket.io)
![NPM version](https://badge.fury.io/js/socket.io.svg)
![Downloads](http://img.shields.io/npm/dm/socket.io.svg?style=flat)
[![](http://slack.socket.io/badge.svg)](http://slack.socket.io)

## How to use


The following example attaches socket.io to a plain Node.JS
HTTP server listening on port `3000`.

따라오는 예제는 socket.io를 plain 한 node.JS에 붙인다. 
node.JS는 HTTP 서버를 포트 '3000'에 리스닝한것이다.  

```js
var server = require('http').createServer();
var io = require('socket.io')(server);
io.on('connection', function(socket){
  socket.on('event', function(data){});
  socket.on('disconnect', function(){});
});
server.listen(3000);
```

### Standalone

```js
var io = require('socket.io')();
io.on('connection', function(socket){});
io.listen(3000);
```

### In conjunction with Express

Starting with **3.0**, express applications have become request handler
functions that you pass to `http` or `http` `Server` instances. You need
to pass the `Server` to `socket.io`, and not the express application
function.

3.0 express로 할땐 handler functions 들을 요청하게된다. 그건 니가 `http` 또는 `http` `Server` 인스턴스를 지난다. 너는 `Server`에서 `socket.io`로 넘어가는 것이 필요하다. 그리고 그건 express function이 아니다.

```js
var app = require('express')();
var server = require('http').createServer(app);
var io = require('socket.io')(server);
io.on('connection', function(){ /* … */ });
server.listen(3000);
```

### In conjunction with Koa

Like Express.JS, Koa works by exposing an application as a request
handler function, but only by calling the `callback` method.



koa는 어플리케이션을 요청핸들 펑션으로 노출시키지만 단지 callback메서드를 부르는것으로 작동한다.
```js
var app = require('koa')();
var server = require('http').createServer(app.callback());
var io = require('socket.io')(server);
io.on('connection', function(){ /* … */ });
server.listen(3000);
```

## API

### Server

  Exposed by `require('socket.io')`.
  
  노출된다 require('socket.io')로 
  

### Server()

  Creates a new `Server`. Works with and without `new`:
  
  새로운 `Server` 생성한다.
  
  `new`를 사용해서 작동하고 사용하지 않고도 작동한다.
  
 

  ```js
  var io = require('socket.io')();
  // or
  var Server = require('socket.io');
  var io = new Server();
  ```

### Server(opts:Object)

  Optionally, the first or second argument (see below) of the `Server`
  constructor can be an options object.
  
  선택적으로, 처음 또는 두번째 `server`구성자의 아규먼트는 옵션 오브젝트가 될 수 있다.

  The following options are supported:

   아래의 옵션들이 지원된다.

  - `serveClient` sets the value for Server#serveClient()
  - `path` sets the value for Server#path()
  -  `serverClient`는 Server#serveClient()를 위한 값으로 설정한다.
  -  `path`는 Server#path()를 위한 값으로 설정한다.

  The same options passed to socket.io are always passed to
  the `engine.io` `Server` that gets created. See engine.io
  [options](https://github.com/learnboost/engine.io#methods-1)
  as reference.

같은 옵션들은 항상  `Server`가 만들어진 `engine.io`  를 지난 socket.io를  통과한다.
 engine.io를봐라.  [options](https://github.com/learnboost/engine.io#methods-1)
 


### Server(srv:http#Server, opts:Object)

  Creates a new `Server` and attaches it to the given `srv`. Optionally
  `opts` can be passed.

### Server(port:Number, opts:Object)

  Binds socket.io to a new `http.Server` that listens on `port`.

### Server#serveClient(v:Boolean):Server

  If `v` is `true` the attached server (see `Server#attach`) will serve
  the client files. Defaults to `true`.

  This method has no effect after `attach` is called.

  ```js
  // pass a server and the `serveClient` option
  var io = require('socket.io')(http, { serveClient: false });

  // or pass no server and then you can call the method
  var io = require('socket.io')();
  io.serveClient(false);
  io.attach(http);
  ```

  If no arguments are supplied this method returns the current value.

### Server#path(v:String):Server

  Sets the path `v` under which `engine.io` and the static files will be
  served. Defaults to `/socket.io`.

  If no arguments are supplied this method returns the current value.

### Server#adapter(v:Adapter):Server

  Sets the adapter `v`. Defaults to an instance of the `Adapter` that
  ships with socket.io which is memory based. See
  [socket.io-adapter](https://github.com/Automattic/socket.io-adapter).

  If no arguments are supplied this method returns the current value.

### Server#origins(v:String):Server

  Sets the allowed origins `v`. Defaults to any origins being allowed.

  If no arguments are supplied this method returns the current value.

### Server#origins(v:Function):Server

  Sets the allowed origins as dynamic function. Function takes two arguments `origin:String` and `callback(error, success)`, where `success` is a boolean value indicating whether origin is allowed or not.

  __Potential drawbacks__:
  * in some situations, when it is not possible to determine `origin` it may have value of `*`
  * As this function will be executed for every request, it is advised to make this function work as fast as possible
  * If `socket.io` is used together with `Express`, the CORS headers will be affected only for `socket.io` requests. For Express can use [cors](https://github.com/troygoode/node-cors/)


### Server#sockets:Namespace

  The default (`/`) namespace.

### Server#attach(srv:http#Server, opts:Object):Server

  Attaches the `Server` to an engine.io instance on `srv` with the
  supplied `opts` (optionally).

### Server#attach(port:Number, opts:Object):Server

  Attaches the `Server` to an engine.io instance that is bound to `port`
  with the given `opts` (optionally).

### Server#listen

  Synonym of `Server#attach`.

### Server#bind(srv:engine#Server):Server

  Advanced use only. Binds the server to a specific engine.io `Server`
  (or compatible API) instance.

### Server#onconnection(socket:engine#Socket):Server

  Advanced use only. Creates a new `socket.io` client from the incoming
  engine.io (or compatible API) `socket`.

### Server#of(nsp:String):Namespace

  Initializes and retrieves the given `Namespace` by its pathname
  identifier `nsp`.

  If the namespace was already initialized it returns it right away.

### Server#emit

  Emits an event to all connected clients. The following two are
  equivalent:

  ```js
  var io = require('socket.io')();
  io.sockets.emit('an event sent to all connected clients');
  io.emit('an event sent to all connected clients');
  ```

  For other available methods, see `Namespace` below.

### Server#close

  Closes socket server

  ```js
  var Server = require('socket.io');
  var PORT   = 3030;
  var server = require('http').Server();

  var io = Server(PORT);

  io.close(); // Close current server

  server.listen(PORT); // PORT is free to use

  io = Server(server);
  ```

### Server#use

  See `Namespace#use` below.

### Namespace

  Represents a pool of sockets connected under a given scope identified
  by a pathname (eg: `/chat`).

  By default the client always connects to `/`.

#### Events

  - `connection` / `connect`. Fired upon a connection.

    Parameters:
    - `Socket` the incoming socket.

### Namespace#name:String

  The namespace identifier property.

### Namespace#connected:Object<Socket>

  Hash of `Socket` objects that are connected to this namespace indexed
  by `id`.

### Namespace#clients(fn:Function)

  Gets a list of client IDs connected to this namespace (across all nodes if applicable).

  An example to get all clients in a namespace:

  ```js
  var io = require('socket.io')();
  io.of('/chat').clients(function(error, clients){
    if (error) throw error;
    console.log(clients); // => [PZDoMHjiu8PYfRiKAAAF, Anw2LatarvGVVXEIAAAD]
  });
  ```

  An example to get all clients in namespace's room:

  ```js
  var io = require('socket.io')();
  io.of('/chat').in('general').clients(function(error, clients){
    if (error) throw error;
    console.log(clients); // => [Anw2LatarvGVVXEIAAAD]
  });
  ```

  As with broadcasting, the default is all clients from the default namespace ('/'):

  ```js
  var io = require('socket.io')();
  io.clients(function(error, clients){
    if (error) throw error;
    console.log(clients); // => [6em3d4TJP8Et9EMNAAAA, G5p55dHhGgUnLUctAAAB]
  });
  ```

### Namespace#use(fn:Function):Namespace

  Registers a middleware, which is a function that gets executed for
  every incoming `Socket` and receives as parameter the socket and a
  function to optionally defer execution to the next registered
  middleware.

  ```js
  var io = require('socket.io')();
  io.use(function(socket, next){
    if (socket.request.headers.cookie) return next();
    next(new Error('Authentication error'));
  });
  ```

  Errors passed to middleware callbacks are sent as special `error`
  packets to clients.

### Socket

  A `Socket` is the fundamental class for interacting with browser
  clients. A `Socket` belongs to a certain `Namespace` (by default `/`)
  and uses an underlying `Client` to communicate.

### Socket#rooms:Array

  A list of strings identifying the rooms this socket is in.

### Socket#client:Client

  A reference to the underlying `Client` object.

### Socket#conn:Socket

  A reference to the underlying `Client` transport connection (engine.io
  `Socket` object).

### Socket#request:Request

  A getter proxy that returns the reference to the `request` that
  originated the underlying engine.io `Client`. Useful for accessing
  request headers such as `Cookie` or `User-Agent`.

### Socket#id:String

  A unique identifier for the socket session, that comes from the
  underlying `Client`.

### Socket#emit(name:String[, …]):Socket

  Emits an event to the socket identified by the string `name`. Any
  other parameters can be included.

  All datastructures are supported, including `Buffer`. JavaScript
  functions can't be serialized/deserialized.

  ```js
  var io = require('socket.io')();
  io.on('connection', function(socket){
    socket.emit('an event', { some: 'data' });
  });
  ```

### Socket#join(name:String[, fn:Function]):Socket

  Adds the socket to the `room`, and fires optionally a callback `fn`
  with `err` signature (if any).

  The socket is automatically a member of a room identified with its
  session id (see `Socket#id`).

  The mechanics of joining  rooms are handled by the `Adapter`
  that has been configured (see `Server#adapter` above), defaulting to
  [socket.io-adapter](https://github.com/Automattic/socket.io-adapter).

### Socket#leave(name:String[, fn:Function]):Socket

  Removes the socket from `room`, and fires optionally a callback `fn`
  with `err` signature (if any).

  **Rooms are left automatically upon disconnection**.

  The mechanics of leaving rooms are handled by the `Adapter`
  that has been configured (see `Server#adapter` above), defaulting to
  [socket.io-adapter](https://github.com/Automattic/socket.io-adapter).

### Socket#to(room:String):Socket
### Socket#in(room:String):Socket

  Sets a modifier for a subsequent event emission that the event will
  only be _broadcasted_ to sockets that have joined the given `room`.

  To emit to multiple rooms, you can call `to` several times.

  ```js
  var io = require('socket.io')();
  io.on('connection', function(socket){
    socket.to('others').emit('an event', { some: 'data' });
  });
  ```

### Socket#compress(v:Boolean):Socket

  Sets a modifier for a subsequent event emission that the event data will
  only be _compressed_ if the value is `true`. Defaults to `true` when you don't call the method.

  ```js
  var io = require('socket.io')();
  io.on('connection', function(socket){
    socket.compress(false).emit('an event', { some: 'data' });
  });
  ```

### Client

  The `Client` class represents an incoming transport (engine.io)
  connection. A `Client` can be associated with many multiplexed `Socket`
  that belong to different `Namespace`s.

### Client#conn

  A reference to the underlying `engine.io` `Socket` connection.

### Client#request

  A getter proxy that returns the reference to the `request` that
  originated the engine.io connection. Useful for accessing
  request headers such as `Cookie` or `User-Agent`.

## Debug / logging

Socket.IO is powered by [debug](http://github.com/visionmedia/debug).
In order to see all the debug output, run your app with the environment variable
`DEBUG` including the desired scope.

To see the output from all of Socket.IO's debugging scopes you can use:

```
DEBUG=socket.io* node myapp
```

## License

MIT
