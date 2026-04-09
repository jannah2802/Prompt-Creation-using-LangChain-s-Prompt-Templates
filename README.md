# Prompt-Creation-using-LangChain-s-Prompt-Templates

As AI applications grow more advanced, simply asking a question isn't enough. ​You need structured prompts that control tone, behavior, and context. ​In this demo, we'll quickly walk through how to set up our project, build prompts using ​prompt template and chat prompt template, and run an interactive workflow that shows ​the power of structured, role-based prompting.

Essential dependencies from requirements.txt file have 3 key libraries which are:
1. Langchain - for managing prompts and workflow
2. Langchain Google Gene - for connecting Langchain with the Gemini model
3. Python-dotenv - For securely loading our API key from a hidden .env file.

**prompt_demo.py**

This is innitialization script, where we start by importing essential modules. 
Python OS module, load_dotenv from the .env package, which allows us to securely load environment variables from our .env file.
Next, import core Langchain components required for this round demo which is prompt template and chat template. 
Also, the chat google generative ai class from Langchain Google GenAi. 
These give us the tools we need to build structured prompts and interact directly with Gemini trough Langchain.

    import os
    from dotenv import load_dotenv
    from langchain_core.prompts import PromptTemplate, ChatPromptTemplate
    from langchain_google_genai import ChatGoogleGenerativeAI

Load the key using Gemini API_KEY variable

    load_dotenv()
    GEMINI_API_KEY = os.getenv("GEMINI_API_KEY")

Innitialize our model wrapper explicit choose model for fast and responses. So that this LLM instance becomes the central point of communication with the model. And because we're using LangChain, we aren't sending raw text directly. ​Instead, we work with structured prompt and chat message objects that we'll construct ​in the next steps.

    # Gemini LLM wrapper (LangChain)
    llm = ChatGoogleGenerativeAI(
        model="gemini-2.5-flash",
        api_key=GEMINI_API_KEY
    )


​Our first workflow uses the prompt template class, which is perfect for generating single ​customized responses where you need to fill in a few variables. 
​We start by defining a template text string that contains placeholders indicated by curly ​braces, tone, topic, and length. ​Think of this as a fill in the blanks letter.
    
    print("\n--- PromptTemplate ---")

    # Ask user inputs
    topic = input("Enter a topic: ")
    tone = input("Choose tone (funny / formal / simple): ")
    length = input("Choose length (short / medium / long): ")

    # Template with 3 variables
    template_text = (
        "Write a {tone} explanation about the topic: {topic}. "
        "Make the explanation {length} and easy to understand."
    )

Next, we create the prompt template object itself, specifying exactly what our input ​variables are. ​We then ask the user for values for these variables, topic, tone, and length. 

    # Create PromptTemplate
    prompt = PromptTemplate(
        input_variables=["tone", "topic", "length"],
        template=template_text
    )

​The magic happens when we call prompt.format. Prompt.format iject user values into placeholder to produce the final prompt.

    final_prompt = prompt.format(
        tone=tone,
        topic=topic,
        length=length
    )

​This takes the user's inputs, safely injects them into the template string, and creates ​the final prompt. 
​We display this generated prompt so you can see the fully customized instruction that ​Gemini receives before we send it to llm.invoke. ​This ensures the user's request is specific, structured, and effective. 

    print("\nGenerated Prompt:")
    print(final_prompt)

    print("\nSending to Gemini...\n")
    response = llm.invoke(final_prompt)

    print("Gemini Response:")
    print(response.content)

Advanced tools: chat prompt template
This is used when you need to enforce roles and behavior within the interaction.so it not a single string.
its built from a list of structured messages.

We defined 3 specific roles which are System, User, Assistant.
System - The global instruction that acts as the model core directive
User - The actual queries from the user
Assistant - Act as additional contstraint on the tone, capture user input for the system role, the question, and the tone.

    # Chat template with structured message roles
    chat_template = ChatPromptTemplate.from_messages([
        ("system", "{system_role}"),
        ("user", "{user_question}"),
        ("assistant", "Respond in a {tone} tone.")
    ])

chatTemplate.formatMessages method generates a list of structured message objects. 
It will display these generated messages so we can see how Langchain prepares a precise multi-part command for Gemini leading to highly controlled and contextual responses.

**Main.py**

This is where we combine those previous module into one interative menu.
Start by greeting the user, presenting user 2 clear option which is PromptTemplateWorkflow & chatPromptTemplateWorkflow.
We wrap these 2 option into a while loop to keep the application running until the user types exit.

If they choose option 1, we call single_prompt_demo() and walk them trough the variable filling processs.
if they choose option 2, we call chat_prompt_demo() and let them assign roles and personas.

This structure allows the user to easily switch between exploring basic templating and advanced conversational role-playing.

To Run

    python main.py

If there are error after filling variables, try to go to setting "Preferences: Open User Settings (JSON)"

    "python.terminal.useEnvFile": true




