meta-ai-chatbot/
│
├── app/
│   ├── __init__.py
│   └── main.py
│
├── static/
│   ├── index.html
│   └── script.js
│
├── .env.example
├── .gitignore
├── requirements.txt
├── README.mdfrom fastapi import FastAPI
from fastapi.staticfiles import StaticFiles
from pydantic import BaseModel
import os
from huggingface_hub import InferenceClient
from dotenv import load_dotenv

load_dotenv()

HF_TOKEN = os.getenv("HF_TOKEN")
MODEL_ID = "distilgpt2"

client = InferenceClient(token=HF_TOKEN)

app = FastAPI(title="Meta AI Chatbot")

# Serve frontend
app.mount("/", StaticFiles(directory="static", html=True), name="static")

class Query(BaseModel):
    text: str
    max_length: int = 120


@app.post("/api/chat")
async def chat(q: Query):
    output = client.text_generation(
        model=MODEL_ID,
        inputs=q.text,
        parameters={"max_new_tokens": q.max_length}
    )

    if isinstance(output, list):
        return {"reply": output[0]["generated_text"]}

    return {"reply": str(output)}
from fastapi import FastAPI
from fastapi.staticfiles import StaticFiles
from pydantic import BaseModel
import os
from huggingface_hub import InferenceClient
from dotenv import load_dotenv

load_dotenv()

HF_TOKEN = os.getenv("HF_TOKEN")
MODEL_ID = "distilgpt2"

client = InferenceClient(token=HF_TOKEN)

app = FastAPI(title="Meta AI Chatbot")

# Serve frontend
app.mount("/", StaticFiles(directory="static", html=True), name="static")

class Query(BaseModel):
    text: str
    max_length: int = 120


@app.post("/api/chat")
async def chat(q: Query):
    output = client.text_generation(
        model=MODEL_ID,
        inputs=q.text,
        parameters={"max_new_tokens": q.max_length}
    )

    if isinstance(output, list):
        return {"reply": output[0]["generated_text"]}

    return {"reply": str(output)}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Meta AI Chatbot</title>
    <link rel="stylesheet" href="style.css" />
</head>
<body>

    <div class="container">
        <h1>Meta AI Chatbot</h1>

        <div id="chat-box"></div>

        <div class="input-area">
            <input id="user-input" type="text" placeholder="Type your message..." />
            <button onclick="sendMessage()">Send</button>
        </div>
    </div>

    <script src="script.js"></script>
</body>
</html>
body {
    background: #0d0d0d;
    color: white;
    font-family: Arial, sans-serif;
    display: flex;
    justify-content: center;
    padding-top: 40px;
}

.container {
    width: 90%;
    max-width: 500px;
}

h1 {
    text-align: center;
    margin-bottom: 20px;
}

#chat-box {
    background: #1a1a1a;
    height: 400px;
    overflow-y: auto;
    padding: 15px;
    border-radius: 10px;
    margin-bottom: 20px;
}

.msg {
    margin-bottom: 15px;
}

.user {
    text-align: right;
    color: #00eaff;
}

.bot {
    text-align: left;
    color: #81ff6b;
}

.input-area {
    display: flex;
    gap: 10px;
}

input {
    flex: 1;
    padding: 12px;
    border-radius: 8px;
    border: none;
    outline: none;
}

button {
    padding: 12px 18px;
    border: none;
    border-radius: 8px;
    background: #00eaff;
    color: black;
    font-weight: bold;
    cursor: pointer;
}async function sendMessage() {
    const input = document.getElementById("user-input");
    const text = input.value.trim();
    if (!text) return;

    addMessage(text, "user");
    input.value = "";

    const response = await fetch("/api/chat", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ text, max_length: 100 })
    });

    const data = await response.json();
    addMessage(data.reply, "bot");
}

function addMessage(text, sender) {
    const box = document.getElementById("chat-box");
    const msg = document.createElement("div");
    msg.className = "msg " + sender;
    msg.innerText = text;
    box.appendChild(msg);
    box.scrollTop = box.scrollHeight;
}
git clone https://github.com/ttsh933-lab/Meta-ai.git
cd Meta-ai

python -m venv venv
# Linux / Mac:
source venv/bin/activate
# Windows:
# venv\Scripts\activate


pip install --upgrade pip
pip install -r requirements.txt

python app.py
# python chat.py


# Default URL:
# http://127.0.0.1:5000
venv\Scripts\activate
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
python app.py