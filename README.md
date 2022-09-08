## Training schedule 

<img src="sch.png">

## Revision 

<img src="rev1.png">

## Spinnaker deployment stages

<img src="dep1.png">

### we can generally install halyard as Container 

```
[ec2-user@client ~]$ docker  images
REPOSITORY                                             TAG       IMAGE ID       CREATED         SIZE
us-docker.pkg.dev/spinnaker-community/docker/halyard   stable    d4bdf593e57a   2 weeks ago     835MB
gcr.io/spinnaker-marketplace/halyard                   stable    f81059ec68f2   17 months ago   737MB
[ec2-user@client ~]$ 
[ec2-user@client ~]$ 
[ec2-user@client ~]$ docker  run -it --rm  d4bdf593e57a   bash 
bash-5.0$ hal -v
1.49.0
bash-5.0$ exit
[ec2-user@client ~]$ docker  run -it --rm   f81059ec68f2  bash 
bash-5.0$ hal -v
1.42.0-20210408182114
bash-5.0$ exit
[ec2-user@client ~]$ 

```

### Halyard in container and spinnaker services in Kubernetes 

<img src="k8ssp.png">

## Understanding k8s app deployment 

<img src="appd.png">

### lets interact with kubernetes to deploy a sample app 

```
[ec2-user@client ~]$ kubectl  version --client 
Client Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.6", GitCommit:"ad3338546da947756e8a88aa6822e9c11e7eac22", GitTreeState:"clean", BuildDate:"2022-04-14T08:49:13Z", GoVersion:"go1.17.9", Compiler:"gc", Platform:"linux/amd64"}
[ec2-user@client ~]$ 


```

### configure a user to interact with k8s control plane 

```
[spinnaker@client ~]$ mkdir ~/.kube
mkdir: cannot create directory '/home/spinnaker/.kube': File exists
[spinnaker@client ~]$ 
[spinnaker@client ~]$ ls  /home/spinnaker/.kube/
cache
[spinnaker@client ~]$ cp -v /tmp/config  /home/spinnaker/.kube/
'/tmp/config' -> '/home/spinnaker/.kube/config'
[spinnaker@client ~]$ 
[spinnaker@client ~]$ kubectl  get  nodes
NAME            STATUS   ROLES           AGE     VERSION
control-plane   Ready    control-plane   2d11h   v1.25.0
node1           Ready    <none>          2d11h   v1.25.0
node2           Ready    <none>          2d11h   v1.25.0
[spinnaker@client ~]$ 


```

### Resources Understanding in K8s 

<img src="res.png">

### Deploy sample app and access it 

```
 kubectl  create  deployment ashuapp  --image=dockerashu/jecrcapp:v1  --port 80 --dry-run=client -o yaml   >app.yaml 
```
### lets deploy it 

```
[spinnaker@client ashu]$ kubectl apply -f  app.yaml 
deployment.apps/ashuapp configured
[spinnaker@client ashu]$ kubectl  get  deploy 
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashuapp   1/1     1            1           3m35s
premapp   1/1     1            1           6s
[spinnaker@client ashu]$ 

```

### Lets expose app to external world 

```
[spinnaker@client ashu]$ kubectl  expose deployment  ashuapp  --type NodePort --port 80 --dry-run=client -o yaml >expose.yaml
[spinnaker@client ashu]$ kubectl apply -f  expose.yaml 
service/ashuapp created
[spinnaker@client ashu]$ kubectl  get  service
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
ashuapp      NodePort    10.96.168.146   <none>        80:30640/TCP   24s
```


## Lets deploy spinnaker to kubernetes cluster 

### creating halyard container -- the installer of spinnaker 

```
mkdir  ~/.hal
docker run -d --name  halyard -v  ~/.hal:/home/spinnaker/.hal -v ~/.kube/config:/home/spinnaker/.kube/config   --restart  always gcr.io/spinnaker-marketplace/halyard:stable
```

### creating namesapce for spinnaker installation 

