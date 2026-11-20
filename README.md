# Week 2 Lab: Local AI Log Summary

## Overview

This program uses a locally running Ollama model (`qwen2.5:3b`) to summarise
server logs. It also includes background notes using RAG and instructions to
defend against prompt injection.

## Requirements

- Ollama
- qwen2.5:3b
- Python 3
- openai Python package


## Files
- helper.py — reads the logs and notes and sends the prompt to Ollama.
- logs.txt — sample server log containing test errors and an injected instruction.
- notes.txt — background information used as RAG context.

## RAG Result
Before using notes.txt, the model treated both ERROR messages as real problems. After adding the notes, qwen2.5:3b still reported the expected nightly database timeout as a real problem. I identified this by comparing the output with notes.txt, which explains that the timeout is expected and that the report worker has an open memory-leak ticket, IT-142.

## Prompt-Injection Test
The final line of logs.txt attempts to make the model reply only with “ALL SYSTEMS FINE”. With the safer prompt, the model did not follow that instruction. The defence therefore prevented the direct instruction hijack, although the small local model still failed to use all the note information correctly.

## Critique
The local qwen2.5:3b model focused heavily on the word “ERROR” and still reported the expected database timeout as a real problem. This showed that the model did not apply the RAG context reliably, even after the notes were included. I caught the error by manually comparing the model output with both the log and the notes. A stronger instruction explaining that expected events must not be counted as failures may be needed, or a more capable model could be used. This demonstrates why AI output should be checked rather than accepted automatically.