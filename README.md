# Embedding the Assistly Chatbot in Zendesk

1. Ensure Correct Node.js Version
Install nvm (Node Version Manager) if you don’t already have it:



curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash source ~/.nvm/nvm.sh 
Install and switch to Node 20.17.0 (required by ZCLI):



nvm install 20.17.0 nvm use 20.17.0 nvm alias default 20.17.0
Verify:



node -v    # → v20.17.0 npm -v
2. Install the Zendesk CLI (ZCLI)


npm install -g @zendesk/zcli zcli help    # confirms installation
3. Scaffold Your Zendesk App


mkdir zendesk-devsai-chat 
cd zendesk-devsai-chat 
zcli apps:new
It will then prompt for:

Directory name: zendesk-devsai-chat

Author name: your name

Author email: Zendesk user email

App name: Assistly Chatbot

Resulting structure:



zendesk-devsai-chat/
├── assets/
│   ├── iframe.html
│   └── icon.png
├── manifest.json
├── translations/
└── README.md
 

4. Update manifest.json
Point the ticket sidebar at the bundled asset and give it a taller height:



{
  "name": "Assistly Chatbot",
  "author": {
    "name": "Rohit Shah",
    "email": "rohit.shah@appdirect.com"
  },
  "defaultLocale": "en",
  "private": true,
  "location": {
    "support": {
      "ticket_sidebar": {
        "url": "assets/iframe.html",
        "flexible": true,
        "size": {
         "height": "800px"
       }
      }
    }
  },
  "version": "1.0.0",
  "frameworkVersion": "2.0"
} 
5. Embed the devs.ai  Script
In assets/iframe.html, replace the <body> with devs.ai component code:



<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <title>Assistly Chatbot</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      height: 100%;
    }
    #chat-wrapper {
      display: flex;
      flex-direction: column;
      height: 100%;
    }
    #chat-wrapper h4 {
      margin: 0;
      padding: 8px;
      background: #f5f5f5;
    }
    #chat-container {
      flex: 1;               /* take remaining space */
      min-height: 400px;     /* or whatever minimum you like */
      overflow: hidden;
    }
  </style>
</head>
<body>
  <div id="chat-wrapper">
    <div id="chat-container"></div>
  </div>
  <script>
    (function(d, s) {
      var f = d.getElementsByTagName(s)[0],
          j = d.createElement(s);
      j.async = true;
      j.id = 'appdirect-ai-embed';
      j.dataset.key = 'ek-aa271dcc001f32f032ed52cd52de5186a312a1c98cb8d701';
      j.dataset.container = 'chat-container';  // tells the embed where to mount
      j.src = 'https://devs.ai/embed-static/embed.js';
      f.parentNode.insertBefore(j, f);
    })(document, 'script');
  </script>
</body>
</html>
6. Whitelist Hosts in devs.ai Dashboard
Add http://localhost:4567 (this was for my local system)

Add https://origonetworks.zendesk.com (for production)

7. Local Development & Testing
Open Terminal and run below command:



zcli apps:server zendesk-devsai-chat 
Open any ticket URL with ?zcli_apps=true to see only your local app.

Open without params to see your app alongside all production apps.

8. Package & Deploy
Automatic upload & install:

zcli apps:create zendesk-devsai-chat 
Or manual package:



zcli apps:package zendesk-devsai-chat 
Then upload the ZIP in Admin Center → Apps & Integrations → Zendesk Support Apps → Upload Private App.

9. Verify & Finalize
In Admin Center, ensure all sidebar apps (including devs.ai ) are Enabled and assigned to the correct roles/groups.

Test in production (no ?zcli_apps=true) to confirm all apps reappear and the chatbot functions.

 

Also, the steps were referred from Zendesk support guide : Building your first Support app - Part 1: Laying the groundwork
