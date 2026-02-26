# Article 2: Building the Twi ASR Inference Server with BentoML & Docker

## Why BentoML?
While you *can* wrap a model in a raw FastAPI script, it’s like using a sedan to move furniture—it works, but it’s not efficient. **BentoML** is the moving truck. It is a framework specifically designed for ML serving that handles things like request batching, model versioning, and worker management out of the box.

## Step 1: Defining the Service
In our Twi ASR implementation, the core logic lives in `twi_asr_service.py`. We define a `Runner` which is a specialized abstraction that BentoML uses to scale model execution independently of the API logic.

```python
class TwiASRModel(bentoml.Runnable):
    SUPPORTED_RESOURCES = ("cpu",) # Or "cuda" for GPUs
    
    def __init__(self):
        self.processor = WhisperProcessor.from_pretrained("teckedd/whisper-small-serlabs-twi-asr")
        self.model = WhisperForConditionalGeneration.from_pretrained("teckedd/whisper-small-serlabs-twi-asr")
```

By wrapping the model in a `bentoml.Service`, we automatically get a standardized API with a Swagger UI for testing.

## Step 2: The Blueprint (`bentofile.yaml`)
The "magic" of BentoML is that you don't have to write a complex Dockerfile manually. Instead, you define a `bentofile.yaml`:

```yaml
service: "twi_asr_service:svc"
labels:
  stage: "production"
python:
  requirements: |
    transformers==4.35.0
    librosa==0.10.0
    torch==2.0.0
```

When you run `bentoml build`, the framework analyzes this file and packages your code, models, and dependencies into a "Bento."

## Step 3: Containerization
Once the Bento is created, turning it into a Docker image is a single command:

```bash
bentoml containerize twi_asr_inference:latest
```

BentoML intelligently chooses a base image (like `python:3.9-slim` or a CUDA-enabled image) and installs all your requirements. The result is an optimized Docker image ready for the cloud.

## Why this is better than a manual Dockerfile
1.  **Optimized Layers:** BentoML knows how to structure the Docker layers so that heavy dependencies (like Torch) are cached, making subsequent builds much faster.
2.  **Standardized Interface:** Every model you deploy with BentoML will have a consistent health check and monitoring endpoint.
3.  **Local Testing:** You can run `bentoml serve` locally to replicate the production environment exactly before you ever push to a container registry.

## Next Steps
Now that our model is "Dockerized" and running on port 8001, how do we make sure it stays healthy and handles high traffic? In the final article, we’ll explore scaling and monitoring in a real-world MLOps pipeline.
