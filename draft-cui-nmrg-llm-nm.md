---
title: "A Framework for LLM Agent-Assisted Network Management with Human-in-the-Loop"
abbrev: "LLM4Net"
category: info

docname: draft-cui-nmrg-llm-nm-latest
submissiontype: IRTF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "IRTF"
workgroup: "Network Management"
keyword:
  - Large Language Model
  - Autonomous Agent
  - Network Management
  - Human in The Loop
venue:
  group: "Network Management"
  type: "Research Group"
  mail: "nmrg@irtf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/nmrg"
  github: "xmzzyo/draft_llm_nm"
  latest: "https://xmzzyo.github.io/draft_llm_nm/draft-cui-nmrg-llm-nm.html"

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
    title: Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks
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


This document describes a reference framework for collaborative network management between Large Language Model (LLM)-assisted agents and human operators. Because network management actions can affect service availability, security posture, customer traffic, and compliance obligations, LLM-generated recommendations need to be validated, reviewed, and audited before they are applied to operational networks. The framework therefore focuses on human-in-the-loop control for safe, auditable, and operator-supervised use of LLM-assisted decision support in network operations. The document is intended to be compatible with existing network management systems and protocols while identifying research issues, rather than specifying a complete implementation of all LLM agent mechanisms.


--- middle

# Introduction

## Motivation
Traditional network automation systems often fail to handle unanticipated scenarios or manage complex, multi-domain data dependencies. Large Language Models (LLMs), when used as agent-assisted components, offer multimodal data comprehension, adaptive reasoning, and broad generalization, making them a candidate technology for network management assistance {{TM-IG1230}}. This document describes a framework for using LLM-assisted agents as decision-support components in network management workflows.

## Why Human-in-the-Loop is Necessary

Human-in-the-loop operation is necessary because network management actions can affect service availability, security posture, customer traffic, and compliance obligations. An LLM-generated recommendation may be syntactically valid but still operationally unsafe if it relies on stale telemetry, misinterprets local policy, affects the wrong service scope, or ignores maintenance windows and business constraints.

In addition, many network-management decisions depend on operational knowledge that may not be fully represented in telemetry or retrieved documents, such as planned maintenance, customer exceptions, escalation procedures, and local risk tolerance. Human review therefore provides the point at which evidence, uncertainty, operational impact, and authorization are assessed before a recommendation is applied to the network. This document treats human-in-the-loop control as a necessary part of the safety and accountability model for LLM agent-assisted network management.


## Problem Statement
Network management presents persistent operational challenges, including multi-vendor configuration complexity, correlation of heterogeneous telemetry data, and timely response to dynamic security threats. LLM agents offer a potential approach to address these challenges through their data comprehension and reasoning capabilities.

However, applying LLM agents in network management raises several research and engineering questions. These include how to provide semantic context for telemetry and configuration state, how to represent confidence and operational risk, how to validate LLM-generated recommendations before execution, and how to preserve auditability through provenance tracking.

This document is intended as input for NMRG discussion on AI in network management. It focuses on research challenges and reference components rather than specifying a new network management protocol, a new LLM interface, or fully autonomous network control. Although the framework shows several supporting components, their purpose is to illustrate the human-supervised decision path from context collection to recommendation, validation, operator audit, and controlled execution.

## Research Questions

This document motivates discussion of the following research questions:

- What semantic context is needed for LLM-assisted systems to reason correctly over network telemetry and configuration state?
- How can confidence, uncertainty, validation results, and operational risk be represented so that operators can make informed decisions?
- How can human review, approval, modification, and rejection be designed as a meaningful control step rather than a procedural confirmation?
- What evidence and decision records are needed to make LLM-assisted recommendations and operator actions auditable and reproducible?



# Terminology

## Acronyms and Abbreviations

- LLM: Large Language Model
- RAG: Retrieval-Augmented Generation
- MCP: Model Context Protocol
- A2A: Agent-to-Agent Protocol
- NACM: NETCONF Access Control Model



# Reference Framework

    +-------------------------------------------------------------+
    |         LLM-Agent Assisted Network Management System        |
    +-------------------------------------------------------------+
    |+---------------LLM Agent Decision Module-------------------+|
    ||                                                           ||
    ||               +----Task Agent Module---+  +-------------+ ||
    ||               | +---------------------+|  | Task Agent  <-----+
    ||               | | Tools/Agent Comms   ||<-> Mgt Module  | ||  |
    ||               | +---------------------+|  +-------------+ ||  |
    ||               | +------+  +----------+ |  |Syntax Verify| ||  |
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

    Figure 1: The LLM agent-Assisted Network Management Framework

