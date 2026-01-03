# ArgoCD

These are examples of bootstrapping multi-tier applications using Argocd.

## App of Apps Pattern

This example deploys three types of charts in the following order:
1. Operator Charts: Define CRDs
2. Resource Charts: Create CRs to make use of CRDs
3. Application Charts: Actual application or service code that makes use of resources from Step 2.

Podinfo is used as a sample application with values read from the values directory at repo root. Dependent resources are deployed in order using Argocd syncwaves on the root application.

## App of Charts Pattern

> [!CRITICAL]  
> Helm by itself does not make any guarantees around subchart deployment ordering or timing. We're still making use of Argocd syncwaves within subcharts to control deployment ordering.
> [!IMPORTANT]  
> Argocd syncwaves have a default value of "0", but negative numbers can be used to ensure deployment prior to un-annotated resources.

This example uses Argcdo for bootstrapping a Helm umbrella application chart in the following order:
1. Operator Charts: Define CRDs
2. Application Charts: App code that uses Helm dependencies to define resources and/or components.

A small sample chart using Nginx and the same resource charts as the app-of-apps pattern is used to demonstrate this pattern. Syncwave annotations can be overridden at the application chart in this model to allow finer grained control of resource deployment per application.

# Instructions
After Argo has been installed to the cluster run the following from this directory:
```bash
ARGO_EXAMPLE=app-of-apps  # one of: `app-of-apps`, `app-of-charts`
# Add healthchecks to Argocd applications and failing HCs to Custom Resources
kubectl patch cm/argocd-cm -n argocd --type=merge --patch-file ./argocd-cm.yaml
# Deploy the root Argocd application
kubectl apply -f https://raw.githubusercontent.com/roscoejp/gitops-samples/main/gitops/argocd/$ARGO_EXAMPLE/root.yaml
# Take this time to breath and observe that applications are not deploying due to failing resource healthchecks...
# "Fix" the healthchecks for CRs to unblock the deployment
kubectl patch cm/argocd-cm -n argocd --type=merge --patch-file ./argocd-cm-fix.yaml
```

## Progressive Syncs
[Progressive Syncs](https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/Progressive-Syncs/) are an alpha feature so I would not recommend their use for anything outside of a homelab or development environment.

## References
- [This specific bug that needs more +1s IMO](https://github.com/argoproj/argo-cd/issues/16870)
- [Akuity Application Dependencies](https://akuity.io/blog/application-dependencies-with-argo-cd)
- [Argocd Cluster Bootstrapping](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/)
- [Argocd Resource health](https://argo-cd.readthedocs.io/en/stable/operator-manual/health/#resource-health)
- [Argocd Helm](https://argo-cd.readthedocs.io/en/latest/user-guide/helm/)
- [Argocd Multiple Sources for an Application](https://argo-cd.readthedocs.io/en/latest/user-guide/multiple_sources/)