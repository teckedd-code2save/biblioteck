# 🔍 Deep Dive: Building a High-Performance Twi ASR Inference Server

While generic MLOps principles are important, the specific requirements of **Automatic Speech Recognition (ASR)** for low-resource languages like **Twi** demand a specialized architectural approach. This article explores the stack and optimizations behind our Twi ASR Inference Server.

## The Model: Whisper (Small) Fine-tuned for Twi

The heart of our server is a **Whisper Small** model fine-tuned on the Serlabs Twi dataset. 

*   **Why Whisper?** Whisper's encoder-decoder transformer architecture is incredibly robust to background noise and varying accents, which is critical for real-world Twi audio.
*   **Why "Small"?** We chose the `small` variant (approx. 244M parameters) because it provides the best balance between transcription accuracy and real-time inference speed on mid-range hardware.

## The Stack: Why BentoML?

Our stack is built around **BentoML**, which serves as the orchestration layer for the model.

1.  **Python 3.11+**: For modern typing and performance.
2.  **PyTorch & Transformers**: The engine and the library used to interface with the Hugging Face weights.
3.  **Librosa**: A specialized library used to resample and normalize incoming audio to 16kHz, which Whisper expects.
4.  **BentoML Runners**: This allows us to separate the "API serving" (handling HTTP requests) from the "Inference Execution" (running the heavy math on the GPU).

## ⚡ Performance Optimizations

To ensure the server can handle production loads, we implemented several key optimizations in our `TwiASRService`:

### 1. FP16 (Half-Precision) Inference
If a GPU is detected, we automatically convert the model to `half-precision` (FP16). This reduces the memory footprint by 50% and significantly increases transcription speed on NVIDIA cards with Tensor Cores, with virtually zero loss in accuracy.

### 2. RTF (Real-Time Factor) Tracking
We now track the **Real-Time Factor (RTF)** for every request. RTF is the ratio of `Processing Time` to `Audio Duration`. 
*   An RTF of **0.1** means we transcribed 10 seconds of audio in just 1 second.
*   By monitoring RTF, we can detect if the server is becoming overloaded before users start complaining.

### 3. Automated System Dependencies
Audio processing requires binary system libraries like `libsndfile1` and `ffmpeg`. Our `bentofile.yaml` now explicitly includes these in the Docker build process, ensuring the container is "batteries-included."

> [!NOTE]
> **The Improved API Response**
> A production API should return more than just text. Our improved server now returns structured metadata that the backend can use for logging and billing:
> 
> ```json
> {
>   "transcript": "Mema wo akye...",
>   "metadata": {
>     "audio_duration_sec": 12.5,
>     "processing_time_sec": 1.2,
>     "rtf": 0.096,
>     "device": "cuda"
>   }
> }
> ```

## Conclusion

By combining the robust transformer architecture of Whisper with the production-ready features of BentoML, we’ve created an ASR server that isn't just accurate—it's efficient, measurable, and ready for the real world.

---

### 🗺️ Navigation
**Previous:** [Article 2: Building the Twi ASR Inference Server](./2_implementation.md)  
**Next:** [Article 3: Scaling & Monitoring](./4_scaling_ops.md)  

**Up:** [Back to README](../../README.md)
