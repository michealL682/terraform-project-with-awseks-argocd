# GitOps with ArgoCD on Amazon EKS using Terraform: A Complete Implementation Guide

In the rapidly evolving world of DevOps and cloud-native applications, GitOps has emerged as a revolutionary approach to continuous deployment and infrastructure management. This blog post explores how to implement a complete GitOps workflow using ArgoCD on Amazon Elastic Kubernetes Service (EKS), providing you with a production-ready setup that follows industry best practices.

GitOps represents a paradigm shift where Git repositories serve as the single source of truth for both application code and infrastructure configuration. By leveraging ArgoCD as our GitOps operator, we can achieve automated, reliable, and auditable deployments while maintaining the declarative nature of Kubernetes.

## What is GitOps?

GitOps is a modern approach to continuous deployment that uses Git as the single source of truth for declarative infrastructure and applications. The core principles include:

- **Declarative Configuration**: Everything is described declaratively in Git
- **Version Control**: All changes are tracked and auditable
- **Automated Deployment**: Changes in Git trigger automatic deployments
- **Continuous Monitoring**: The system continuously ensures the desired state matches the actual state

## Why ArgoCD?

ArgoCD is a declarative, GitOps continuous delivery tool for Kubernetes that offers:

- **Application Management**: Centralized management of multiple applications
- **Multi-Cluster Support**: Deploy to multiple Kubernetes clusters
- **Rich UI**: Intuitive web interface for monitoring deployments
- **RBAC Integration**: Fine-grained access control
- **Rollback Capabilities**: Easy rollback to previous versions

## Architecture Overview

This implementation demonstrates a complete GitOps workflow using ArgoCD on Amazon EKS, creating a production-ready, cloud-native application delivery platform. 

The architecture consists of three main layers: 
- **Infrastructure Layer** (Terraform-managed AWS resources) - The infrastructure layer provisions a secure VPC with public and private subnets across multiple availability zones, an EKS cluster with managed node groups, and an NGINX Ingress Controller exposed via AWS Network Load Balancer.

- **Platform Layer** (Kubernetes services and ArgoCD) - The platform layer deploys ArgoCD server with custom ingress configuration for web UI access, implements the App-of-Apps pattern for centralized application management, and establishes Route53 DNS records for both ArgoCD (`argocd.chinmayto.com`) and applications (`app.chinmayto.com`).

- **Application Layer** (containerized workloads) - The application layer showcases a sample Node.js application deployed via GitOps, demonstrating automated synchronization, self-healing capabilities, and ingress-based external access. 

This architecture enables teams to achieve Infrastructure as Code through Terraform, GitOps-driven deployments through ArgoCD, automated application lifecycle management, and secure, scalable access patterns through AWS-native networking services.

```
┌──────────────────────────────────────────────────────────────┐
│                        AWS Cloud                             │
│  ┌──────────────────────────────────────────────────────────┐│
│  │                    VPC                                   ││
│  │  ┌─────────────────┐    ┌──────────────────────────────┐ ││
│  │  │  Public Subnets │    │      Private Subnets         │ ││
│  │  │                 │    │  ┌─────────────────────────┐ │ ││
│  │  │  ┌───────────┐  │    │  │      EKS Cluster        │ │ ││
│  │  │  │    NAT    │  │    │  │  ┌─────────────────────┐│ │ ││
│  │  │  │  Gateway  │  │    │  │  │   NGINX Ingress     ││ │ ││
│  │  │  └───────────┘  │    │  │  │    Controller       ││ │ ││
│  │  │                 │    │  │  └─────────────────────┘│ │ ││
│  │  └─────────────────┘    │  │  ┌─────────────────────┐│ │ ││
│  │                         │  │  │     ArgoCD          ││ │ ││
│  │                         │  │  │     Server          ││ │ ││
│  │                         │  │  └─────────────────────┘│ │ ││
│  │                         │  │  ┌─────────────────────┐│ │ ││
│  │                         │  │  │   Application       ││ │ ││
│  │                         │  │  │   Workloads         ││ │ ││
│  │                         │  │  └─────────────────────┘│ │ ││
│  │                         │  └─────────────────────────┘ │ ││
│  │                         └──────────────────────────────┘ ││
│  └──────────────────────────────────────────────────────────┘│
│                                                              │
│  ┌──────────────────────────────────────────────────────────┐│
│  │                   Route53                                ││
│  │  argocd.devcloudproject.com → NGINX Ingress NLB                ││
│  │  app.devcloudproject.com → NGINX Ingress NLB                   ││
│  └──────────────────────────────────────────────────────────┘│
└──────────────────────────────────────────────────────────────┘

GitOps Flow:
Git Repository → ArgoCD (App-of-Apps) → Kubernetes Applications
```

### The Significance of App-of-Apps Pattern

The `app-of-apps.yaml` file implements ArgoCD's **App-of-Apps pattern**, which serves as the cornerstone of scalable GitOps architecture in this implementation. This pattern creates a root ArgoCD application that monitors the `argocd/` directory in the Git repository and automatically manages the lifecycle of all other applications defined within it. When the App-of-Apps application syncs, it discovers application manifests like `nodejs-app-application.yaml` and `project.yaml`, then creates and manages these applications in ArgoCD without manual intervention. This approach transforms application deployment from a manual, imperative process into a fully automated, declarative workflow where adding a new application is as simple as committing a new YAML file to the `argocd/` directory. 

The pattern provides several critical benefits: 
- **centralized management** of multiple applications from a single source, 
- **automatic discovery** and deployment of new applications, - **self-healing capabilities** that ensure applications remain in their desired state, and 
- **GitOps compliance** where Git serves as the single source of truth for the entire application portfolio. 

In production environments, this pattern enables teams to achieve true Infrastructure as Code for application management, supports multi-environment deployments through branch-based strategies, provides complete audit trails through Git history, and enables disaster recovery scenarios where the entire application ecosystem can be restored from Git repository state alone.

## Prerequisites

Before starting, ensure you have:

- AWS CLI configured with appropriate permissions
- Terraform installed (version >= 1.0)
- kubectl installed
- A registered domain name in Route53
- Helm installed (version >= 3.0)


## Conclusion

This implementation demonstrates a production-ready GitOps workflow using ArgoCD on Amazon EKS. By following this guide, you've created:

- A secure, scalable Kubernetes cluster on AWS
- Automated application deployment pipeline
- Centralized application management through ArgoCD
- DNS-based access to your GitOps platform

The GitOps approach with ArgoCD provides numerous benefits including improved deployment reliability, enhanced security through Git-based workflows, and simplified application lifecycle management. This foundation can be extended to support multiple environments, advanced deployment strategies, and comprehensive monitoring solutions.

## References and Further Reading


- **ArgoCD Documentation**: [https://argo-cd.readthedocs.io/](https://argo-cd.readthedocs.io/)