Figure 1 illustrates the principal components of the LLM agent-assisted network management framework. The figure is a functional decomposition used to discuss human-supervised decision support, not a complete product architecture. A human operator instantiates a specific task agent (e.g., for fault analysis or topology optimization) via the Task Agent Management Module by specifying a foundation model, a prompt, and optional fine-tuned adapter parameters {{Hu22}}. The Enhanced Telemetry Module enriches raw telemetry data obtained from the underlying network management system and supplies it to the LLM Agent Decision Module. After decision-making, the generated configuration is validated for syntactic correctness, checked against access control rules, and classified for human review. The Operator Audit Module provides a structured mechanism for human review of generated configurations; upon operator approval, configurations are issued to the network management system for deployment.



## Enhanced Telemetry Module

The Enhanced Telemetry Module enriches raw telemetry data with semantic context, providing structured input to the LLM Agent Decision Module.
Telemetry data retrieved from network devices via NETCONF {{RFC6241}} (e.g., in XML format) typically lacks field descriptions, structured metadata, and vendor-specific context. Because this supplementary information is not present in the pre-trained knowledge of general-purpose LLMs, its absence can lead to misinterpretation and erroneous reasoning. To address this, an external knowledge base is used to store YANG model schemas, device manuals, and other relevant documentation.
The Enhanced Telemetry Module operates as middleware between the network management system and the external knowledge base. Through its southbound interface, it retrieves NETCONF data from the NETCONF client of the existing network management system. Through its northbound interface, it queries the external knowledge base for the corresponding YANG model or device documentation.
To improve semantic richness, the module processes retrieved data by simplifying formatted content (e.g., removing redundant or closing XML tags) and appending YANG tree path and field-description information to the relevant data elements. This produces structured, context-enriched input suitable for LLM analysis and reasoning.



## LLM Agent Decision Module

### RAG Module
A pre-trained LLM may lack knowledge of operator-specific configurations, vendor-specific syntax, or domain-specific operational procedures. Retrieval-Augmented Generation (RAG) {{Lewis20}} can retrieve relevant information from operator-defined sources, such as device documentation and operational knowledge bases, and combine it with semantically enriched telemetry.
For human-in-the-loop review, retrieved evidence needs to be traceable. The identifiers, versions, timestamps, and scope of retrieved documents SHOULD be recorded so that the operator and later audit processes can understand which evidence influenced the recommendation.



### Task Agent Module

A task agent is created to support a specific network management task, such as traffic analysis, traffic optimization, or fault remediation. A task agent can include a selected foundation model, an associated prompt, and optionally fine-tuned adapter weights. These are deployment choices rather than the primary focus of this document.

The framework can use libraries for foundation models, prompts, and fine-tuned adapter weights {{Hu22}}. From the human-in-the-loop perspective, the important requirement is that the selected model, prompt template, adapter version, input context, and output format are recorded as part of the decision context. This allows operators to understand the source of a recommendation and allows later audit or reproduction of the decision process.



### Task Agent Communication Module

A task agent may interact with external tools (e.g., Python scripts, network verification tools such as Batfish, or optimization solvers) to acquire additional information or perform specific operations.
Emerging agent protocols such as the Model Context Protocol (MCP) {{mcp}} and Agent-to-Agent Protocol (A2A) {{a2a}} illustrate possible mechanisms for tool invocation and inter-agent coordination. This document does not require a specific agent protocol. A deployment may use any mechanism that provides authenticated tool access, schema validation for tool inputs and outputs, error propagation, and audit correlation between an LLM-assisted recommendation and the external evidence or tool results used to produce it.

In multi-domain or complex scenarios, multiple task agents may collaborate to achieve a shared network management objective. Such collaboration should preserve task context, partial results, constraints, and confidence information so that handoffs remain auditable and operator review remains meaningful.

Agent-to-agent communication can be peer-to-peer for task coordination, while task creation, identity assignment, permission scope, and termination remain governed by the Task Agent Management Module. In this model, the management module provides lifecycle control and audit correlation; it does not need to be a central controller in every inter-agent message exchange.



