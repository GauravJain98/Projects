# Terraform-Managed PostgreSQL Secure Git-Managed Query Runner

## Overview

This project provides a secure, auditable system for executing database queries in production environments. It was designed to handle critical database operations like indexing, feature flags, and schema changes in a controlled, tracked manner that maintains full audit trails and approval workflows.

## Problem Statement

Running production database queries safely requires:

- **Security**: Secure connection methods to production databases
- **Auditability**: Full tracking of what queries were executed and when
- **Approval Process**: Team review and approval before execution
- **State Management**: Tracking successful executions to prevent re-runs
- **Developer Experience**: Simple interface for developers to submit queries

## Solution Architecture

This system combines several technologies to create a secure, automated workflow:

### Core Components

- **Terraform State Management**: Uses `terraform_data` resource to track query execution status
- **AWS Systems Manager (SSM)**: Provides secure SSH tunneling to databases
- **GitHub Integration**: Issue templates and Actions for workflow automation
- **Slack Notifications**: Team communication and approval coordination

### Key Benefits

- **Full GitHub Audit Trail**: All approvals and interactions tracked in GitHub
- **Background Execution**: Automated workflow requiring minimal manual intervention
- **Secure Access**: No direct database credentials or connections required
- **State Persistence**: Terraform state prevents accidental query re-execution

## Project Structure

```
.
├── environment/
│   ├── data.tf                 # Data sources and remote state
│   ├── locals.tf               # Local values and configurations
│   ├── provider.tf             # Provider configurations
│   └── queries/                # Query definitions directory
│       ├── 1234.yaml          # Individual query files
│       ├── asdfjka.yaml       # (randomly generated names)
│       └── ...
└── terraform-modules/
    └── query/                  # Reusable query execution module
        ├── main.tf            # Main module logic
        ├── providers.tf       # Module provider requirements
        ├── run.sh            # Query execution script
        └── variables.tf       # Module input variables
```

### Architecture Details

- **One Module Instance Per Query**: Each YAML file triggers a separate module invocation
- **Terraform Data Resource**: Tracks execution state without requiring external providers
- **Constant Plan Time**: Performance scales independently of query count
- **State-Based Tracking**: Similar to shell script success/failure tracking in Terraform state

## Developer Workflow

### Step 1: Issue Creation

Developers create GitHub issues using structured templates that collect:

1. **New Issue Creation**
   <figure>
   <img src="issue.png" alt="GitHub Issue Creation Interface"/>
   <figcaption>GitHub Issue Template for Query Requests</figcaption>
   </figure>

2. **Required Information Input**
   <figure>
   <img src="input.png" alt="Issue Input Form"/>
   <figcaption>Structured Input Form for Query Details</figcaption>
   </figure>

**Required Information:**
- Target database name
- PostgreSQL instance identifier
- Query code to execute
- Justification and context

### Step 2: Automated Processing

Once an issue is created, GitHub Actions automatically:

1. **Validation**: Extract and validate information from the issue template
2. **File Generation**: Create a randomly-named YAML file in the `queries/` directory
3. **Branch Creation**: Generate a new branch with the random name and commit the query file
4. **Pull Request**: Create a PR for team review and approval
5. **Notification**: Send Slack notification to relevant team members

### Step 3: Review and Approval

The automated system provides:

- **Terraform Plan**: GitHub Action comments the execution plan in the PR
- **Team Review**: Appropriate team members review the query and impact
- **Approval Process**: Standard GitHub PR approval workflow
- **Execution Tracking**: Terraform state management prevents duplicate runs

## Security Features

- **No Direct Database Access**: All connections via secure AWS SSM tunnels
- **Credential Management**: No database credentials stored in code or state
- **Audit Trail**: Complete history of all query requests and executions
- **Access Control**: GitHub permissions control who can approve and merge
- **State Protection**: Terraform state prevents accidental re-execution
