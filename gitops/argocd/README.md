# App of Apps Pattern

This example of a fully Argo solution deploys three types of charts in the following order:
1. Operator Charts: Define CRDs
2. Resource Charts: Create CRs to make use of CRDs
3. Component Charts: Actual application or service code that makes use of resources from Step 2.

Podinfo is used as a sample application with values read from the values directory at repo root.

## Instructions
After Argo has been installed to the cluster...
```bash
ARGO_EXAMPLE=app-of-apps  # one of: `app-of-apps`, `app-of-charts`
# Add real healthchecks to Argocd applications and failing ones to CRs
kubectl patch cm/argocd-cm -n argocd --type=merge --patch-file https://raw.githubusercontent.com/roscoejp/gitops-samples/main/gitops/argocd/argocd-cm.yaml
# Deploy the root Argocd applications
kubectl apply -f https://raw.githubusercontent.com/roscoejp/gitops-samples/main/gitops/argocd/$ARGO_EXAMPLE.yaml
# "Fix" the healthchecks for CRs to unblock progress
kubectl patch cm/argocd-cm -n argocd --type=merge --patch-file https://raw.githubusercontent.com/roscoejp/gitops-samples/main/gitops/argocd/argocd-cm-fix.yaml
```

## Progressive Syncs
[Progressive Syncs](https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/Progressive-Syncs/) are an alpha feature so I would not recommend their use for anything outside of a homelab or development environment.

## References
- [Akuity Application Dependencies](https://akuity.io/blog/application-dependencies-with-argo-cd)
- [Argocd Cluster Bootstrapping](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/)
- [Argocd Resource health](https://argo-cd.readthedocs.io/en/stable/operator-manual/health/#resource-health)
- [Argocd Helm](https://argo-cd.readthedocs.io/en/latest/user-guide/helm/)
- [Argocd Multiple Sources for an Application](https://argo-cd.readthedocs.io/en/latest/user-guide/multiple_sources/)