### Task Agent Management Module
The Task Agent Management Module is responsible for controlled creation, update, and deletion of task agents. In this framework, its main role is to bind a task agent to an operational objective, permission scope, and audit context.

A task agent may be instantiated in response to an operator request, an automated policy trigger, or a higher-level orchestration workflow. Creation includes parsing the high-level intent, selecting an appropriate task template, initializing network context, assigning credentials or permissions, and creating a session identifier for audit correlation.

Task agent updates may be needed when network conditions, model versions, prompts, or operator policies change. Updates SHOULD preserve the audit context and SHOULD record changes that may affect the interpretation of later recommendations.

When a task agent completes, fails, or is explicitly terminated, its final state, generated actions, tool results, and relevant performance information SHOULD be archived in the audit log. This ensures that operator decisions and LLM-assisted recommendations can be traced after the task has ended.

By providing structured, auditable, and policy-governed lifecycle management, the Task Agent Management Module supports operator-supervised LLM-assisted decision support rather than unconstrained autonomous network control.


## Config Verification Module

### Syntax Validation Module

LLM-generated configurations MUST pass YANG schema validation before being queued for human approval. This module ensures that only syntactically correct configurations are presented for operator review, reducing the likelihood of invalid configurations reaching the deployment stage.

### Access Control Module
Syntactic correctness alone does not prevent an LLM from generating configurations that would perform unintended or harmful operations on critical network devices. It is therefore necessary to enforce explicit permission boundaries for LLM task agents.

The NETCONF Access Control Model (NACM) defined in {{RFC8341}} provides a framework for specifying access permissions that can be applied to LLM task agents. NACM defines the concepts of users, groups, access operation types, and action types, which are applied as follows:


- User and Group: Each task agent is registered as a distinct user, representing an entity with defined access permissions for specific devices. A task agent (user) is identified by a unique string within the system. Access control may also be applied at the group level, where a group consists of zero or more members and a task agent may belong to multiple groups.
- Access Operation Types: These define the types of operations permitted, including create, read, update, delete, and execute. Each task agent is assigned a set of permitted operation types based on its role.
- Action Types: These specify whether a given operation is permitted or denied, determining whether an LLM-generated operation request is allowed under the configured access control rules.
- Rule List: Each rule governs access control by specifying the content and operations a task agent is authorized to handle within the system.


This module MUST enforce explicit restrictions on the operations an LLM agent is permitted to perform, ensuring that network configurations remain compliant with operational security policies.



### Feedback Module

LLM-generated configurations may not always satisfy YANG schema constraints, access control rules, or operational requirements. The Feedback Module supplies structured feedback (e.g., in structured text format) and corrective hints to the LLM agent, enabling iterative refinement of generated configurations to meet these constraints.


## Operator Audit Module

The Operator Audit Module provides a structured mechanism for human review of LLM-generated configurations prior to deployment. The output of the LLM Decision Module includes both the generated configuration and an associated confidence score. The configuration is validated against the YANG model and subject to access control enforcement. The confidence score (e.g., on a scale of 0 to 100) provides operators with a quantitative reference for assessing the reliability of the recommendation.

The purpose of this module is not only to collect an approval action. It is intended to give operators enough context to judge whether the recommendation is consistent with the operational objective, whether the supporting data is complete and fresh, whether the affected network scope is acceptable, and whether additional verification or escalation is needed.

The system SHOULD classify recommendations into operator-review tiers before execution. The classification can consider operation type, affected scope, policy sensitivity, validation results, confidence score, freshness of supporting evidence, and rollback difficulty. For example, read-only analysis may require notification and audit recording; low-risk configuration changes may require single-operator approval; high-impact or low-confidence changes may require secondary approval or deferral to an established operational process. Confidence thresholds are deployment-specific and MUST NOT be the sole basis for escalation.

Each audit instance MUST record the input context (e.g., input data, RAG query content, model selection, relevant configuration files) and the corresponding decision output. The audit steps include the following:

- Result Verification: The operator verifies that the LLM-generated output is consistent with operational objectives and policy requirements.
- Compliance Check: The operator confirms that the output adheres to applicable regulatory standards and operational policies.
- Security Verification: The operator checks the output for potential security issues, such as misconfigurations or unintended access changes.
- Correction: If issues are identified, the operator documents the findings and applies corrective modifications.


Upon completion of the audit, the system records an audit decision entry to ensure traceability of operator actions. The audit record includes:

