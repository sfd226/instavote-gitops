# Instavote GitOps Repository

This repository contains the GitOps configurations and Helm charts for the Instavote application stack. It works in conjunction with the platform configuration repository to provide a complete multi-tenant deployment solution.

## Overview

The Instavote application consists of multiple microservices deployed across different environments using ArgoCD and Helm. This repository contains the application-specific configurations and Helm charts, while platform-level configurations are maintained in the [instavote-platform-config](https://github.com/GITHUB_ORG/instavote-platform-config) repository.

## Repository Structure

```
instavote-gitops/
├── charts/
│   ├── vote/                  # Vote frontend service
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   ├── templates/
│   │   │   ├── rollout.yaml        # Argo Rollouts configuration
│   │   │   ├── service.yaml        # Main service
│   │   │   └── preview-service.yaml # Preview service for blue/green
│   │   └── env/
│   │       ├── dev.yaml      # Dev environment values
│   │       ├── staging.yaml  # Staging environment values
│   │       └── prod.yaml     # Production environment values
│   └── redis/                 # Redis service
│       ├── Chart.yaml
│       ├── values.yaml
│       ├── templates/
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── env/
│           ├── dev.yaml
│           ├── staging.yaml
│           └── prod.yaml
└── docs/                      # Additional documentation
    └── architecture.md        # Architecture documentation
```

## Charts

### Vote Application

The vote application is the frontend service that allows users to cast votes.

#### Features
- Blue/Green deployment using Argo Rollouts
- Configurable resource limits
- Health checks and probes
- Service mesh integration ready

#### Configuration

Key configuration parameters in `values.yaml`:
```yaml
replicaCount: 4
image:
  repository: schoolofdevops/vote
  tag: v2
  pullPolicy: Always
resources:
  requests:
    cpu: "50m"
    memory: "64Mi"
  limits:
    cpu: "250m"
    memory: "128Mi"
```

### Redis

Redis serves as the in-memory database for storing vote data.

#### Features
- Standalone Redis instance
- Configurable persistence
- Resource management
- Security configurations

## Environment-Specific Configurations

Each service has environment-specific values stored in the `env/` directory:

- `dev.yaml`: Development environment configurations
- `staging.yaml`: Staging environment configurations
- `prod.yaml`: Production environment configurations

## Usage

### Prerequisites
- Kubernetes cluster
- ArgoCD v2.8 or later
- Access to the platform configuration repository
- Helm v3

### Deployment

The deployment is managed by ArgoCD ApplicationSets defined in the platform configuration repository. The ApplicationSet controller automatically creates ArgoCD Applications for each service in each environment.

Refer to the [platform configuration repository](https://github.com/GITHUB_ORG/instavote-platform-config) for:
- Tenant configurations
- RBAC settings
- Network policies
- Resource quotas
- ApplicationSet definitions

### Adding a New Service

1. Create a new directory under `charts/`
2. Initialize Helm chart structure:
   ```bash
   helm create charts/new-service
   ```
3. Add environment-specific values in `env/`
4. Update platform repository ApplicationSet if needed

### Updating Configurations

1. Create a feature branch
2. Make changes to relevant Helm chart or values
3. Test in development environment
4. Submit pull request
5. After approval, changes will be automatically deployed by ArgoCD

## Development Workflow

1. Local Development:
   ```bash
   # Test Helm chart locally
   helm template charts/vote -f charts/vote/env/dev.yaml
   
   # Lint chart
   helm lint charts/vote
   ```

2. Testing:
   ```bash
   # Validate manifests
   kubectl apply --dry-run=client -f <generated-manifests>
   ```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make changes
4. Submit pull request
5. Ensure CI checks pass

## Troubleshooting

Common issues and solutions:

1. Sync Failures:
   - Check Helm chart validity
   - Verify environment values
   - Check resource quotas

2. Deployment Issues:
   - Validate resource requests/limits
   - Check container image availability
   - Verify network policies

## License

```
Copyright 2025 Gourav Shah, Initcron Systems Private Limited (School of Devops)

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

## Contact

- Author: Gourav Shah
- Organization: Initcron Systems Private Limited (School of Devops)
- Website: https://www.schoolofdevops.com
