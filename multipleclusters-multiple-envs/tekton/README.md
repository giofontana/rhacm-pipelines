
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

> tkn pipelinerun logs deploy-using-acm-run-1 -f
[fetch-repository : clone] + CHECKOUT_DIR=/workspace/output/
[fetch-repository : clone] + '[[' true '==' true ]]
[fetch-repository : clone] + cleandir
[fetch-repository : clone] + '[[' -d /workspace/output/ ]]
[fetch-repository : clone] + rm -rf /workspace/output//onecluster-multiple-envs
[fetch-repository : clone] + rm -rf /workspace/output//.git
[fetch-repository : clone] + rm -rf '/workspace/output//..?*'
[fetch-repository : clone] + test -z 
[fetch-repository : clone] + test -z 
[fetch-repository : clone] + test -z 
[fetch-repository : clone] + /ko-app/git-init -url https://github.com/giofontana/rhacm-pipelines.git -revision master -refspec  -path /workspace/output/ '-sslVerify=true' '-submodules=true' -depth 1
[fetch-repository : clone] {"level":"info","ts":1597794549.2025642,"caller":"git/git.go:136","msg":"Successfully cloned https://github.com/giofontana/rhacm-pipelines.git @ f111d89c17e5647d3cd4dedca6968f20b73cfb0d (grafted, HEAD, origin/master) in path /workspace/output/"}
[fetch-repository : clone] {"level":"info","ts":1597794549.240191,"caller":"git/git.go:177","msg":"Successfully initialized and updated submodules in path /workspace/output/"}
[fetch-repository : clone] + cd /workspace/output/
[fetch-repository : clone] + git rev-parse HEAD
[fetch-repository : clone] + tr -d '\n'
[fetch-repository : clone] + RESULT_SHA=f111d89c17e5647d3cd4dedca6968f20b73cfb0d
[fetch-repository : clone] + EXIT_CODE=0
[fetch-repository : clone] + '[' 0 '!=' 0 ]
[fetch-repository : clone] + echo -n f111d89c17e5647d3cd4dedca6968f20b73cfb0d

[create-namespaces : creating-namespaces] **** Creating namespaces ****
[create-namespaces : creating-namespaces] namespace/etherpad-acm-dev unchanged
[create-namespaces : creating-namespaces] namespace/etherpad-acm-qa unchanged
[create-namespaces : creating-namespaces] namespace/etherpad-acm-prd unchanged

[etherpad-dev-deployment : etherpad-dev-deployment] **** DEV ENVIRONMENT: Creating application on RH ACM ****
[etherpad-dev-deployment : etherpad-dev-deployment] application.app.k8s.io/etherpad-acm-dev unchanged
[etherpad-dev-deployment : etherpad-dev-deployment] **** DEV ENVIRONMENT: Creating channel on RH ACM ****
[etherpad-dev-deployment : etherpad-dev-deployment] channel.apps.open-cluster-management.io/etherpad-app-latest unchanged
[etherpad-dev-deployment : etherpad-dev-deployment] **** DEV ENVIRONMENT: Creating placementrule on RH ACM ****
[etherpad-dev-deployment : etherpad-dev-deployment] placementrule.apps.open-cluster-management.io/dev-cluster unchanged
[etherpad-dev-deployment : etherpad-dev-deployment] **** DEV ENVIRONMENT: Creating subscription on RH ACM ****
[etherpad-dev-deployment : etherpad-dev-deployment] subscription.apps.open-cluster-management.io/etherpad-acm-dev unchanged

[etherpad-qa-deployment : etherpad-qa-deployment] **** QA ENVIRONMENT: Creating application on RH ACM ****
[etherpad-qa-deployment : etherpad-qa-deployment] application.app.k8s.io/etherpad-acm-qa unchanged
[etherpad-qa-deployment : etherpad-qa-deployment] **** QA ENVIRONMENT: Creating channel on RH ACM ****
[etherpad-qa-deployment : etherpad-qa-deployment] channel.apps.open-cluster-management.io/etherpad-app-latest unchanged
[etherpad-qa-deployment : etherpad-qa-deployment] **** QA ENVIRONMENT: Creating placementrule on RH ACM ****
[etherpad-qa-deployment : etherpad-qa-deployment] placementrule.apps.open-cluster-management.io/qa-cluster unchanged
[etherpad-qa-deployment : etherpad-qa-deployment] **** QA ENVIRONMENT: Creating subscription on RH ACM ****
[etherpad-qa-deployment : etherpad-qa-deployment] subscription.apps.open-cluster-management.io/etherpad-acm-qa unchanged

[etherpad-prd-deployment : etherpad-qa-deployment] **** PRD ENVIRONMENT: Creating application on RH ACM ****
[etherpad-prd-deployment : etherpad-qa-deployment] application.app.k8s.io/etherpad-acm-prd unchanged
[etherpad-prd-deployment : etherpad-qa-deployment] **** PRD ENVIRONMENT: Creating channel on RH ACM ****
[etherpad-prd-deployment : etherpad-qa-deployment] channel.apps.open-cluster-management.io/etherpad-app-latest unchanged
[etherpad-prd-deployment : etherpad-qa-deployment] **** PRD ENVIRONMENT: Creating placementrule on RH ACM ****
[etherpad-prd-deployment : etherpad-qa-deployment] placementrule.apps.open-cluster-management.io/prd-cluster unchanged
[etherpad-prd-deployment : etherpad-qa-deployment] **** PRD ENVIRONMENT: Creating subscription on RH ACM ****
[etherpad-prd-deployment : etherpad-qa-deployment] subscription.apps.open-cluster-management.io/etherpad-acm-prd unchanged

```

