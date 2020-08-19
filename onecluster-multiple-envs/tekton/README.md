
# Create CD workflow on RH ACM using Tekton

Procedure:

```console
> oc new-project etherpad-cicd

Now using project "etherpad-cicd" on server "https://api.acmhub.rhbr-labs.com:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app ruby~https://github.com/sclorg/ruby-ex.git

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node

>  oc get serviceaccount pipeline
NAME       SECRETS   AGE
pipeline   2         29s

> cd onecluster-multiple-envs/tekton

> oc create -f tasks/01_create_namespaces.yaml
task.tekton.dev/create-namespaces created

> oc create -f tasks/02_create_dev_app_using_acm.yaml
task.tekton.dev/etherpad-dev-deployment created

> oc apply -f tasks/03_create_qa_app_using_acm.yaml
task.tekton.dev/etherpad-qa-deployment created

> oc apply -f tasks/04_create_prd_app_using_acm.yaml
task.tekton.dev/etherpad-prd-deployment created

> oc adm policy add-cluster-role-to-user cluster-admin system:serviceaccount:etherpad-cicd:pipeline
clusterrole.rbac.authorization.k8s.io/cluster-admin added: "system:serviceaccount:etherpad-cicd:pipeline"

> oc apply -f pipeline-acm.yaml
pipeline.tekton.dev/deploy-using-acm created

> oc apply -f prepare/tekton-source-pvc.yaml
persistentvolumeclaim/source-pvc created

> oc apply -f pipelinerun.yaml
pipelinerun.tekton.dev/deploy-using-acm-run-1 created
```