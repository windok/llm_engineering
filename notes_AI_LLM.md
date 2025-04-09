# AI, LLM

## Links

https://www.udemy.com/course/llm-engineering-master-ai-and-large-language-models

https://huggingface.co/blog/mlabonne/llm-course

https://www.udemy.com/course/aws-ai-practitioner-certified/

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
    
    https://www.vellum.ai/llm-leaderboard
    
    ## Providers
    
    - [ChatGPT](https://chatgpt.com/?model=gpt-4o) (latest model GPT-4o and o1) from OpenAI
        
        Billing: [https://platform.openai.com/settings/organization/billing/overview](https://platform.openai.com/settings/organization/billing/overview)
        
        [https://platform.openai.com/api-keys](https://platform.openai.com/api-keys)
        
        OpenAI: `sk-proj-*******` 
        
        ```python
        import os
        import json
        from dotenv import load_dotenv
        from IPython.display import Markdown, display, update_display
        from openai import OpenAI
        
        load_dotenv(override=True)
        api_key = os.getenv('OPENAI_API_KEY')
        
        if not api_key or not api_key.startswith('sk-proj-') or not len(api_key)>10:
            print("There might be a problem with your API key? Please visit the troubleshooting notebook!")
            
        MODEL = 'gpt-4o-mini'
        openai = OpenAI()
        
        # to receive json format:
        response = openai.chat.completions.create(
            model=MODEL,
            messages=[
                {"role": "system", "content": "You will generate JSON and return it"}, # system prompt
                {"role": "user", "content": "Generate dummy json and retrun in JSON. It's important to mention this in prompt even when response_format is json_object"} # user prompt
            ],
            response_format={"type": "json_object"}
        )
        result = response.choices[0].message.content
        data = json.loads(result)
        
        # to stream response and rerender Markdown
        stream = openai.chat.completions.create(
            model=MODEL,
            messages=[{"role": "system", "content": system_prompt}, {"role": "user", "content": user_prompt}],
            stream=True
        )
            
        response = ""
        display_handle = display(Markdown(""), display_id=True)
        for chunk in stream:
            response += chunk.choices[0].delta.content or ''
            response = response.replace("```","").replace("markdown", "")
            update_display(Markdown(response), display_id=display_handle.display_id)
        ```
        
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

## Theory

**How to use:**

- Chat interface
- Cloud APIs
- Direct inference
    - With HuggingFace and Transformers library
    - Or with Ollama to run locally

**Evolution of AI:**

- Prompt engineers
- Custom GPTs (GPT Store)
- Copilots (Github or Microsoft)
- Agentization (Github Copilot Workspace)

**Paramaters/weights** - 1b, 10b, 100b, 1t, 10t, ….

billions, trilons parameters to configure model

- gpt-4 ~ 1.76t
- gpt-3 ~ 175b

Tokens: [https://platform.openai.com/tokenizer](https://platform.openai.com/tokenizer)

1 token ~4 characters (in English)

1 token ~0.75 words (in English)

numbers are tokenized by ~3 digits

Context window

ChatGPT sends whole history of conversation because there is no “memory”

Prompting:

zero-shot promting -  perform a task without providing any examples

`Translate the following sentence to French: 'How are you?'`

one-shot promting - 1 example

`Translate the following sentence to French: Example: 'Good morning' -> 'Bonjour'Now, translate: 'How are you?'`

multiple-shot promting - multiple examples

## Course: thenewboston LLM Application Development

https://www.youtube.com/watch?v=u4LMdo-2EP4&list=PL6gx4Cwl9DGDLqIXStz_Zrk2utoTgrsfW

https://www.youtube.com/watch?v=GrX4WfT5FI4&list=PL6gx4Cwl9DGDv5eyBLEd9l3ZZzVoroxIZ

https://promptlayer.com/ (AI management)

https://www.pinecone.io/ (Long term memory for AI)
