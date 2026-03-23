# Long Term Memory Agent Example



This BPMN process model demonstrates how to build an AI-powered agent with long-term memory capabilities using Camunda. The agent is designed to answer client questions about tech products, learn from human interactions, and improve its knowledge over time.

## Process Overview

The process flow is as follows:

1.  **Initiation**: The process begins when a client submits a question through a start form.
2.  **AI Agent Activation**: The question is passed to a central "Tech Agent," an AI-powered Ad-Hoc Sub-Process. This agent uses an LLM (configured for AWS Bedrock with AI assistant Sonnet) to understand the request and decide on the next best action.
3.  **Tool Execution**: The agent has access to a set of tools to find an answer:
    *   **List of Tech stuff**: A Service Task that calls an external API to get a list of available tech products.
    *   **Query for Policy Stuff**: A Service Task that queries a vector database (Amazon OpenSearch) to find relevant information from its long-term memory.
    *   **Ask the Clerk**: A User Task that assigns a task to a human clerk if the agent cannot find the answer on its own.
    *   **Answer to Client**: A User Task used by the agent to communicate its findings back to the client.
4.  **Learning Loop (Long-Term Memory)**: If the agent consults a human clerk, the process can store the clerk's answer. A gateway checks if the answer should be saved. If so, the "Store in memory" task embeds the new information and adds it to the vector database, making it available for future queries.
5.  **Completion**: The process ends once the agent has provided an answer to the client.

## Key Components

*   **Start Event (Client Question)**: Captures the initial question from the user via a Camunda Form.
*   **Tech Agent (Ad-Hoc Sub-Process)**: The core of the process, containing the AI logic and the tools it can use. It orchestrates the entire question-answering flow.
*   **Service Task (Query for Policy Stuff)**: Searches the knowledge base (vector DB) for answers. Includes an error boundary event to handle cases where the knowledge index is not found.
*   **User Task (Ask the Clerk)**: A human-in-the-loop task for escalating complex questions.
*   **Service Task (Store in memory)**: Updates the vector database with new information learned from the human clerk, effectively creating the agent's long-term memory.
*   **End Event (Client Question Answered)**: Concludes the process instance.

## Setup &amp; Configuration

To run this process, you will need to configure the following:

*   **Camunda Connectors**:
    *   AI Agent Job Worker
    *   HTTP JSON Connector
    *   Embeddings Vector DB Connector
*   **Secrets**: The process relies on secrets for connecting to AWS services (Bedrock, OpenSearch). These must be configured in your Camunda environment (e.g., `AWS_ACCESS_KEY`, `AWS_SECRET_KEY`, `AWS_REGION`, etc.).
*   **Forms**: The associated Camunda Forms for the start event and user tasks must be deployed.