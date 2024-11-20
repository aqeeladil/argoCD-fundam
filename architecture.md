## ARCHITECTURE OF ARGO CD 

Argo CD consists of several microservices working together:

#### Repo Server:
- Connects to Git to fetch the desired state.
- Monitors changes in the Git repository.

#### Application Controller:
- Interacts with Kubernetes to fetch the actual state.
- Compares the actual state with the desired state from Git.
- Syncs any discrepancies by reconciling Kubernetes to match Git.

#### API Server:
- Acts as the interface for users, providing both UI and CLI interactions.
- Handles user authentication (supports SSO via OIDC).

#### Dex:
- A lightweight OIDC provider for SSO authentication.

#### Redis:
- Used for caching data, ensuring stateful operations and recovery in case of service restarts.
 
