# Ent-DevOps-Standards

This repo is part of custom 'Enterprise DevOps MCP server with RAG' project that injects company tech standard(s) into agent-generated Infrastructure code (e.g. Terraform).
Central set of tech standards for Enterprise in .md format that could be used as AI context or RAG DB inputs

## Issue description

Company has many DevOps teams (>5) that maintain number of AWS accounts via IaC (Terraform), hosting various projects/applicaitons/environments. Unfortunately there is no single standard folllowed for resource naming, ownership or resource tagging which means FinOps/Cost analysis, Security Incident escalation or just centralized monitoring/support is problematic. 

## Proposed solution

Take existing DevOps/Tech standard documents (they are stored in GitHub repo as separate .md files - cicd.md, iac-terraform.md, aws-infra.md, security.md, observability.md ) and cover mandatory CI/CD, IaC, Security, Observability conventions and convert them into private MCP server with RAG vector DB that injects requirements into terraform code that is  either generated or refactored by  AI agents (e.g. ClaudeCode) by using reference LangChain/Quadrant integration architecture

## Standards included

- IaC/Terraform naming and tagging convention
- CI/CD pipeline mandatory checks
- Observability standard
- Others (tba)
