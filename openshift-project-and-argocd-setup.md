# OpenShift Project & Argo CD Setup

This document outlines the initial commands required to provision the target namespace and configure permissions for Red Hat OpenShift GitOps (Argo CD).

---

## Prerequisites

* `oc` CLI installed and authenticated with cluster-admin (or project-creation) privileges.
* Red Hat OpenShift GitOps operator installed in the `openshift-gitops` namespace.

---

## Provisioning Steps

### 1. Create the Application Project

Create a dedicated namespace for the workload:

```bash
oc new-project httpd-app
```

---

### 2. Configure Argo CD Project Management

Label the namespace so that OpenShift GitOps automatically detects and manages resources deployed to it:

```bash
oc label namespace httpd-app argocd.argoproj.io/managed-by=openshift-gitops
```

---

### 3. Grant Deployment Permissions to Argo CD

Assign the `admin` role to the Argo CD application controller ServiceAccount inside the new namespace. This enables Argo CD to create and manage Deployments, Services, Routes, and ConfigMaps:

```bash
oc adm policy add-role-to-user admin \
  system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller \
  -n httpd-app
```

---

## One-Liner Script

To execute all setup commands sequentially:

```bash
oc new-project httpd-app && \
oc label namespace httpd-app argocd.argoproj.io/managed-by=openshift-gitops && \
oc adm policy add-role-to-user admin \
  system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller \
  -n httpd-app
```

---

## Verification

To verify that the role binding was properly applied:

```bash
oc get rolebindings -n httpd-app | grep argocd-application-controller
```
