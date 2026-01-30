# ADR – Distribute AWS Lambda Artifacts via GitHub Releases and Terraform HTTP Provider

## Context and Problem Statement

The team needs a consistent and maintainable way to build, version, store, and deploy AWS Lambda packages while keeping a clear separation between Infrastructure as Code (Terraform) and application code.

The main question was: **how can Lambda artifacts be produced and consumed in a way that is reproducible, avoids local build constraints during `terraform apply`, and cleanly separates application versioning from IaC versioning?**

Several approaches were discussed during a Discord thread following feedback on a GitHub pull request, focusing on build reliability, developer experience, security, and long-term maintainability.

## Considered Options

* **Build Lambda artifacts during `terraform apply`**  
  Lambda packages are built locally when Terraform is executed, potentially using Docker to mimic the Lambda runtime.  
  This requires all users running Terraform to have consistent local tooling (Python, Go, Docker) and increases the risk of non-deterministic builds.

* **Build Lambda artifacts in CI and store them as GitHub Release assets, retrieved via Terraform HTTP provider**  
  Lambda packages are built in GitHub Actions, versioned using semantic-release, attached to GitHub Releases, and downloaded by Terraform using the HTTP provider.

* **Commit Lambda `.zip` artifacts directly into the Terraform repository**  
  Pre-built Lambda packages are stored alongside Terraform code, coupling Lambda versioning with IaC versioning.

* **Use Lambda container images stored in a container registry**  
  Lambda functions are packaged as container images, built in CI, and deployed via a container registry (e.g. GitHub Container Registry or Amazon ECR).

## Decision Outcome

Chosen option: **”Build Lambda artifacts in CI and retrieve them from GitHub Releases via the Terraform HTTP provider”**, because it best satisfies the main decision drivers:

- Clear separation between application lifecycle and infrastructure lifecycle
- Deterministic and reproducible builds executed in a controlled CI environment
- No local tooling requirements (Python, Go, Docker) for users running `terraform apply`
- Independent and explicit versioning of Lambda code through semantic releases
- Reduced coupling between Terraform modules and application changes

Terraform remains focused on infrastructure provisioning, while CI pipelines fully own build, packaging, and release responsibilities.

### Consequences

**Good, because:**
- Lambda artifacts are built once in CI and reused consistently across environments
- GitHub Releases provide a clear audit trail of Lambda versions
- No AWS credentials are required in GitHub Actions for artifact storage
- The solution scales well as the number of Lambda functions grows

**Bad, because:**
- Terraform execution depends on the availability of GitHub Releases
- Additional care is required to manage artifact URLs, checksums, and release naming conventions
- We need to think about terraform module versioning as GitHub release (and git tags) in this repos are now used for lambdas versioning

**Neutral / trade-offs:**
- Lambda container images remain a viable future option if runtime or dependency complexity increases
- CI complexity increases slightly, while Terraform usage becomes significantly simpler