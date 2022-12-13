Upoound Cloud has been retired. The directions provided here are no longer relevant, but may provide some value in understanding similar config needs.

# Configure Argo CD for Upbound Cloud
To configure argoCD to work with UBC, we need to conifgue it to ignore resources UBC blcoks List on. The `argocd-cm` ConfigMap in the
argocd namespace is where this is configured. The following manifest should be applied to argocd namespace (Replace cluster URL with the URL in your kubeconfig for the UBC cluster, add multiple UBC cluster URLs if configuring multiple):

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  labels:
    app.kubernetes.io/name: argocd-cm
    app.kubernetes.io/part-of: argocd
  name: argocd-cm
  namespace: argocd
data:
  resource.exclusions: |
    - apiGroups:
      - ""
      - "velero.io"
      - "storage.k8s.io"
      - "scheduling.k8s.io"
      - "crossplanecluster.cloud.upbound.io"
      - "policy"
      - "node.k8s.io"
      - "networking.k8s.io"
      - "flowcontrol.apiserver.k8s.io"
      - "extensions"
      - "discovery.k8s.io"
      - "crossplanecluster.cloud.upbound.io"
      - "coordination.k8s.io"
      - "certificates.k8s.io"
      - "batch"
      - "autoscaling"
      - "authorization.k8s.io"
      - "apps"
      - "apiregistration.k8s.io"
      - "admissionregistration.k8s.io"
      - "rbac.authorization.k8s.io"
      kinds:
      - "*"
      clusters:
      -  <Replace here with the url to UBC cluster from kubeconfig>
    - apiGroups:
      - pkg.crossplane.io
      kinds:
      - "Lock"
      - "ControllerConfig"
      clusters:
      -  <Replace here with the url to UBC cluster from kubeconfig>
  ```
  
## Add UBC cluster to argoCD
Fill cluster url and auth token and then apply to argocd namespace.

  
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: ubc-secret
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: cluster
type: Opaque
stringData:
  name: ubc
  server: <Replace here with the url to UBC cluster from kubeconfig>
  config: |
    {
      "bearerToken": "<Replace here with token field in kubeconfig for UBC context>",
      "tlsClientConfig": {
        "insecure": false,
        "caData": ""
      }
    }
```
