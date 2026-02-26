# Article 3: Scaling & Monitoring: MLOps in the Real World

## Beyond the "Up" Status
Getting a container to run is easy. Keeping it running under the pressure of real-world audio traffic is where the "Ops" in MLOps truly begins. Once your Twi ASR model is in Docker, you need to treat it like a living organism.

## 1. Resource Management & Limits
ML models are greedy. A Whisper model can easily consume 2GB of RAM and 100% of a CPU core during a single transcription. Without limits, one heavy audio file could crash your entire host machine.

In a production environment (like Kubernetes or Docker Compose), you must define resource constraints:
```yaml
services:
  inference:
    image: twi_asr_inference:latest
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 4G
```
This ensures that your model stays in its "sandbox" and doesn't starve other services.

## 2. Intelligent Health Checks
In our FastAPI backend (`main.py`), we implemented a dedicated `/health` endpoint that doesn't just check if the backend is up—it checks the status of the inference server too.

```python
@app.get("/health")
async def health():
    try:
        inference_health = requests.get(f"{INFERENCE_SERVER_URL}/health", timeout=5).json()
    except:
        inference_health = {"status": "unreachable"}
    return {"backend": "healthy", "inference_server": inference_health}
```

This "Cascade Health Check" is vital. If the inference server goes down (e.g., due to an Out-of-Memory error), the load balancer should stop sending traffic to the backend until the model is back online.

## 3. Horizontal Scaling
Because our model is containerized, scaling is "stateless." If users are waiting too long for transcriptions, we don't need a bigger server (Vertical Scaling). We simply spin up three more containers (Horizontal Scaling) and put them behind a load balancer. 

BentoML handles this beautifully by allowing the API server and the Model Runner to run as separate processes, which can even be scaled across different physical machines.

## 4. The Model CI/CD Pipeline
In traditional software, you commit code and deploy. In MLOps, you commit **data or weights** and deploy. A robust pipeline should:
1.  **Trigger:** When a new `ggml-model.bin` is pushed to S3.
2.  **Test:** Run a benchmark suite to ensure the new model isn't *less* accurate than the old one.
3.  **Build:** Run the `bentoml containerize` command.
4.  **Deploy:** Perform a "Blue-Green" deployment where the new model starts serving traffic only after it passes health checks.

## Summary: The Container Advantage
By moving our Twi ASR model into a Docker Model architecture, we’ve transformed a fragile research script into a resilient, scalable microservice. We have:
*   **Separated Concerns:** Backend developers focus on S3 and Auth; ML engineers focus on accuracy.
*   **Guaranteed Consistency:** No more "It worked in my notebook."
*   **Prepared for Growth:** Scaling is now a configuration change, not a code rewrite.

Docker isn't just a packaging tool for ML—it's the foundation of a professional MLOps strategy.
