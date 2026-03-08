+++
date = '2025-02-03T17:07:58+05:00'
title = 'Setting Up Ollama with DeepSeek Coder on Manjaro: A Step-by-Step Guide'
tags = ['langchain', 'RAG']
+++

## What is Ollama?

**Ollama** is an open-source framework designed to run and manage large language models (LLMs) efficiently on local machines. It allows developers to load and interact with various AI models without relying on cloud-based APIs.

### Why Use Ollama?
- **Privacy**: No data leaves your local system.
- **Customization**: Run and fine-tune models as per your requirements.
- **Performance**: Optimized for running models efficiently on consumer hardware.

## Note
This guide is based on **Manjaro Linux (Kernel: Base Phoenix)**. Ubuntu users should replace package manager commands accordingly (e.g., using `apt` instead of `pacman`). Make sure to check compatibility for your specific distribution.

## Installing Ollama on Manjaro Linux

Follow these steps to install Ollama on Manjaro Linux:

### Step 1: Update Your System
```sh
sudo pacman -Syu
```

### Step 2: Install Ollama
```sh
sudo pacman -S ollama --noconfirm
```

### Step 3: Enable and Start Ollama Service
```sh
sudo systemctl enable ollama
sudo systemctl start ollama
sudo systemctl restart ollama
sudo systemctl status ollama
```

## Available Ollama Models

Ollama supports multiple models that can be used for various applications. You can check out the official Ollama models repository at:

**[Ollama Models List](https://ollama.ai/library)**

Some of the available models include:
- **DeepSeek Coder**
- **Llama 2**
- **Mistral**
- **Gemma**

## Installing DeepSeek Coder Model

DeepSeek Coder is an advanced open-source LLM optimized for code generation and completion.

### Step 1: Pull the DeepSeek Coder Model
```sh
ollama pull deepseek-r1
```

### Step 2: Verify the Installation
```sh
ollama list
```

### Step 3: Running the Model
```sh
ollama run deepseek-r1
```

**Note:** The guide covers DeepSeek Coder installation, but you can run any other model you have downloaded by replacing `deepseek-r1` with the model's name.

## Writing a Sample Program Using DeepSeek

To test the model, you can generate a simple Python script. For example, let's ask it to generate a Python script to calculate the factorial of a number:

```sh
echo "Write a Python program to calculate factorial using recursion." | ollama run deepseek-r1
```

This should return a Python script like:
```python
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)

num = int(input("Enter a number: "))
print("Factorial:", factorial(num))
```

## Congratulations! You Have Deployed Your Own Chatbot on a Local System.

## Conclusion
Ollama provides a simple and efficient way to run LLMs locally, including DeepSeek for programming-related tasks. By following the above steps, you can easily set up and interact with the model on your Manjaro Linux system.