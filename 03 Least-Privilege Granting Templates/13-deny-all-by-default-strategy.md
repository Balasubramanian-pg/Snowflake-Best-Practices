# 13-deny-all-by-default-strategy

What is 13-deny-all-by-default-strategy and why does it matter?  
The 13-deny-all-by-default-strategy refers to a security approach where all incoming and outgoing network traffic is blocked by default, unless explicitly allowed. This strategy matters because it provides a robust security posture by minimizing the attack surface. It is a fundamental principle in network security and access control.

## The Problem It Solves
What specific problem or tension led to the need for 13-deny-all-by-default-strategy?  
The problem of unauthorized access to network resources and data led to the need for the 13-deny-all-by-default-strategy. Without this approach, networks are vulnerable to various types of attacks, including malware, denial-of-service (DoS), and unauthorized data breaches.

## How to Think About It
Describe the mental model for understanding this topic.  
To understand the 13-deny-all-by-default-strategy, think of it as a "default deny" mindset, where all traffic is blocked unless there is a specific reason to allow it. This approach requires a thorough understanding of the network architecture, traffic flows, and security requirements. It involves analyzing the network traffic patterns, identifying the required traffic flows, and configuring the security controls to allow only the necessary traffic.

## Core Truths
List the essential principles that always apply.

- All network traffic is blocked by default, unless explicitly allowed
- Only authorized traffic is permitted to flow through the network
- Security controls, such as firewalls and access control lists, are used to enforce the default deny policy

## What It Looks Like in Practice
Briefly explain how 13-deny-all-by-default-strategy is typically used in real situations.  
In practice, the 13-deny-all-by-default-strategy is typically used in network security architectures, where firewalls, intrusion prevention systems, and access control lists are configured to block all incoming and outgoing traffic, unless explicitly allowed. This approach is often used in conjunction with other security controls, such as authentication and authorization mechanisms, to provide an additional layer of security.

## Where People Go Wrong
Common misunderstandings or misuses.

- Overly permissive security policies, which can lead to unauthorized access to network resources, and why it is tempting because it can simplify network management
- Insufficient monitoring and logging, which can make it difficult to detect and respond to security incidents, and its hidden cost is the increased risk of undetected security breaches

## When to Use It and When Not To
Define clear boundaries.

**Use it when**
- In high-security environments, such as financial institutions or government agencies, where the risk of unauthorized access is high
- In networks with sensitive data, such as healthcare or financial information, where the risk of data breaches is high

**Avoid it when**
- In low-security environments, such as public Wi-Fi networks, where the risk of unauthorized access is low
- In networks with minimal security requirements, such as small office/home office (SOHO) networks, where the overhead of implementing and managing a default deny policy may be unnecessary

## One Line to Remember
A single sentence that captures the essence of 13-deny-all-by-default-strategy.
The 13-deny-all-by-default-strategy is a security approach that blocks all network traffic by default, unless explicitly allowed, to minimize the attack surface and prevent unauthorized access to network resources.

## References (Optional)
Up to three authoritative links if deeper reading is useful.
National Institute of Standards and Technology (NIST) [Guide to Firewalls and Network Security](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-41r1.pdf)^1 
SANS Institute [Firewall Configuration and Management](https://www.sans.org/reading-room/whitepapers/firewalls/paper/55)^2 
Center for Internet Security (CIS) [Firewall Configuration Benchmark](https://www.cisecurity.org/benchmark/firewall/)^3