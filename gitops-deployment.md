# Using Tekton and ArgoCD as devops tool

## Steps

### Create roks cluster in IBM Cloud

### Deploy Tekton
- Use Tekton for the CI part of your workflow
#### Install the Tekton 

- Deploy tekton in cluster 
```
oc apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
```
- In case Replicaset is not created run these commands 
```
oc adm policy add-scc-to-user anyuid/privileged  -z tekton-pipelines-controller
oc adm policy add-scc-to-user anyuid/privileged  -z tekton-pipelines-webhook
oc delete rs tekton-pipelines-controller-74f9946bd6 tekton-pipelines-webhook-654d84f6
```
- Verify the pods are running
```
oc get pods -n tekton-pipelines
```
- Install Tekton Dashboard
```
oc apply --filename https://storage.googleapis.com/tekton-releases/dashboard/latest/tekton-dashboard-release.yaml
```
- Expose `tekton-dashboard` service as route 
```
http://tekton-dashboard-tekton-pipelines.amitabh-spp-snapshot-fae6c235d7a3aed6346697c0e75f4896-0000.us-south.containers.appdomain.cloud/#/conditions
```

### Deploy ArgoCD
- Use Argo `Gitops tool` for the CD aspect
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
- Create route for accessing argocd instance
```
spec:
  host: argocd-argocd.apps.rpa-multi-tenant.cp.fyre.ibm.com
  to:
    kind: Service
    name: argocd-server
    weight: 100
  port:
    targetPort: https
  tls:
    termination: passthrough
    insecureEdgeTerminationPolicy: Redirect
  wildcardPolicy: None
```
- Get initial ArgoCD password
```
```
- Rrequires for openshift deployment
oc adm policy add-scc-to-user anyuid/privileged  -z argocd-redis
oc adm policy add-scc-to-user anyuid/privileged  -z argocd-redis
```
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
- Deploy Argocd CLI
    ```
    curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
    chmod +x /usr/local/bin/argocd
    argo version --short
    ```

- Reference 
    https://www.youtube.com/watch?v=c4v7wGqKcEY
    https://100daysofkubernetes.io/tools/argo.html

### Create application
- Simple application that displays information about cluster and some other details

### 