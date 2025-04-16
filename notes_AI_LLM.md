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

https://www.youtube.com/playlist?list=PLWHe-9GP9SMMdl6SLaovUQF2abiLGbMjs

https://platform.openai.com/docs/concepts - Key Concepts

https://ai-2027.com/

## Course: **LLM Engineering: Master AI, Large Language Models & Agents**

https://www.udemy.com/course/llm-engineering-master-ai-and-large-language-models

https://edwarddonner.com/2024/11/13/llm-engineering-resources/

https://github.com/ed-donner/llm_engineering

https://www.youtube.com/playlist?list=PLWHe-9GP9SMMdl6SLaovUQF2abiLGbMjs

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

### **Training** vs I**nference**

When working with Data Science models, you could be carrying out 2 very different activities: **training** and **inference**.

### 1. Training

**Training** is when you provide a model with data for it to adapt to get better at a task in the future. It does this by updating its internal settings - the parameters or weights of the model. If you're Training a model that's already had some training, the activity is called "fine-tuning".

### 2. Inference

**Inference** is when you are working with a model that has *already been trained*. You are using that model to produce new outputs on new inputs, taking advantage of everything it learned while it was being trained. Inference is also sometimes referred to as "Execution" or "Running a model".

All of our use of APIs for GPT, Claude and Gemini in the last weeks are examples of **inference**. The "P" in GPT stands for "Pre-trained", meaning that it has already been trained with data (lots of it!) In week 6 we will try fine-tuning GPT ourselves.

The pipelines API in HuggingFace is only for use for **inference** - running a model that has already been trained. In week 7 we will be training our own model, and we will need to use the more advanced HuggingFace APIs that we look at in the up-coming lecture.

I recorded this playlist on YouTube with more on parameters, training and inference:

