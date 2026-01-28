# 03-apply-principle-of-least-privilege

The principle of least privilege is a fundamental concept in security and access control, stating that users, processes, and systems should only have the minimum privileges necessary to perform their tasks. This principle matters because it helps prevent unauthorized access, reduces the attack surface, and limits the damage that can be caused by a security breach. By applying this principle, organizations can significantly improve their overall security posture.

## The Problem It Solves
The problem that the principle of least privilege solves is the over-privileging of users, processes, and systems, which can lead to security breaches, data leaks, and other malicious activities. When users or systems have more privileges than necessary, they become attractive targets for attackers, who can exploit these excessive privileges to gain unauthorized access to sensitive data or disrupt critical systems. This can result in significant financial losses, reputational damage, and legal liabilities.

## How to Think About It
To understand the principle of least privilege, it's essential to think about the concept of "need-to-know" and "need-to-do". This mental model involves identifying the minimum set of privileges required for a user, process, or system to perform its tasks, and then granting only those privileges. It's also important to consider the separation of duties, where multiple users or systems are required to collaborate to complete a task, reducing the risk of a single point of failure or exploitation.

## Core Truths
The following principles are essential to the principle of least privilege:
- **Minimize privileges**: Grant only the minimum privileges necessary for a user, process, or system to perform its tasks.
- **Limit access**: Restrict access to sensitive data and systems to only those who need it to perform their tasks.
- **Separate duties**: Divide tasks and responsibilities among multiple users or systems to reduce the risk of a single point of failure or exploitation.

## What It Looks Like in Practice
In practice, the principle of least privilege is typically applied by implementing role-based access control (RBAC), where users are assigned roles that define their privileges and access levels. This approach ensures that users only have the privileges necessary to perform their tasks, and that access to sensitive data and systems is strictly controlled. Additionally, organizations may use techniques such as privilege escalation, where users are granted temporary elevated privileges to perform specific tasks, and then returned to their normal privilege level.

## Where People Go Wrong
Common misunderstandings or misuses of the principle of least privilege include:
- **Over-privileging**: Granting users or systems more privileges than necessary, which can lead to security breaches and other malicious activities. This is often tempting because it can simplify administration and reduce the need for frequent privilege adjustments.
- **Under-privileging**: Granting users or systems too few privileges, which can lead to frustration and decreased productivity. This can have a hidden cost, as users may find ways to circumvent security controls or exploit vulnerabilities to gain the necessary privileges.

## When to Use It and When Not To
**Use it when**:
- Assigning privileges to new users or systems
- Configuring access control for sensitive data or systems
**Avoid it when**:
- Emergency situations require rapid privilege escalation
- Legacy systems or applications require broad privileges to function

## One Line to Remember
The principle of least privilege is about granting only the minimum privileges necessary for a user, process, or system to perform its tasks, to prevent unauthorized access and reduce the risk of security breaches.

## References (Optional)
National Institute of Standards and Technology (NIST) [Guide to Attribute Based Access Control](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-162.pdf)¹
SANS Institute [Implementing Least Privilege](https://www.sans.org/white-papers/35584-implementing-least-privilege/)²
Center for Internet Security (CIS) [Least Privilege](https://www.cisecurity.org/controls/least-privilege/)³