```
[ec2-user@client ~]$ docker  exec -it halyard bash 
bash-5.0$ 
bash-5.0$ 
bash-5.0$ hal -v
1.42.0-20210408182114
bash-5.0$ 
bash-5.0$ kubectl get nodes
NAME            STATUS   ROLES           AGE     VERSION
control-plane   Ready    control-plane   2d12h   v1.25.0
node1           Ready    <none>          2d11h   v1.25.0
node2           Ready    <none>          2d11h   v1.25.0
bash-5.0$ 
bash-5.0$ 
bash-5.0$ kubectl create ns  spinnaker 
namespace/spinnaker created
bash-5.0$ 


```

### add k8s to halyard 

```
bash-5.0$ hal config provider kubernetes enable
+ Get current deployment
  Success
+ Edit the kubernetes provider
  Success
Validation in default:
- WARNING You have not yet selected a version of Spinnaker to
  deploy.
? Options include: 
  - 1.28.1
  - 1.27.1
  - 1.26.7
  - 1.25.7
  - 1.24.6

```

###  setting namespace default and using in halyard configuration 

```
kubectl config set-context --current --namespace spinnaker

bash-5.0$ hal config provider kubernetes account add  ashu-k8s-acc  --context $(kubectl config current-context)
+ Get current deployment
  Success
+ Add the ashu-k8s-acc account
  Success
Validation in default:
- WARNING You have not yet selected a version of Spinnaker to
  deploy.
? Options include: 
  - 1.28.1
  - 1.27.1
  - 1.26.7
  - 1.25.7
  - 1.24.6
  - 1.23.7

Validation in halconfig:
- WARNING There is a newer version of Halyard available (1.49.0),
  please update when possible
? Run 'sudo apt-get update && sudo apt-get install
  spinnaker-halyard -y' to upgrade

+ Successfully added account ashu-k8s-acc for provider
  kubernetes.

```

#### enable artifacts in k8s halyard

```
bash-5.0$ hal config features edit --artifacts  true
+ Get current deployment
  Success
+ Get features
  Success
+ Edit features
  Success
Validation in default:
- WARNING You have not yet selected a version of Spinnaker to
  deploy.
? Options include: 
  - 1.28.1
  - 1.27.1
  - 1.26.7
  - 1.25.7
  - 1.24.6
  - 1.23.7

Validation in halconfig:
- WARNING There is a newer version of Halyard available (1.49.0),
  please update when possible
? Run 'sudo apt-get update && sudo apt-get install
  spinnaker-halyard -y' to upgrade

+ Successfully updated features.

```

### choosing distributed Env 

```
bash-5.0$ hal config deploy edit --type distributed --account-name ashu-k8s-acc 
+ Get current deployment
  Success
+ Get the deployment environment
  Success
+ Edit the deployment environment
  Success
Validation in default:
- WARNING You have not yet selected a version of Spinnaker to
  deploy.
? Options include: 
  - 1.28.1
  - 1.27.1
  - 1.26.7

```

### storage s3 for spinnaker 

```
bash-5.0$ hal config storage s3 edit  --access-key-id  AKIA25YZWFYDTRSR6V6K  --secret-access-key  --region  us-west-2 
Your AWS Secret Key.: 
+ Get current deployment
  Success
+ Get persistent store
  Success
Generated bucket name: spin-019a4ec2-0860-4463-9c81-f5a73e2334ec
+ Edit persistent store
  Success


====
bash-5.0$ hal config storage edit --type s3
+ Get current deployment
  Success
+ Get persistent storage settings
  Success
+ Edit persistent storage settings
  Success
Validation in default:
- WARNING You have not yet selected a version of Spinnaker to
  deploy.
? Options include: 

```

### adding version 

```
bash-5.0$ hal config version edit --version 1.25.7 
+ Get current deployment
  Success
+ Edit Spinnaker version
  Success
Validation in halconfig:
- WARNING There is a newer version of Halyard available (1.49.0),
  please update when possible
? Run 'sudo apt-get update && sudo apt-get install
  spinnaker-halyard -y' to upgrade

+ Spinnaker has been configured to update/install version "1.25.7".
  Deploy this version of Spinnaker with `hal deploy apply`.
```

