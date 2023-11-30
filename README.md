# GitOps with Argo CD

![argocd-gitops](./assets/argo-cd-gitops.png)

## Traditional CI/CD workflows

Traditional CI/CD workflows consists in two steps:

- Test, build, and push images to a container repository (CI)
- Deploy the application to the environment (CD)

```mermaid
flowchart LR
    subgraph CI
     test
     build
     pushImage
    end

    subgraph CD
    deploy
    end

    CI --> CD
```

This adoption addresses some challenges during deployment:

- Install tools used in pipelines
    - `kubectl`
    - Cloud CLIs (`aws-cli`, `azure-cli` and so on)
    - etc
- Manage cloud credentials
- Manage `kubectl` access
- Manage external tools access (if have 5 external tools, **need to
manage** 5 accesses)
- If the deployment creates cloud resources, it need to be executed
**every time** you want to create those resources.

## What is GitOps?

GitOps is a framework that defines [Git](https://git-scm.com/) as
the **Single Source of Truth (SST)**. This way, a repository reflects
**exactly** what should be in the environment. It automates the
provision of everything an application needs to run (_X as code_).

```mermaid
flowchart LR

subgraph x_as_code["fas:fa-code X as Code"]
    iac["fas:fa-file-code Infra as Code"]
    cac["fas:fa-file-code  Config as Code"]
    etc[...]
end

git["fab:fa-git-alt Git
    (Single Source of Truth)
    "]
ci_cd["fas:fa-spinner CI/CD"]
env["fas:fa-cloud Environment"]

x_as_code -->|leaves at| git -->|change triggers| ci_cd -->|deploys to| env
```

### Benefits

- **Environment transparency**: The environment **will match what**
is defined in your Git repository.

- **Fast recovery/easy rollbacks**: If anything goes wrong, you can
simple **apply a `git revert`** and the previous state will be applied
to the environment.

- **Security**: You can **control application deployment**
via `git merge` permissions.

- **Reliability**: If an application resource is directly removed,
the _reconcile_ process **will recreate the resource** 
to match the desired state.

## Argo CD

Argocd is a `pull-model` strategy, `gitops operator` that
**automatically syncs (_reconcile_) Kubernetes
resources** in the cluster.

```mermaid
flowchart TD

subgraph x_as_code["fas:fa-code X as Code"]
    iac["fas:fa-file-code Infra as Code"]
    cac["fas:fa-file-code  Config as Code"]
    etc[...]
end

git["fab:fa-git-alt Git
    (Single Source of Truth)
    "]
argo["fas:fa-spinner argoCD"]
cluster["fas:fa-server Kubernetes Cluster"]

x_as_code -->|leaves at| git
argo -->|watches| git
argo -->|sync states in| cluster
```

It takes a **desired state** (defined in the `Git` repository) and make
changes to the **actual state** to **keep states synchronized**.

```mermaid
flowchart TD

git["fab:fa-git-alt Git
    (Desired state)"]

argo["Argo CD sync"]

cluster["fas:fa-server Kubernetes Cluster
        (Actual state)"]

argo --> git
argo --> cluster
```

## Useful links

- [Devops Journey - Explaining GitOps](https://www.youtube.com/watch?v=dIaX5IhRqkI)