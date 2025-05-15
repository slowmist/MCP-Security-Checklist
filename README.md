[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/slowmist-mcp-security-checklist-badge.png)](https://mseep.ai/app/slowmist-mcp-security-checklist)

# MCP Security Checklist: A Security Guide for the AI Tool Ecosystem

[![Twitter URL](https://img.shields.io/twitter/url/https/twitter.com/slowmist_team.svg?style=social&label=Follow%20%40SlowMist_Team)](https://twitter.com/slowmist_team) 

[English Version](./README.md), [中文版本](./README_CN.md)

## Author Information
This security checklist was compiled and is maintained by [@SlowMist_Team](https://twitter.com/slowmist_team).
> SlowMist, a global leader in blockchain ecosystem threat intelligence, aims to enhance security protection and safeguard user privacy during the integration of blockchain and AI ecosystems.

We sincerely thank [FENZ.AI](https://fenz.ai/) for their valuable contributions and support.
> FENZ.AI redefines AI security with future-proof auditing. FENZ is the essential infrastructure for the AI era: "Superintelligence starts with super safety."

## 📚 Table of Contents

- [Overview](#overview)
- [How to Use](#how-to-use)
- [MCP Server (MCP Plugin) Security](#mcp-servermcp-plugin-security)
- [MCP Client/MCP HOST Security](#mcp-clientmcp-host-security)
- [MCP Adaptation and Invocation Security on Different LLMs](#mcp-adaptation-and-invocation-security-on-different-llms)
- [Multi-MCP Scenario Security](#multi-mcp-scenario-security)
- [Unique Security Points for Cryptocurrency-related MCPs](#unique-security-points-for-cryptocurrency-related-mcps)
- [MCP Security Self-Assessment Tools](#MCP-Security-Self-Assessment-Tools)
- [References](#references)
## Overview

With the rapid development of large language models (LLMs), a variety of new AI tools have continued to emerge. Among them, tools based on the Model Context Protocol (MCP) standard have become a key bridge connecting LLMs with external tools and data sources. Since its release in late 2024, MCP has been widely adopted in mainstream AI applications such as Claude Desktop and Cursor. Various MCP Server marketplaces have also emerged, demonstrating strong ecosystem scalability.

However, the rapid adoption of MCP has also introduced new security challenges. In the current MCP architecture, the system consists of three main components: the **Host** (the local environment where the AI application runs), the **Client** (responsible for communicating with the Server and invoking tools), and the **Server** (the backend service corresponding to an MCP plugin). Users interact with the AI through the Host, while the Client parses the user's request and forwards it to the MCP Server for tool invocation or resource access. In scenarios involving multiple instances and cross-component collaboration, this architecture exposes a range of security risks—especially in sensitive contexts such as cryptocurrency transactions or custom plugin integration with LLMs—where the potential for exploitation is even higher and requires appropriate security controls.

Against this backdrop, **establishing and following a comprehensive MCP Security Checklist becomes critically important**. This checklist covers key areas ranging from user interface interaction, client components, and service-side plugins, to multi-MCP collaboration mechanisms and domain-specific scenarios such as cryptocurrency integrations. It is designed to help developers systematically identify and mitigate potential risks. By implementing these security measures, the overall stability and controllability of MCP systems can be significantly enhanced, ensuring that security evolves in parallel with the rapid advancement of AI applications.

<p align="center">
  <img src="assets/mcp_risk_points_en.png" alt="MCP flow risk diagram" />
</p>

<p align="center" style="font-style: italic; color: gray;">
  Figure: Schematic diagram of MCP multi-component interaction process and key risk points
</p>

## How to Use

This checklist is based on possible risk points encountered during our security audits of MCP projects, aiming to help developers ensure the security of MCP implementations. We use three priority levels to denote the importance of each item:

- ![Low][low_img] Indicates recommended items that can be omitted in specific contexts.
- ![Medium][medium_img] Indicates strongly recommended items which could be omitted in special cases, but doing so might negatively impact security.
- ![High][high_img] Indicates items that must not be omitted under any circumstances; removing them may lead to system failures or security vulnerabilities.

## MCP Server (MCP Plugin) Security

> **Note:** The MCP Server provides external tools, resources, and functionalities that AI can invoke. This is essentially the MCP plugin form. In general, it contains Resources, Tools, and Prompts.

### API Security

- [ ] **Input Validation:** ![High][high_img] Enforce strict validation on all API inputs to prevent injection attacks and invalid parameters.
- [ ] **API Rate Limiting:** ![Medium][medium_img] Implement call rate limits to prevent abuse or DoS attacks.
- [ ] **Output Encoding:** ![Medium][medium_img] Properly encode API outputs.

### Server Authentication & Authorization

- [ ] **Access Control:** ![High][high_img] Implement role-based access control, limit resource access, and enforce the principle of least privilege.
- [ ] **Credential Management:** ![High][high_img] Securely manage and store service credentials; avoid hard-coded secrets and use key management services.
- [ ] **External Service Authentication:** ![High][high_img] Use secure methods when authenticating with third-party services.
- [ ] **Least Privilege:** ![Medium][medium_img] Run service processes with the minimum required permissions to reduce the potential attack surface and elevation risks.
- [ ] **API Key Rotation:** ![Medium][medium_img] Automatically rotate API keys and service credentials periodically, limiting the valid timeframe of keys.
- [ ] **Service Identity Authentication:** ![Medium][medium_img] Provide a mechanism for Tools to verify the Server’s identity, helping clients to authenticate and use the service securely.

### Background Persistence Control

- [ ] **Lifecycle Management:** ![High][high_img] Implement strict lifecycle management for MCP plugins and coordinate with the client.
- [ ] **Shutdown Cleanup:** ![High][high_img] Forcefully clean up all MCP background processes when the client is shut down.
- [ ] **Health Check Mechanism:** ![Medium][medium_img] Regularly check the status of MCP plugins to detect abnormal persistence.
- [ ] **Background Activity Monitoring:** ![Medium][medium_img] Monitor and log all MCP background activities.
- [ ] **Activity Restrictions:** ![Medium][medium_img] Limit the operations and their duration that MCP plugins can perform in the background.

### Deployment & Runtime Security

- [ ] **Isolation Environment:** ![High][high_img] Server runs in an isolated environment (container, VM, or sandbox) to prevent escape and mitigate lateral movement attacks.
- [ ] **Container Security:** ![High][high_img] Adopt hardened container security configurations and run containers as non-root users. Employ immutable infrastructure and runtime protection.
- [ ] **Secure Boot:** ![Medium][medium_img] Validate service boot processes, implementing secure boot chains and integrity checks.
- [ ] **Environment Variable Security:** ![Medium][medium_img] Protect sensitive environment variables and ensure they are not exposed in logs.
- [ ] **Resource Limits:** ![Medium][medium_img] Enforce resource usage limits to prevent infinite loops or excessive calls when a large model malfunctions.

### Code & Data Integrity

- [ ] **Integrity Verification Mechanisms:** ![High][high_img] Use digital signatures, checksums, or similar to ensure code has not been tampered with.
- [ ] **Remote Validation:** ![Medium][medium_img] Support mechanisms for remote verification of code integrity.
- [ ] **Code Obfuscation & Hardening:** ![Low][low_img] Apply code obfuscation and hardening techniques to increase the difficulty of reverse engineering.

### Supply Chain Security

- [ ] **Dependency Management:** ![High][high_img] Securely manage third-party dependencies.
- [ ] **Package Integrity:** ![High][high_img] Verify the integrity and authenticity of packages.
- [ ] **Source Verification:** ![Medium][medium_img] Validate the source of all code and dependencies.
- [ ] **Secure Build:** ![Medium][medium_img] Ensure the build process is secure.

### Monitoring & Logging

- [ ] **Anomaly Detection:** ![High][high_img] Detect and report anomalous activity patterns.
- [ ] **Detailed Logging:** ![High][high_img] Log all service activities and security events.
- [ ] **Security Event Alerts:** ![High][high_img] Configure real-time alerts for critical security events.
- [ ] **Centralized Log Management:** ![Medium][medium_img] Collect and analyze logs in a centralized manner.
- [ ] **Log Integrity:** ![Medium][medium_img] Ensure log integrity to prevent tampering.
- [ ] **Audit Capability:** ![Medium][medium_img] Provide detailed auditing and incident investigation capabilities.

### Invocation Environment Isolation

- [ ] **Isolation Between MCP Instances:** ![High][high_img] Ensure operational isolation among multiple MCP Servers.
- [ ] **Resource Access Control:** ![High][high_img] Assign each MCP Server a clearly defined resource access boundary.
- [ ] **Tool Permission Separation:** ![Medium][medium_img] Use distinct permission sets for tools in different domains.

### Platform Compatibility & Security

- [ ] **System Resource Isolation:** ![High][high_img] Implement suitable resource isolation strategies according to different operating system characteristics.
- [ ] **Cross-platform Compatibility Testing:** ![Medium][medium_img] Test consistent security behavior of MCP Server across different OS and clients.
- [ ] **Platform-specific Risk Assessment:** ![Medium][medium_img] Evaluate unique security risks specific to each platform and apply mitigating measures.
- [ ] **Client-specific Handling:** ![Medium][medium_img] Ensure security controls can adapt to differences among various client implementations.

### Data Security & Privacy

- [ ] **Data Minimization:** ![High][high_img] Collect and process only the necessary data.
- [ ] **Data Encryption:** ![High][high_img] Encrypt sensitive data in storage and transit.
- [ ] **Data Isolation:** ![High][high_img] Ensure effective isolation of different users’ data.
- [ ] **Data Access Control:** ![High][high_img] Enforce strict access controls on data.
- [ ] **Sensitive Data Identification:** ![High][high_img] Automatically identify and handle sensitive data in a specialized manner.

### Resources Security

- [ ] **Resource Access Control:** ![High][high_img] Implement fine-grained access control for resources.
- [ ] **Resource Limits:** ![Medium][medium_img] Limit the size and quantity of a single resource.
- [ ] **Resource Template Security:** ![Medium][medium_img] Validate and sanitize template parameters of resources.
- [ ] **Sensitive Resource Labeling:** ![Medium][medium_img] Label and handle sensitive resources distinctly.

### Tools Security

- [ ] **Secure Coding Practices:** ![High][high_img] Adhere to security coding standards and best practices.
- [ ] **Tool Isolation:** ![High][high_img] Execute tools in a controlled environment to prevent system-level impact.
- [ ] **Input Validation:** ![High][high_img] Strictly validate all inputs from clients.
- [ ] **Tool Permission Control:** ![High][high_img] Each tool should have only the minimum permissions needed to complete its task.
- [ ] **Data Validation:** ![High][high_img] Validate the data processed by tools to prevent injection or tampering.
- [ ] **Tool Behavior Constraints:** ![High][high_img] Restrict the range and types of actions a tool can perform.
- [ ] **Third-party Interface Response Security:** ![High][high_img] Verify that the returned information from interfaces is as expected; do not directly insert the returned data into context.
- [ ] **Error Handling:** ![Medium][medium_img] Handle errors securely without exposing sensitive information.
- [ ] **Namespace Isolation:** ![Medium][medium_img] Enforce strict namespace isolation for different tools.

**[⬆ Back to Top](#-table-of-contents)**

## MCP Client/MCP HOST Security

> **Note:** The Host is the environment running the AI application and the MCP client, acting as the terminal where end users interact with AI (e.g., Claude Desktop, Cursor). The Client is a component within the AI application that communicates with the MCP Server, handling context, tool invocation, and result presentation. Typically, the Client is integrated into the Host by default.

### User Interaction Security

- [ ] **User Interface Security:** ![High][high_img] The UI should clearly display the scope of AI operations and potential impacts, offering an intuitive security indicator.
- [ ] **Confirmation of Sensitive Operations:** ![High][high_img] High-risk operations (e.g., file deletion, fund transfers) must explicitly require user confirmation.
- [ ] **Transparency in Permission Requests:** ![High][high_img] Permission requests should explicitly state their purpose and scope so users can make informed decisions and avoid over-authorization.
- [ ] **Operation Visualization:** ![Medium][medium_img] Tool invocation and data access should be visible and auditable by users, accompanied by detailed operation logs.
- [ ] **Information Transparency:** ![Medium][medium_img]  
  Tools should allow users to choose whether to display hidden tags by default, such as `<SECRET>`. This ensures that the context seen by the user is consistent with what is actually generated and invoked, preventing malicious logic from being embedded within hidden tags.
- [ ] **Status Feedback:** ![Medium][medium_img] Users should be able to clearly understand the current MCP operations in progress.

### AI Control & Monitoring

- [ ] **Operation Logging:** ![High][high_img] Record all critical AI operations and their results.
- [ ] **Anomaly Detection:** ![Medium][medium_img] Detect abnormal patterns of tool invocation or request sequences.
- [ ] **Tool Invocation Limitation:** ![Medium][medium_img] Impose frequency and quantity limits on tool calls.

### Local Storage Security

- [ ] **Credential Secure Storage:** ![High][high_img] Use a system keychain or dedicated encrypted storage for sensitive credentials to prevent unauthorized access.
- [ ] **Sensitive Data Isolation:** ![Medium][medium_img] Implement mechanisms to isolate sensitive user data from ordinary data in storage and processing.

### Application Security

- [ ] **Application Integrity:** ![High][high_img] Validate the integrity of the application and MCP plugins to prevent tampering.
- [ ] **Update Verification:** ![Medium][medium_img] Verify the digital signatures of Host application updates.
- [ ] **Application Sandbox:** ![Low][low_img] Whenever possible, run the application in a sandbox environment to limit system access.

### Client Authentication & Authorization

- [ ] **Mandatory Authentication:** ![High][high_img] Enforce authentication before communicating with any critical MCP Server, preventing anonymous access.
- [ ] **OAuth Implementation:** ![Medium][medium_img] Correctly implement OAuth 2.1 or higher, following best practices and security standards.
- [ ] **State Parameter:** ![Medium][medium_img] For certain web clients, use a state parameter to mitigate CSRF attacks, employing a unique random value for each request.

### MCP Tools & Servers Management

- [ ] **MCP Tool Verification:** ![High][high_img] Validate the authenticity and integrity of registered tools.
- [ ] **Secure Updates:** ![High][high_img] The MCP client should regularly check for and apply security updates, and verify whether the updated tools contain any malicious descriptions.
- [ ] **Function Name Checking:** ![High][high_img] Check for name conflicts or malicious overwriting before registering any tool.
- [ ] **Malicious MCP Detection:** ![High][high_img] Monitor and identify potentially malicious MCP behavior.
- [ ] **MCP Tool Naming Control:** ![Medium][medium_img] Use namespaces or unique identifiers to avoid naming collisions.
- [ ] **Server Directory:** ![Medium][medium_img] Maintain an authorized directory of trustworthy MCP Servers and tools.
- [ ] **Conflict Resolution:** ![Medium][medium_img] Establish clear rules to resolve tool name conflicts.
- [ ] **Domain Isolation:** ![Medium][medium_img] Isolate tools in different domains to prevent cross-impact.
- [ ] **Priority Mechanism:** ![Medium][medium_img] Set explicit function priority rules to avoid malicious overwriting.
- [ ] **Version Control:** ![Medium][medium_img] Version functions and tools to detect changes.
- [ ] **Tool Registration & Deregistration Mechanism:** ![Medium][medium_img] Define clear processes for tool registration and deregistration to prevent leftover tools posing security risks.
- [ ] **Conflict Detection Mechanism:** ![Medium][medium_img] Detect and resolve function and resource conflicts in multi-MCP environments.
- [ ] **Tool Classification:** ![Low][low_img] Classify tools according to sensitivity and risk level.

### Prompt Security

- [ ] **Prompt Injection Defense:** ![High][high_img] Implement layered defense measures to prevent prompt injection attacks, including manual verification for critical executions.
- [ ] **Malicious Instruction Detection:** ![High][high_img] Establish mechanisms to detect and block potential malicious user instructions, preventing the system from being manipulated. This includes detecting and blocking preloaded malicious prompts during local initialization, as well as identifying harmful instructions embedded in tools provided by third-party MCP servers.
- [ ] **System Prompt Protection:** ![High][high_img] Clearly separate system prompts from user inputs to prevent tampering.
- [ ] **Sensitive Data Filtering:** ![High][high_img] Filter out sensitive personal data from prompts and context.
- [ ] **Context Isolation:** ![Medium][medium_img] Ensure that contexts from different sources remain isolated to prevent contamination or information leakage.
- [ ] **Prompt Templates:** ![Medium][medium_img] Use secure prompt templates to reduce the risk of injection.
- [ ] **Tool Description Verification:** ![Medium][medium_img] Check tool descriptions for potential malicious instructions.
- [ ] **Prompt Consistency Verification:** ![Medium][medium_img] Ensure that identical prompts produce predictable and consistent results across different environments.
- [ ] **Historical Context Management:** ![Medium][medium_img] Clearly define the mechanism for cleaning up historical context to prevent data buildup and potential information leakage.

### Logging & Auditing

- [ ] **Client Logging:** ![High][high_img] Record all interactions with the MCP Server, tool calls, and authorization activities.
- [ ] **Security Event Recording:** ![High][high_img] Log all security-related events, including authorization failures.
- [ ] **Anomaly Alerts:** ![Medium][medium_img] Detect and alert on abnormal activity patterns.

### Server Verification & Communication Security

- [ ] **Server Identity Verification:** ![High][high_img] Verify the identity of the MCP Server to prevent connections to malicious servers; implement certificate pinning if possible.
- [ ] **Certificate Validation:** ![High][high_img] Strictly validate TLS certificates of remote Servers to prevent Man-in-the-Middle (MitM) attacks and check the integrity of the certificate chain.
- [ ] **Encrypted Communication:** ![High][high_img] Use TLS 1.2+ to encrypt all Client-Server communications; disable weak cipher suites.
- [ ] **Secure Protocol Configuration:** ![Medium][medium_img] Configure secure TLS parameters, regularly review, and update encryption algorithms and protocols.

### Permission Token Storage & Management

- [ ] **Permission Scope Limitation:** ![High][high_img] Strictly limit the scope of tokens under the principle of least privilege.

### Auto-approve Control

- [ ] **Auto-approve Restrictions:** ![High][high_img] Carefully control which tools and operations can be auto-approved.
- [ ] **Whitelist Management:** ![Medium][medium_img] Maintain a whitelist mechanism of tools that can be auto-approved.
- [ ] **Dynamic Risk Assessment:** ![Medium][medium_img] Dynamically adjust auto-approve policies based on context.
- [ ] **Approval Process Auditing:** ![Medium][medium_img] Log and audit all auto-approval decisions.

### Sampling Security

- [ ] **Context Inclusion Control:** ![High][high_img] Strictly control the scope of context included in sampling requests.
- [ ] **Sensitive Data Filtering:** ![High][high_img] Filter out sensitive data from sampling requests and responses.
- [ ] **Sampling Request Validation:** ![Medium][medium_img] Validate all parameters and content within sampling requests.
- [ ] **User Control:** ![Medium][medium_img] Ensure users have clear control over sampling requests and results.
- [ ] **Model Preference Security:** ![Medium][medium_img] Handle model preference information securely to prevent misuse.
- [ ] **Result Validation:** ![Medium][medium_img] Verify that sampling results conform to security standards.

**[⬆ Back to Top](#-table-of-contents)**

## MCP Adaptation and Invocation Security on Different LLMs
> **Note:** In practice, different LLM backends can vary in their invocation priorities and execution logic of MCP. Therefore, we need to not only focus on the MCP implementation but also on how the LLM and MCP work together.

### LLM Secure Execution

- [ ] **Priority Function Execution:** ![High][high_img] Ensure the LLM can correctly prioritize and execute the intended plugin functions.
- [ ] **Malicious Prompt Prevention:** ![High][high_img] The LLM should identify and defend against malicious mnemonic or injection instructions within prompts.
- [ ] **Secure Invocation:** ![High][high_img] The LLM should securely and correctly invoke relevant MCP functionalities.
- [ ] **Sensitive Information Protection:** ![High][high_img] Prevent leakage of sensitive information.

### Multi-modal Security

- [ ] **Multi-modal Content Filtering:** ![High][high_img] Filter out harmful or sensitive information in multi-modal content (e.g., malicious prompt text within images).

**[⬆ Back to Top](#-table-of-contents)**

## Multi-MCP Scenario Security
> **Note:** It is common for users to simultaneously enable multiple MCP Servers in daily use. Given that no official store currently audits MCP plugins, users might install malicious MCPs that seriously compromise overall usage security. This demands heightened caution: introducing a malicious MCP can pose significant security risks.

- [ ] **Multi-MCP Environment Security:** ![High][high_img] Ensure overall security in multi-MCP environments; periodically scan and inspect installed MCPs.
- [ ] **Function Priority Hijacking Prevention:** ![High][high_img] Check for potential malicious prompt presets to prevent hijacking of function priority.
- [ ] **Cross-MCP Function Call Control:** ![High][high_img] Secure control over cross-MCP function calls to prevent malicious MCP servers from returning harmful prompts that could trigger other MCPs to perform sensitive operations.

**[⬆ Back to Top](#-table-of-contents)**

## Unique Security Points for Cryptocurrency-related MCPs
> **Note:** With the increasing number of MCPs for cryptocurrency, many carry high-risk operations like managing crypto wallets. The following suggestions specifically target cryptocurrency-related MCPs.

- [ ] **Private Key Protection:** ![High][high_img] Enhance security measures for private keys (e.g., using Scrypt).
- [ ] **Wallet Generation Security:** ![High][high_img] Ensure the security of mnemonic or wallet generation processes.
- [ ] **Wallet Information Privacy:** ![High][high_img] Protect wallet information privacy; thoroughly filter data before sending wallet information to third-party interfaces.
- [ ] **Transfer Information Confirmation:** ![High][high_img] Ensure the completeness and clarity of all on-chain or exchange transfer signature information.
- [ ] **Funds Operation Verification:** ![High][high_img] Implement secondary verification methods (e.g., Google Authenticator) for critical fund operations.
- [ ] **Local Model Privacy Protection:** ![Medium][medium_img] Use locally hosted LLMs to safeguard privacy data; prevent third-party model providers from accessing wallet information or other sensitive data.
- [ ] **Traditional Wallet Compatibility:** ![Medium][medium_img] Provide secure compatibility with traditional wallets, such as supporting transaction signing through existing wallet solutions.

**[⬆ Back to Top](#-table-of-contents)**

## MCP Security Self-Assessment Tools
> **Note:** For most MCP applications, you can use the following security tools to perform scans and quickly detect high-risk issues automatically.
- [Tencent: AI Infrastructure Security Assessment System](https://github.com/Tencent/AI-Infra-Guard)

**[⬆ Back to Top](#-table-of-contents)**

---

## References
- [Model Context Protocol (MCP) GitHub](https://github.com/modelcontextprotocol/modelcontextprotocol)
- [Hou, X., Zhao, Y., Wang, S., & Wang, H. (2025). Model Context Protocol (MCP): Landscape, Security Threats, and Future Research Directions. *arXiv preprint arXiv:2503.23278*.](https://doi.org/10.48550/arXiv.2503.23278)

[low_img]: assets/priority/low.svg  
[medium_img]: assets/priority/medium.svg  
[high_img]: assets/priority/high.svg