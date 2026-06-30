---
layout: project
title: Code Cli – Terminal-based AI Coding Agent
date: 2025-06-01
description: A terminal-based AI coding assistant in TypeScript that integrates with LLMs to autonomously read, write, edit code, execute shell commands, and navigate codebases via natural language.
github_link: https://github.com/harikrishna/code-cli
tech_stack: ["TypeScript", "Node.js", "DeepSeek API", "LLM Orchestration", "CLI"]
tags: [cli, agent, ai, typescript]
---

<p class="message">
  Code Cli is an autonomous, terminal-based AI coding assistant built in TypeScript, helping developers read, write, refactor, and execute commands directly from the console.
</p>

## Overview

Code Cli bridges the gap between AI code generation and local project workflows. Rather than copying and pasting snippets from web-based chat tools, Code Cli interfaces directly with your filesystem and terminal using a secure, sandboxed orchestration model. It can navigate directories, modify files, run tests, and debug issues dynamically.

## Technical Implementation

### Core Architecture

- **TypeScript & Node.js**: High-performance, type-safe command-line application structure.
- **LLM Integration**: Extensible provider interface supporting DeepSeek and other LLM APIs via REST and stream adapters.
- **Tool Orchestration**: A custom-designed agent loop that parses LLM intent, maps it to local system tools, and feeds the execution outputs back into the context.
- **Safety Mechanisms**: Built-in command validation filters and sandboxed filesystem checks to prevent unauthorized commands or recursive file damage.

## Key Features

- **Natural Language Workspace Command**: Tell the CLI what you want to build, edit, or search, and it will execute the sequence autonomously.
- **Interactive REPL Interface**: A clean console prompting layer for continuous agent dialog and review.
- **Extensible LLM Adapters**: Easily swap underlying models depending on the task complexity and speed requirements.
- **Terminal Execution & Feedback**: The agent runs builds, runs linters, and reads compile errors to self-correct code implementation.

<div class="project-links">
  <a href="https://github.com/harikrishna/code-cli" class="github-link">View on GitHub</a>
</div>
<div class="project-meta">
  <span class="tech-badge">TypeScript</span>
  <span class="tech-badge">Node.js</span>
  <span class="tech-badge">DeepSeek API</span>
  <span class="tech-badge">Agent Orchestration</span>
  <span class="date-badge">June 2025</span>
</div>