- Timestamp of the audit action
- LLM Task Agent ID
- Operator decision (approve, reject, modify, or defer)
- Review tier and risk classification
- Final executed command
- Operation type (e.g., configuration update, deletion, or execution)

The Operator Audit Module also provides explainability support to improve transparency in LLM-assisted decision-making. Each LLM-generated configuration includes a structured rationale indicating the key factors that influenced the decision. For example, if the system recommends increasing bandwidth allocation, the decision log indicates whether this was driven by high latency observed in telemetry, an SLA threshold breach, or another contributing factor.

The audit process additionally supports counterfactual reasoning, enabling operators to assess the projected outcome if no action is taken. For example, the system may indicate that without intervention, packet loss is expected to increase by a specified percentage within a defined time window. This provides operators with a comparative basis for evaluating proposed actions.

If an LLM agent decision is based on incomplete or uncertain data, the system flags it accordingly. For example, if real-time telemetry data is insufficient, the confidence score is lowered and the condition is noted in the audit record, allowing operators to exercise appropriate judgment.

# Research Challenges and Considerations

This section summarizes research challenges raised by the reference framework. The intent is to identify issues for further NMRG discussion rather than to prescribe a single implementation.

## Semantic Context and Provenance

Human review depends on context that is both machine-processable and meaningful to operators. Telemetry values, topology information, configuration state, and retrieved documents need scope, timestamp, source, collection method, and provenance metadata so that operators can judge whether the recommendation is based on relevant and current evidence.

## Uncertainty and Risk Representation

Confidence scores alone are insufficient for network-management decisions. Research is needed on how to present model uncertainty, input-data uncertainty, validation status, and operational impact as operator-facing risk information.

## Human Review Workflow

Human review needs to be designed so that operators can make informed decisions under time pressure. Open questions include what evidence to present, when to require secondary approval, how to avoid automation bias, and how to support operator requests for more evidence.

## Accountability and Auditability

LLM-assisted decisions need audit records that connect input evidence, model output, tool results, validation outcomes, operator decisions, and final actions. Such records are needed for incident analysis, rollback, compliance, and research reproducibility, while still protecting sensitive operational data and credentials.

# Use Cases

## DDoS Intelligent Defense

Distributed Denial of Service (DDoS) attacks represent a persistent operational threat. Conventional mitigation systems based on rate-limiting and signature matching may not adapt rapidly enough to generate fine-grained filtering rules in response to multi-dimensional telemetry patterns.

This use case illustrates how the LLM agent-assisted framework supports filtering recommendation generation with human oversight. The Enhanced Telemetry Module retrieves and enriches traffic statistics, and the LLM-assisted agent generates a filtering recommendation with a rationale and evidence references.

The recommendation is passed through syntax validation and access-control enforcement before being shown to the operator. Human review is needed because the operator may need to judge collateral impact, customer exceptions, threat-intelligence context, and escalation policy. The final operator decision and any executed command are recorded in the audit log.

## Traffic Scheduling and Optimization

In large-scale networks, dynamic traffic scheduling is required to respond to fluctuating load, maintain QoS, and satisfy SLA requirements. Static or rule-based methods may not provide sufficient responsiveness or cross-domain visibility.

This use case illustrates how the framework supports LLM agent-assisted traffic scheduling with operator control. The Enhanced Telemetry Module collects and enriches link utilization, queue occupancy, delay metrics, topology information, and service-class constraints.

The LLM-assisted agent proposes a traffic-engineering adjustment, such as rerouting selected traffic classes through underutilized paths or adjusting policy parameters. The recommendation is validated before operator review. The operator then reviews the recommendation, validation result, expected path shift, policy constraints, and possible impact on other traffic classes before approving, modifying, rejecting, or deferring the action. The final operator decision and any revised configuration are stored in the audit log.


# Security Considerations {#Security}

This section summarizes the main security issues introduced by LLM-assisted network management. The analysis assumes that LLM-assisted agents can access telemetry, retrieve contextual knowledge, invoke external tools, and generate recommendations or candidate configuration changes subject to human oversight.

Security-sensitive assets include network configuration state, telemetry data, external knowledge bases, prompts and system instructions, fine-tuned weights, agent credentials, and human audit records. Compromise of these assets may lead to service disruption, policy violations, data leakage, or unauthorized configuration changes.

