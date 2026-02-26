# Article 2: Building the Workbench: Containerized Execution Environments

## From Theory to Containers
In the previous article, we discussed *why* agents need sandboxes. Now, let's talk about the *how*. While there are many ways to isolate code, **Docker** remains the industry standard for creating reliable, reproducible agent workbenches.

## The Ephemeral Container Pattern
The most secure way to run agent code is the "One-Task-One-Container" pattern. 

1.  **Trigger:** The agent generates a block of code (e.g., Python).
2.  **Provision:** Your orchestrator spins up a new Docker container from a "clean" image (like `python:3.11-slim`).
3.  **Execute:** The code is injected into the container and run.
4.  **Report:** The STDOUT and STDERR are captured and sent back to the agent.
5.  **Destroy:** The container is instantly deleted.

This ensures that the agent starts every task with a completely blank slate.

## The Blueprint: A Minimal Sandbox Dockerfile
You don't want your agent to have *too* many tools. A good sandbox is lean.

```dockerfile
FROM python:3.11-slim

# Install only essential tools
RUN apt-get update && apt-get install -y 
    curl 
    ca-certificates 
    && rm -rf /var/lib/apt/lists/*

# Create a non-root user for security
RUN useradd -m agent_user
USER agent_user
WORKDIR /home/agent_user/workspace

# Pre-install common data science libraries
RUN pip install --no-cache-dir pandas numpy matplotlib
```

## Bridging the Gap: Volume Mounting
How does the agent get its files? We use **Targeted Volume Mounting**. Instead of giving the agent access to your whole project, you mount a single "scratch" directory.

```bash
docker run -v ./agent_scratch:/home/agent_user/workspace my-agent-sandbox
```

This way, the agent can read the CSV you gave it and write its report, but it can't see your `.git` folder or your SSH keys.

## Limiting Resources
To prevent "infinite loop" hallucinations from freezing your server, you must use Docker's resource flags:

*   `--memory="512m"`: Caps RAM usage.
*   `--cpus="0.5"`: Limits the agent to half a CPU core.
*   `--network="none"`: The ultimate security—the agent can run code, but it can't talk to the internet.

## The Result: A Trusted Executor
By using this containerized approach, your agent transitions from a "text generator" to a "reliable engineer." It can test its own code, iterate on errors, and produce verified results, all within the safety of its workbench.

In our final article, we’ll explore how to handle more complex scenarios like multi-step research and persistent memory in a sandboxed world.
