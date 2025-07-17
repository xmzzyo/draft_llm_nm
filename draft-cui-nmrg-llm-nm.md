---
###
# Internet-Draft Markdown Template
#
# Rename this file from draft-todo-yourname-protocol.md to get started.
# Draft name format is "draft-<yourname>-<workgroup>-<name>.md".
#
# For initial setup, you only need to edit the first block of fields.
# Only "title" needs to be changed; delete "abbrev" if your title is short.
# Any other content can be edited, but be careful not to introduce errors.
# Some fields will be set automatically during setup if they are unchanged.
#
# Don't include "-00" or "-latest" in the filename.
# Labels in the form draft-<yourname>-<workgroup>-<name>-latest are used by
# the tools to refer to the current version; see "docname" for example.
#
# This template uses kramdown-rfc: https://github.com/cabo/kramdown-rfc
# You can replace the entire file if you prefer a different format.
# Change the file extension to match the format (.xml for XML, etc...)
#
###
title: "A Framework for LLM-Assisted Network Management with Human-in-the-Loop"
abbrev: "LLM4Net"
category: info

docname: draft-cui-nmrg-llm-nm-00
submissiontype: IRTF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: IRTF
workgroup: Network Management Research Group
keyword:
  - Large Language Model
  - Network Management
  - Human in The Loop
venue:
  group: WG
  type: Working Group
  mail: WG@example.com
  arch: https://example.com/WG
  github: USER/REPO
  latest: https://example.com/LATEST

author:
- role:  # remove if not true
  ins: Y. Cui
  name: Yong Cui
  org: Tsinghua University
  street:
  city:
  region: Beijing # not always available
  code: 100084
  country: China # use TLD (except UK) or country name
  phone:
  email: cuiyong@tsinghua.edu.cn
  uri: http://www.cuiyong.net/
- role: # remove if not true
  ins: M. Xing
  name: Mingzhe Xing
  org: Zhongguancun Laboratory
  street:
  city:
  region: Beijing # not always available
  code: 100094
  country: China # use TLD (except UK) or country name
  phone:
  email: xingmz@zgclab.edu.cn
- role: # remove if not true
  ins: L. Zhang
  name: Lei Zhang
  org: Zhongguancun Laboratory
  street:
  city:
  region: Beijing # not always available
  code: 100094
  country: China # use TLD (except UK) or country name
  phone:
  email: zhanglei@zgclab.edu.cn

normative:
  RFC8341:
  RFC6241:
informative:
  TM-IG1230:
    title: Autonomous Networks Technical Architecture
    author:
    - name: Kevin McDonnell
    - name: Azahar Machwe
    - name: Dave Milham
    - name: James O’Sullivan
    - name: Jörg Niemöller
    - name: Luca Franco Varvello
    - name: Vinay Devadatta
    - name: Wang Lei
    - name: Wang Xu
    - name: Xie Yuan
    - name: Yuval Stein
    date: 2023-02
  Huang25:
    title: A Survey on Hallucination in Large Language Models Principles, Taxonomy, Challenges, and Open Questions
    author:
    - name: Lei Huang,
    - name: Weijiang Yu
    - name: Weitao Ma
    - name: Weihong Zhong
    - name: Zhangyin Feng
    - name: Haotian Wang
    - name: Qianglong Chen
    - name: Weihua Peng
    - name: Xiaocheng Feng
    - name: Bing Qin
    - name: Ting Liu
  Hu22:
    title: LoRA Low-Rank Adaptation of Large Language Models
    author:
    - name: Edward J Hu
    - name: Yelong Shen
    - name: Phillip Wallis
    - name: Zeyuan Allen-Zhu
    - name: Yuanzhi Li
    - name: Shean Wang
    - name: Lu Wang
    - name: Weizhu Chen
  Lewis20:
    title: Retrieval-augmented generation for knowledge-intensive NLP tasks
    author:
    - name: Patrick Lewis
    - name: Ethan Perez
    - name: Aleksandra Piktus
    - name: Fabio Petroni
    - name: Vladimir Karpukhin
    - name: Naman Goyal
    - name: Heinrich Küttler
    - name: Mike Lewis
    - name: Wen-tau Yih
    - name: Tim Rocktäschel
    - name: Sebastian Riede
  mcp:
   title: Model Context Protocol
   target: https://modelcontextprotocol.io/introduction
   date: 2025-07
  a2a:
   title: Announcing the Agent2Agent Protocol (A2A)
   target: https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/
   date: 2025-07