The framework introduces trust boundaries between the LLM agent and the network management system, between the agent and external toolchains, between cooperating task agents, between retrieval databases and the LLM context, and between automated decision modules and human operators. Each boundary needs explicit protection through authentication, authorization, input validation, integrity protection, and audit logging.

Prompt injection and RAG knowledge poisoning are important risks because they can influence operator-facing recommendations. Malicious telemetry fields, compromised documentation, poisoned retrieval databases, or cross-agent messages may affect model output. Mitigations include context separation, structured role tagging, input sanitization, integrity verification of retrieval documents, versioning of knowledge sources, logging of retrieved document identifiers, and deterministic validation of generated configurations.

Agent identity and tool access also need protection. Each task agent should be bound to a distinct identity and to explicit permissions, for example through NACM-based access control. Tool invocation should use authenticated access, schema validation for tool inputs and outputs, sandboxing where appropriate, and human confirmation for high-risk tool invocations.

The MCP/A2A tool and inter-agent surface requires an explicit threat model. Threats include tool spoofing, malicious or compromised MCP servers, forged inter-agent messages, replayed tool responses, confused-deputy behavior, and unauthorized agent impersonation. Mitigations include mutual authentication, signed or otherwise integrity-protected messages, least-privilege tool registration, allow-listed tool schemas, freshness checks, replay protection, and audit correlation across tool calls and agent-to-agent messages.

The LLM-assisted decision layer can itself become a denial-of-service target. Excessive task instantiation requests, high-frequency telemetry triggers, or multi-agent loops may cause resource exhaustion or delayed incident response. Mitigations include rate limiting, admission control, quotas, maximum reasoning-depth or token limits, and circuit breakers in the Task Agent Management Module.

LLMs may generate syntactically correct but semantically invalid configurations, such as referencing non-existent interfaces, misinterpreting vendor-specific syntax, or using incorrect parameter units. Mitigations include YANG schema validation, deterministic configuration simulation, access-control checks, confidence-based escalation thresholds, explicit reasoning logs, and operator review. Human approval MUST remain the final authority for high-impact changes.

The confidence score itself is not a trusted security signal. It may be affected by hallucination, prompt injection, incomplete context, poor calibration, or implementation errors in the scoring method. Deployments SHOULD treat confidence as one input to risk classification, validate it against deterministic checks and evidence quality, and record how it was produced.

To support structured oversight, each generated configuration SHOULD be assigned a risk level derived from factors such as scope of impact, operation type, policy sensitivity, confidence score, and historical rollback frequency. Risk classification MUST be included in the audit record.


# IANA Considerations {#IANA}

This document includes no request to IANA.

--- back

# Acknowledgments
{:numbered="false"}

We thank Shailesh Prabhu from Nokia and Jaime Jiménez for their contributions to this document.

# Appendix
{:numbered="false"}

## Appendix A.1 Data Model
{:numbered="false"}

This section defines illustrative data models for LLM agent-assisted network management, including the LLM agent decision response and human audit records. These models are examples for discussion and are not intended for IETF YANG module registration in their current form.

### LLM Response Data Model
{:numbered="false"}
The LLM Agent Decision Module returns generated configuration parameters and an associated confidence score. If the LLM cannot produce a valid configuration, it returns an error reason.


~~~ yang
module: llm-response-module
  +--rw llm-response
     +--rw config?         string
     +--rw confidence?     uint8
     +--rw error-reason?   enumeration
~~~

The LLM response YANG model is structured as follows:

~~~ yang
module llm-response-module {
  namespace "https://example.com/ns/yang/llm-response-module";
  prefix llmresponse;
  container llm-response {
    leaf config {
      type string;
    }
    leaf confidence {
      type uint8;
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
This data model defines the structure for human audit operations and records. It supports collaborative decision-making by recording LLM-generated actions alongside the operator's final decision.


~~~ yang
module: human-audit-module
  +--rw human-audit
     +--rw task-id?      string
     +--rw generated-config?   string
     +--rw confidence?         uint8
     +--rw human-actions
        +--rw operator?          string
        +--rw action?            enumeration
        +--rw modified-config?   string
        +--rw timestamp?         yang:date-and-time
~~~

The human audit YANG model is structured as follows:

~~~ yang
module human-audit-module {
  namespace "https://example.com/ns/yang/human-audit-module";
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
      type uint8;
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
          enum defer;
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
