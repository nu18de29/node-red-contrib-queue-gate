# node-red-contrib-queue-gate
A Node-RED node for controlling message flow, with queueing capability

## Install

Run the following command in your Node-RED user directory (typically `~/.node-red`):

    npm install node-red-contrib-queue-gate

## Usage

The `q-gate` node is similar to the `gate` node published as `node-red-contrib-simple-gate`, with the added capability of queueing messages and releasing them when triggered. 

The node will transmit the input message to its output when in the `open` state and block it when `closed`. In the `queueing` state, the input message is added to the end of the message queue, provided space is available. The queue is limited to 100 messages. Messages in the queue can be released (in the order received) either singly or the entire queue at once.

Messages with the user-defined topic `Control Topic` (set when the node is deployed) are not passed through but are used to control the state of the gate or the queue.

Control messages can have values representing commands for `open`, `close`, `toggle`, `queue`,  `default`, `trigger`, `flush` or `purge`. The (case-insensitive) strings representing these commands are set by the user when the node is deployed. If a control message is received but not recognized, there is no output or change of state, and the node reports an error.

When first deployed or after a `default` command, the gate is in the user-selected state defined by `Default State`. When a valid control message is received, the gate performs one of the following actions:
<p align="center">
  <img width="85%" src="/Users/mikebell/GitHub/node-red-contrib-queue-gate/images/actions.png">
</p>
where flush = send all queued messages; purge = delete all queued messages; dequeue = send oldest message in queue

The specified behaviors of the queuing state (flush before opening, purge before closing, and purge before default) can be reversed by sending a sequence of commands, i.e., [purge, open], [flush, close] or [flush, default].

## Node status
The state of the gate is indicated by a status object: 
<p align="center">
<img width="85%" src="/Users/mikebell/GitHub/node-red-contrib-queue-gate/images/status.png")</p>

where n = the number of messages in the queue

## Examples
This flow demonstrates the basic operation of the `gate` node and the commands that can be used to change its state.

```
[{"id":"4405e5fa.2576d4","type":"gate","z":"5c82144c.a24d2c","name":"gate demo","controlTopic":"control","defaultState":"open","openCmd":"open","closeCmd":"close","toggleCmd":"toggle","defaultCmd":"default","x":310,"y":100,"wires":[["f2454fca.a69808"]]},{"id":"31a144c3.58ab6c","type":"inject","z":"5c82144c.a24d2c","name":"input","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":110,"y":100,"wires":[["4405e5fa.2576d4"]]},{"id":"286fcfb6.811e08","type":"inject","z":"5c82144c.a24d2c","name":"open","topic":"control","payload":"open","payloadType":"str","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":110,"y":160,"wires":[["4405e5fa.2576d4"]]},{"id":"d70ab41.582e248","type":"inject","z":"5c82144c.a24d2c","name":"close","topic":"control","payload":"close","payloadType":"str","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":110,"y":200,"wires":[["4405e5fa.2576d4"]]},{"id":"f46bc39.5974c4","type":"inject","z":"5c82144c.a24d2c","name":"toggle","topic":"control","payload":"toggle","payloadType":"str","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":110,"y":240,"wires":[["4405e5fa.2576d4"]]},{"id":"b044faad.785378","type":"inject","z":"5c82144c.a24d2c","name":"reset","topic":"control","payload":"default","payloadType":"str","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":110,"y":280,"wires":[["4405e5fa.2576d4"]]},{"id":"f2454fca.a69808","type":"debug","z":"5c82144c.a24d2c","name":"output","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"payload","x":470,"y":100,"wires":[]}]
```
<img src="https://github.com/drmibell/node-red-contrib-gate/blob/master/screenshots/gate-demo.png?raw=true"/>

This flow shows how the `gate` node can be controlled from the dashboard using buttons and switches.

