Developing, optimising and refining interacting with AI systems to produce optimal results. 
Not just limited to GPT.

### What is GPT
GPT stands for generative pre-trained transformers

It is a particular kind of Deep learning based Artificial network following the Transformer Architecture developed by google.

> These networks are called Large Language Models (**LLM**)

Trained on large datasets, large enough to include nearly everything that has been written on the internet. The model is massively large - GPT 4 is based on 13 trillion parameters

### Tokens
The fundamental units of text that LLM models use to process and generate language. 
Tokens can represent individual characters, words or subwords or combinations of characters. 

>[!exampe]-
![[Pasted image 20230724105917.png | center | 300]]
>https://platform.openai.com/tokenizer

Open AI charges for the input + output tokens. 

### How LLM Works
On the input string "The capital of New Zealand"

The model will predict that based on the training data, the next most likely word is "is".

It will do the same thing again to predict the next word after "is" is "Wellington"

It will next decide that this is enough and stop here. 

These model can detect long range dependencies between words meaning it can understand context. 

This means that a better prompt / better input will lead to a better answer. 

### LLM Settings
#### Temperature
Controls the randomness of the model's output. 

Low Temperature: (eg: 0.2). LLM Chooses the most probable next tokens
 - More focuses and deterministic responses. 

Medium Temperature
- Creative responses, surprising responses. 

High Temperature (>1):
- Nonsensical responses. 

#### Top P 
Top-P \[0; 1]: Controls the diversity of the models's output by limiting the set of words it can choose from. 

**Low Top-P** values restrict he model to selecting from a smaller set of tokens, those with higher probabilities. 

**High top-P** values allow the model to choose from a wider range of tokens including those with lower probabilities. 

#### Summary
Together these settings control how random or focused the output is   .....








### Prompt Engineering 101
- **Instruction**
- **Context** 
- **Input Data**
- **Output Indicator**


==stuff==


### LLM Limitations
- Lack of real understanding
LLM aren't really AI. They are just really good at predicting the next word in a sentence, unrelated to any real understanding of language. 

- Tendency to be overconfident
Cannot provide garentee that its output is right, only that it sounds right. 

- High Computational requirements
Very expensive to train, query and maintain (OpenAI spends over $1 Million a day to run it)

- Vulnerable to Biases and Errors
Prone to errors due to biasses in the training data. 




### THing
The AI should only give hints when the user explicitly asks for one
