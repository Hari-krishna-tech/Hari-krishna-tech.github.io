---
layout: post
title: Top 30 System Design Concepts
date: 2025-03-29
tags:
  [
    system design,
    scalibility,
    distributed systems,
    backend,
    microservices,
    devops,
  ]
---

<div class="message">
  Intro to 30 must know concepts in System Design 
</div>

# Client-Server Architecture: The Backbone of Modern Web Applications

Ever wondered how your favorite apps and websites actually work behind the scenes? Let's dive into the fascinating world of client-server architecture, the foundation that powers virtually all modern web and mobile applications.

## IP Address: The Digital Street Address

- Every server deployed on the public internet has its own unique IP address
- Clients use these IP addresses to communicate with servers over the internet
- Think of IP addresses as the postal addresses of the digital world—without them, your data wouldn't know where to go!

## Domain Names: Because Who Remembers Numbers?

- IP addresses (like 172.217.168.238) are about as memorable as your tax ID number
- Domain names (like google.com) provide a human-friendly alternative
- When you type a domain name, your Internet Service Provider (ISP) connects to a Domain Name System (DNS) to resolve it to the correct IP address
- It's like having a contact list in your phone instead of memorizing everyone's number!

## Proxy & Reverse Proxy: The Digital Middlemen

### Proxy

- Acts as a middleman between you and the internet
- Makes requests on your behalf, hiding your IP address from the websites you visit
- Like having a friend buy concert tickets for you so the venue doesn't know who's coming

### Reverse Proxy

- Works in the opposite direction, intercepting client requests before they reach the server
- Forwards requests to the appropriate backend servers
- Think of it as a receptionist who directs visitors to the right department

## Latency: The Digital Distance Tax

- Latency is the delay caused by the physical distance between client and server
- The farther away the server, the longer it takes for data to travel
- One solution? Deploy multiple servers across the world
- Because nobody likes waiting for a page to load while their coffee gets cold!

## HTTP/HTTPS: The Communication Protocol

- HTTP (Hypertext Transfer Protocol) is how clients and servers talk to each other
- HTTPS is the secure, encrypted version of HTTP
- Without HTTPS, your data travels naked across the internet—not a good look!

## API: The Digital Translator

- APIs (Application Programming Interfaces) act as intermediaries between clients and servers
- Clients send requests via HTTP to the API in a specific format
- The API processes these requests and returns responses
- It's like a waiter taking your order, delivering it to the kitchen, and bringing back your food

## REST: Rules of Engagement

- REST (Representational State Transfer) defines a structured way for clients and servers to communicate
- Key characteristics:
  - Stateless: Every request is independent (the server has no memory of previous requests)
  - Resource-oriented: Everything is treated as a resource
  - Standard methods: GET, POST, PUT, DELETE
- It's like having a standardized language so everyone can understand each other

## GraphQL: The Picky Eater

- REST can be inefficient, often returning unwanted data or requiring multiple requests
- GraphQL lets you specify exactly what data you need—no more, no less
- Comes with trade-offs: requires more backend processing and is harder to cache
- Like ordering a custom sandwich instead of taking whatever's on the menu

## Databases: The Digital Filing Cabinet

- Databases ensure data is stored, retrieved, and managed efficiently
- They keep your data secure, consistent, and durable
- Without them, your favorite apps would have amnesia!

## SQL vs NoSQL: Different Strokes for Different Folks

### SQL (Relational Databases)

- Uses tables with rows and columns
- Follows ACID properties (Atomicity, Consistency, Isolation, Durability)
- Provides strong consistency
- Perfect for structured data with clear relationships

### NoSQL

- Comes in various flavors: key-value stores, document stores, graph databases, wide-column stores
- Offers high scalability and performance
- Features flexible schemas
- Ideal for unstructured or semi-structured data
- When your data doesn't fit neatly into tables, NoSQL is your friend

## Scaling: Growing Pains

### Vertical Scaling

- Adding more resources (storage, compute, RAM) to a single machine
- Quick fix but not a long-term solution
- Every machine has its limits
- Like trying to make your car faster by adding a bigger engine—eventually, you hit physical constraints

### Horizontal Scaling

- Distributing requests among multiple servers
- Reduces demand on any single system
- Eliminates single points of failure
- Like adding more lanes to a highway instead of making one lane wider

## Load Balancer: The Traffic Director

- When scaling horizontally, how does a client know which server to talk to?
- Load balancers take in all requests and distribute them to the appropriate servers
- Distribution can be based on various strategies (round-robin, least connections, etc.)
- They also collect responses from servers and return them to clients
- Think of them as air traffic controllers for your data

## Indexing: The Digital Shortcut

- As data grows, retrieval and updates become slower
- Indexing creates efficient lookup tables to find values without scanning entire tables
- Like the index in a book—it helps you find specific topics quickly
- Speeds up reads but slows down writes (since indexes need updating)
- No free lunch in computer science!

