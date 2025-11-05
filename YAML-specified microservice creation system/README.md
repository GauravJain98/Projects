# YAML-Specified Microservice Creation System

## Overview

This project provides an automated Infrastructure as Code (IaC) solution for creating and managing microservices at scale. It transforms the complex, error-prone process of manually updating multiple Terraform workspaces into a simple, declarative YAML-based approach that dramatically reduces deployment time and human error.

## Problem Statement

Managing microservices infrastructure at scale presents significant challenges:

### Original Pain Points

- **Manual Configuration**: Each new microservice required updating ~4 separate Terraform workspaces
- **Error-Prone Process**: Manual updates across multiple files led to configuration drift and mistakes
- **Scalability Issues**: As services grew to 30+, the main `config.tf` file exceeded 300 lines
- **Time-Intensive**: New microservice deployment took several hours of manual work
- **Maintenance Overhead**: Updating existing services required changes across multiple locations

### Scale Challenge

With 30+ microservices, the centralized configuration became:

- Difficult to navigate and maintain
- Prone to merge conflicts in team environments
- Hard to track changes for specific services
- Challenging for new team members to understand

## Solution Architecture

This system implements a modular, file-based approach that separates concerns and enables scalable microservice management.

### Core Components

#### 1. Modular Terraform Structure

- **Environment-Specific Module**: Handles resources like image registries, S3 buckets, databases
- **Global Resources Module**: Manages DNS, GitHub repositories, shared infrastructure

#### 2. YAML-Based Configuration

Individual YAML files for each microservice containing:

- Service-specific configuration
- Resource requirements
- Environment variables
- Deployment settings

#### 3. Dynamic Configuration Loading

```hcl
applications = {
  for file in fileset("../applications", "*.yaml") :
  trimsuffix(file, ".yaml") => yamldecode(file("../applications/${file}"))
}
```

This Terraform expression:

- Automatically discovers all YAML files in the `applications/` directory
- Creates a map with service names as keys
- Loads and parses YAML configuration for each service
- Enables dynamic service registration without code changes

## Benefits

### Developer Experience

- **Simple Service Creation**: New microservice = new YAML file
- **Quick Access**: Service configuration accessible via file shortcuts
- **Self-Documenting**: YAML format is human-readable and version-controlled

### Operational Excellence

- **Dramatic Time Reduction**: Deployment time reduced from hours to 30 minutes
- **Reduced Errors**: Eliminates manual workspace updates
- **Scalable Architecture**: Supports unlimited services without configuration bloat
- **Improved Maintainability**: Each service configuration is isolated and manageable

### Team Collaboration

- **Clear Ownership**: Each YAML file can be owned by specific teams
- **Easy Code Reviews**: Changes are isolated to relevant service files

## Project Structure

```text
.
├── modules/
│   ├── environment-resources/     # Environment-specific infrastructure
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── global-resources/          # Global infrastructure
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── applications/                  # Service configurations
│   ├── service-a.yaml            # Individual service configs
│   ├── service-b.yaml
│   ├── service-c.yaml
│   └── ...
├── environments/
│   ├── dev/
│   ├── staging/
│   └── production/
└── config.tf                     # Dynamic configuration loading
```