```
[{"id":"d81194ef.313d6","type":"gate","z":"389e6e43.7ac652","name":"gate 1","controlTopic":"control","defaultState":"open","openCmd":"open","closeCmd":"close","toggleCmd":"toggle","defaultCmd":"default","x":450,"y":140,"wires":[["f2823cc0.7548c"]]},{"id":"a2c1bfc0.3be47","type":"status","z":"389e6e43.7ac652","name":"status 1","scope":["d81194ef.313d6"],"x":590,"y":180,"wires":[["4e7accb1.6a1244","c7ba6981.1c5ca8"]]},{"id":"ffac76df.157138","type":"inject","z":"389e6e43.7ac652","name":"timestamp","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":140,"y":100,"wires":[["717d6cd.b967594"]]},{"id":"5f1d51af.3f9ef8","type":"gate","z":"389e6e43.7ac652","name":"gate 2","controlTopic":"control","defaultState":"open","openCmd":"open","closeCmd":"close","toggleCmd":"toggle","defaultCmd":"default","x":450,"y":320,"wires":[["9b2a6a4.c99b098"]]},{"id":"10481de0.41060a","type":"ui_switch","z":"389e6e43.7ac652","name":"open/close","label":"open/close","group":"5cfe21d2.307938","order":3,"width":0,"height":0,"passthru":false,"decouple":"false","topic":"control","style":"","onvalue":"open","onvalueType":"str","onicon":"","oncolor":"","offvalue":"close","offvalueType":"str","officon":"","offcolor":"","x":290,"y":340,"wires":[["5f1d51af.3f9ef8"]]},{"id":"dbc56c5c.9a499","type":"ui_button","z":"389e6e43.7ac652","name":"toggle","group":"fdb6c31c.4f2818","order":3,"width":0,"height":0,"passthru":true,"label":"toggle","color":"","bgcolor":"","icon":"","payload":"toggle","payloadType":"str","topic":"control","x":310,"y":140,"wires":[["d81194ef.313d6"]]},{"id":"fb3862a9.febaa8","type":"ui_button","z":"389e6e43.7ac652","name":"reset","group":"fdb6c31c.4f2818","order":4,"width":0,"height":0,"passthru":true,"label":"reset","color":"","bgcolor":"","icon":"","payload":"default","payloadType":"str","topic":"control","x":310,"y":180,"wires":[["d81194ef.313d6"]]},{"id":"717d6cd.b967594","type":"ui_button","z":"389e6e43.7ac652","name":"message 1","group":"fdb6c31c.4f2818","order":5,"width":0,"height":0,"passthru":true,"label":"Send","color":"","bgcolor":"","icon":"","payload":"","payloadType":"date","topic":"anything","x":290,"y":100,"wires":[["d81194ef.313d6"]]},{"id":"cf3d94f3.62e01","type":"ui_button","z":"389e6e43.7ac652","name":"message 2","group":"5cfe21d2.307938","order":4,"width":0,"height":0,"passthru":false,"label":"Send","color":"","bgcolor":"","icon":"","payload":"","payloadType":"date","topic":"","x":290,"y":300,"wires":[["5f1d51af.3f9ef8"]]},{"id":"f2823cc0.7548c","type":"ui_text","z":"389e6e43.7ac652","group":"fdb6c31c.4f2818","order":2,"width":0,"height":0,"name":"display 1","label":"Message","format":"{{msg.payload}}","layout":"row-spread","x":600,"y":140,"wires":[]},{"id":"4e7accb1.6a1244","type":"ui_text","z":"389e6e43.7ac652","group":"fdb6c31c.4f2818","order":1,"width":0,"height":0,"name":"status 1","label":"Status","format":"{{msg.status.text}}","layout":"row-spread","x":740,"y":180,"wires":[]},{"id":"9b2a6a4.c99b098","type":"ui_text","z":"389e6e43.7ac652","group":"5cfe21d2.307938","order":2,"width":0,"height":0,"name":"display 2","label":"Message","format":"{{msg.payload}}","layout":"row-spread","x":600,"y":320,"wires":[]},{"id":"9fdf32be.f66568","type":"ui_text","z":"389e6e43.7ac652","group":"5cfe21d2.307938","order":1,"width":0,"height":0,"name":"status 2","label":"Status","format":"{{msg.status.text}}","layout":"row-spread","x":720,"y":360,"wires":[]},{"id":"d3b465d7.ab11d","type":"inject","z":"389e6e43.7ac652","name":"timestamp","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":140,"y":300,"wires":[["cf3d94f3.62e01"]]},{"id":"7378029e.0e7004","type":"status","z":"389e6e43.7ac652","name":"status 2","scope":["5f1d51af.3f9ef8"],"x":590,"y":360,"wires":[["9fdf32be.f66568"]]},{"id":"66b1fc61.8e356c","type":"inject","z":"389e6e43.7ac652","name":"toggle","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":150,"y":140,"wires":[["dbc56c5c.9a499"]]},{"id":"c7bd73e3.460f","type":"inject","z":"389e6e43.7ac652","name":"reset","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":150,"y":180,"wires":[["fb3862a9.febaa8"]]},{"id":"c7ba6981.1c5ca8","type":"debug","z":"389e6e43.7ac652","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"status","x":750,"y":220,"wires":[]},{"id":"5cfe21d2.307938","type":"ui_group","z":"","name":"Gate #2","tab":"3616a722.b89bc8","order":2,"disp":true,"width":"6","collapse":false},{"id":"fdb6c31c.4f2818","type":"ui_group","z":"","name":"Gate #1","tab":"3616a722.b89bc8","order":1,"disp":true,"width":"6","collapse":false},{"id":"3616a722.b89bc8","type":"ui_tab","z":"","name":"Home","icon":"dashboard"}]
```

<img src="https://github.com/drmibell/node-red-contrib-gate/blob/master/screenshots/gate-dashboard-demo.png?raw=true"/>