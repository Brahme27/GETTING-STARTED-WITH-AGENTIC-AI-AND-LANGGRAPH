# LangGraph Getting Started Examples

This repository contains two simple example projects to get started with **LangGraph**:

* A basic graph workflow simulating a game selection.
* A simple chatbot graph integrating an LLM.

## What is LangGraph?

* **LangGraph**: A framework to build **stateful, multi-step AI workflows** using graphs.
* **State**: Data that moves and updates through the graph as each step (node) is executed.
* **Node**: A single action or step in the graph that takes input (state), processes it, and outputs an updated state.
* **Edge**: A connection that defines how the flow moves from one node to another. Conditional edges allow dynamic decision-making in the flow.
* **START / END**: Special nodes that define where a graph begins and ends.

## Included Examples

### 🎮 Game Selection Workflow

* Demonstrates creating nodes (`start_play`, `cricket`, `badminton`) and routing with conditional edges.

### 🤖 Simple Chatbot

* Demonstrates integrating OpenAI / Groq LLMs in a LangGraph workflow with message state handling.

## Requirements

* Python 3.12+
pip install -r requirements.txt

## Running

1. Set API keys in a `.env` file:

   ```
   OPENAI_API_KEY=your_openai_key
   GROQ_API_KEY=your_groq_key
   ```
2. Run the scripts in a notebook or Python environment.