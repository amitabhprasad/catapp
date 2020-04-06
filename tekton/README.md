# Tekton CI/CD on OpenShift

The [Tekton Pipelines](https://github.com/tektoncd/pipeline) project provides
Kubernetes-style resources for declaring CI/CD-style pipelines.

## Build and run Nodejs app on OpenShift using a cloud native Tekton Pipeline

### Install Tekton Pipelines on your OpenShift environment

```bash
oc new-project tekton-pipelines
oc adm policy add-scc-to-user anyuid -z tekton-pipelines-controller
oc apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.notags.yaml
```

If you would like more detailed install instructions, or if you are installing
on OpenShift, then read [these instructions](https://github.com/tektoncd/pipeline/blob/master/docs/install.md#installing-tekton-pipelines) from the Tekton Pipelines documentation.

### Install the Tekton resources on your OpenShift environment

The Tekton resources are in this `tekton/` directory.

```bash
oc apply -f tekton/
```

#### (Optional) You might need to give your ServiceAccount the proper permissions

```bash
cat << EOF | oc apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: default
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
EOF
```

### Run the Tekton Pipeline

Run the Pipeline by creating a PipelineRun resource such as the following:

```bash
NAMESPACE=catapp
cat << EOF | oc apply -f -
apiVersion: tekton.dev/v1alpha1
kind: PipelineRun
metadata:
  name: catapp-build-and-deploy
spec:
  serviceAccountName: catapp
  pipelineRef:
    name: build-and-deploy-openshift
  resources:
  - name: source
    resourceSpec:
      type: git
      params:
      - name: revision
        value: triggers
      - name: url
        value: https://github.com/ncskier/catapp
  - name: image
    resourceSpec:
      type: image
      params:
      - name: url
        value: image-registry.openshift-image-registry.svc:5000/${NAMESPACE}/catapp:latest
  params:
  - name: DEPLOYMENT
    value: catapp
EOF
```

### View the deployed Nodejs app

Get the URL for your route with `oc get route catapp`, and open the route URL in your web browser.

If you would like to view the PipelineRun logs, then read [these instructions](https://github.com/tektoncd/pipeline/blob/master/docs/logs.md) from the Tekton Pipelines
documentation.

## Run the Trigger

```bash
URL="https://github.com/catapp" # The URL of your fork of CatApp
ROUTE_HOST=$(oc get route el-catapp --template='http://{{.spec.host}}')
curl -v \
   -H 'X-GitHub-Event: pull_request' \
   -H 'Content-Type: application/json' \
   -d '{
     "repository": {"url": "'"${URL}"'"},
     "pull_request": {"head": {"sha": "master"}}
   }' \
   ${ROUTE_HOST}
```