### deploy spinnaker 

```
  version 1.25.7: Artifacts are now enabled by default.
? You no longer need this.

Validation in default.stats:
- INFO Stats are currently ENABLED. Usage statistics are being
  collected. Thank you! These stats inform improvements to the product, and that
  helps the community. To disable, run `hal config stats disable`. To learn more
  about what and how stats data is used, please see
  https://www.spinnaker.io/community/stats.

Validation in halconfig:
- WARNING There is a newer version of Halyard available (1.49.0),
  please update when possible
? Run 'sudo apt-get update && sudo apt-get install
  spinnaker-halyard -y' to upgrade


```

### checking in k8s cluster about spinnaker components 

```
[ec2-user@client ~]$ kubectl  get  deploy -n spinnaker 
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
spin-clouddriver   1/1     1            1           34m
spin-deck          1/1     1            1           34m
spin-echo          1/1     1            1           34m
spin-front50       1/1     1            1           34m
spin-gate          1/1     1            1           34m
spin-orca          1/1     1            1           34m
spin-redis         1/1     1            1           34m
spin-rosco         1/1     1            1           34m
[ec2-user@client ~]$ kubectl  get service   -n spinnaker 
NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
spin-clouddriver   ClusterIP   10.101.45.207    <none>        7002/TCP   34m
spin-deck          ClusterIP   10.100.125.30    <none>        9000/TCP   34m
spin-echo          ClusterIP   10.101.185.242   <none>        8089/TCP   34m
spin-front50       ClusterIP   10.102.114.175   <none>        8080/TCP   34m
spin-gate          ClusterIP   10.107.61.251    <none>        8084/TCP   34m
spin-orca          ClusterIP   10.99.19.54      <none>        8083/TCP   34m
spin-redis         ClusterIP   10.105.117.110   <none>        6379/TCP   34m
spin-rosco         ClusterIP   10.109.43.98     <none>        8087/TCP   34m
[ec2-user@client ~]$ 

```

### changing service type of deck to Ndoeport / Loadbalancer

```
kubectl edit service  spin-deck 
```

### all service type 

```
[ec2-user@client ~]$ kubectl  get service   -n spinnaker 
NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
spin-clouddriver   ClusterIP   10.101.45.207    <none>        7002/TCP         40m
spin-deck          NodePort    10.100.125.30    <none>        9000:30770/TCP   40m
spin-echo          ClusterIP   10.101.185.242   <none>        8089/TCP         40m
spin-front50       ClusterIP   10.102.114.175   <none>        8080/TCP         40m
spin-gate          NodePort    10.107.61.251    <none>        8084:32247/TCP   40m
spin-orca          ClusterIP   10.99.19.54      <none>        8083/TCP         40m
spin-redis         ClusterIP   10.105.117.110   <none>        6379/TCP         40m
spin-rosco         ClusterIP   10.109.43.98     <none>        8087/TCP         40m
[ec2-user@client ~]$ 

```

### update URL of deck and gate

```
 25   hal config security ui edit --override-base-url "http://34.233.5.195:30770"
   26  history 
   27   hal config security api edit --override-base-url "http://34.233.5.195:32247"
   28  hal deploy apply 
```

### Spinnaker application and Pipeline 

<img src="app_line.png">


## Now performing CI option with Jenkins 

### setting up Vscode with Docker & jenkins Instances 

<img src="vscode.png">

### app test and deploy

<img src="appd1.png">

## jenkins integration with Spinnaker 

### using halyard to configure 

```
 32  hal config ci jenkins enable
   33  hal config ci jenkins master add  ashu-jenkins  --address http://54.210.210.201:8080/  --username admin --password
     38  hal config ci jenkins master edit   ashu-jenkins --csrf  true 
     hal deploy apply 
```


### spinnaker Igor for Trigger 

<img src="igor.png">




