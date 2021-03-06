# vlla (Very Large LED Array)
## Background
the vlla resides on 2E, East Campus, MIT. vlla represents a rethinking of the traditional LED paradigm, and uses traditional, GMO free, farm-to-table LED rendering techniques. the vlla is (probably) not sentient. beast is not a hivemind.

## See it in action!
![He-Man](https://thumbs.gfycat.com/CalmLinedDesertpupfish-size_restricted.gif)
![Nyan cat](https://thumbs.gfycat.com/FatScrawnyEland-size_restricted.gif)
![Conway's game of life](https://thumbs.gfycat.com/BlandIllustriousAlaskajingle-size_restricted.gif)
## Launching
### Server
```sh
cd server
npm run start
```

### Client
```sh
cd client
npm run start -- visualizations/[YOUR VISUALIZATION]
```

## Structure
### /client
The local client starts up a node server that sends frames to the server. Ideally, you'll want to keep this local client running when no remote clients are connected to the vlla. You're expected to write your visualizations in `/client/visualizations`. Since this renders to node-canvas, any valid HTML5 Canvas commands will work.

#### Example
```javascript
var vlla = require('../libvlla.js');

// request a canvas and context to render to.
// vlla will ensure the canvas has the correct dimensions
var canvas = vlla.requestCanvas();
var context = vlla.requestContext();

// clear the background
context.fillStyle = 'rgb(0, 0, 0)';
context.fillRect(0, 0, vlla.width, vlla.height);

// write 'hello' to canvas
context.fillStyle = 'rgb(255, 255, 0)';
context.font = '20px Impact';
context.fillText("hello", 0, 29);

// actually send the frame to the vlla
vlla.render();
```

### /server
WebSocket server that accepts connections on **port 8080**. To send a frame, send a ``vlla`` request with the data as a binary array with 60 x 32 x 3 bytes, in R G B R G B format (each color channel is one byte). The server splits and forwards the data to the Teensys through serial.

### /VideoDisplay_8bit
Teensy driver. Accepts 1 byte per LED (960 bytes total, 60 wide by 16 px high), sent in reading order (left-to-right, then top-to-bottom). Each byte should be in the 256 color format: RRRGGGBB. Before sending the data, you must send 0xFF (255). This helps the Teensy know when a frame starts. **Make sure that in your color data, you aren't sending 0xFF (255).**
