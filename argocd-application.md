# Argo CD Application Configuration

This document contains the declarative Kubernetes manifest for the Argo CD `Application` resource corresponding to the application created via the Argo CD Web UI.

## Application Manifest (`application.yaml`)

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: httpd-app
  namespace: openshift-gitops
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  project: default
  source:
    repoURL: https://github.com/neil-thakur/openshift-4.20-gitops-rollout-extension-ui-testing.git
    targetRevision: HEAD
    path: .
  destination:
    server: https://kubernetes.default.svc
    namespace: httpd-app
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=false
```

---

## Configuration Breakdown

| Field | Value | Purpose |
| :--- | :--- | :--- |
| `metadata.name` | `httpd-app` | Name of the Argo CD application. |
| `metadata.namespace` | `openshift-gitops` | Namespace where OpenShift GitOps runs the Argo CD control plane. |
| `metadata.finalizers` | `resources-finalizer...` | Ensures resources deployed by this app are cleanly deleted from the cluster if the Argo CD application is removed. |
| `spec.project` | `default` | Standard Argo CD AppProject defining allowed repositories and target destinations. |
| `spec.source.repoURL` | `https://github.com/neil-thakur/...` | Target Git repository containing the Kubernetes manifests. |
| `spec.source.path` | `.` | Root directory of the repository where manifests reside. |
| `spec.source.targetRevision` | `HEAD` | Tracks the latest commit on the default branch. |
| `spec.destination.server` | `https://kubernetes.default.svc` | The local in-cluster Kubernetes API endpoint. |
| `spec.destination.namespace` | `httpd-app` | Target namespace on OpenShift where manifests are applied. |

---

## Deployment Options

### Apply via `oc` CLI
To deploy or update this application declaratively without using the UI:

```bash
oc apply -f - <<EOF
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: httpd-app
  namespace: openshift-gitops
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  project: default
  source:
    repoURL: https://github.com/neil-thakur/openshift-4.20-gitops-rollout-extension-ui-testing.git
    targetRevision: HEAD
    path: .
  destination:
    server: https://kubernetes.default.svc
    namespace: httpd-app
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
EOF
```

### Manual Sync via CLI
To trigger an immediate sync and check status:

```bash
# Sync application
oc -n openshift-gitops exec -it deployment/openshift-gitops-server -- argocd app sync httpd-app

# View application status
oc get application httpd-app -n openshift-gitops
```