--- abstract


This document defines an interoperable framework that facilitates collaborative network management between Large Language Models (LLMs) and human operators. The proposed framework introduces an enhanced telemetry module, an LLM decision module, and standardized interaction data models between human operators and LLM-driven systems, and workflows to enforce human oversight. The approach ensures compatibility with existing network management systems and protocols while improving automation and decision-making capabilities in network operations.


--- middle

# Introduction

## Motivation
Traditional network automation systems often face challenges in handling unanticipated scenarios and managing complex multi-domain data dependencies. Large Language Models (LLMs) offer advanced multimodal data comprehension, adaptive reasoning, and generalization capabilities, making them a promising tool for network management and autonomous network{{TM-IG1230}}. However, full automation remains impractical due to risks such as model hallucination, operational errors, and a lack of accountability in decision-making{{Huang25}}. This document proposes a structured framework that integrates LLMs into network management through human-in-the-loop collaboration, leveraging their strengths while ensuring oversight, reliability, and operational safety.


## Problem Statement
Network management faces significant challenges, including the complexity of multi-vendor configurations, the real-time correlation of heterogeneous telemetry data, and the need for rapid responses to dynamic security threats.
LLMs offer a promising approach to addressing these challenges through their advanced multimodal data understanding and adaptive reasoning capabilities.
However, the direct application of LLMs in network management introduces several technical considerations.
These include the need for semantic enrichment of network telemetry to enhance LLM comprehension, a dual-channel decision execution mechanism with confidence-based escalation, and auditability of LLM-generated decisions through provenance tracking.
Addressing these requirements is critical to integrating LLMs effectively into network management workflows while maintaining reliability, transparency, and interoperability.


# Terminology

## Acronyms and Abbreviations

- LLM: Large Language Model
- RAG: Retrieve Augmented Generation
- MCP: Model Context Protocol
- A2A: Agent-to-Agent Protocol



# Framework Overview

    +-------------------------------------------------------------+
    |             LLM-Assisted Network Management System          |
    +-------------------------------------------------------------+
    |+--------------------LLM Decision Module--------------------+|
    ||                                                           ||
    ||               +----Task Agent Module---+  +-------------+ ||
    ||               | +---------------------+|  | Task Agent  <-----+
    ||               | |      Tool Lib       ||<-> Mgt Module  | ||  |
    ||               | +---------------------+|  +-------------+ ||  |
    ||               | +------+  +----------+ |  |Config Verify| ||  |
    ||               | |Prompt|  |Fine-Tuned| <->|     Module  | ||  |
    ||               | | Lib  |  |Weight Lib| |  +-------------+ ||  |
    || +----------+  | +------+  +----------+ |  +--------------+||  |
    || |RAG Module|<-> +--------------------+ |  |Access Control|||  |
    || +-----^----+  | |Foundation Model Lib| -->|    Module    |||  |
    ||       |       | +--------------------+ |  +-------|------+||  |
    ||       |       +----^---------------^---+          |       ||  |
    |+-------|------------|---------------|--------------|-------+|  |
    |+-------v------------v----+ +--------v--------------v-------+|  |
    ||Enhanced Telemetry Module| |   Operator Audit Module       ||  |
    |+-----------^-------------+ +--------------|-------------^--+|  |
    +------------|------------------------------|-------------|---+  |
                 |                              |       +-----v---+  |
                 |                              |       |Operator <--+
                 |                              |       +---------+
    +------------v------------------------------v------------------+
    |               Original Network Management System             |
    +------------------------------^-------------------------------+
                                   |
    +------------------------------v-------------------------------+
    |                       Physical Network                       |
    +--------------------------------------------------------------+

    Figure 1: The LLM-Assisted Network Management Framework

The proposed framework is shown in Figure 1, highlighting the key components of LLM-assisted network management.
The human operator can generate a specific task agent, e.g., fault analysis or topology optimization, using the task agent management module.
According to the task type, the task agent module can instantiate a task agent with specific foundation model, prompt, and adaptation fine-tuned parameters{{Hu22}}.
The enahnced telemetry module improves the semantics of raw telemetry data from original network management system, providing supplementary information to the LLM decision module for more informed decision-making.
After the decision-making, the generated configuration parameters are validated against the YANG model and enforced with access control rules.
The operator audit module provides a structured mechanism for human oversight of LLM-generated configurations, and the configuration can be issued to the original network management system for deployment once the operator approves.


