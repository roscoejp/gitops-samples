# App of Apps Pattern

This example of a fully Argo solution deploys three types of charts in the following order:
1. Operator Charts: Define CRDs
2. Resource Charts: Create CRs to make use of CRDs
3. Component Charts: Actual application or service code that makes use of resources from Step 2.

Podinfo is used as a sample application with values read from the values directory at repo root.

## Instructions
After Argo has been installed to the cluster...
```bash
# Add healthchecks to Application resources
kubectl patch cm/argocd-cm -n argocd --type=merge -f https://raw.githubusercontent.com/roscoejp/gitops-samples/main/gitops/argocd/argocd-cm.yaml
# Deploy the root Application
kubectl apply -f https://raw.githubusercontent.com/roscoejp/gitops-samples/main/gitops/argocd/app-of-apps.yaml
```

## Progressive Syncs
Progressive Syncs are an alpha feature so I would not recommend their use for anything outside of a homelab or development environment.

## References
- [Akuity](https://akuity.io/blog/application-dependencies-with-argo-cd) has good docs on the user of custom health checks to enable syncwaves at the application level.