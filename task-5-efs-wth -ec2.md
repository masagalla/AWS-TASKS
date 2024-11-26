
Creating a real-time application using AWS EC2 with an Elastic File System (EFS) volume allows multiple instances to share a common file system,
making it ideal for scenarios like real-time data processing, content management, or collaborative applications. 
Below, I’ll guide you through setting up a simple real-time chat application that utilizes EFS for storing chat messages.

Project Overview: Real-Time Chat Application Using AWS EC2 with EFS

Prerequisites
- An AWS account
- AWS CLI installed and configured
- Basic knowledge of Node.js and Express

Step-by-Step Guide

Step 1: Create an EFS File System

1. **Log in to the AWS Management Console** and navigate to the **EFS** service.
![preview](imgs2/aws%20cosloe%20login.png)

2. **Create a New File System:**
   - Click on "Create file system."
   ![preview](imgs2/create%20efs%20file.png)
   - Choose the default settings, and make sure to create the file system in the same VPC where your EC2 instances will reside.
   - Note the **File System ID** for later use.
   - Click "Create file system."
   ![preview](imgs2/create%20efs%20file.png)
   ![preview](imgs2/efs%20created.png)

3. **Configure Access Points (optional):** You can set access points for permissions and paths, but for simplicity, we’ll use the default settings.

Step 2: Create and Launch EC2 Instances

1. **Navigate to the EC2 Dashboard** in the AWS Management Console.
![preview](imgs2/ec2%20instace%20page.png)

2. **Launch EC2 Instances:**
   - Click on "Launch Instance."
   - Choose an Amazon Machine Image (AMI), such as **Amazon Linux 2**.
   ![preview](imgs2/efs%20ami.png)
   - Select an instance type (e.g., `t2.micro` for the free tier).
   ![preview](imgs2/efs%20t2%20micro.png)
   - Configure instance details, ensuring they are in the same VPC as your EFS.
   - **Add Storage:** Leave the default settings.
   ![preview](imgs2/efs%20default%20storage.png)
   - **Configure Security Group:**
     - Allow inbound traffic on port `22` (SSH) and port `3000` (for the chat application).
     ![preview](imgs2/efs%20sgs.png)
   - Review and launch the instance.
   ![preview](imgs2/efs%20ec2%20instace%20created.png)

3. **Note the public DNS or IP address** of the instance for accessing the application later.
![preview](imgs2/efs%20ec2%20insta%20pub%20ip.png)

Step 3: Mount EFS on the EC2 Instances

1. **SSH into Your EC2 Instance:**
   ```bash
   ssh -i your-key.pem ec2-user@your-instance-public-dns
   ```
   ![preview](imgs2/efs%20connecting%20server.png)
   ![preview](imgs2/efs%20server%20connecting.png)

2. **Install the EFS Utilities:**
   ```bash
   sudo yum install -y amazon-efs-utils
   ```
   ![preview](imgs2/efs%20amazon%20install.png)

3. **Create a Mount Point:**
   ```bash
   sudo mkdir /mnt/efs
   ```
   ![preview](imgs2/efs12.png)

4. **Mount the EFS File System:**
   - Replace `fs-XXXXXX` with your EFS File System ID.
   ```bash
   sudo mount -t efs fs-XXXXXX:/ /mnt/efs
   ```
   ![preview](imgs2/efs12.png)

5. **Ensure EFS is Mounted on Reboot:**
   - Edit the fstab file:
   ```bash
   echo "fs-XXXXXX:/ /mnt/efs efs defaults,_netdev 0 0" | sudo tee -a /etc/fstab
   ```

Step 4: Set Up the Node.js Application

1. **Install Node.js and NPM:**
   ```bash
   sudo yum install -y nodejs npm
   ```
   ![preview](imgs2/efs%20update%2012.png)
   ![preview](imgs2/install%20node%20js.png)
   ![preview](imgs2/installed%20node%20js%20.png)


