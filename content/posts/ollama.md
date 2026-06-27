---
title: "Ollama + DeepSeek: How I Run LLMs Locally"
date: 2025-02-03
tags: ["ollama", "deepseek", "llm", "arch-linux", "ubuntu"]
---
## Why I Use Ollama

I wanted to run LLMs on my own hardware without paying for API calls or sending data to the cloud. **Ollama** made that straightforward — install, pull a model, start chatting.

---

## Install Ollama

### Arch Linux

Update your system, then install Ollama:

```bash
sudo pacman -Syu
sudo pacman -S ollama
```

Enable and start the service:

```bash
sudo systemctl enable ollama
sudo systemctl start ollama
sudo systemctl status ollama
```

You should see `active (running)`.

### Ubuntu

Install Ollama with the official install script:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Start the service:

```bash
sudo systemctl enable ollama
sudo systemctl start ollama
sudo systemctl status ollama
```

---

## Available Models

Browse models at [ollama.com/library](https://ollama.com/library).

Popular choices:

- **deepseek-r1** — strong reasoning and coding help
- **llama3** — general-purpose chat
- **mistral** — fast and lightweight
- **gemma** — small models for weaker hardware

---

## Pull and Run DeepSeek

This example uses **deepseek-r1**. You can swap the name for any model you prefer.

### Step 1: Download the model

```bash
ollama pull deepseek-r1
```

### Step 2: Confirm it is installed

```bash
ollama list
```

You should see `deepseek-r1` in the list.

### Step 3: Start a chat session

```bash
ollama run deepseek-r1
```

Type a question and press Enter. Type `/bye` to exit.

---

## Simple Test: Generate Python Code

Ask the model to write a short program:

```bash
echo "Write a Python program to calculate factorial using recursion." | ollama run deepseek-r1
```

Example output:

```python
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)

num = int(input("Enter a number: "))
print("Factorial:", factorial(num))
```

---

## Troubleshooting

| Problem | What to try |
| ------- | ----------- |
| `command not found: ollama` | Install Ollama first (see above) |
| Service not running | `sudo systemctl restart ollama` |
| Model download fails | Check your internet connection |
| Slow responses | Try a smaller model like `gemma` |

---

## Conclusion

Ollama makes it easy to run LLMs locally. Install it once, pull a model, and start chatting from your terminal — on Arch Linux or Ubuntu.