## Replication: Copy That

- When requests are very high, indexing alone isn't enough
- Creating copies of the database allows load distribution
- Write operations happen on the primary replica and get copied to others
- Improves availability and is great for read-heavy databases
- It's like having multiple copies of a popular book in a library

## Sharding: Divide and Conquer

- When data grows to terabytes, even replicated databases struggle
- Sharding splits the main database into multiple shards, each containing a subset of data
- Data is divided based on a sharding key (e.g., primary key)
- Queries are distributed, improving both read and write performance
- Like splitting a massive encyclopedia into multiple volumes

## Vertical Partitioning: Column Control

- Sometimes the issue isn't the number of rows but the number of columns
- Vertical partitioning splits tables into smaller ones (normalization)
- Separates chunks of data that can be split from the primary table
- Like organizing your closet by type of clothing instead of throwing everything together

## Caching: The Speed Demon

- Retrieving data from disk is always slower than from memory
- Caching stores frequently accessed data in memory for faster retrieval
- The cache-aside pattern checks the cache first before going to the database
- Time-to-live (TTL) values prevent serving outdated data
- It's like keeping your most-used cooking ingredients on the counter instead of in the pantry

## Denormalization: Breaking the Rules

- Normalized databases reduce redundancy but can require many joins
- Joins slow down as datasets grow
- Denormalization reduces joins by combining related data into a single table
- Accepts some data duplication for performance gains
- Used primarily in read-heavy applications
- Sometimes you have to break the rules to win the game!

## CAP Theorem: Pick Two, Not Three

- No distributed database system can simultaneously guarantee all three:
  - Consistency: All nodes see the same data at the same time
  - Availability: Every request receives a response
  - Partition Tolerance: System continues to function despite network failures
- In real-world scenarios, network partitions are inevitable, so we choose between CP and AP
- CP systems prioritize consistency over availability
- AP systems prioritize availability over consistency (think social media likes, view counts)
- It's the "good, fast, cheap—pick two" of database design

## Blob Storage: The Digital Warehouse

- Services like Amazon S3 store large structured files (images, videos, documents)
- Related data is stored in buckets with unique URLs for easy access
- Advantages: scalability, pay-as-you-go model, automatic replication
- Streaming directly from blob storage can be slow for distant users
- Great for storing your cat videos, not so great for serving them quickly worldwide

## Content Delivery Network (CDN): The Global Express

- CDNs are distributed servers across the world
- They deliver HTML, JavaScript, images, and videos based on geographical location
- Solves the problem of slow loading from distant servers
- Like having local warehouses for Amazon instead of shipping everything from headquarters

## Real-time Applications: The Instant Gratification

- Live chat, stock markets, and online games need real-time updates
- Frequent polling is inefficient and increases server load
- WebSockets provide continuous two-way communication between client and server
- Like having a phone call instead of repeatedly asking "Any news?"

## Webhooks: The Notification System

- Instead of constantly checking for updates, webhooks notify you when events occur
- Applications register a webhook URL with a server
- When an event occurs, the server sends an HTTP request to that URL
- Like having someone call you when your package arrives instead of checking the mailbox every hour

## Microservices: The Modular Approach

- Traditional monolithic applications have all services in one application
- This creates single points of failure and makes code hard to manage
- Microservices split applications into separate servers that scale independently
- They communicate via HTTP or message queues
- Like having specialized teams instead of everyone trying to do everything

## Message Queues: The Asynchronous Communicator

- Synchronous communication (HTTP) doesn't scale well for inter-server communication
- Message queues enable asynchronous communication
- Producers place messages in the queue and forget about them
- Consumers retrieve messages when they're ready to process them
- Example: Payment service queues notification requests instead of waiting for notifications to send
- It's like dropping off your dry cleaning and coming back later, instead of waiting there

## Rate Limiting: The Bouncer

- Public services need protection from bots and abusive users
- Rate limiting assigns quotas per user/IP address
- Exceeding quotas results in blocked requests
- Common algorithms: fixed window, sliding window, token bucket
- Like a nightclub that only lets in a certain number of people per hour

## API Gateway: The Swiss Army Knife

- Handles rate limiting, authentication, logging, monitoring, and request routing
- Acts as a single entry point to your microservices
- Simplifies the client interface to your complex backend
- Like having a concierge desk at a large hotel

## Idempotency: The Safety Net

- In distributed networks, service failures and retries are common
- Accidental refreshes during payments could result in double charges
- Idempotency ensures repeated requests produce the same result as a single request
- Each request gets a unique ID; the server checks if it's already been handled
- Like having a system that recognizes you've already paid, even if you accidentally hit "Pay" twice

---

There you have it—the essential components that power the digital experiences we take for granted every day. Next time you're scrolling through social media or ordering food online, you'll have a better appreciation for the complex architecture working behind the scenes!