2. **Create a New Directory for Your Project:**
   ```bash
   mkdir /mnt/efs/chat-app
   cd /mnt/efs/chat-app
   ```
   ![preview](imgs2/creating%20directory.png)

3. **Initialize a New Node.js Project:**
   ```bash
   npm init -y
   ```
   ![preview](imgs2/intialisation%20of%20node%20js.png)

4. **Install Required Packages:**
   ```bash
   npm install express ws body-parser
   ```
   ![preview](imgs2/efs%20npm%20install%20express.png)

5. **Create the Server:**
![preview](imgs2/efs ser)
   - Create a file named `server.js`:
     ```javascript
     
     const express = require('express');
     const WebSocket = require('ws');
     const bodyParser = require('body-parser');
     const fs = require('fs');
     const path = require('path');

     const app = express();
     const server = require('http').createServer(app);
     const wss = new WebSocket.Server({ server });

     const DATA_FILE = '/mnt/efs/messages.txt'; // EFS file path

     app.use(bodyParser.json());
     app.use(express.static('public'));

     app.get('/messages', (req, res) => {
         fs.readFile(DATA_FILE, 'utf8', (err, data) => {
             if (err) return res.status(500).send('Error reading messages');
             res.send(data);
         });
     });

     wss.on('connection', (ws) => {
         console.log('New client connected');

         ws.on('message', (message) => {
             console.log(`Received: ${message}`);
             fs.appendFile(DATA_FILE, message + '\n', (err) => {
                 if (err) console.error('Error saving message:', err);
             });

             // Broadcast the message to all connected clients
             wss.clients.forEach((client) => {
                 if (client.readyState === WebSocket.OPEN) {
                     client.send(message);
                 }
             });
         });

         ws.on('close', () => {
             console.log('Client disconnected');
         });
     });

     const PORT = process.env.PORT || 3000;
     server.listen(PORT, () => {
         console.log(`Server is running on port ${PORT}`);
     });
     ```

6. **Create a Simple HTML Client:**
   - Create a directory named `public`, and inside it create an `index.html` file:
     ```html
     <!DOCTYPE html>
     <html>
     <head>
         <title>Real-Time Chat</title>
         <style>
             body { font-family: Arial, sans-serif; }
             #messages { border: 1px solid #ccc; height: 300px; overflow-y: scroll; }
         </style>
     </head>
     <body>
         <h1>Chat Room</h1>
         <div id="messages"></div>
         <input type="text" id="message" placeholder="Type a message..." />
         <button id="send">Send</button>

         <script>
             const ws = new WebSocket('ws://' + window.location.host);
             const messagesDiv = document.getElementById('messages');
             const messageInput = document.getElementById('message');

             ws.onmessage = function(event) {
                 const message = document.createElement('div');
                 message.textContent = event.data;
                 messagesDiv.appendChild(message);
             };

             document.getElementById('send').onclick = function() {
                 const message = messageInput.value;
                 ws.send(message);
                 messageInput.value = '';
             };
         </script>
     </body>
     </html>
     ```
Step 5: Run Your Application

1. **Start the Server:**
   ```bash
   node server.js
   ```
   ![preview](imgs2/121.png)

2. **Access Your Application:**
   - Open a web browser and navigate to `http://your-instance-public-dns:3000`.
   ![preview](imgs2/nps%20run%20application.png)

Step 6: (Optional) Setup Auto Scaling

If you want to deploy this application across multiple EC2 instances, you can set up an **Auto Scaling Group** and a **Load Balancer** through the EC2 console or using CloudFormation.

Step 7: Cleanup

When you are done with your project, remember to terminate your EC2 instances and delete the EFS to avoid unnecessary charges.

### Conclusion

You have successfully created a real-time chat application using AWS EC2 with EFS for storage! 
This setup allows multiple EC2 instances to access shared data in real-time. 