## Enhanced Telemetry Module
The Enhanced Telemetry Module improves the semantics of raw telemetry data, providing supplementary information to the LLM decision module for more informed decision-making.
Telemetry data retrieved from network devices via NETCONF{{RFC6241}}, e.g., in XML format, often lacks field descriptions, structured metadata, and vendor-specific details. Since this information is not included in the pre-trained knowledge of LLMs, it can lead to misinterpretation and erroneous reasoning.
To address this limitation, an external knowledge base should be introduced to store YANG model schema, device manuals, and other relevant documentation.
The Enhanced Telemetry Module functions as middleware between the network management system and the external knowledge base. Through its southbound interface, it retrieves NETCONF data from the NETCONF client of existing network management system. Through its northbound interface, the module queries the external knowledge base for the corresponding YANG model or device manual.
To enhance semantic richness, the Enhanced Telemetry Module processes the retrieved data by simplifying formatted content (e.g., removing redundant or closing XML tags) and appending XML path and description information from the YANG tree to the relevant fields. This approach ensures that the LLM has access to structured, contextually enriched data, improving its ability to analyze and reason about network telemetry.

## LLM Decision Module

### RAG Module
The pre-trained LLM may not encompass specific task requirements or vendor-specific knowledge. To address this kind of limitation, the Retrieve-Augmented Generation (RAG){{Lewis20}} approach is widely used. This module retrieves relevant information from operator-defined sources, such as device documentation and expert knowledge, and integrates it with the Enhanced Telemetry Module to obtain YANG model schema.
The retrieved textual data is stored in a database, either as raw text or in a vectorized format for efficient search and retrieval. For a given task context, the module retrieves relevant knowledge from the database and incorporates it into the input context, improving the understanding and response accuracy of LLM.


### Task Agent Module

To execute a specific task, such as traffic analysis, traffic optimization, or fault remediation, a corresponding task agent must be created. A task agent consists of a selected LLM foundation model, an associated prompt, and fine-tuned weights.

- Foundation Model Library. Operators must select an appropriate foundation model based on the specific task requirements. Examples include general-purpose models such as GPT-4, LLaMA, and DeepSeek, as well as domain-specific models fine-tuned on private datasets. Since foundation models are trained on different datasets using varying methodologies, their performance may differ across tasks.
- Fine-Tuned Weight Library. For domain-specific applications, fine-tuned weights can be applied on top of a foundation model to efficiently adapt it to private datasets. One commonly used approach is to store the fine-tuned weights as the difference between the original foundation model and the adapted model, which can largely reduce storage requirements.
The Fine-Tuned Weights Module supports the selection and loading of an appropriate foundation model along with the corresponding fine-tuned weights, based on the selection of operators. This ensures flexibility in leveraging both general-purpose and domain-specific knowledge while maintaining computational efficiency.
- Prompt Library. For each task, it is essential to accurately define the task description, the format of its inputs and outputs. These definitions are stored in a structured prompt library. When an operator instantiates a task, the corresponding prompt, including placeholders for contextual information, is automatically retrieved. operator inputs and device data are then incorporated into the prompt at the designated placeholders, ensuring a structured and consistent interaction with the language model.
- Tool Library. Operators can specify the tools used for the task agent, such as a Python script or an API call. These tools are stored in a tool library, and can be interacted with the agent via Model Context Prtocol (MCP){{mcp}}.


### Task Agent Management Module
The Task Agent Management Module is responsible for the creation, update, and deletion of task agents. This module ensures that each agent is appropriately configured to align with the intended network management objective.
Note that there can be multiple agents colloboratively to achieve the same objective.
In this scenario, the Task Agent Management Module can be used to coordinate the task agents, ensuring they are executed in a synchronized and efficient manner.
The agents can communicate with each other with the Agent-to-Agent Protocol (A2A){{a2a}}.

### Config Verify Module
To ensure correctness and policy compliance, LLM-generated configurations MUST pass the YANG schema validation steps before being queued for human approval.
This module ensures that only syntactically correct configurations are presented for operator review, thereby reducing errors and enhancing network reliability.

