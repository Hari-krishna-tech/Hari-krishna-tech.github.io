---
layout: project
title: T3 Chat – Unified Chat Across Multiple AI Models
date: 2025-05-10
description: A full-stack chat platform using Next.js, NextRouter, and PostgreSQL, integrating multiple LLMs via their respective SDKs for seamless, unified interaction.
github_link: https://github.com/harikrishna/t3-chat
tech_stack: ["Next.js", "PostgreSQL", "NextRouter", "LLM Integration", "OAuth 2.0"]
tags: [ai, chatbot, nextjs, llm]
---

<p class="message">
  T3 Chat is a unified, full-stack chat platform that integrates multiple Large Language Models (LLMs) into a single interface, offering users a seamless conversation experience.
</p>

## Overview

T3 Chat was created to address the friction of switching between different AI interfaces (like ChatGPT, Claude, and Gemini). By consolidating various model SDKs into a single responsive application, T3 Chat allows developers and power users to query, compare, and interact with multiple LLMs concurrently.

## Technical Implementation

### Core Architecture

- **Next.js & App Router**: Fast, server-side rendered application structure with modern routing patterns.
- **PostgreSQL**: Robust, relational database management to store user chat histories, settings, and agent preferences securely.
- **LLM Provider SDK Integration**: Integrated OpenAI, Anthropic, and Google Gemini SDKs under a clean, abstract interface layer.
- **OAuth 2.0 & OpenID Connect**: Secure user authentication handled through Google OAuth, ensuring safe session management and identity protection.

## Key Features

- **Unified Interface**: Chat with different AI models without navigating between separate platforms.
- **Session Management**: Secure user account creation and historical chat tracking.
- **Model Comparison**: Easy switching and parallel query capabilities to compare responses from different LLMs side by side.
- **OAuth Authentication**: Google login integration for security and simplicity.

<div class="project-links">
  <a href="https://github.com/harikrishna/t3-chat" class="github-link">View on GitHub</a>
</div>
<div class="project-meta">
  <span class="tech-badge">Next.js</span>
  <span class="tech-badge">PostgreSQL</span>
  <span class="tech-badge">Google OAuth</span>
  <span class="tech-badge">LLM SDKs</span>
  <span class="date-badge">May 2025</span>
</div>
