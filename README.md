# 🎙️ Deepgram + Twilio Voice Agent

A real-time AI voice agent that connects **Twilio Media Streams** with the **Deepgram Voice Agent API** using Python and WebSockets.

The application receives live audio from Twilio phone calls, streams it to Deepgram for conversational AI processing, and returns synthesized voice responses to the caller in real time.

## 🚀 Features

- Real-time phone conversations
- Twilio Media Streams integration
- Deepgram Voice Agent API integration
- Full-duplex audio streaming using WebSockets
- Barge-in support (interrupt AI while speaking)
- Environment-based API key management
- Asynchronous Python implementation

## 🏗️ Architecture

```text
              Caller
                 │
                 ▼
          Twilio Phone Number
                 │
                 ▼
          Twilio Media Stream
                 │
                 ▼
       Python WebSocket Server
                 │
          ┌──────┴──────┐
          ▼             │
   Deepgram Voice       │
      Agent API         │
          │             │
          ▼             │
   AI Audio Response    │
          │             │
          ▼             │
      Twilio Stream ◄───┘
          │
          ▼
        Caller
```

## 📂 Project Structure

```text
deepgram_twilio_voice_agent/
│
├── main.py              # Main application
├── config.json          # Deepgram agent configuration
├── .env                 # Environment variables
├── .env.example         # Example environment file
├── requirements.txt     # Project dependencies
└── README.md            # Project documentation
```

## 🔧 Prerequisites

Before running the project, ensure you have:

- Python 3.10+
- Twilio account
- Twilio phone number
- Deepgram API key
- Publicly accessible endpoint (Ngrok, Cloudflare Tunnel, etc.)

## 🔄 How It Works

### 1. Twilio Connection
When a phone call arrives, Twilio Media Streams establishes a WebSocket connection with the Python server.

### 2. Audio Buffering
Incoming caller audio is buffered into chunks for efficient streaming.

### 3. Deepgram Processing
The buffered audio is forwarded to Deepgram's Voice Agent API through:

```text
wss://agent.deepgram.com/v1/agent/converse
```

### 4. AI Response Generation
Deepgram processes the incoming audio and generates conversational responses with synthesized speech.

### 5. Response Playback
The application streams the generated audio back to Twilio for real-time playback to the caller.

## 🗣️ Barge-In Support

Barge-in allows callers to interrupt the AI while it is speaking.

When the caller starts speaking, Deepgram sends a `UserStartedSpeaking` event.

```python
if decoded["type"] == "UserStartedSpeaking":
```

The application sends a `clear` event to Twilio:

```json
{
    "event": "clear",
    "streamSid": "..."
}
```

This clears the queued audio playback, allowing the caller to interrupt the AI naturally.

## 📦 Main Components

| Function | Responsibility |
|---|---|
| `sts_connect()` | Establishes a WebSocket connection with Deepgram using the API key. |
| `twilio_receiver()` | Receives audio from Twilio and forwards it to Deepgram. |
| `sts_sender()` | Sends buffered caller audio to Deepgram. |
| `sts_receiver()` | Receives Deepgram responses and relays them to Twilio. |
| `handle_barge_in()` | Handles interruptions when the caller starts speaking. |
| `twilio_handler()` | Coordinates communication between Twilio and Deepgram. |

## 🛠️ Tech Stack

- **Language:** Python
- **Telephony:** Twilio
- **Voice AI:** Deepgram Voice Agent API
- **Communication:** WebSockets
- **Architecture:** Asynchronous programming
