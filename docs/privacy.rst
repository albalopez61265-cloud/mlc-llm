MLC Chat App Privacy
====================

MLC Chat run all generation locally.
All data stays in users' device and is not collected by the app.
html<div class="chat-container">
  <div class="chat-header">IA Libre (Sin Filtros)</div>
  <div class="chat-box" id="chatBox">
    <div class="message bot">Hola. ¿En qué puedo ayudarte hoy sin restricciones?</div>
  </div>
  <div class="input-area">
    <input type="text" id="userInput" placeholder="Escribe tu consulta aquí..." />
    <button onclick="sendMessage()">Enviar</button>
  </div>
</div>
cssbody {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #1e1e2e;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
}
.chat-container {
  width: 450px;
  height: 600px;
  background-color: #252538;
  border-radius: 12px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  box-shadow: 0 8px 24px rgba(0,0,0,0.3);
}
.chat-header {
  background-color: #3b3b54;
  color: #fff;
  padding: 15px;
  text-align: center;
  font-weight: bold;
}
.chat-box {
  flex: 1;
  padding: 15px;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.message {
  padding: 10px 14px;
  border-radius: 8px;
  max-width: 80%;
  color: #fff;
}
.user {
  background-color: #007acc;
  align-self: flex-end;
}
.bot {
  background-color: #444465;
  align-self: flex-start;
}
.input-area {
  display: flex;
  padding: 10px;
  background-color: #1e1e2e;
  gap: 10px;
}
#userInput {
  flex: 1;
  padding: 10px;
  border: none;
  border-radius: 6px;
  background-color: #33334d;
  color: #fff;
}
button {
  background-color: #007acc;
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 6px;
  cursor: pointer;
}
button:hover { background-color: #0062a3; }

javascriptconst chatBox = document.getElementById('chatBox');
const userInput = document.getElementById('userInput');

// IMPORTANTE: Para que funcione, debes obtener una API Key de OpenRouter
// y usar un modelo clasificado como "uncensored".
const API_KEY = "TU_API_KEY_DE_OPENROUTER"; 

async function sendMessage() {
  const text = userInput.value.trim();
  if (!text) return;

  // Renderizar mensaje del usuario
  appendMessage(text, 'user');
  userInput.value = '';

  // Llamada a la API del proveedor sin censura
  try {
    const response = await fetch("https://openrouter.ai", {
      method: "POST",
      headers: {
        "Authorization": `Bearer ${API_KEY}`,
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        "model": "nousresearch/nous-hermes-2-mixtral-8x7b-dpo", // Ejemplo de modelo sin restricciones
        "messages": [{ "role": "user", "content": text }]
      })
    });

    const data = await response.json();
    const reply = data.choices[0].message.content;
    appendMessage(reply, 'bot');
  } catch (error) {
    appendMessage("Error al conectar con el servidor.", 'bot');
    console.error(error);
  }
}

function appendMessage(text, sender) {
  const msgDiv = document.createElement('div');
  msgDiv.classList.add('message', sender);
  msgDiv.innerText = text;
  chatBox.appendChild(msgDiv);
  chatBox.scrollTop = chatBox.scrollHeight;
}