### Access Control Module
Although the Configuration Verify Module can guarantee the syntactic correction, LLMs may generate unintended or potentially harmful operations on critical network devices, it is essential for operators to enforce clear permission boundaries for LLM task agent to ensure security and operational integrity.
The Network Configuration Access Control Model defined in {{RFC8341}} provides a framework for specifying access permissions, which can be used to grant access to LLM task agents. This data model includes the concepts of users, groups, access operation types, and action types, which can be applied as follows:

- User and Group: Each task agent should be registered as a specific user, representing an entity with defined access permissions for particular devices. These permissions control the types of operations the LLM is authorized to perform. A task agent (i.e., user) is identified by a unique string within the system. Access control can also be applied at the group level, where a group consists of zero or more members, and a task agent can belong to multiple groups.
- Access Operation Types: These define the types of operations permitted, including create, read, update, delete, and execute. Each task agent may support different sets of operations depending on its assigned permissions.
- Action Types: These specify whether a given operation is permitted or denied. This mechanism determines whether an LLM request to perform an operation is allowed based on predefined access control rules.
- Rule List: A rule governs access control by specifying the content and operations a task agent is authorized to handle within the system.

This module must enforce explicit restrictions on the actions an LLM is permitted to perform, ensuring that network configurations remain secure and compliant with operational policies.

## Operator Audit Module

The Operator Audit Module provides a structured mechanism for human oversight of LLM-generated configurations before deployment.
The output from the LLM Decision Module should include both the generated configuration parameters and an associated confidence score.
The configuration parameters are validated for compliance with the YANG model and are subject to Access Control Rules enforcement.
The confidence score, e.g., ranging from 0 to 100, serves as a reference for operators to assess the reliability of the generated configuration.
Each audit process must track the input context (e.g., input data, RAG query content, model selection, configuration files) and the corresponding output results. The auditing steps are as follows:

- Result Verification: The operator verifies the LLM-generated output to ensure alignment with business objectives and policy requirements.
- Compliance Check: The operator ensures that the LLM output adheres to regulatory standards and legal requirements.
- Security Verification: The operator examines the output for potential security risks, such as misconfigurations or vulnerabilities.
- Suggestions and Corrections: If issues are identified, the operator documents the findings and proposes corrective actions.

Upon completing the audit, the system maintains an audit decision record to ensure traceability of operator actions. The audit record includes the following information:

- Timestamp of the audit action
- LLM Task Agent ID associated with the action
- Operator decisions, including approval, rejection, modification, or pending status
- Executed command reflecting the final action taken
- Operation type (e.g., configuration update, deletion, or execution)

This structured approach ensures that all LLM-generated configurations undergo rigorous human review, maintaining operational accountability, and security.

The Operator Audit Module is enhanced with Explainability functionality to improve transparency and trust in LLM-assisted decision-making. This enhancement ensures that operators can understand the reasoning behind LLM-generated configurations, verify their reliability, and take corrective actions when necessary.

To achieve Explainability, the module provides a structured breakdown of the decision-making process by capturing key influencing factors, justification logs, and confidence scores. Each LLM-generated configuration must include a detailed rationale explaining why a particular decision was made. For example, if the system recommends increasing bandwidth allocation, the decision log should indicate whether this was due to high latency detected in telemetry, SLA thresholds being exceeded, or other contributing factors.

Additionally, the audit process incorporates counterfactual analysis, allowing operators to assess alternative outcomes. For instance, the system may indicate that if no action is taken, packet loss is expected to increase by 20% in the next ten minutes. This capability enhances operational decision-making by providing a comparative assessment of different actions.

To further ensure reliability, the module includes a mechanism to detect potential biases or hallucinations in LLM-generated outputs. If an LLM decision is based on incomplete or uncertain data, the system flags it accordingly, allowing operators to make informed judgments. For example, if real-time telemetry data is insufficient, the system may indicate that the confidence in a particular recommendation is low.

By integrating these Explainability features, the Operator Audit Module strengthens human oversight, ensuring that LLM-generated configurations align with business policies, security requirements, and regulatory standards. This structured approach enhances accountability and mitigates risks associated with automated decision-making.

# Use Cases

