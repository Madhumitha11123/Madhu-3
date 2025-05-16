# Madhu-3
Madhu 3 description 
1.Back End -app.py
from flask import Flask, request, jsonify
from transformers import pipeline

app = Flask(__name__)

# Load a pretrained conversational pipeline
chatbot = pipeline('conversational', model='microsoft/DialoGPT-medium')

@app.route('/chat', methods=['POST'])
def chat():
    user_input = request.json.get('message')
    if not user_input:
        return jsonify({'error': 'No message provided'}), 400
    
    # Generate chatbot response
    response = chatbot(user_input)
    answer = response[0]['generated_text'] if response else "Sorry, I couldn't understand."
    
    return jsonify({'response': answer})

if __name__ == '__main__':
    app.run(debug=True)
2.Front End-index.html
	<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Customer Support Chatbot</title>
  <style>
    body { font-family: Arial, sans-serif; max-width: 600px; margin: auto; padding: 20px; }
    #chatbox { border: 1px solid #ccc; padding: 10px; height: 400px; overflow-y: scroll; }
    .user { color: blue; }
    .bot { color: green; }
    #message { width: 80%; }
    #send { width: 15%; }
  </style>
</head>
<body>
  <h2>Customer Support Chatbot</h2>
  <div id="chatbox"></div>
  <input type="text" id="message" placeholder="Type your message here..." />
  <button id="send">Send</button>

  <script>
    const chatbox = document.getElementById('chatbox');
    const messageInput = document.getElementById('message');
    const sendButton = document.getElementById('send');

    function appendMessage(sender, text) {
      const p = document.createElement('p');
      p.className = sender;
      p.textContent = (sender === 'user' ? 'You: ' : 'Bot: ') + text;
      chatbox.appendChild(p);
      chatbox.scrollTop = chatbox.scrollHeight;
    }

    async function sendMessage() {
      const message = messageInput.value.trim();
      if (!message) return;
      appendMessage('user', message);
      messageInput.value = '';

      const response = await fetch('/chat', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ message })
      });
      const data = await response.json();
      appendMessage('bot', data.response);
    }

    sendButton.addEventListener('click', sendMessage);
    messageInput.addEventListener('keypress', e => {
      if (e.key === 'Enter') sendMessage();
    });
  </script>
</body>
</html>
