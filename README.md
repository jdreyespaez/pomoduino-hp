# Firs: Preparing the Arduino board
With board attached via USB open the Arduino IDE, choose from the menu the **Firmata standard** sketch and upload it to the board.
# Second: Preparing NodeJS application
In a new folder start by loading all the necessary library via npm.
```
npm install socket.io --save

npm install express --save

npm install johnny-five --save
```
and via bower all the component needed for the frontend of the application
```
bower install angularjs --save

bower install socket.io-client --save

bower install angular-socket-io --save
```
# Third: NodeJS and Express – Server
Download the file server.js. The complete code of the server is below:
```
// server.js
var express        = require('express');  
var app            = express();  
var httpServer = require("http").createServer(app);  
var five = require("johnny-five");  
var io=require('socket.io')(httpServer);
 
var port = 3000; 
 
app.use(express.static(__dirname + '/public'));
 
app.get('/', function(req, res) {  
        res.sendFile(__dirname + '/public/index.html');
});
 
httpServer.listen(port);  
console.log('Server available at http://localhost:' + port);  
var led;
 
//Arduino board connection
 
var board = new five.Board();  
board.on("ready", function() {  
    console.log('Arduino connected');
    led = new five.Led(2);
});
 
//Socket connection handler
io.on('connection', function (socket) {  
        console.log(socket.id);
 
        socket.on('led:on', function (data) {
           led.on();
           console.log('LED ON RECEIVED');
        });
 
        socket.on('led:off', function (data) {
            led.off();
            console.log('LED OFF RECEIVED');
 
        });
    });
 
console.log('Waiting for connection');
```
# Fourth: AngularJS application – Frontend
In the public folder of the application create a index.html file and put the code below inside it:
```
<!DOCTYPE html>  
<html lang="en" ng-app="myApp" class="no-js"> <!--<![endif]-->  
<head>  
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <title>CODETutorial Mean seed</title>
</head>  
<body>  
  <script src="lib/socket.io-client/socket.io.js"></script>
  <script src="lib/angularjs/angular.js"></script>  
  <script src="lib/angular-socket-io/socket.js"></script>
 
    <h1>{{"Led controller"}}</h1>
  <div ng-controller="ArduController">
    <button ng-click="ledOn()">On</button>
    <button ng-click="ledOff()">Off</button>
  </div>
 
<script type="text/javascript">  
    var app = angular.module('myApp', ['btford.socket-io']).
    factory('mySocket', function (socketFactory) {
        return socketFactory();
    }).
    controller('ArduController', function ($scope,mySocket) {
 
        $scope.ledOn = function () {
 
            mySocket.emit('led:on');
            console.log('LED ON');
        };
 
 
        $scope.ledOff = function () {
 
            mySocket.emit('led:off');
            console.log('LED OFF');  
        };    
});
 
</script>  
</body>
```
# Fifth: Starting the app 
Now that’s all is in place start your application with:
```
node server.js
````
Then visit http:\\localhost:3000 . If all is ok you will get a page with two button able to turn on and off the led attached to Arduino.
