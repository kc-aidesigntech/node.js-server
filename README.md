# node.js-server

Establishing a Node.js server involves a few fundamental steps, whether you're building a simple HTTP server or a more complex web application with frameworks like Express.js.
1. Install Node.js and npm:
Download and install Node.js from the official website (nodejs.org). This installation typically includes npm (Node Package Manager) as well.
Verify the installation by opening your terminal or command prompt and running: 
Code

    node -v
    npm -v
This should display the installed versions.
2. Create Your Project:
Create a new directory for your project and navigate into it using your terminal: 
Code

    mkdir my-node-server
    cd my-node-server
Initialize a new Node.js project, which creates a package.json file to manage project metadata and dependencies:
Code

    npm init -y
3. Choose Your Server Approach:
Simple HTTP Server (Core Node.js): For basic server functionality without external dependencies.
Create a file (e.g., server.js) and add the following code: 
JavaScript

        const http = require('http');

        const server = http.createServer((req, res) => {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('Hello World from Node.js!');
        });

        const port = 3000;
        server.listen(port, () => {
            console.log(`Server running at http://localhost:${port}/`);
        });
Express.js Server (Recommended for Web Applications): Express.js simplifies routing, middleware, and request handling.
Install Express.js:
Code

        npm install express
Create a file (e.g., app.js) and add the following code: 
JavaScript

        const express = require('express');
        const app = express();
        const port = 3000;

        app.get('/', (req, res) => {
            res.send('Welcome to my Express.js server!');
        });

        app.listen(port, () => {
            console.log(`Server running at http://localhost:${port}/`);
        });
4. Run Your Server:
Open your terminal in the project directory and execute the server file:
For the simple HTTP server:
Code

        node server.js
For the Express.js server.
Code

        node app.js
Your Node.js server will now be running and accessible in your web browser at the specified address (e.g., http://localhost:3000/).