## DDoS Intelligent Defense

Distributed Denial of Service (DDoS) attacks remain a critical operational threat. While conventional systems use rate-limiting and signature matching, they often fail to adapt in real-time or generate fine-grained filtering rules based on multi-dimensional telemetry data.

This use case demonstrates how the LLM-assisted framework enables intelligent filtering rule generation and secure deployment with human oversight.

1. **Telemetry Collection and Semantic Enrichment**
   The Enhanced Telemetry Module retrieves real-time traffic statistics and interface metrics from network devices using NETCONF. These raw telemetry data are semantically enriched by referencing YANG models and device-specific documentation to generate a context-rich dataset suitable for LLM processing.

2. **DDoS Filtering Task Instantiation**
   The operator initiates a `ddos-mitigation` task. The Task Agent Module selects a security-specialized foundation model and task-specific prompt.

   It first analyzes the following conclusions:
   - Interface `GigabitEthernet0/1` receiving sustained traffic > 100,000 pps
   - 95% of incoming packets are TCP SYN
   - Top source prefixes identified as: `IP1/24`, `IP2/24`

   The RAG Module supplements the analysis with:
   - Cisco ACL syntax documentation
   - Prior incident response templates


3. **LLM-Generated Firewall Configuration Output**
   The LLM reasons that a TCP SYN flood is underway and generates the following ACL-based filtering policy:

~~~ shell
ip access-list extended BLOCK-DDOS
deny tcp IP1 0.0.0.255 any
deny tcp IP2 0.0.0.255 any
permit ip any any

interface GigabitEthernet0/1
ip access-group BLOCK-DDOS in
~~~

This configuration includes both source-based filtering and a global SYN rate-limiter. It is passed to the Config Verify Module for syntax validation and to the Access Control Module for permission checks.

4. **Operator Audit and Decision**
The Operator Audit Module provides the following metadata:

- **Task Agent ID**: `ddos-mitigation-task-01`
- **Confidence Score**: 85/100
- **RAG Context**: `Cisco IOS ACL Syntax`, `Internal Threat List v5`
- **Input Summary**:
  - Affected Interface: `GigabitEthernet0/1`
  - Malicious Sources: `IP1/24`, `IP2/24`

The human operator performs the audit with the following steps:

- **Result Verification**: ACL syntax matches Cisco IOS 15.6 standards.
- **Compliance Check**: Confirmed that both prefixes appear in the enterprise blacklist.
- **Security Review**: Low false-positive risk for blanket deny of SYN traffic from identified prefixes.

- **Audit Record**:
  - Timestamp: `2025-07-16T17:03:00Z`
  - Decision: *Approved*
  - Action: `Apply via NMS interface`
  - Operator Note: “Confirmed match with active threat list. Monitor for collateral traffic drops.”

The configuration is deployed through the network management system, completing the defense workflow with human oversight and traceability.

This simplified case shows how the LLM can assist in quickly generating actionable filtering rules in response to DDoS patterns, while adhering to operational safeguards.

## Traffic Scheduling and Optimization

In large-scale networks, dynamic traffic scheduling is required to adapt to fluctuating loads, ensure QoS, and meet SLA requirements. Traditional methods may lack responsiveness or cross-domain visibility.

The proposed framework enables intelligent, context-aware traffic scheduling through LLM-based reasoning. The following illustrates the process:

1. **Telemetry Data Acquisition**
The Enhanced Telemetry Module gathers link utilization, queue occupancy, and delay metrics from multiple routers. The semantic enrichment process tags each metric with human-readable labels from the YANG model, including path topology and policy tags (e.g., gold/silver/bronze service classes).

2. **Optimization Task Execution**
An operator initiates a traffic scheduling task. The Task Agent Module selects a foundation model fine-tuned on traffic engineering datasets and uses a structured prompt to describe current constraints: high utilization on core links `L1-L3`, SLA violations for gold-class VoIP traffic.

3. **Sample Configuration Output**
The LLM suggests adjusting RSVP-TE path metrics to reroute gold traffic via underutilized backup paths:

~~~ shell
policy-options {
    policy-statement reroute-gold {
        term gold-traffic {
            from {
                community gold-voip;
            }
            then {
                metric 10;
                next-hop [IP];
            }
        }
    }
}
protocols {
    rsvp {
        interface ge-0/0/0 {
            bandwidth 500m;
        }
    }
}
~~~

