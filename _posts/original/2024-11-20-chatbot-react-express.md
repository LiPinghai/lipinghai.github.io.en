---
layout: page
title: 'Building a Full-Stack Chatbot: React Frontend, Express Backend, and OpenAI API Integration'
categories: [tech]
tags: [original]
excerpt: 'Building a Full-Stack Chatbot: React Frontend, Express Backend, and OpenAI API Integration'
---
[DEMO](https://www.lipinghai.com/chatbot/)https://www.lipinghai.com/chatbot/

Chatbots are becoming increasingly popular for creating interactive web applications, and combining the power of React, Express, and OpenAI's API makes it easy to develop one. In this tutorial, you’ll learn how to build a fully functional chatbot that features:

* A React frontend for a chatgpt-like and user-friendly interface.
* An Express backend that handles API calls and serves the React app as static files.
* Integration with the OpenAI API for natural language processing.

By the end of this guide, you’ll have a chatbot website that is ready to be deployed, combining seamless interaction and advanced AI capabilities. Let’s dive in!

## Build the server

### 1. Set Up the Project

We use node.js and Express to build a easy simply api.

1. Create a new project directory:

```bash
  mkdir chatbot
  cd chatbot
```

2. Initialize a Node.js project:

```bash
npm init -y
```

Then a package.json file will appear.

3. Install the required dependencies:

```bash
npm install express cors openai dotenv
```

### 2. Get Your OpenAI API Key

1. Sign up at [OpenAI](https://platform.openai.com/) if you don’t have an account.
2. Generate an API key from the [OpenAI API keys page](https://platform.openai.com/api-keys).
3. Keep the API key secure; you’ll use it in the .env file.

### 3. Create the Express Server

Create a file named server.js in the project root directory.

Add the following code to build the server:

```javascript
const express = require("express");
const cors = require("cors");
const { Configuration, OpenAIApi } = require("openai");
require("dotenv").config();

const app = express();

// Middleware
app.use(cors());
app.use(express.json());

// Configure OpenAI API
const configuration = new Configuration({
  apiKey: process.env.OPENAI_API_KEY,
});
const openai = new OpenAIApi(configuration);

// Define the chat endpoint
app.post("/chatbot/chat", async (req, res) => {
  const { message } = req.body;

  try {
    const response = await openai.createChatCompletion({
      model: "gpt-3.5-turbo",
      messages: [{ role: "user", content: message }],
    });

    res.json({ reply: response.data.choices[0].message.content });
  } catch (error) {
    console.error(error);
    res.status(500).json({ error: "Something went wrong with the API." });
  }
});

// Start the server
const PORT = process.env.PORT || 3005;
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

Now we can run the server:

```bash
node server.js
```

And we can also add a script in package.json:
```js
  "scripts": {
    "server": "node server.js",
  }
```
Then we can launch the server by `npm run server` command.

Then we can test the `/chat` endpoint using tools like Postman or curl:

```bash
curl -X POST http://localhost:3005/chatbot/chat \
-H "Content-Type: application/json" \
-d '{"message": "Hello, ChatGPT!"}'
```
If everything works correctly, you should receive a response from ChatGPT.

## Create the React Web App

### Step 1: Set Up React Project
Create a new React + Vite project:

```bash
npm create vite@latest chatbot-frontend
```

Choose the following options:

* Framework: React
* Variant: JavaScript or TypeScript (your preference)
Navigate to the project directory and install dependencies: 

```bash
cd chatbot-frontend
npm install
```

Start the development server:

```bash
npm run dev
```

This starts the app at http://localhost:5173.

### Step 2: Create the Chatbot Page

#### 2.1 Modify src/main.jsx
This file is the entry point of the app. It should look like this:

```javascript
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import './index.css'
import App from './App.jsx'

createRoot(document.getElementById('root')).render(
  <StrictMode>
      <App />
  </StrictMode>,
)
```

#### 2.2 Build the Chat Interface in src/App.jsx
Replace the content of App.jsx with this simple chatbot UI:

```javascript
import * as React from 'react';
import { useState } from "react";
import './App.css'
import axios from "axios";

const Default = () => {

    const [inputText, setInputText] = useState("");

    const list = []
    const [chatList, setChatList] = useState(list);

    const addChatItem = (newItem) => {
      setChatList(prevItems => [...prevItems, newItem]);
    };

    const handleKeyDown = (event) => {
      if (event.key === 'Enter') {
        sendMessage();
      }
    };

    const sendMessage = async() => {

        const input = document.querySelector('.chat-text');
        const question = input.value;
        addChatItem({from:'self', content: question});
        setInputText('');
        
        const response = await axios.post("/chatbot/chat", {
          question,
        });
        addChatItem({from:'other', content: response.data});
 
    };

    return (
      <div className='control-pane'>
        <div className="control-section">
          <h1 hidden={chatList.length}>What can I help with?</h1>
          <ul className='chat-list' hidden={!chatList.length}>
            {chatList.map((chat, index) => (
              <li className='chat-item' key={index}>
                <div className={'chat-content ' + (chat.from === 'self' ? 'chat-content-self': 'chat-content-other')  }>
                  {chat.content}
                </div>
              </li>
            ))}
          </ul>
          <div className={'chat-input '+ (chatList.length ? 'chat-input-chated' : '' )}>
            <input maxLength="500" rows="5" value={inputText} className='chat-text' type="text" onChange={(e)=>setInputText(e.target.value)} onKeyDown={handleKeyDown} placeholder="Ask me anything"/>
            <button className={'submit-button ' + (inputText ? 'inputed' : '')}  onClick={sendMessage}>↑</button>
          </div>
        </div>
      </div>);
};

export default Default;
```

### Step 3: Add Styles in src/App.css
Create a basic style for the chatbot UI in App.css:

```css
#root {
  max-width: 1280px;
  min-width: 500px;
  margin: 0 auto;
  text-align: center;
  height: 100vh;
  position: relative;
  box-sizing: border-box;
}

.chat-input {
  color: rgb(13, 13, 13);
  background-color: rgb(244, 244, 244);
  line-height: 40px;
  padding: 6px 10px;
  border-radius: 25px;
  width: 100%;
  box-sizing: border-box;
}

.chat-input-chated {
  position: absolute;
  bottom: 10px;
}

.chat-text{
  background-color: rgb(244, 244, 244);
  border: none;
  font-size: 16px;
  width: 85%;
}

.submit-button {
  display: flex;
  justify-content: center;
  background-color: rgb(227, 227, 227);
  color: rgb(244, 244, 244);
  font-size: 28px;
  border-radius: 100%;
  border: none;
  width: 32px;
  height: 32px;
  font-family: system-ui;
  font-weight: 600;
  cursor: pointer;
  float: right;
  top: 4px;
  position: relative;

}

.inputed {
  background-color: rgb(0, 0, 0);
}

ul {
  padding-left: 0;
}

li {
  list-style: none;
}

.chat-list {
  padding-left: 0;
  max-height: calc(100vh - 80px);
  overflow: auto;
  scroll-behavior: smooth;
}

.chat-content {
  width: fit-content;
  max-width: 400px;
  display: flex;
  margin-bottom: 8px;
  padding: 10px 20px;
  line-height: 22px;
  border-radius: 25px;
  word-break: break-word;
  text-align: left;
}

.chat-content-self {
  color: rgb(13, 13, 13);
  background-color: rgb(244, 244, 244);
  justify-self: end;
}

input:focus{
  outline: none;
}
```

### Step 4: Run and Test
Start the Vite development server:

```bash
npm run dev
```

Open the app in the browser at http://localhost:5173.

Type a message in the input box and send it. The chatbot will call your backend and display the ChatGPT response.

## Build and Deploy
To build the app for production:

```bash
npm run build
```

This creates a production build of the React app in the client/build directory.

Go back to the root project directory:

```bash
cd ..
```

Start the Express server:

```bash
node server.js
```

Deploy the generated files in the dist directory to a static hosting service like Vercel, Netlify, or your custom server.
