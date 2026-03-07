# 12-default-role-restrictions

What is 12-default-role-restrictions and why does it matter?  
12-default-role-restrictions refers to a set of predefined limitations on user roles within a system, aiming to enhance security and compliance by restricting access to sensitive data and functions. This concept is crucial in today's digital landscape, where data breaches and unauthorized access are significant concerns. By implementing default role restrictions, organizations can mitigate these risks and ensure that users only have the necessary permissions to perform their tasks.

## The Problem It Solves
What specific problem or tension led to the need for 12-default-role-restrictions?  
The primary issue that 12-default-role-restrictions addresses is the over-assignment of privileges, which can lead to data breaches, unauthorized data modifications, and compliance violations. When users have excessive access rights, it increases the attack surface and the potential for insider threats, making it challenging for organizations to maintain the confidentiality, integrity, and availability of their data.

## How to Think About It
Describe the mental model for understanding this topic.  
To comprehend 12-default-role-restrictions, it's essential to think in terms of the principle of least privilege (PoLP), which states that users should only have the minimum permissions necessary to perform their tasks. This mental model involves analyzing the requirements of each role, identifying the necessary permissions, and restricting access to sensitive data and functions. By adopting this mindset, organizations can ensure that their access control mechanisms are aligned with their security and compliance objectives.

## Core Truths
List the essential principles that always apply.

- **Least Privilege**: Users should only have the necessary permissions to perform their tasks, reducing the risk of data breaches and unauthorized access.
- **Role-Based Access Control**: Access rights should be assigned based on roles, rather than individual users, to simplify management and reduce errors.
- **Continuous Monitoring**: Default role restrictions should be regularly reviewed and updated to ensure they remain effective and aligned with changing business requirements.

## What It Looks Like in Practice
Briefly explain how 12-default-role-restrictions is typically used in real situations.  
In practice, 12-default-role-restrictions involves defining a set of default roles with limited permissions, which are then assigned to users based on their job functions. For example, a default role for a sales team might only include access to customer data, sales reports, and order management systems, while a default role for IT administrators might include access to system configuration, network settings, and security logs.

## Where People Go Wrong
Common misunderstandings or misuses.

- **Overly Permissive Roles**: Assigning too many permissions to a default role, which can lead to unauthorized access and data breaches, as users may have access to sensitive data or functions they don't need.
- **Insufficient Review**: Failing to regularly review and update default role restrictions, which can result in outdated permissions and increased security risks, as business requirements and user roles evolve over time.

## When to Use It and When Not To
Define clear boundaries.

**Use it when**
- **New User Onboarding**: Assigning default roles with limited permissions to new users, ensuring they only have the necessary access to perform their tasks.
- **Role Changes**: Updating default role restrictions when a user's role changes, to reflect their new responsibilities and required permissions.

**Avoid it when**
- **Highly Customized Roles**: In situations where users require highly customized roles with unique permissions, as default role restrictions may not be sufficient to meet their needs.
- **Temporary Access**: When temporary access is required, such as for contractors or vendors, as default role restrictions may not be flexible enough to accommodate their specific needs.

## One Line to Remember
A single sentence that captures the essence of 12-default-role-restrictions.

12-default-role-restrictions is a security best practice that involves assigning limited permissions to users based on their roles, to minimize the risk of data breaches and unauthorized access.

## References (Optional)
Up to three authoritative links if deeper reading is useful.
National Institute of Standards and Technology (NIST) [Guide to Role-Based Access Control](https://csrc.nist.gov/publications/detail/sp/800-162/final)^[1]
SANS Institute [Role-Based Access Control](https://www.sans.org/security-awareness-training/developer/role-based-access-control)^[2]
International Organization for Standardization (ISO) [ISO/IEC 27002:2022](https://www.iso.org/standard/77070.html)^[3]