This configuration is syntax-validated and checked against the Access Control Module to ensure that traffic engineering policy updates are permitted for this task agent.

4. **Operator Audit and Decision**
The Operator Audit Module presents:

- **Confidence Score**: 91/100
- **Audit Metadata**:
  - Task Agent: `traffic-opt-te-v2`
  - Input Context: “Link L1 (95% utilization), gold-voip SLA latency breach”
  - Recommendation: “Increase TE path metric to reroute VoIP via L2 backup path”

The operator reviews:

- **Result Verification**: Simulates expected path shift via NMS.
- **Compliance Check**: Confirms SLA routing rules permit TE adjustment.
- **Security Review**: Confirms backup link L2 is secure and isolated.
- **Final Action**: *Approved with modification*: “Set bandwidth cap to 400m on backup to avoid overuse.”

The revised configuration is stored and forwarded to the network management system for application.

This use case illustrates how the framework enables LLMs to propose adaptive, policy-compliant traffic engineering strategies while maintaining operator control, traceability, and auditability.


# IANA Considerations {#IANA}

This document includes no request to IANA.

# Security Considerations {#Security}

- Model Hallucination: A key challenge is that, without proper constraints, the LLM may produce malformed or invalid configurations. This issue can be mitigated using techniques such as Constrained Decoding, which enforces syntactic correctness by modeling the configuration syntax and restricting the output to conform to predefined rules during the generation process.

- Training Data Poisoning: LLMs can be trained on malicious or biased data, potentially leading to unintended behavior or security vulnerabilities. To mitigate this risk, LLMs should be trained on curated, high-quality datasets with rigorous validation and filtering processes. Periodic retraining and adversarial testing should also be conducted to detect and correct anomalies before deployment.


--- back

# Acknowledgments
{:numbered="false"}

We thanks Shailesh Prabhu from Nokia for his contributions to this document.

# Appendix
{:numbered="false"}

## Appendix A.1 Data Model
{:numbered="false"}

This section defines the essential data models for LLM-assisted network management, including the LLM decision response and human audit records.

### LLM Response Data Model
{:numbered="false"}
The LLM decision module should respond with the generated configuration parameters along with an associated confidence score. If the LLM is unable to generate a valid configuration, it should return an error message accompanied by an explanation of the issue.

~~~ yang
module: llm-response-module
  +--rw llm-response
     +--rw config?         string
     +--rw confidence?     uint64
     +--rw error-reason?   enumeration
~~~

The LLM response YANG model is structured as follows:

~~~ yang
module llm-response-module {
  namespace "urn:ietf:params:xml:ns:yang:ietf-nmrg-llmn4et";
  prefix llmresponse;
  container llm-response {
    leaf config {
      type string;
    }
    leaf confidence {
      type uint64;
    }
    leaf error-reason {
      type enumeration {
        enum unsupported-task;
        enum unsupported-vendor;
      }
    }
  }
}
~~~

### Human Audit Data Model
{:numbered="false"}
This data model defines the structure for human audit operations and record-keeping. It facilitates collaborative decision-making by allowing LLMs to generate actionable insights while ensuring that human operators retain final operational authority.

~~~ yang
module: human-audit-module
  +--rw human-audit
     +--rw task-id?      string
     +--rw generated-config?   string
     +--rw confidence?         int64
     +--rw human-actions
        +--rw operator?          string
        +--rw action?            enumeration
        +--rw modified-config?   string
        +--rw timestamp?         yang:date-and-time
~~~

The human audit YANG model is structured as follows:

~~~ yang
module human-audit-module {
  namespace "urn:ietf:params:xml:ns:yang:ietf-nmrg-llmn4et";
  prefix llmaudit;
  import ietf-yang-types { prefix yang; }

  container human-audit {
    leaf task-id {
      type string;
      }
    leaf generated-config {
      type string;
      }
    leaf confidence {
      type int64;
      }
    container human-actions {
      leaf operator {
        type string;
        }
      leaf action {
        type enumeration {
          enum approve;
          enum modify;
          enum reject;
          }
        }
      leaf modified-config {
        type string;
        }
      leaf timestamp {
        type yang:date-and-time;
        }
    }
  }
}
~~~