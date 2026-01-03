# App of Apps Pattern

This example of a fully Argo solution deploys three types of charts in the following order:
1. Operator Charts: Define CRDs
2. Resource Charts: Create CRs to make use of CRDs
3. Application Charts: Actual application or service code that makes use of resources from Step 2.

Podinfo is used as a sample application with values read from the values directory at repo root. 

# App of Charts Pattern
> [!IMPORTANT]  
> Helm by itself does not make any guarantees around subchart deployment ordering or timing.

This example uses Argo for bootstrapping a Helm umbrella chart in the following order:
1. Operator Charts: Define CRDs
2. Application Charts: App code that uses Helm dependencies to define resources or components.

A small sample chart using Nginx and the same resource charts as the app-of-apps pattern is used to demonstrate this pattern.

## Instructions
After Argo has been installed to the cluster run the following from this directory:
```bash
ARGO_EXAMPLE=app-of-apps  # one of: `app-of-apps`, `app-of-charts`
# Add real healthchecks to Argocd applications and failing ones to CRs
kubectl patch cm/argocd-cm -n argocd --type=merge --patch-file ./argocd-cm.yaml
# Deploy the root Argocd applications
kubectl apply -f https://raw.githubusercontent.com/roscoejp/gitops-samples/main/gitops/argocd/$ARGO_EXAMPLE.yaml
# Take note that with Argo, dependent charts are deployed in sync-wave order, while with vanilla Helm ordering is not provided for custom resources.
# "Fix" the healthchecks for CRs to unblock progress
kubectl patch cm/argocd-cm -n argocd --type=merge --patch-file ./argocd-cm-fix.yaml
```

## Progressive Syncs
[Progressive Syncs](https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/Progressive-Syncs/) are an alpha feature so I would not recommend their use for anything outside of a homelab or development environment.

## References
- [Akuity Application Dependencies](https://akuity.io/blog/application-dependencies-with-argo-cd)
- [Argocd Cluster Bootstrapping](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/)
- [Argocd Resource health](https://argo-cd.readthedocs.io/en/stable/operator-manual/health/#resource-health)
- [Argocd Helm](https://argo-cd.readthedocs.io/en/latest/user-guide/helm/)
- [Argocd Multiple Sources for an Application](https://argo-cd.readthedocs.io/en/latest/user-guide/multiple_sources/)