SH.erlock is an application developed to support the diagnosis and analysis of failed actuations in Self-Healing applications, using modern Artificial Intelligence techniques executed locally. User interaction is provided through a lightweight web interface built with the Gradio framework, enabling simple and direct use without the need for additional tools.

The solution combines Large Language Models (LLMs), Docker container-based execution, and Retrieval-Augmented Generation (RAG) mechanisms, enabling contextualized analysis of logs, technical documents, and corporate knowledge bases without relying on external services.

Designed to run on-premise on each employee's machine, the agent provides greater control over sensitive data, reduces the exposure of corporate information to external services, and contributes to compliance with the LGPD (Brazilian General Data Protection Law), while also supporting adherence to the information security policies adopted by the client and PowerSyslab.

In this model, processing is performed directly on the user's workstation, leveraging computational resources that are frequently idle (CPU, memory, and, when available, GPU). This enables better utilization of the computing resources already available on the company's workstations, reducing the need for dedicated centralized infrastructure.

The use of the SH.erlock agent enables:

Faster diagnosis of incidents in the Self-Healing system
Support for technical analysis based on historical data and documentation
Reduced reliance on tacit knowledge
Standardized interpretation of logs and events

The solution also benefits from a continuous improvement cycle based on the evolution of documentation in Confluence. As new content, case studies, and procedures are recorded, these materials simultaneously serve as training resources for new employees and as a knowledge source for the AI system through the ingestion (RAG) process. In this way, organizational knowledge is continuously consolidated and reused, promoting technical alignment, reducing reliance on tacit knowledge, and progressively improving the quality of the analyses performed by the agent.
