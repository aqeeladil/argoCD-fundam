# Continuous Delivery | GitOps | ArgoCD 

## What is GitOps?

- GitOps is a modern approach to managing infrastructure and application deployments. 
- It uses Git as the single source of truth for declarative infrastructure and applications, enabling continuous delivery and operational workflows. 
- It bridges the gap between Continuous Integration (CI) and Continuous Deployment (CD), ensuring that deployment processes are version-controlled and auditable.
- With GitOps, you store the desired state of your system in a Git repository, and a GitOps tool ensures that the actual system state matches the desired state through automated synchronization.
- Argo CD and Flux are two popular GitOps controllers, mainly used with Kubernetes.
- GitOps principles are not exclusive to Kubernetes and can be extended to other infrastructures.
- While GitOps doesn't strictly require Git, its tooling and workflows are deeply aligned with Git's capabilities. Using a different VCS for GitOps is possible but might involve extra effort and customizations.

### Key Principles of GitOps

1. **Declarative Configuration:**
- All system states (infrastructure, applications, etc.) are defined declaratively in code.
- Common tools: YAML manifests for Kubernetes, Terraform for infrastructure.
2. **Git as the Source of Truth:**
- The desired state of the system is version-controlled in Git.
- Git's version history serves as a record of changes.
3. **Automation and Reconciliation:**
- A GitOps operator (e.g., Flux, ArgoCD) watches the Git repository and automatically applies changes to the system.
- It continuously reconciles the actual state with the desired state.
4. **Security & Self-Healing Systems:**
- If the actual state deviates (e.g., manual changes or system failures), the GitOps operator brings it back to the desired state.
- Unauthorized changes to the cluster are overridden, as the system only accepts changes from Git.

### GitOps Workflow

- Store Infrastructure configurations (e.g., Kubernetes YAML manifests (node.yaml, deploy.yaml) or Helm charts, or Kustomize files or Terraform files) in a Git repository.
- Set up a GitOps operator (e.g., Flux or ArgoCD) in your cluster.
- Developer makes changes to the manifests (e.g., updates an image version).
- Creates a pull request for review.
- The pull request is reviewed and merged into the main branch.
- The GitOps operator detects the change and applies it to the target system automatically.
- The operator continuously monitors and ensures that the system matches the desired state.
- If someone directly edits the cluster without Git, the GitOps controller reverts the change.

### Problems GitOps Solves:

- **No tracking of changes:** When someone updates Kubernetes resources (like a node configuration) manually, there is no built-in version control or audit trail. It's hard to answer questions like:
  - Who made this change?
  - What change was made 10 days ago?
- **No versioning or immutability:** Changes made using tools like ```kubectl``` or scripts cannot be rolled back easily, and there's no history of previous configurations.
- **Lack of visibility:** Without a system like GitOps, it's challenging to determine the state of infrastructure at a given time.

**Example:** Imagine someone modifies a Kubernetes cluster directly using ```kubectl apply``` or a script. If issues arise later, there’s no easy way to track or roll back those changes. This lack of tracking and auditability poses risks.

### Real-World Use Cases:

- **Continuous Delivery in Kubernetes:** Automating updates for microservices hosted on Kubernetes clusters.
- **Multi-Environment Management:** Managing separate repositories for dev, staging, and production environments.
- **Disaster Recovery:** Quick rollbacks to stable states in case of failed deployments.
- **Compliance and Auditing:** Git serves as an audit log for all cluster changes.

### GitOps Tools:

- **FluxCD:** A lightweight GitOps tool for Kubernetes. Watches a Git repository and applies changes automatically.
- **ArgoCD:** A more feature-rich GitOps tool, ideal for complex Kubernetes environments. Provides a web interface and integrates well with CI/CD pipelines.
- **Jenkins X:** Combines CI/CD capabilities with GitOps.
- **Spinnaker:** Primarily a deployment tool, not entirely GitOps-focused.

### Applications Beyond Kubernetes

Although current GitOps tools primarily target Kubernetes, the principles can be applied to manage:
- Docker environments.
- AWS infrastructure.
- Other cloud resources.
____________________________________________________________________________________________

### GitOps for Applications and Infrastructure:

GitOps is not limited to application deployments. It is equally useful for managing infrastructure. For example:
- Application Delivery: Deploying services like web applications or microservices.
- Infrastructure Delivery: Managing Kubernetes nodes, admission controllers, namespaces, etc.

**Example:**
- Application Delivery: Deploying a web app using a deployment.yaml manifest.
- Infrastructure Delivery: Modifying Kubernetes node configurations using a ```node.yaml``` manifest.
_________________________________________________________________________________________

### Onboarding GitOps into an Existing Kubernetes Environment:

- A common challenge arises when an organization transitions to GitOps with pre-existing Kubernetes resources.
- You have two applications already deployed in the Kubernetes cluster before adopting GitOps. These applications were not deployed via the GitOps pipeline.
- By default, GitOps tools may try to remove resources not defined in the Git repository, treating them as "drift" from the desired state.
- This can cause accidental deletion of critical applications.
- **Manually Import Existing Resources:** Export the YAML definitions of current applications and add them to the Git repository.
- **Selective Syncing:** Use GitOps configurations to exclude unmanaged namespaces or applications from reconciliation.
- **Gradual Onboarding:** Begin with a subset of resources, testing and validating the reconciliation behavior before full-scale adoption.

### GitOps Handling Admission Controller Changes:

When an admission controller modifies pod configurations (e.g., adding resource requests, limits, or annotations), GitOps tools may attempt to revert these changes to align with the repository. This can lead to issues such as deployment failures in clusters with mandatory policies.

**How GitOps Handles This ?**
- **Pre-define Admission Controller Requirements in Git:** Add required fields (e.g., resource requests, limits, labels) to the pod.yaml in the Git repository to prevent discrepancies.
- **Exclude Specific Fields from Reconciliation:** Use GitOps annotations to ignore changes made by admission controllers for certain fields.
- **Leverage Customization Tools:** Tools like Kustomize or Helm can merge dynamic admission controller configurations with the desired state during deployment.

By aligning the Git repository with mandatory cluster policies and using tools to manage overrides, GitOps ensures smoother reconciliation and compliance.

