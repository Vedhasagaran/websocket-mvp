# WebSocket MVP

A minimal WebSocket application built with Node.js, Express, and the `ws` library. It demonstrates real-time, bidirectional communication by echoing client messages back, ideal for prototyping chat or notification systems.

## Features
- **HTTP Server**: Serves a simple HTML client interface (`index.html`).
- **WebSocket Server**: Echoes messages sent by connected clients.
- **Minimal Setup**: Quick to set up for real-time app development.

## Prerequisites
- **Node.js**: Version 14 or higher.
- **npm**: Included with Node.js.

## Installation
1. **Create project directory**:
   ```bash
   mkdir websocket-mvp
   cd websocket-mvp
   ```

2. **Initialize project**:
   ```bash
   npm init -y
   ```

3. **Install dependencies**:
   ```bash
   npm install express ws
   ```

4. **Create server file (`server.js`)**:
   ```javascript
   const WebSocket = require('ws');
   const http = require('http');
   const express = require('express');

   const app = express();
   const server = http.createServer(app);
   const wss = new WebSocket.Server({ server });

   app.get('/', (req, res) => {
     res.sendFile(__dirname + '/index.html');
   });

   wss.on('connection', (ws) => {
     console.log('Client connected');
     ws.on('message', (message) => {
       console.log('Received:', message.toString());
       ws.send(`Echo: ${message}`);
     });
     ws.on('close', () => {
       console.log('Client disconnected');
     });
   });

   const PORT = process.env.PORT || 8080;
   server.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

5. **Create client file (`index.html`)**:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
     <title>WebSocket MVP</title>
   </head>
   <body>
     <h1>WebSocket Test</h1>
     <input type="text" id="message" placeholder="Type a message" />
     <button onclick="sendMessage()">Send</button>
     <div id="output"></div>
     <script>
       const ws = new WebSocket('ws://localhost:8080');
       const output = document.getElementById('output');
       ws.onopen = () => {
         output.innerHTML += '<p>Connected to server</p>';
       };
       ws.onmessage = (event) => {
         output.innerHTML += `<p>Server: ${event.data}</p>`;
       };
       ws.onclose = () => {
         output.innerHTML += '<p>Disconnected from server</p>';
       };
       function sendMessage() {
         const message = document.getElementById('message').value;
         ws.send(message);
         document.getElementById('message').value = '';
       }
     </script>
   </body>
   </html>
   ```

## Usage
1. **Run the server**:
   ```bash
   node server.js
   ```
   Server starts at `http://localhost:8080`.

2. **Access the client**:
   - Open `http://localhost:8080` in a browser.
   - Enter a message, click "Send," and view the echoed response.
   - Server logs connections and disconnections in the console.

3. **Test disconnection**:
   - Close the browser tab to see "Client disconnected" in the server console.

## Project Structure
```
websocket-mvp/
├── index.html    # Client HTML with WebSocket logic
├── server.js     # HTTP and WebSocket server
├── package.json  # Project metadata and dependencies
└── node_modules/ # Installed dependencies
```

## How It Works
- **HTTP Server**: Express serves `index.html` for the client.
- **WebSocket Server**: The `ws` library echoes client messages in real-time.
- **Client**: HTML page connects to the WebSocket server, sends messages, and displays responses.

## Extending the MVP
- **Broadcast Messages**:
   ```javascript
   ws.on('message', (message) => {
     wss.clients.forEach((client) => {
       if (client.readyState === WebSocket.OPEN) {
         client.send(message.toString());
       }
     });
   });
   ```
- **Add Authentication**: Use tokens in the WebSocket handshake.
- **Deploy**: Host on platforms like Heroku, ensuring WebSocket support.

## Troubleshooting
- **File not found**: Ensure `index.html` is in the project root.
- **Module errors**: Run `npm install express ws`.
- **Port conflict**: Change `PORT` if 8080 is in use.

## License
MIT License.