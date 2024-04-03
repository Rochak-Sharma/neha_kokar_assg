<div align="center">
  <h1>AI Tutor with Agent-Based Infrastructure</h1>
  <p>An intelligent chatbot that can handle text and image inputs, providing relevant YouTube video links for better understanding.</p>
  <br>
</div>

<div align="center">
  <img src="https://img.freepik.com/free-vector/chat-bot-concept-illustration_114360-5522.jpg" alt="AI Tutor" width="300">
</div>

<br>

## ✨ Features

- 💬 Responds to both text and image-based queries
- 🔗 Provides real YouTube video links using the YouTube Data API (not generated by the LLM)
- 🧠 Maintains conversation memory for context (using an in-memory buffer)
- 🤖 Utilizes OpenAI language models for text generation
- 🛠️ Follows an agent-based architecture with different tools (image analysis, YouTube search)
- 📦 Built using the powerful LangChain framework

## 📋 Requirements

- Python 3.7+
- OpenAI API key
- Google API key
- Google Custom Search Engine ID

## 🚀 Set up the environment variables:
export OPENAI_API_KEY="your_openai_api_key"
export GOOGLE_API_KEY="your_google_api_key"
export GOOGLE_CSE_ID="your_google_cse_id"

Interact with the AI Tutor by providing text or image inputs:

## 🤝 Contributing

Contributions are welcome! If you find any issues or have suggestions for improvements, please open an issue or submit a pull request.

## 📄 License

This project is licensed under the [MIT License](LICENSE).

<div align="center">
  <sub>Made with ❤︎ by <a href="https://github.com/your-username">Your Name</a></sub>
</div>

```python
import os
from typing import Any, Dict, List
from langchain import OpenAI, LLMChain
from langchain.agents import AgentType, initialize_agent
from langchain.agents.agent_toolkits import (
    PlayWrightBrowserToolkit,
    PlaywrightBrowserToolkitRun,
)
from langchain.tools import Tool
from langchain.utilities.youtube import SearchYouTube
from langchain.vectorstores import STAPLEVECTORS
from langchain.memory import ConversationBufferMemory
from langchain.llms import HuggingFaceHub

# Set up environment variables
os.environ["OPENAI_API_KEY"] = "YOUR_OPENAI_API_KEY"
os.environ["GOOGLE_API_KEY"] = "YOUR_GOOGLE_API_KEY"
os.environ["GOOGLE_CSE_ID"] = "YOUR_GOOGLE_CSE_ID"

# Define the tools
search = SearchYouTube(
    engine="google",
    google_api_key=os.environ["GOOGLE_API_KEY"],
    google_cse_id=os.environ["GOOGLE_CSE_ID"],
)
search_tool = Tool(
    name="Search YouTube",
    description="Search for relevant YouTube videos",
    func=search.run,
)

# Set up the OpenAI LLM
llm = OpenAI(temperature=0.4)

# Set up the CLIP model for image analysis
clip_model = HuggingFaceHub(repo_id="openai/clip-vit-base-patch32", model_kwargs={"device": "cpu"})

# Initialize the agent with the tools
toolkit = PlayWrightBrowserToolkit(llm=llm, clip_model=clip_model)
toolkit_run = PlaywrightBrowserToolkitRun(toolkit=toolkit)

memory = ConversationBufferMemory(memory_key="chat_history")
agent = initialize_agent(
    agent_type=AgentType.CONVERSATIONAL_REACT_DESCRIPTION,
    toolkit=toolkit,
    toolkit_run=toolkit_run,
    memory=memory,
    tools=[search_tool],
    llm=llm,
)

# Define the chat function
def chat(input_text_or_image: str) -> Dict[str, Any]:
    response = agent(input_text_or_image)
    return response

# Example usage
print(chat("How do I solve a quadratic equation?"))
print(chat("path/to/image.jpg"))  # Replace with the actual path to your image



