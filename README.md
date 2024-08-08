# Kubernetes - Generate yaml files using kubectl create command

## 1. Namespace

kubectl create namespace mynamespace --dry-run=client -o ymal | kubectl apply -f -

## 2. ConfigMap

##### Create a new config map named my-config based on folder
kubectl create configmap myconfigmap --from-file=./file.txt --dry-run=client -o yaml | kubectl apply -f -

##### Create a new config map named my-config with specified keys instead of file basenames on disk
kubectl create configmap myconfigmap --from-file=key1=./test.txt --from-file=key2=./test1.txt --dry-run=client -o yaml

##### Create a new config map named my-config with key1=config1 and key2=config2
kubectl create configmap myconfigmap --from-literal=daugther=yuktha --from-literal=spouse=meghana -n mynamespace --dry-run=client -o yaml

##### Create a new config map named my-config from the key=value pairs in the file
kubectl create configmap myconfigmap --from-file=/root/bar -n mynamespace --dry-run=client -o yaml

##### Create a new config map named my-config from an env file

kubectl create configmap myconfigmap --from-file=./configmap.env -n namespace --dry-run=client -o yaml

## 3. CronJob

kubectl create cronjob mycronjob --image=busybox --schedule='* * * * *' --restart=OnFailure -- /bin/sh -c "date; echo Hello K8 Purushotham"

kubectl get cronjob mycronjob

kubectl get jobs

kubectl get pod --selector=job-name=<job Name>

kubectl logs <pod name>

Key Points

1. concurrencyPolicy (Forbid,Replace,Allow) - Controls how CronJobs behave when the previous Job hasn't finished yet.
spec:
  concurrencyPolicy: "Forbid"

2. Control how many successful or failed Jobs are retained.
spec:
  successfulJobsHistoryLimit: 3
  failedJobsHistoryLimit: 1

## 4. Deployment

##### Create a deployment named mydeployment that runs the purshotaml/my-flask-app:0.1 image with 3 replicas
kubectl create deployment mydeployment --image=purshotaml/my-flask-app:0.1 --port=5000 --replicas=3

##### create a deployment named mydeploymentmultiple that runs multiple containers
kubectl create deployment mydeploymentmultiple  --image=ubuntu:latest --image=nginx

