# Article 5: Quick Start: Deploying the Full Agentic Stack with Docker Compose

One of the biggest hurdles in AI development is the setup. To make our Twi ASR system and Agent Sandbox accessible to everyone, we have unified the entire stack into a single **Docker Compose** configuration. 

Now, you can move from "code on GitHub" to a "running agentic environment" in just 60 seconds.

## The One-Command Launch
In the root of our project, we’ve included a `docker-compose.yml` file. This file acts as the conductor for two specialized containers:

1.  **`inference-server`**: Our BentoML-powered Whisper engine running on port 8001.
2.  **`agent-sandbox`**: A secure Python workbench where your agent can safely run code and call the inference engine.

To start the stack, simply run:
```bash
docker-compose up --build
```

## Why This is a Game Changer for Readers
By providing this "Push-to-GitHub" ready infrastructure, we solve three major problems for our readers:

### 1. Zero-Install Environment
Readers don't need to worry if they have the right version of `ffmpeg` or `libsndfile1` installed on their Mac, Windows, or Linux machine. The Dockerfiles handle the system dependencies automatically.

### 2. Isolated Testing
The `agent-sandbox` comes with its own `sandbox_workspace` folder. You can drop audio files into this folder on your host machine, and they will instantly appear inside the agent's safe environment for processing.

### 3. Inter-Service Communication
The `docker-compose` network allows the sandbox to talk to the inference server using a simple internal URL: `http://inference-server:8001`. This mirrors how real-world microservices communicate in the cloud.

## Hands-On: A Simple Test
Once the stack is up, you can test the "Bridge" we talked about in the previous article. Exec into the sandbox and send an audio file to the engine:

```bash
# Enter the sandbox
docker exec -it agent-sandbox bash

# Inside the sandbox, use curl to call the internal inference-server
curl -X POST http://inference-server:8001/transcribe 
  -F "audio_file=@my_twi_audio.wav"
```

## Conclusion: Ready for GitHub
This structure is now ready for a public repository. It provides the **Models** (Inference Server), the **Execution Environment** (Sandbox), and the **Orchestration** (Docker Compose) all in one place. 

Whether you are building a Twi transcription tool or a fully autonomous AI researcher, you now have a rock-solid foundation that is easy to share, easy to test, and easy to scale.
