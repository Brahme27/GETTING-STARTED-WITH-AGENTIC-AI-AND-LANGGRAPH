# Import TypedDict to define the structure of our state (a kind of dictionary)
from typing_extensions import TypedDict

# Import tools to build the workflow graph
from langgraph.graph import StateGraph, START, END

# Import Annotated and add_messages for extra instructions on how to handle data
from typing import Annotated
from langgraph.graph.message import add_messages

# Define the State: this is the data passed through our chatbot workflow
class State(TypedDict):
    # messages is a list that will hold chat messages
    # Annotated tells LangGraph to handle messages using add_messages logic (e.g. merging history)
    messages: Annotated[list, add_messages]


# Import os and dotenv to load API keys from .env file (for security)
import os
from dotenv import load_dotenv

# Load the .env file containing the API keys
load_dotenv()

# Set the environment variables for OpenAI and Groq using loaded values
os.environ["OPENAI_API_KEY"] = os.getenv("OPENAI_API_KEY")
os.environ["GROQ_API_KEY"] = os.getenv("GROQ_API_KEY")


# Import and set up OpenAI model using LangChain
from langchain_openai import ChatOpenAI

# Create an OpenAI chat model (gpt-4o)
llm = ChatOpenAI(model="gpt-4o")

# Example: Try sending a message to OpenAI model
llm.invoke("Hii").content


# Import and set up Groq model using LangChain
from langchain_groq import ChatGroq

# Create a Groq chat model (Deepseek Llama model)
llm_groq = ChatGroq(model="Deepseek-R1-Distill-Llama-70b")

# Example: Send a message to Groq model
response = llm_groq.invoke("Hii")

# Print the Groq model's response
response.content


# Define a node (step) in our graph that sends messages to Groq model
def superbot(state: State):
    # Takes in the state, calls Groq model with the messages, and returns a new messages list
    return {"messages": [llm_groq.invoke(state["messages"])]}


# Create a graph (workflow) that uses our State structure
graph = StateGraph(State)

# Add our node (superbot) to the graph
graph.add_node("superbot", superbot)

# Define the start of the graph: from START go to superbot
graph.add_edge(START, "superbot")

# After superbot is done, end the graph
graph.add_edge("superbot", END)

# Compile the graph so it’s ready to run
graph_builder = graph.compile()

# Optional: visualize the graph structure (if running in Jupyter)
from IPython.display import Image, display
display(Image(graph_builder.get_graph().draw_mermaid_png()))


# Run the graph with an initial message
graph_builder.invoke({"messages": ["Hi, my name is Brahmesh"]})

# Stream responses (get them piece by piece instead of waiting for all at once)
graph_builder.stream({"messages": ["Hi, my name is Brahmesh"]})

# Example: print out each streamed response
for i in graph_builder.stream({"messages": ["Hi, my name is Brahmesh"]}):
    print(i)

# Example: print only the values as they stream
for i in graph_builder.stream({"messages": ["Hi, my name is Brahmesh"]}, stream_mode="values"):
    print(i)

# Example: print updates as they stream
for i in graph_builder.stream({"messages": ["Hi, my name is Brahmesh"]}, stream_mode="updates"):
    print(i)



## What is happening overall?
You’re building a chatbot workflow using LangGraph.

State keeps your chat messages.

The superbot node sends the message to Groq’s model and gets a response.

The graph defines the flow: START → superbot → END.

You run the graph with an initial message, and can stream the response in different ways.