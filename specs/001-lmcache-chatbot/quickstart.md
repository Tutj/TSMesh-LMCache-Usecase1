# Quickstart Guide: Multi-Turn Conversational AI

**Date**: 2026-02-06
**Status**: Draft

This guide provides instructions for setting up and running the conversational AI project.

## Prerequisites

- Python 3.11
- Poetry for dependency management
- Access to a running vLLM instance

## Setup

1.  **Clone the repository**:
    ```bash
    git clone [repository-url]
    cd [repository-name]
    ```

2.  **Install dependencies**:
    ```bash
    poetry install
    ```

3.  **Configure the environment**:
    Create a `.env` file and add the following variables:
    ```
    VLLM_API_BASE="http://localhost:8000"
    LANGFUSE_PUBLIC_KEY="..."
    LANGFUSE_SECRET_KEY="..."
    ```

## Running the Application

1.  **Start the API server**:
    ```bash
    poetry run uvicorn src.main:app --host 0.0.0.0 --port 8080
    ```
    The API will be available at `http://localhost:8080`.

## Verifying Performance Claims

The success criteria can be verified using a set of test scripts that will be provided in the `tests/performance` directory.

1.  **Run the performance tests**:
    ```bash
    poetry run pytest tests/performance
    ```

2.  **Interpret the results**:
    The test script will output a report with the following metrics:
    -   **TTFT (Time to First Token)**: The script will measure the TTFT for both cached and non-cached responses and calculate the percentage decrease.
    -   **Cache Hit Rate**: The script will send a series of repeated prompts and measure the cache hit rate.
    -   **CPU Usage**: The script will simulate a long conversation and measure the CPU usage for the first and last few turns.

3.  **Monitor in Langfuse**:
    You can also monitor the performance in real-time in the Langfuse UI. The dashboards will be pre-configured to show the key metrics.