## 4. Ingress
##### to create ingress with TLS 
kubectl create ingress myingress --rule=example.local/*=service:80,tls=my-tls-secret --dry-run=client -o yaml

##### to create ingress with path-type
kubectl create ingress myingress --rule=example.local/*=service:80,tls=my-tls-secret,path-type=prefix --dry-run=client -o yaml

##### Basic ingress with host and path
kubectl create ingress my-ingress --rule=exmaple.com/*=my-service:80 --dry-run=client -o yaml

##### ingress with TLS
kubectl create ingress my-ingress --rule=example.com/*=my-service:80,tls=my-tls-secret --dry-run=client -o yaml

##### ingress with TLS and Pathtype
kubectl create ingress myingress --rule=example.local/*=service:80,tls=my-tls-secret,path-type=Prefix --dry-run=client -o yaml

##### ingress with Multiple Rules and TLS
kubectl create ingress myingress --rule=example.local/*=service:80,tls=example-tls-secret,path-type=Prefix \
--rule=example1.local/*=another-service:5000,tls=example1-tls-secret,path-type=Prefix --dry-run=client -o yaml

##### ingress with path based routing
kubectl create ingress pathingress --rule=example.local/app1=service:80 \
--rule=example.local/app2=service1:5000,tls=my-tls-secret \
--dry-run=client -o yaml

To ingress to work we need to have ingress-controller installed on kubernetes cluster
1. nginx ingress controller
2. HAProxy Ingress Controller
3. **Traefik Ingress Cons
4. **Contour Ingress Cons
5. Istio Ingress Gateway
6. AWS ALB Ingress Controller
7. GCE Ingress Controller

## 5. Job

##### Create a job
kubectl create job my-job --image=busybox

##### Create a job with a command
kubectl create job my-job --image=busybox -- date
kubectl create job testing41 --image=busybox -- /bin/sh -c  "echo Hello Kubernetes!"

##### Create a job from a cron job named "a-cronjob"
kubectl create job test-job --from=cronjob/a-cronjob

##### Job with Multiple Completions
kubectl create job multi-completion-job --image=busybox -- /bin/sh -c "sleep 10; echo 'Job complete!'"
kubectl patch job multi-completion-job -p '{"spec":{"completions":3,"parallelism":5}}'

## 6. PodDisruptionBudget

kubectl create pdb mypdb --min-available=2 --selector=app=nginx --dry-run=client -o yaml
kubectl create pdb mypdb --max-unavailable=1 --selector=app=nginx --dry-run=client -o yaml

## 7. PriorityClass

##### create priority class
kubectl create priorityclass highpriority --value=1000 --global-default=false --dry-run=client -o yaml

##### create deployment of ngnix
kubectl create deployment my-deployment --image=nginx --replicas=3

##### patch the deployment to apply priority class
kubectl patch deployment my-deployment -p '{"spec": {"template": {"spec": {"priorityClassName": "highpriority"}}}}'

## 8. Quotas

Kubernetes resource quotas are a way to limit the resource consumption (like CPU, memory, number of pods, etc.) 
within a namespace. They help ensure that a single namespace doesn't consume more than its fair share of resources.

kubectl create namespace my-namespace

kubectl create quota my-quota --namespace=my-namespace --hard=cpu=1,memory=1G,pods=2,services=3,replicationcontrollers=2,resourcequotas=1,secrets=5,persistentvolumeclaims=10 --dry-run=client -o yaml > resource-quota.yaml

kubectl apply -f resource-quota.yaml

## 9. Role

##### Create a role named "pod-reader" that allows user to perform "get", "watch" and "list" on pods
kubectl create role pod-reader --verb=get --verb=list --verb=watch --resource=pods
  
##### Create a role named "pod-reader" with ResourceName specified
kubectl create role pod-reader --verb=get --resource=pods --resource-name=readablepod --resource-name=anotherpod
  
##### Create a role named "foo" with API Group specified
kubectl create role foo --verb=get,list,watch --resource=rs.apps
  
##### Create a role named "foo" with SubResource specified
kubectl create role foo --verb=get,list,watch --resource=pods,pods/status

## 10. Role Binding

##### Create a role binding for user1, user2, and group1 using the admin cluster role
kubectl create rolebinding admin --clusterrole=admin --user=user1 --user=user2 --group=group1 --dry-run=client -o yaml
  
##### Create a role binding for serviceaccount monitoring:sa-dev using the admin role
kubectl create rolebinding admin-binding --role=admin --serviceaccount=monitoring:sa-dev --dry-run=client -o yaml

## 11. Secrets - Docker Registry

kubectl create secret docker-registry my-registry-secret \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=purshotaml \
  --docker-password=Mp833452230*\
  --docker-email=purshotaml@gmail.com --dry-run=client -o yaml


kubectl create deployment mydeployment --image=purshotaml/my-flask-app:0.1 --dry-run=client -o yaml > deployment.yaml

apiVersion: v1
kind: Pod
metadata:
  name: private-reg-pod
spec:
  containers:
  - name: private-reg-container
    image: purshotaml\my-flask-app:latest
  imagePullSecrets:
  - name: my-registry-secret

## 12. Secrets - Generic

##### Create a Secret with Literal Values
kubectl create secret generic my-secret --from-literal=username=myuser --from-literal=password=mypassword --dry-run=client -o yaml


##### create a secrete from a file
kubectl create secret generic my-secret --from-file=username=./username.txt --from-file=password=./password.txt --dry-run=client -o yaml

##### create a secret from a directory; pre-requisite create directory and then create two files 
mkdir test-data
echo 'admin' > test-data/username.txt
echo 'password' > test-data/password.txt

kubectl create secret generic my-secret --from-file=./test-data --dry-run=client -o yaml | kubctl apply -f -
echo 'migration' > test-data/migration.txt

kubectl create secret generic my-secret --from-file=./test-data  --dry-run=client -o yaml | kubectl replace -f -

How to test:

1. create a deployment file and set env variable value using the secret
2. Login using exec to the container and print the env variables

## 13. Secret TLS

##### Create a new TLS secret named tls-secret with the given key pair
kubectl create secret tls tls-secret --cert=path/to/tls.crt --key=path/to/tls.key
