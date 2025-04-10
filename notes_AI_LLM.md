# AI, LLM

## Links

https://www.udemy.com/course/llm-engineering-master-ai-and-large-language-models

https://huggingface.co/blog/mlabonne/llm-course

https://www.udemy.com/course/aws-ai-practitioner-certified/

https://towardsdatascience.com/understanding-llms-from-scratch-using-middle-school-math-e602d27ec876/

https://pangea.cloud/landing/ai-escape-room/

https://www.youtube.com/watch?v=l8pRSuU81PU

https://www.youtube.com/watch?v=7xTGNNLPyMI

https://www.youtube.com/watch?v=1Se2zTlXDwY

https://platform.openai.com/docs/concepts - Key Concepts

https://ai-2027.com/

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
        
        Keys: [https://platform.openai.com/api-keys](https://platform.openai.com/api-keys)
        
        **new API added: [Responses vs. Chat Completions](https://platform.openai.com/docs/guides/responses-vs-chat-completions)**
        
        OpenAI: `sk-proj-*******` 
        
        - Chat Completion with streaming
            
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
            
            # to receive in structured json: https://platform.openai.com/docs/guides/structured-outputs
            # to receive json format (old varation):
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
            
        - Image generation
            
            ```python
            import os
            from dotenv import load_dotenv
            from openai import OpenAI
            import base64
            from io import BytesIO
            from PIL import Image
            from IPython.display import display
            
            load_dotenv(override=True)
            openai_api_key = os.getenv('OPENAI_API_KEY')
                
            MODEL = "gpt-4o-mini"
            openai = OpenAI()
            
            def artist(city):
                image_response = openai.images.generate(
                        model="dall-e-3",
                        prompt=f"An image representing a vacation in {city}, showing tourist spots and everything unique about {city}, in a vibrant pop-art style",
                        size="1024x1024",
                        n=1,
                        response_format="b64_json",
                    )
                image_base64 = image_response.data[0].b64_json
                image_data = base64.b64decode(image_base64)
                return Image.open(BytesIO(image_data))
            
            image = artist("New York City")
            display(image)
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

### **How to use:**

- Chat interface
- Cloud APIs
- Direct inference
    - With HuggingFace and Transformers library
    - Or with Ollama to run locally

### **Evolution of AI:**

- Prompt engineers
- Custom GPTs (GPT Store)
- Copilots (Github or Microsoft)
- Agentization (Github Copilot Workspace)

### **Paramaters/weights** - 1b, 10b, 100b, 1t, 10t, ….

billions, trilons parameters to configure model

- gpt-4 ~ 1.76t
- gpt-3 ~ 175b

### Context window

ChatGPT sends whole history of conversation because there is no “memory”

Tokens: [https://platform.openai.com/tokenizer](https://platform.openai.com/tokenizer)

1 token ~4 characters (in English)

1 token ~0.75 words (in English)

numbers are tokenized by ~3 digits

Prompting:

zero-shot promting -  perform a task without providing any examples

`Translate the following sentence to French: 'How are you?'`

one-shot promting - 1 example

`Translate the following sentence to French: Example: 'Good morning' -> 'Bonjour'Now, translate: 'How are you?'`

multiple-shot promting - multiple examples

### Tools

- fetch data or add knowledge or context
- Take action, like booking a meeting
- Perform calculations
- Modify the UI

## LLM Utilities / Tools

### https://www.gradio.app/

- Example with multiple models answering question
    
    ```python
    import os
    from dotenv import load_dotenv
    from openai import OpenAI
    import anthropic
    import gradio as gr
    
    load_dotenv(override=True)
    openai_api_key = os.getenv('OPENAI_API_KEY')
    anthropic_api_key = os.getenv('ANTHROPIC_API_KEY')
    
    if not openai_api_key:
        print("OpenAI API Key not set")
        
    if not anthropic_api_key:
        print("Anthropic API Key not set")
    
    openai = OpenAI()
    claude = anthropic.Anthropic()
    
    system_message = "You are a helpful assistant that responds in markdown"
    
    def stream_gpt(prompt):
        messages = [
            {"role": "system", "content": system_message},
            {"role": "user", "content": prompt}
          ]
        stream = openai.chat.completions.create(
            model='gpt-4o-mini',
            messages=messages,
            stream=True
        )
        result = ""
        for chunk in stream:
            result += chunk.choices[0].delta.content or ""
            yield result # cumulative results instead of single chunk piece
    
    def stream_claude(prompt):
        result = claude.messages.stream(
            model="claude-3-haiku-20240307",
            max_tokens=1000,
            temperature=0.7,
            system=system_message,
            messages=[
                {"role": "user", "content": prompt},
            ],
        )
        response = ""
        with result as stream:
            for text in stream.text_stream:
                response += text or ""
                yield response  # cumulative results instead of single chunk piece
    
    def stream_model(prompt, model):
        if model=="GPT":
            result = stream_gpt(prompt)
        elif model=="Claude":
            result = stream_claude(prompt)
        else:
            raise ValueError("Unknown model")
        yield from result
    
    view = gr.Interface(
        fn=stream_model,
        inputs=[gr.Textbox(label="Your message:"), gr.Dropdown(["GPT", "Claude"], label="Select model", value="GPT")],
        outputs=[gr.Markdown(label="Response:")],
        flagging_mode="never"
    )
    view.launch(
        inbrowser=False, # Adding inbrowser=True opens up a new browser window automatically
        share=False # Adding share=True means that it can be accessed publically. NOTE: Some Anti-virus software and Corporate Firewalls might not like you using share=True. If you're at work on on a work network, I suggest skip this test.
    ) 
    ```
    
- Chatbot with changing system prompt
    
    ```python
    import os
    from dotenv import load_dotenv
    from openai import OpenAI
    import gradio as gr
    
    load_dotenv(override=True)
    openai_api_key = os.getenv('OPENAI_API_KEY')
    
    openai = OpenAI()
    MODEL = 'gpt-4o-mini'
    
    system_message = "You are a helpful assistant"
    
    def chat(message, history):
        relevant_system_message = system_message
    
        # it's possible to adjust system message based on user prompt
        if 'php' in message:
            relevant_system_message += " You shouldn't answer questions related to PHP, PHP applications and packages."
            
        messages = [{"role": "system", "content": system_message}] + history + [{"role": "user", "content": message}]
    
        stream = openai.chat.completions.create(model=MODEL, messages=messages, stream=True)
    
        response = ""
        for chunk in stream:
            response += chunk.choices[0].delta.content or ''
            yield response
    ```
    
- Chatbot with tools (informed user support agent)
    
    ```python
    import os
    import json
    from dotenv import load_dotenv
    from openai import OpenAI
    import gradio as gr
    
    load_dotenv(override=True)
    openai_api_key = os.getenv('OPENAI_API_KEY')
    
    MODEL = "gpt-4o-mini"
    openai = OpenAI()
    
    system_message = "You are a helpful assistant for an Airline called FlightAI. \
       Give short, courteous answers, no more than 1 sentence. Always be accurate. If you don't know the answer, say so."
    
    ticket_prices = {"london": "$799", "paris": "$899", "tokyo": "$1400", "berlin": "$499"}
    def get_ticket_price(destination_city):
        print(f"Tool get_ticket_price called for {destination_city}")
        city = destination_city.lower()
        return ticket_prices.get(city, "Unknown")
        
    price_function = {
        "name": "get_ticket_price",
        "description": "Get the price of a return ticket to the destination city. Call this whenever you need to know the ticket price, for example when a customer asks 'How much is a ticket to this city'",
        "parameters": {
            "type": "object",
            "properties": {
                "destination_city": {
                    "type": "string",
                    "description": "The city that the customer wants to travel to",
                },
            },
            "required": ["destination_city"],
            "additionalProperties": False
        }
    }
    tools = [{"type": "function", "function": price_function}]
    
    def chat(message, history):
        messages = [{"role": "system", "content": system_message}] + history + [{"role": "user", "content": message}]
        response = openai.chat.completions.create(
            model=MODEL,
            messages=messages,
            tools=tools # tools are added to ask for info if needed
        )
        
        if response.choices[0].finish_reason=="tool_calls":
            tool_calls_message = response.choices[0].message
            tool_calls_response = handle_tool_call(tool_calls_message)
            
            messages = messages + [tool_calls_message, tool_calls_response]
    
    				# ask again with information about prices
            response = openai.chat.completions.create(model=MODEL, messages=messages)
    
        return response.choices[0].message.content
    
    def handle_tool_call(tool_calls_message):
        tool_call = tool_calls_message.tool_calls[0]
        arguments = json.loads(tool_call.function.arguments)
        city = arguments.get('destination_city')
        
        return {
            "role": "tool",
            "content": json.dumps({
                "destination_city": city,
                "price": get_ticket_price(city)
            }),
            "tool_call_id": tool_call.id
        }
    
    gr.ChatInterface(fn=chat, type="messages").launch()
    ```
    
- Multimodal example: completion with tools + image generation + audio
    
    ```python
    import os
    import json
    from dotenv import load_dotenv
    from openai import OpenAI
    import gradio as gr
    import base64
    from io import BytesIO
    from PIL import Image
    from pydub import AudioSegment
    from pydub.playback import play
    
    load_dotenv(override=True)
    openai_api_key = os.getenv('OPENAI_API_KEY')
    MODEL = "gpt-4o-mini"
    openai = OpenAI()
    
    system_message = "You are a helpful assistant for an Airline called FlightAI. \
       Give short, courteous answers, no more than 1 sentence. Always be accurate. If you don't know the answer, say so."
    
    ticket_prices = {"london": "$799", "paris": "$899", "tokyo": "$1400", "berlin": "$499"}
    def get_ticket_price(destination_city):
        print(f"Tool get_ticket_price called for {destination_city}")
        city = destination_city.lower()
        return ticket_prices.get(city, "Unknown")
        
    price_function = {
        "name": "get_ticket_price",
        "description": "Get the price of a return ticket to the destination city. Call this whenever you need to know the ticket price, for example when a customer asks 'How much is a ticket to this city'",
        "parameters": {
            "type": "object",
            "properties": {
                "destination_city": {
                    "type": "string",
                    "description": "The city that the customer wants to travel to",
                },
            },
            "required": ["destination_city"],
            "additionalProperties": False
        }
    }
    tools = [{"type": "function", "function": price_function}]
    
    def handle_tool_call(tool_calls_message):
        tool_call = tool_calls_message.tool_calls[0]
        arguments = json.loads(tool_call.function.arguments)
        city = arguments.get('destination_city')
        
        return {
            "role": "tool",
            "content": json.dumps({
                "destination_city": city,
                "price": get_ticket_price(city)
            }),
            "tool_call_id": tool_call.id
        }, city
        
    def artist(city):
        image_response = openai.images.generate(
                model="dall-e-3",
                prompt=f"An image representing a vacation in {city}, showing tourist spots and everything unique about {city}, in a vibrant pop-art style",
                size="1024x1024",
                n=1,
                response_format="b64_json",
            )
        image_base64 = image_response.data[0].b64_json
        image_data = base64.b64decode(image_base64)
        return Image.open(BytesIO(image_data))
    
    def talker(message):
        response = openai.audio.speech.create(
          model="tts-1",
          voice="onyx",    # Also, try replacing onyx with alloy
          input=message
        )
        
        audio_stream = BytesIO(response.content)
        audio = AudioSegment.from_file(audio_stream, format="mp3")
        play(audio)
        
    def chat(history):
        messages = [{"role": "system", "content": system_message}] + history
        response = openai.chat.completions.create(model=MODEL, messages=messages, tools=tools)
        image = None
        
        if response.choices[0].finish_reason=="tool_calls":
            tool_calls_message = response.choices[0].message
            tool_calls_response, city = handle_tool_call(tool_calls_message)
            
            messages = messages + [tool_calls_message, tool_calls_response]
            image = artist(city)
    
            response = openai.chat.completions.create(model=MODEL, messages=messages)
            
        reply = response.choices[0].message.content
        history += [{"role":"assistant", "content":reply}]
    
        # Comment out or delete the next line if you'd rather skip Audio for now..
        talker(reply)
        
        return history, image
        
    with gr.Blocks() as ui:
        with gr.Row():
            chatbot = gr.Chatbot(height=500, type="messages")
            image_output = gr.Image(height=500)
        with gr.Row():
            entry = gr.Textbox(label="Chat with our AI Assistant:")
        with gr.Row():
            clear = gr.Button("Clear")
    
        def do_entry(message, history):
            history += [{"role":"user", "content":message}]
            return "", history
    
        entry.submit(do_entry, inputs=[entry, chatbot], outputs=[entry, chatbot]).then(
            chat, inputs=chatbot, outputs=[chatbot, image_output]
        )
        clear.click(lambda: None, inputs=None, outputs=chatbot, queue=False)
    
    ui.launch(inbrowser=True)
    ```
    

https://www.vellum.ai/llm-leaderboard

## Course: thenewboston LLM Application Development

https://www.youtube.com/watch?v=u4LMdo-2EP4&list=PL6gx4Cwl9DGDLqIXStz_Zrk2utoTgrsfW

https://www.youtube.com/watch?v=GrX4WfT5FI4&list=PL6gx4Cwl9DGDv5eyBLEd9l3ZZzVoroxIZ

https://promptlayer.com/ (AI management)

https://www.pinecone.io/ (Long term memory for AI)
