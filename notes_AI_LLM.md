# AI, LLM

## Courses

https://www.udemy.com/course/llm-engineering-master-ai-and-large-language-models

https://huggingface.co/blog/mlabonne/llm-course

https://www.udemy.com/course/aws-ai-practitioner-certified/

## Others

https://towardsdatascience.com/understanding-llms-from-scratch-using-middle-school-math-e602d27ec876/

https://pangea.cloud/landing/ai-escape-room/

https://www.youtube.com/watch?v=l8pRSuU81PU

https://www.youtube.com/watch?v=7xTGNNLPyMI

https://platform.openai.com/docs/concepts - Key Concepts

## Course: **LLM Engineering: Master AI, Large Language Models & Agents**

https://www.udemy.com/course/llm-engineering-master-ai-and-large-language-models

https://edwarddonner.com/2024/11/13/llm-engineering-resources/

https://github.com/ed-donner/llm_engineering

- Anaconda
    
    https://www.anaconda.com/docs/getting-started/anaconda/install#macos-linux-installation
    
    https://www.anaconda.com/download/success
    
    ~~https://formulae.brew.sh/cask/anaconda~~
    
    `brew install --cask anaconda`
    
    does not install properly `conda` program and there were issues with desktop app that it couldn’t be updated
    
- Models
    
    ### How to use:
    
    - Chat interface
    - Cloud APIs
    - Direct inference
        - With HuggingFace and Transformers library
        - Or with Ollama to run locally
    
    ### Providers
    
    - [ChatGPT](https://chatgpt.com/?model=gpt-4o) (latest model GPT-4o and o1) from OpenAI
        
        Billing: [https://platform.openai.com/settings/organization/billing/overview](https://platform.openai.com/settings/organization/billing/overview)
        
        [https://platform.openai.com/api-keys](https://platform.openai.com/api-keys)
        
        OpenAI: `sk-proj-e_O*****`
        
    - Ollama from Meta (open)
        
        https://ollama.com/search
        
        `ollama run llama3.2`
        
        Check is running: [http://localhost:11434/](http://localhost:11434/)
        
        To prevent Ollama from starting on Mac startup: `System Settings > General > Login Items.` 
        
        To start ollama if disabled on startup: `ollama serve` 
        
        In another Terminal window: `ollama pull llama3.2`
        
        Check size before downloading: `ollama pull deepseek-r1:1.5b --info`
        
        ```python
        import requests
        from bs4 import BeautifulSoup
        from IPython.display import Markdown, display
        
        OLLAMA_API = "http://localhost:11434/api/chat"
        OLLAMA_API_FOR_OPENAPI = 'http://localhost:11434/v1'
        HEADERS = { "Content-Type": "application/json" }
        MODEL = "llama3.2"
        
        messages = [{ "role": "user", "content": "Describe some of the business applications of Generative AI" }]
        payload = { "model": MODEL, "messages": messages, "stream": False }
        
        response = requests.post(OLLAMA_API, json=payload, headers=HEADERS)
        # print(response.json()['message']['content'])
        display(Markdown(response.json()['message']['content']))
        
        # Instead of raw HTTP requests:
        import ollama
        response = ollama.chat(model=MODEL, messages=messages)
        display(Markdown(response['message']['content']))
        
        # There's actually an alternative approach that some people might prefer
        # You can use the OpenAI client python library to call Ollama:
        from openai import OpenAI
        ollama_via_openai = OpenAI(base_url=OLLAMA_API_FOR_OPENAPI, api_key='anything')
        response = ollama_via_openai.chat.completions.create(model=MODEL, messages=messages)
        display(Markdown(response.choices[0].message.content))
        ```
        
    - [Meta.ai](https://www.meta.ai/) (model is Llama 3) from Meta
    - [Claude](https://claude.ai/chats) (latest model Claude 3.5 Sonnet) from Anthropic
        
        [Claude API](https://console.anthropic.com/) from Anthropic
        
    - [Gemini Advance](https://gemini.google.com/app) (latest model Gemini 2.0 Flash) from Google
        
        [Gemini API](https://ai.google.dev/gemini-api) from Google
        
    - [DeepSeek](https://chat.deepseek.com/) (latest models DeepSeek R1 and V3) from DeepSeek AI
        
        [DeepSeek API](https://platform.deepseek.com/) from DeepSeek AI
        
    - [Le Chat](https://chat.mistral.ai/chat) from French AI powerhouse Mistral
        
        
    - [Chat with Command R+](https://coral.cohere.com/) from Cohere
        
        
    - Qwen from Alibaba
    - Gemma from Google
    - Phi from Microsoft
    - [Perplexity](https://www.perplexity.ai/) (latest model is Perplexity Pro) from Perplexity.ai

## Course: thenewboston LLM Application Development

https://www.youtube.com/watch?v=u4LMdo-2EP4&list=PL6gx4Cwl9DGDLqIXStz_Zrk2utoTgrsfW

https://www.youtube.com/watch?v=GrX4WfT5FI4&list=PL6gx4Cwl9DGDv5eyBLEd9l3ZZzVoroxIZ

https://promptlayer.com/ (AI management)

https://www.pinecone.io/ (Long term memory for AI)
