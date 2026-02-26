# Article 3: Beyond Code: Sandboxing Files and Web Access

## The "Real World" Problem
A sandbox that is *too* isolated can be useless. If an agent is tasked with "Researching the latest Twi translation trends," it needs two things that are traditionally restricted in a sandbox:
1.  **Memory:** The ability to save data from one task to the next.
2.  **Web Access:** The ability to browse the internet.

How do we provide these safely?

## 1. Managed State (Persistent Memory)
If we destroy the container after every task, how does the agent remember its work? 

The solution is **State Externalization**. Instead of saving files *inside* the container, the agent is trained to save results to a "State Store" (like an S3 bucket or a mounted PostgreSQL database).

In the MCP (Model Context Protocol) servers I found in your project, this is exactly what's happening. The `mcp_postgres_server` allows the agent to `add-note` and `summarize-notes`. The database stays persistent, while the agent's "thinking environment" remains ephemeral.

## 2. Controlled Egress (The Internet Firewall)
Giving an agent full internet access is a major security risk. A better approach is **Egress Filtering**:

*   **Allow-listing:** Only allow the agent to talk to specific domains (e.g., `google.com` or `wikipedia.org`).
*   **Proxying:** Use a specialized tool like `curl-impersonate` or a web-scraping API. The agent doesn't browse the web directly; it asks a "Scraper Tool" to fetch a URL for it. This way, the sandbox never actually touches the public internet.

## 3. The "Human-in-the-Loop" Bridge
Sometimes, a sandbox isn't enough. For high-risk actions—like deleting a database record or sending an email—we add a final layer: **Manual Approval.**

The sandbox prepares the action, but it sits in a "Pending" state until a human reviews it. This is a core feature of the ADK patterns, where consequential actions require explicit user confirmation.

## Summary: The Maturity of the Agent Stack
We have moved through three levels of agent architecture:
1.  **Level 1 (Chat):** Pure text, no action.
2.  **Level 2 (Sandboxed Code):** Secure execution of logic in isolated Docker containers.
3.  **Level 3 (Managed Agency):** Sandboxed execution with persistent memory, controlled web access, and human oversight.

## Final Thoughts
Whether you are building a **Docker Model** for high-speed inference or an **Agent Sandbox** for safe code execution, the goal is the same: **Reliability through Isolation.** 

By containerizing these components, we turn the chaotic potential of AI into a predictable, scalable, and secure engineering tool.
