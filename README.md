<!DOCTYPE html>
<html lang="fa">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>چت هوش مصنوعی پیشرفته</title>
  <style>
    body {
      margin: 0;
      padding: 0;
      font-family: "Vazir", Arial, sans-serif;
      background-color: #1e1e2f;
      color: #fff;
      height: 100vh;
      display: flex;
      flex-direction: column;
    }
    #home, #chatPage {
      flex: 1;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
    }
    #chatPage { display: none; height: 100%; }
    h2 {
      color: #00d4ff;
      margin-bottom: 15px;
    }
    #modelSelect {
      padding: 10px;
      font-size: 16px;
      border-radius: 8px;
      border: none;
      margin: 10px 0;
      background: #2c2c3d;
      color: #fff;
    }
    button {
      background: #00d4ff;
      border: none;
      padding: 10px 20px;
      font-size: 16px;
      border-radius: 8px;
      cursor: pointer;
      transition: 0.3s;
    }
    button:hover { background: #00aacc; }
    #chatWindow {
      flex: 1;
      width: 100%;
      overflow-y: auto;
      padding: 15px;
      background: #12121c;
      display: flex;
      flex-direction: column;
    }
    .message {
      margin: 8px 0;
      padding: 12px;
      border-radius: 12px;
      max-width: 75%;
      word-wrap: break-word;
      font-size: 15px;
      line-height: 1.5;
    }
    .user {
      background-color: #00d4ff;
      color: black;
      align-self: flex-end;
      border-bottom-right-radius: 0;
    }
    .bot {
      background-color: #2c2c3d;
      color: white;
      align-self: flex-start;
      border-bottom-left-radius: 0;
    }
    #inputArea {
      display: flex;
      padding: 10px;
      background: #1a1a27;
      border-top: 1px solid #333;
    }
    #userInput {
      flex: 1;
      padding: 12px;
      border-radius: 8px;
      border: none;
      font-size: 15px;
      margin-right: 10px;
    }
    #sendBtn {
      padding: 12px 20px;
      border-radius: 8px;
      border: none;
      background: #00d4ff;
      font-size: 15px;
      cursor: pointer;
    }
    #sendBtn:hover { background: #00aacc; }
    .typing {
      font-style: italic;
      opacity: 0.7;
    }
  </style>
</head>
<body>
  <!-- صفحه انتخاب مدل -->
  <div id="home">
    <h2>🤖 انتخاب مدل هوش مصنوعی</h2>
    <select id="modelSelect">
      <option value="openai">OpenAI</option>
      <option value="deepseek">DeepSeek</option>
      <option value="mistral">Mistral (تحلیل تصویر)</option>
      <option value="openai-large">OpenAI Large (GPT-4)</option>
    </select>
    <button onclick="enterChat()">شروع چت</button>
  </div>

  <!-- صفحه چت -->
  <div id="chatPage">
    <div id="chatWindow"></div>
    <div id="inputArea">
      <input type="text" id="userInput" placeholder="پیام خود را وارد کنید...">
      <button id="sendBtn" onclick="sendMessage()">ارسال</button>
    </div>
  </div>

  <script>
    let selectedModel = "openai";

    function enterChat() {
      selectedModel = document.getElementById("modelSelect").value;
      document.getElementById("home").style.display = "none";
      document.getElementById("chatPage").style.display = "flex";
    }

    async function sendMessage() {
      const input = document.getElementById("userInput");
      const text = input.value.trim();
      if (!text) return;

      addMessage(text, "user");
      input.value = "";

      // پیام در حال تایپ
      const typingMsg = addMessage("در حال دریافت پاسخ...", "bot typing");

      const formData = new FormData();
      formData.append("userid", "user123");
      formData.append("prompt", text);
      formData.append("model", selectedModel);

      try {
        const res = await fetch("https://api2.api-code.ir/gpt-save-v2/", {
          method: "POST",
          body: formData
        });
        const data = await res.text();
        typingMsg.remove();
        addMessage(data, "bot");
      } catch (err) {
        typingMsg.remove();
        addMessage("❌ خطا در ارتباط با سرور!", "bot");
        console.error(err);
      }
    }

    function addMessage(text, sender) {
      const chatWindow = document.getElementById("chatWindow");
      const msgDiv = document.createElement("div");
      msgDiv.className = "message " + sender;
      msgDiv.innerHTML = text;
      chatWindow.appendChild(msgDiv);
      chatWindow.scrollTop = chatWindow.scrollHeight;
      return msgDiv;
    }
  </script>
</body>
</html>
