# System Summary
    1. Install node.js server
    2. Create Your Project
    3. Install pm2 process manager to enable the node.js server to persist after closing the CLI
    4. Establish a domain that serves the localhost:5000

# 1. Install node.js-server
https://nodejs.org/en/download/current

## Download and install nvm:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

## in lieu of restarting the shell
\. "$HOME/.nvm/nvm.sh"

## Download and install Node.js:
nvm install 24

### Verify the Node.js version:
node -v # Should print "v24.4.1".
nvm current # Should print "v24.4.1".
npm -v # Should print version

### Verify npm version:
npm -v # Should print "11.4.2".


Establishing a Node.js server involves a few fundamental steps, whether you're building a simple HTTP server or a more complex web application with frameworks like Express.js.

This should display the installed versions.

# 2. Create Your Project:
Create a new directory for your project and navigate into it using your terminal: 
Code

    mkdir my-node-server
    cd my-node-server
Initialize a new Node.js project, which creates a package.json file to manage project metadata and dependencies:
Code

    npm init -y
    
4. Choose Your Server Approach:
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

# 3. Install pm2 Process Manager to persist the server
https://betterstack.com/community/guides/scaling-nodejs/pm2-guide/

To harness the power of PM2 for managing your Node.js applications, you'll first need to install its npm package as follows:
Code

        npm install -g pm2
Go ahead and confirm the version you have installed through the following command:
Code
 
        pm2 --version

Interestingly, your PM2 installation actually provides four distinct executables:

pm2: The primary PM2 binary.
pm2-dev: A development tool akin to nodemon that auto-restarts the Node.js process during development.
pm2-runtime: A seamless alternative to the node command, tailored for containerized environments.
pm2-docker: Essentially an alias for pm2-runtime.

When it's time to shift from development to production, the pm2 start command is one to use. First, ensure you've terminated any running development server with Ctrl-C. Then, to launch your application in a production setting, use:

 

pm2 start --name 'dadjokes' server.js
You should see the following output:

 Output
[PM2] Starting /home/ayo/dev/demo/dadjokes/server.js in fork_mode (1 instance)
[PM2] Done.
┌─────┬─────────────┬─────────────┬─────────┬─────────┬──────────┬────────┬──────┬───────────┬──────────┬──────────┬──────────┬──────────┐
│ id  │ name        │ namespace   │ version │ mode    │ pid      │ uptime │ ↺    │ status    │ cpu      │ mem      │ user     │ watching │
├─────┼─────────────┼─────────────┼─────────┼─────────┼──────────┼────────┼──────┼───────────┼──────────┼──────────┼──────────┼──────────┤
│ 0   │ dadjokes    │ default     │ 1.0.0   │ fork    │ 1658     │ 0s     │ 0    │ online    │ 0%       │ 18.3mb   │ ayo      │ disabled │
└─────┴─────────────┴─────────────┴─────────┴─────────┴──────────┴────────┴──────┴───────────┴──────────┴──────────┴──────────┴──────────┘
This table confirms the successful background launch of the dadjokes application. If your terminal window is compact, you might see fewer columns, so consider expanding it for a comprehensive view.

Here's a breakdown of the table columns:

id: A unique identifier assigned by pm2 to the application.
name: The application's name. If the --name flag isn't specified, it defaults to the entry file name (in this case, server.js).
namespace: Allows for batch operations (like start/stop/restart) on a group of apps.
version: The version number sourced from the app's package.json.
mode: Can be either fork or cluster. It directs pm2 to utilize the child_process.fork or the cluster API.
pid: The process's unique identifier.
uptime: Duration since the application's launch. ↺: Counts the number of application restarts. status: Current application status. cpu: Percentage of CPU usage. mem: Memory consumption. user: The username of the individual who initiated the application. watching: Indicates if pm2 is set to auto-restart the app upon detecting file changes in the directory or its subdirectories. This is controlled by the --watch option.
At this point, you can visit http://localhost:3000 in your browser and everything should work just fine as before.

Ensuring a graceful startup
If you need your application to connect to various services (such as databases, caches, etc) on start up, you can use the --wait-ready flag which instructs PM2 to await a ready signal from your application before marking it as "online". Here's how to implement this:

server.js
Code 
    
    . . .
    
    const server = app.listen(process.env.PORT || 3000, () => {
      console.log(`dadjokes server started on port: ${server.address().port}`);
      // simulate a ready application after 1 second
      setTimeout(function () {
        process.send('ready');
      }, 1000);
    });
After integrating the highlighted lines into your application, you'll need to delete the current instance and restart it, this time using the --wait-ready flag:

Code

    pm2 delete dadjokes
    
    pm2 start --name 'dadjokes' server.js --wait-ready
 Output
[PM2] Starting /home/ayo/dev/demo/dadjokes/server.js in fork_mode (1 instance)
[PM2] Done.
┌─────┬─────────────┬─────────────┬─────────┬─────────┬──────────┬────────┬──────┬───────────┬──────────┬──────────┬──────────┬──────────┐
│ id  │ name        │ namespace   │ version │ mode    │ pid      │ uptime │ ↺    │ status    │ cpu      │ mem      │ user     │ watching │
├─────┼─────────────┼─────────────┼─────────┼─────────┼──────────┼────────┼──────┼───────────┼──────────┼──────────┼──────────┼──────────┤
│ 0   │ dadjokes    │ default     │ 1.0.0   │ fork    │ 18529    │ 1s     │ 0    │ online    │ 0%       │ 57.4mb   │ ayo      │ disabled │
└─────┴─────────────┴─────────────┴─────────┴─────────┴──────────┴────────┴──────┴───────────┴──────────┴──────────┴──────────┴──────────┘
By default, if the ready signal isn't received, PM2 will wait for three seconds before designating your application as online. If you wish to adjust this duration, you can use the --listen-timeout flag followed by the desired timeout in milliseconds:

 

pm2 start --name 'dadjokes' server.js --wait-ready --listen-timeout 10000
Up next, we'll delve into how you can monitor your application's resource consumption and other pertinent metrics using PM2.

## Keeping tabs on application metrics
With your application in motion, PM2 offers a suite of subcommands—list, show, and monit—to help you monitor its performance. To get an overview of all active applications on your server, use:

 

pm2 list
You should observe the following output:

 Output
┌─────┬─────────────┬─────────────┬─────────┬─────────┬──────────┬────────┬──────┬───────────┬──────────┬──────────┬──────────┬──────────┐
│ id  │ name        │ namespace   │ version │ mode    │ pid      │ uptime │ ↺    │ status    │ cpu      │ mem      │ user     │ watching │
├─────┼─────────────┼─────────────┼─────────┼─────────┼──────────┼────────┼──────┼───────────┼──────────┼──────────┼──────────┼──────────┤
│ 0   │ dadjokes    │ default     │ 1.0.0   │ fork    │ 18529    │ 3m     │ 0    │ online    │ 0%       │ 53.3mb   │ ayo      │ disabled │
└─────┴─────────────┴─────────────┴─────────┴─────────┴──────────┴────────┴──────┴───────────┴──────────┴──────────┴──────────┴──────────┘
This command provides a snapshot of each application's status, uptime, memory usage, and more. If you have multiple applications running, you can sort them based on specific metrics:

 

pm2 list --sort [name|id|pid|memory|cpu|status|uptime][:asc|desc]
As in:

 

pm2 list --sort memory:desc
For a more detailed look at a specific application, use the pm2 show command followed by the application's name or id:

 

pm2 show dadjokes
[s/t]

For a live dashboard displaying metrics, metadata, and application logs, use:

 

pm2 monit
Screenshot of pm2 monit in action