[https://www.youtube.com/playlist?list=PLWHe-9GP9SMMdl6SLaovUQF2abiLGbMjs](https://www.youtube.com/playlist?list=PLWHe-9GP9SMMdl6SLaovUQF2abiLGbMjs)

### How to choose correct LLM for the project

1. open-source or closed
2. release date and knowledge cut-off
3. parameters
    1. Sense of strength of the model, how many efforts needed to fine-tune model further.
4. training tokens
    1. Size of training data set
5. context length
    1. Total amount of token that model can keep in a memory
    2. important for multi-shot prompting
6. inference cost (API charge, Subscription or Runtime compute)
    1. how much will it cost to run each query
    2. Important for long run
    3. Counts questions and responses size
7. training cost
    1. if you create your own model instead of using existing one
8. build cost
    1. depends you build your own model or you integrate existing or you combine several models
9. rate limits
    1. Frontier models have rate limits
10. speed
11. latency
12. license

**Chinchilla Scaling Law** - number of parameters ~ proportional to the number of training tokens.

Benchmarks:

- ARC (reasoning)
- DROP (language comprehension)
- HellaSwage (common sense)
- MMLU (understanding)
- TruthfulQA (accuracy)
- Winogrande (context) understands context of the question and resolves ambiguity
- GSM8K (math)
- 
- ELO (chat) comparing LLMs with other LLMs
- HumanEval (python coding) 164 problems writing code based on docstrings
- MultiPL-E (broader coding) Translation of HumanEval to 18 different languages
- 
- GPQA (Graduate Tests) “resistent to google”. 448 expert questions that hard to “google”
- BBHard (Future Capabilities) 204 tasks believed beyond capabilities of LLMs (no longer!)
- Math Lv5 (Math) High school level math competition problems
- IFEval (Difficult instructions) Like “write mote than 400 words” and “mention AI at least 3 times”
- MuSR (Multistep Soft Reasoning) Logical deduction, such as analyzing 1000 word murder mystery and answering “Who has means, motive and opportunity?”
- MMLU-PRO (Harder MMLU) more advanced and cleaned up version of MMLU including choice of 10 answers instead of 4

**🤗 [Open LLM Leaderboard Archived](https://huggingface.co/spaces/open-llm-leaderboard/open_llm_leaderboard#/)**

🤗 [BigCode Models Leaderboard](https://huggingface.co/spaces/bigcode/bigcode-models-leaderboard)

🤗 [LLM-Perf Leaderboard](https://huggingface.co/spaces/optimum/llm-perf-leaderboard) Take a look at `Find Your Best Model` tab

Vellum https://www.vellum.ai/llm-leaderboard

SEAL https://scale.com/leaderboard

🤗 ⛑️ [Medical LLM Leaderboard](https://huggingface.co/spaces/openlifescienceai/open_medical_llm_leaderboard)

Arena: humans evaluating LLMs https://lmarena.ai/

Leaderboardhttps://lmarena.ai/?leaderboard

**Performance metrics:**

- Model-centric or Technical Metrics
    - Loss (e.g. cross-entropy loss)
    - Perplexity
    - Accuracy
    - Precision, Recall, F1
    - AUC-ROC
    - **Easiest to optimize with**
- Business-centric or Outcome Metrics
    - KPIs tied to business objectives
    - ROI
    - Improvements in time, cost or resources
    - Customer satisfaction
    - Benchmark comparisons
    - **Most tangible impact**

## LLM Utilities / Tools

### https://www.gradio.app/

- Example with multi-model answering question
    
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
    

### https://www.vellum.ai/llm-leaderboard

### https://huggingface.co/

- Image generation on free T4 instance
    
    ```python
    # !pip install diffusers transformers accelerate
    from huggingface_hub import login
    from google.colab import userdata
    import torch
    from diffusers import StableDiffusionPipeline
    from IPython.display import display
    from PIL import Image
    
    hf_token = userdata.get('HF_TOKEN')
    login(hf_token, add_to_git_credential=True)
    
    model_id = "OFA-Sys/small-stable-diffusion-v0"
    pipe = StableDiffusionPipeline.from_pretrained(model_id, torch_dtype=torch.float16)
    pipe = pipe.to("cuda")
    pipe.enable_model_cpu_offload()
    
    prompt = "A futuristic class full of students learning AI coding in the surreal style of Salvador Dali"
    image = pipe(prompt).images[0]
    
    # Display the image inline
    display(image)
    ```
    
- Audio on free T4 instance
    
    ```python
    # !pip install diffusers transformers accelerate
    # !pip install datasets
    
    from huggingface_hub import login
    from google.colab import userdata
    from transformers import pipeline
    from datasets import load_dataset
    import soundfile as sf
    import torch
    from IPython.display import Audio
    
    hf_token = userdata.get('HF_TOKEN')
    login(hf_token, add_to_git_credential=True)
    
    synthesiser = pipeline("text-to-speech", "microsoft/speecht5_tts", device='cuda')
    
    embeddings_dataset = load_dataset("Matthijs/cmu-arctic-xvectors", split="validation")
    speaker_embedding = torch.tensor(embeddings_dataset[7306]["xvector"]).unsqueeze(0)
    
    speech = synthesiser("Hi to an artificial intelligence engineer on the way to mastery!", forward_params={"speaker_embeddings": speaker_embedding})
    
    sf.write("speech.wav", speech["audio"], samplerate=speech["sampling_rate"])
    
    # Play the generated audio
    Audio("speech.wav")
    ```
    

It’s possible to **combine Frontier models with open source ones**. E.g. transcribe audio with Frontier and then summarize with open source.

- [🤗 Hub](https://colab.research.google.com/drive/1KSMxOCprsl1QRpt_Rq0UqCAyMtPqDQYx?usp=sharing)
- [datasets](https://pypi.org/project/datasets/) package
    
    `pip install datasets`
    
    **two** main features:
    
    - **one-line dataloaders for many public datasets:** one-liners to download and pre-process any of the  major public datasets (>350k) from [🤗 Hub](https://huggingface.co/datasets)
    - **efficient data pre-processing:** simple, fast and reproducible data pre-processing for the public datasets as well as your own local datasets in CSV, JSON, text, PNG, JPEG, WAV, MP3, Parquet, etc.
- [transformers](https://pypi.org/project/transformers/) and [diffusers](https://pypi.org/project/diffusers/) packages
    
    `pip install transformers diffusers`
    
    🤗 Transformers is a library of pretrained text, computer vision, audio, video, and multimodal models for inference and training. Use Transformers to fine-tune models on your data, build inference applications, and for generative AI use cases across multiple modalities.
    
    🤗 Diffusers is the go-to library for state-of-the-art pretrained diffusion models for generating images, audio, and even 3D structures of molecules. Whether you're looking for a simple inference solution or training your own diffusion models, 🤗 Diffusers is a modular toolbox that supports both. 
    
    Transformers https://huggingface.co/docs/transformers/main_classes/pipelines
    
    Diffusers https://huggingface.co/docs/diffusers/en/api/pipelines/overview
    
    pipelines use cases
    
    - sentiment analysts
        
        ```python
        classifier = pipeline("sentiment-analysis", device="cuda")
        result = classifier("I'm super excited to be on the way to LLM mastery!")
        
        print(result)
        
        # No model was supplied, defaulted to distilbert/distilbert-base-uncased-finetuned-sst-2-english and revision 714eb0f (https://huggingface.co/distilbert/distilbert-base-uncased-finetuned-sst-2-english).
        
        # [{ 'label': 'POSITIVE', 'score': 0.9993460774421692 }]
        ```
        
    - classifier
        
        ```python
        classifier = pipeline("zero-shot-classification", device="cuda")
        result = classifier("Hugging Face's Transformers library is amazing!", candidate_labels=["technology", "sports", "politics"])
        print(result)
        
        # No model was supplied, defaulted to facebook/bart-large-mnli and revision d7645e1 (https://huggingface.co/facebook/bart-large-mnli).
        
        # { 'sequence': "Hugging Face's Transformers library is amazing!",
        #   'labels': ['technology', 'sports', 'politics'],
        #   'scores': [0.9493839740753174, 0.03225007280707359, 0.018365919589996338] }
        ```
        
    - named entity recognition
        
        ```python
        ner = pipeline("ner", grouped_entities=True, device="cuda")
        result = ner("Barack Obama was the 44th president of the United States.")
        print(result)
        
        # No model was supplied, defaulted to dbmdz/bert-large-cased-finetuned-conll03-english and revision 4c53496 (https://huggingface.co/dbmdz/bert-large-cased-finetuned-conll03-english).
        
        # [
        #   {'entity_group': 'PER', 'score': np.float32(0.99918306), 'word': 'Barack Obama', 'start': 0, 'end': 12},
        #   {'entity_group': 'LOC', 'score': np.float32(0.9986908), 'word': 'United States', 'start': 43, 'end': 56}
        # ]
        ```
        
    - question answering (with context
        
        ```python
        question_answerer = pipeline("question-answering", device="cuda")
        result = question_answerer(question="Who was the 44th president of the United States?", context="Barak Obama was the 45th president of the United States")
        print(result)
        
        # No model was supplied, defaulted to distilbert/distilbert-base-cased-distilled-squad and revision 564e9b5 (https://huggingface.co/distilbert/distilbert-base-cased-distilled-squad).
        
        # {'score': 0.9327927827835083, 'start': 0, 'end': 5, 'answer': 'Trump'}
        ```
        
    - summarizing
        
        ```python
        summarizer = pipeline("summarization", device="cuda")
        text = """The Hugging Face transformers library is an incredibly versatile and powerful tool for natural language processing (NLP).
        It allows users to perform a wide range of tasks such as text classification, named entity recognition, and question answering, among others.
        It's an extremely popular library that's widely used by the open-source data science community.
        It lowers the barrier to entry into the field by providing Data Scientists with a productive, convenient way to work with transformer models.
        """
        summary = summarizer(text, max_length=50, min_length=25, do_sample=False)
        print(summary[0]['summary_text'])
        
        # No model was supplied, defaulted to sshleifer/distilbart-cnn-12-6 and revision a4f8f3e (https://huggingface.co/sshleifer/distilbart-cnn-12-6).
        
        # The Hugging Face transformers library is an incredibly versatile and powerful tool for natural language processing.
        # It allows users to perform a wide range of tasks such as text classification, named entity recognition, and question answering .
        ```
        
    - translation
        
        ```python
        translator = pipeline("translation_en_to_fr", device="cuda")
        result = translator("The Data Scientists were truly amazed by the power and simplicity of the HuggingFace pipeline API.")
        print(result[0]['translation_text'])
        
        # No model was supplied, defaulted to google-t5/t5-base and revision a9723ea (https://huggingface.co/google-t5/t5-base).
        
        # Les Data Scientists ont été vraiment étonnés par la puissance et la simplicité de l'API du pipeline HuggingFace.
        ```
        
    - translation with specified model (All translation models: https://huggingface.co/models?pipeline_tag=translation&sort=trending)
        
        ```python
        # Another translation, showing a model being specified
        # All translation models are here: https://huggingface.co/models?pipeline_tag=translation&sort=trending
        
        translator = pipeline("translation_en_to_es", model="Helsinki-NLP/opus-mt-en-es", device="cuda")
        result = translator("The Data Scientists were truly amazed by the power and simplicity of the HuggingFace pipeline API.")
        print(result[0]['translation_text'])
        
        # Los científicos de datos estaban verdaderamente sorprendidos por el poder y la simplicidad de la API de tuberías HuggingFace.
        ```
        
    - text generation
        
        ```python
        generator = pipeline("text-generation", device="cuda")
        result = generator("If there's one thing I want you to remember about using HuggingFace pipelines, it's")
        print(result[0]['generated_text'])
        
        # No model was supplied, defaulted to openai-community/gpt2 and revision 607a30d (https://huggingface.co/openai-community/gpt2).
        
        # If there's one thing I want you to remember about using HuggingFace pipelines, it's 
        #     that the best thing that happens to your brain in this case is that it's totally harmless for any pain you feel. It might go away, but the
        ```
        
    - image generation
        
        ```python
        image_gen = DiffusionPipeline.from_pretrained(
            "stabilityai/stable-diffusion-2",
            torch_dtype=torch.float16,
            use_safetensors=True,
            variant="fp16"
            ).to("cuda")
        
        text = "A class of Data Scientists learning about AI, in the surreal style of Salvador Dali"
        image = image_gen(prompt=text).images[0]
        image
        ```
        
    - audio generation
        
        ```python
        synthesiser = pipeline("text-to-speech", "microsoft/speecht5_tts", device='cuda')
        
        embeddings_dataset = load_dataset("Matthijs/cmu-arctic-xvectors", split="validation")
        speaker_embedding = torch.tensor(embeddings_dataset[7306]["xvector"]).unsqueeze(0)
        
        speech = synthesiser("Hi to an artificial intelligence engineer, on the way to mastery!", forward_params={"speaker_embeddings": speaker_embedding})
        
        sf.write("speech.wav", speech["audio"], samplerate=speech["sampling_rate"])
        Audio("speech.wav")
        ```
        
    
    ### Tokenizers
    
    - encode (into tokens) / decode (back to text)
        - `tokenizer.encode`, `tokenizer.decode`, `tokenizer.batch_decode`
    - vocab of special tokens to signal inforamtion to the LLM, like start of the prompt
        - `tokenizer.get_added_vocab`
    - chat template that knows how to format a chat message for this model
    
    - tokenizer
        
        ```python
        from transformers import AutoTokenizer
        
        tokenizer = AutoTokenizer.from_pretrained('meta-llama/Meta-Llama-3.1-8B', trust_remote_code=True) # include trust_remote_code=True to supress ugly warning
        text = "I am excited to show Tokenizers in action to my LLM engineers"
        tokens = tokenizer.encode(text)
        tokens
        
        len(tokens)
        # 15
        
        tokenizer.decode(tokens)
        # <|begin_of_text|>I am excited to show Tokenizers in action to my LLM engineers
        
        tokenizer.batch_decode(tokens)
        # ['<|begin_of_text|>', 'I', ' am', ' excited', ' to', ' show', ' Token', 'izers', ' in', ' action', ' to', ' my', ' L', 'LM', ' engineers']
        
        # Lists all tokens within model:
        # tokenizer.vocab   
        
        # Lists special tokens within model:
        tokenizer.get_added_vocab()
        # {'<|begin_of_text|>': 128000,
        #  '<|end_of_text|>': 128001,
        #  '<|reserved_special_token_0|>': 128002,
        #  '<|reserved_special_token_1|>': 128003,
        #  '<|finetune_right_pad_id|>': 128004,
        #  '<|reserved_special_token_2|>': 128005,
        #  '<|start_header_id|>': 128006,
        #  '<|end_header_id|>': 128007,
        #  '<|eom_id|>': 128008,
        #  '<|eot_id|>': 128009,
        #  '<|python_tag|>': 128010,
        #  '<|reserved_special_token_3|>': 128011,
        #  ...
        #  '<|reserved_special_token_246|>': 128254,
        #  '<|reserved_special_token_247|>': 128255}
        
        ```
        
    - multi-models tokenizer with chats
        
        Many models have a variant that has been trained for use in Chats.
        
        These are typically labelled with the word "Instruct" at the end.
        
        ```python
        from transformers import AutoTokenizer
        
        tokenizer = AutoTokenizer.from_pretrained('meta-llama/Meta-Llama-3.1-8B-Instruct', trust_remote_code=True)
        phi3_tokenizer = AutoTokenizer.from_pretrained('microsoft/Phi-3-mini-4k-instruct')
        qwen2_tokenizer = AutoTokenizer.from_pretrained('Qwen/Qwen2-7B-Instruct')
        starcoder2_tokenizer = AutoTokenizer.from_pretrained('bigcode/starcoder2-3b', trust_remote_code=True)  # special model for code generation
        
        messages = [
            {"role": "system", "content": "You are a helpful assistant"},
            {"role": "user", "content": "Tell a light-hearted joke for a room of Data Scientists"}
          ]
        prompt = tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True)
        print(prompt)
        
        text = "I am excited to show Tokenizers in action to my LLM engineers"
        print(tokenizer.encode(text))
        print()
        tokens = phi3_tokenizer.encode(text)
        print(tokens)
        print(phi3_tokenizer.batch_decode(tokens))
        print()
        print(qwen2_tokenizer.encode(text))
        
        print(tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True))
        print()
        print(phi3_tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True))
        print()
        print(qwen2_tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True))
        
        code = """
        def hello_world(person):
          print("Hello", person)
        """
        tokens = starcoder2_tokenizer.encode(code)
        for token in tokens:
          print(f"{token}={starcoder2_tokenizer.decode(token)}")
        ```
        
    
    ### Apects of open source models from 🤗
    
    1. Quantization - reduce precisions of the weight in model to fit smaller RAM/CPU/GPU
        1. e.g. decreases 32 bit numbers to 8 bit numbers
    2. Model Internals - layers of transformers
    3. Streaming - stream open source model response
    - quantization
        
        ```python
        # !pip install -q requests torch bitsandbytes transformers sentencepiece accelerate
        from transformers import AutoTokenizer, AutoModelForCausalLM, TextStreamer, BitsAndBytesConfig
        import torch
        
        messages = [
            {"role": "system", "content": "You are a helpful assistant"},
            {"role": "user", "content": "Tell a light-hearted joke for a room of Data Scientists"}
        ]
        
        # Quantization Config - this allows us to load the model into memory and use less memory
        quant_config = BitsAndBytesConfig(
            load_in_4bit=True,
            bnb_4bit_use_double_quant=True,
            bnb_4bit_compute_dtype=torch.bfloat16,
            bnb_4bit_quant_type="nf4"
        )
        
        # Tokenizer
        tokenizer = AutoTokenizer.from_pretrained(LLAMA) # meta-llama/Meta-Llama-3.1-8B-Instruct
        tokenizer.pad_token = tokenizer.eos_token
        inputs = tokenizer.apply_chat_template(messages, return_tensors="pt").to("cuda")
        
        # The model
        # device_map="auto" - if we have GPU then use it
        model = AutoModelForCausalLM.from_pretrained(LLAMA, device_map="auto", quantization_config=quant_config)
        
        memory = model.get_memory_footprint() / 1e6
        print(f"Memory footprint: {memory:,.1f} MB") # Memory footprint: 5,591.5 MB
        
        model
        # LlamaForCausalLM(
        #   (model): LlamaModel(
        #     (embed_tokens): Embedding(128256, 4096)
        #     (layers): ModuleList(
        #       (0-31): 32 x LlamaDecoderLayer(
        #         (self_attn): LlamaAttention(
        #           (q_proj): Linear4bit(in_features=4096, out_features=4096, bias=False)
        #           (k_proj): Linear4bit(in_features=4096, out_features=1024, bias=False)
        #           (v_proj): Linear4bit(in_features=4096, out_features=1024, bias=False)
        #           (o_proj): Linear4bit(in_features=4096, out_features=4096, bias=False)
        #         )
        #         (mlp): LlamaMLP(
        #           (gate_proj): Linear4bit(in_features=4096, out_features=14336, bias=False)
        #           (up_proj): Linear4bit(in_features=4096, out_features=14336, bias=False)
        #           (down_proj): Linear4bit(in_features=14336, out_features=4096, bias=False)
        #           (act_fn): SiLU()
        #         )
        #         (input_layernorm): LlamaRMSNorm((4096,), eps=1e-05)
        #         (post_attention_layernorm): LlamaRMSNorm((4096,), eps=1e-05)
        #       )
        #     )
        #     (norm): LlamaRMSNorm((4096,), eps=1e-05)
        #     (rotary_emb): LlamaRotaryEmbedding()
        #   )
        #   (lm_head): Linear(in_features=4096, out_features=128256, bias=False)
        # )
        
        outputs = model.generate(inputs, max_new_tokens=80)
        print(tokenizer.decode(outputs[0]))
        # The attention mask and the pad token id were not set. As a consequence, you may observe unexpected behavior. Please pass your input's `attention_mask` to obtain reliable results.
        # Setting `pad_token_id` to `eos_token_id`:128001 for open-end generation.
        # The attention mask is not set and cannot be inferred from input because pad token is same as eos token. As a consequence, you may observe unexpected behavior. Please pass your input's `attention_mask` to obtain reliable results.
        
        # <|begin_of_text|><|start_header_id|>system<|end_header_id|>
        # Cutting Knowledge Date: December 2023
        # Today Date: 26 Jul 2024
        # You are a helpful assistant<|eot_id|><|start_header_id|>user<|end_header_id|>
        # Tell a light-hearted joke for a room of Data Scientists<|eot_id|><|start_header_id|>assistant<|end_header_id|>
        # Why did the logistic regression model break up with the linear regression model?
        # Because it was struggling to find a good fit.<|eot_id|>
        
        # Clean up
        del inputs, outputs, model
        torch.cuda.empty_cache()
        ```
        
    - quantization with streaming for open source model
        
        ```python
        # !pip install -q requests torch bitsandbytes transformers sentencepiece accelerate
        from transformers import AutoTokenizer, AutoModelForCausalLM, TextStreamer, BitsAndBytesConfig
        import torch
        
        # Quantization Config - this allows us to load the model into memory and use less memory
        quant_config = BitsAndBytesConfig(
            load_in_4bit=True,
            bnb_4bit_use_double_quant=True,
            bnb_4bit_compute_dtype=torch.bfloat16,
            bnb_4bit_quant_type="nf4"
        )
        
        # Wrapping everything in a function - and adding Streaming and generation prompts
        def generate(model, messages):
          tokenizer = AutoTokenizer.from_pretrained(model)
          tokenizer.pad_token = tokenizer.eos_token
          inputs = tokenizer.apply_chat_template(messages, return_tensors="pt", add_generation_prompt=True).to("cuda")
        
          streamer = TextStreamer(tokenizer)
        
          model = AutoModelForCausalLM.from_pretrained(model, device_map="auto", quantization_config=quant_config)
        
          outputs = model.generate(inputs, max_new_tokens=80, streamer=streamer)
        
          del tokenizer, streamer, model, inputs, outputs
        
          torch.cuda.empty_cache()
          
        messages = [
            {"role": "system", "content": "You are a helpful assistant"},
            {"role": "user", "content": "Tell a light-hearted joke for a room of Data Scientists"}
        ]
        
        generate(PHI3, messages) # microsoft/Phi-3-mini-4k-instruct
        
        ```
        
    
- [PEFT](https://pypi.org/project/peft/) **Parameter-Efficient Fine-Tuning**
    
    `pip install peft`
    
    Fine-tuning large pretrained models is often prohibitively costly due to their scale. Parameter-Efficient Fine-Tuning (PEFT) methods enable efficient adaptation of large pretrained models to various downstream applications by only fine-tuning a small number of (extra) model parameters instead of all the model's parameters. This significantly decreases the computational and storage costs. Recent state-of-the-art PEFT techniques achieve performance comparable to fully fine-tuned models.
    
- [**TRL](https://pypi.org/project/trl/) - Transformer Reinforcement Learning**
    
    `pip install trl`
    
    TRL is a cutting-edge library designed for post-training foundation models using advanced techniques like Supervised Fine-Tuning (SFT), Proximal Policy Optimization (PPO), and Direct Preference Optimization (DPO). Built on top of the [🤗 Transformers](https://github.com/huggingface/transformers) ecosystem, TRL supports a variety of model architectures and modalities, and can be scaled-up across various hardware setups.
    
- [accelerate](https://pypi.org/project/accelerate/)
    
    `pip install accelerate`
    
    **Run your *raw* PyTorch training script on any kind of device**
    

### https://colab.research.google.com/

My test - https://colab.research.google.com/drive/1Nx5HgG37Ehq5qdeKtECaMvJ0_qyP8BMu#scrollTo=xVVPibnxTVmM

## Course: thenewboston LLM Application Development

https://www.youtube.com/watch?v=u4LMdo-2EP4&list=PL6gx4Cwl9DGDLqIXStz_Zrk2utoTgrsfW

https://www.youtube.com/watch?v=GrX4WfT5FI4&list=PL6gx4Cwl9DGDv5eyBLEd9l3ZZzVoroxIZ

https://promptlayer.com/ (AI management)

https://www.pinecone.io/ (Long term memory for AI)
