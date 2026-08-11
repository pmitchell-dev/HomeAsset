# 🤖 Gladstone Webhost Gemini API Integration Guide

This guide details how your local websites and applications can communicate with the dedicated **Gemini API Backend Service** running on your Webhost server (`192.168.50.217:5050`).

---

## 🛰️ 1. Service Endpoint Overview

| Setting | Value |
| :--- | :--- |
| **Endpoint URL** | `http://192.168.50.217:5050/api/query` |
| **HTTP Method** | `POST` |
| **Request Header** | `Content-Type: application/json` |
| **API Key Security** | **Handled automatically by Webhost.** The frontend does NOT need an API key. (Key is stored in `~/gemini-api/.env`). |
| **CORS Access** | **Enabled (`*`)**. Any local origin or port can fetch without cross-origin blocking. |
| **Health Check** | `GET http://192.168.50.217:5050/health` |

---

## 📩 2. Request Payload Format (JSON)

Send an HTTP `POST` request with a JSON body containing the following fields:

```json
{
  "prompt": "What are 3 quick tips for optimizing web performance?",
  "model": "gemini-3.5-flash",
  "system_instruction": "Respond concisely using markdown bullet points."
}
```

### Parameter Reference

- `prompt` *(Required, string)*: The prompt text or question you want Gemini to analyze/answer.
- `model` *(Optional, string)*: The Gemini model name.
  - Default: `"gemini-3.5-flash"`
  - Options: `"gemini-3.5-flash"`
- `system_instruction` *(Optional, string)*: Persona or system guidance for Gemini (e.g. `"Act as an expert software engineer"`).

---

## 📬 3. Response Payload Format (JSON)

### Successful Response (`HTTP 200 OK`)

```json
{
  "status": "success",
  "result": "Here are 3 tips:\n1. Minify CSS/JS assets...\n2. Use WebP images...\n3. Enable caching...",
  "model": "gemini-3.5-flash"
}
```

### Error Response (`HTTP 400` / `500` / `504`)

```json
{
  "status": "error",
  "message": "Missing required field 'prompt' in JSON request body."
}
```

---

## 💻 4. Client Integration Examples

### JavaScript (Browser / Frontend Website)

```javascript
/**
 * Helper function to query the Webhost Gemini API Service
 * @param {string} promptText - The user prompt to send
 * @param {string} systemInstruction - Optional persona/formatting guidance
 * @param {string} modelName - Optional Gemini model name (default: gemini-3.5-flash)
 * @returns {Promise<string>} The generated response text
 */
async function askGemini(promptText, systemInstruction = '', modelName = 'gemini-3.5-flash') {
    const SERVER_URL = 'http://192.168.50.217:5050/api/query';

    try {
        const response = await fetch(SERVER_URL, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                prompt: promptText,
                model: modelName,
                system_instruction: systemInstruction
            })
        });

        const data = await response.json();

        if (response.ok && data.status === 'success') {
            console.log('Gemini Result:', data.result);
            return data.result;
        } else {
            console.error('Gemini Service Error:', data.message);
            throw new Error(data.message || 'Error querying Gemini API');
        }
    } catch (error) {
        console.error('Network error reaching Webhost Gemini Service:', error);
        throw error;
    }
}

// Example usage on a webpage:
// askGemini('Summarize responsive web design in 2 sentences')
//     .then(text => {
//         document.getElementById('resultContainer').innerText = text;
//     });
```

---

## 🧪 5. cURL (Command Line / Terminal Test)

```bash
curl -X POST http://192.168.50.217:5050/api/query \
     -H "Content-Type: application/json" \
     -d '{
       "prompt": "Hello Gemini, return OK if online.",
       "model": "gemini-3.5-flash"
     }'
```

---

## 🐍 6. Python Client Example

```python
import requests

url = "http://192.168.50.217:5050/api/query"
payload = {
    "prompt": "Explain the concept of API gateways in 3 bullet points.",
    "model": "gemini-3.5-flash"
}

response = requests.post(url, json=payload)
data = response.json()

if data.get("status") == "success":
    print("Gemini Response:", data["result"])
else:
    print("Error:", data.get("message"))
```
