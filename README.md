## Training schedule 

<img src="sch.png">

## Spinnaker on EKS -- 

### Deck COmponent on EKS 

<img src="deckeks.png">

### avoid multiple LB for internal spinnaker components we can use Ingress controller 

<img src="ingress.png">

### gate component 

<img src="gate.png">

### verify on k8s 

```
[salesforce@spinnaker ~]$ kubectl  get  ns
NAME              STATUS   AGE
ashu-project      Active   3d15h
default           Active   9d
ingress-nginx     Active   3d13h
kube-node-lease   Active   3d20h
kube-public       Active   9d
kube-system       Active   9d
spinnaker         Active   21h
[salesforce@spinnaker ~]$ kubectl  get  deploy  -n spinnaker  
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
spin-clouddriver   1/1     1            1           21h
spin-deck          1/1     1            1           21h
spin-echo          1/1     1            1           21h
spin-front50       1/1     1            1           21h
spin-gate          1/1     1            1           21h
spin-igor          1/1     1            1           132m
spin-orca          1/1     1            1           21h
spin-redis         1/1     1            1           21h
spin-rosco         1/1     1            1           21h
[salesforce@spinnaker ~]$ kubectl  get  svc  -n spinnaker  
NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
spin-clouddriver   ClusterIP   10.101.53.124    <none>        7002/TCP         21h
spin-deck          NodePort    10.101.175.70    <none>        9000:31595/TCP   21h
spin-echo          ClusterIP   10.103.87.98     <none>        8089/TCP         21h
spin-front50       ClusterIP   10.105.196.153   <none>        8080/TCP         21h
spin-gate          NodePort    10.97.52.168     <none>        8084:32570/TCP   21h
spin-igor          ClusterIP   10.105.55.223    <none>        8088/TCP         133m
spin-orca          ClusterIP   10.104.160.143   <none>        8083/TCP         21h
spin-redis         ClusterIP   10.96.195.111    <none>        6379/TCP         21h
spin-rosco         ClusterIP   10.107.131.39    <none>        8087/TCP         21h
```

### spinnaker provider env 

<img src="spinpro.png">

### aws -- intergration  managing and managed accounts 

<img src="aws.png">


## halyard commands -- for spinnaker --

### checking existing config details 

```
[salesforce@spinnaker ~]$ docker  exec  -it  halyard bash 
bash-5.0$ hal config list 
+ Get all deployment configurations
  Success
+ Retrieved all deployments.
- name: default
  version: 1.26.7
  providers:
    appengine:
      enabled: false

```

### spinnaker with docker & with jenkins and docker hub 

<img src="spd.png">


### Deploying eks cluster from spinnaker halyard instances using eksctl 

```
bash-5.0$ eksctl create cluster --name=eks-spinnaker --nodes=2  --region=us-east-1 --write-kubeconfig=false 
2022-09-23 05:08:08 [ℹ]  eksctl version 0.112.0
2022-09-23 05:08:08 [ℹ]  using region us-east-1
2022-09-23 05:08:08 [ℹ]  setting availability zones to [us-east-1a us-east-1c]
2022-09-23 05:08:08 [ℹ]  subnets for us-east-1a - public:192.168.0.0/19 private:192.168.64.0/19
2022-09-23 05:08:08 [ℹ]  subnets for us-east-1c - public:192.168.32.0/19 private:192.168.96.0/19
2022-09-23 05:08:08 [ℹ]  nodegroup "ng-3b8c255e" will use "" [AmazonLinux2/1.22]
2022-09-23 05:08:08 [ℹ]  using Kubernetes version 1.22
2022-09-23 05:08:08 [ℹ]  creating EKS cluster "eks-spinnaker" in "us-east-1" region with
```

### adding EKS in existing Spinnaker halyard --kubeconfig 

```
bash-5.0$ kubectl config get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   spinnaker
bash-5.0$ 
bash-5.0$ 
bash-5.0$ aws eks update-kubeconfig --name eks-spinnaker-new --region=us-east-1  --alias eks-spinnaker
Added new context eks-spinnaker to /home/spinnaker/.kube/config
bash-5.0$ 
bash-5.0$ 
bash-5.0$ kubectl config get-contexts 
CURRENT   NAME                          CLUSTER                                                        AUTHINFO                                                       NAMESPACE
*         eks-spinnaker                 arn:aws:eks:us-east-1:751136288263:cluster/eks-spinnaker-new   arn:aws:eks:us-east-1:751136288263:cluster/eks-spinnaker-new   
          kubernetes-admin@kubernetes   kubernetes                                                     kubernetes-admin                                               spinnaker

```

### switching betweekn k8s cluster --

```
205  aws eks update-kubeconfig --name eks-spinnaker-new --region=us-east-1  --alias eks-spinnaker
  206  kubectl config get-contexts 
  207  history 
  208  kubectl config get-contexts 
  209  kubectl  get nodes
  210  kubectl config use-context  spinnaker
  211  kubectl config use-context   kubernetes-admin@kubernetes
  212  kubectl  get nodes
  213  history 
bash-5.0$ 
bash-5.0$ kubectl  get nodes
NAME            STATUS   ROLES           AGE   VERSION
control-plane   Ready    control-plane   9d    v1.25.0
node1           Ready    <none>          9d    v1.25.0
node2           Ready    <none>          9d    v1.25.0
node3           Ready    <none>          9d    v1.25.0
bash-5.0$ kubectl config use-context   eks-spinnaker
Switched to context "eks-spinnaker".
bash-5.0$ 
bash-5.0$ kubectl  get nodes
NAME                             STATUS   ROLES    AGE   VERSION
ip-192-168-21-37.ec2.internal    Ready    <none>   14m   v1.22.12-eks-ba74326
ip-192-168-61-165.ec2.internal   Ready    <none>   14m   v1.22.12-eks-ba74326
bash-5.0$ 

```

### adding EKS in Halyard configuration 

```
bash-5.0$ CONTEXT=$(kubectl config current-context)
bash-5.0$ 
bash-5.0$ hal config provider kubernetes account add eks-spinnaker --context $CONTEXT
+ Get current deployment
  Success
+ Add the eks-spinnaker account
  Success
+ Successfully added account eks-spinnaker for provider
  kubernetes.

```

### finally adding eks in halyard config 

```
bash-5.0$ hal deploy apply 
+ Get current deployment
  Success
+ Prep deployment
  Success
Validation in default.features:
- WARNING Field Features.artifacts not supported for Spinnaker
  version 1.26.7: Artifacts are now enabled by default.
? You no longer need this.

Validation in default.stats:
- INFO Stats are currently ENABLED. Usage statistics are being
  collected. Thank you! These stats inform improvements to the product, and that
  helps the community. To disable, run `hal config stats disable`. To learn more
  about what and how stats data is used, please see
  https://spinnaker.io/docs/community/stay-informed/stats.

+ Preparation complete... deploying Spinnaker
+ Get current deployment
  Success

```

### Now creating pipelines using application 

<img src="apps.png">

### creating gitrepo and updating YAML 

```
24  kubectl create  ns ashu-dev-ns --dry-run=client -o yaml 
   
   26  kubectl  create  deployment  ashu-app --image=dockerashu/ashumobiwebapp:6  --replicas=3 --port 80  --namespace ashu-dev-ns  --dry-run=client -o yaml 
   27  history 
   28  kubectl create  service nodeport  ashusvc1 --tcp 1234:80 --namespace ashu-dev-ns --dry-run=client -o yaml 
```

### auto trigger concept in spinnaker 

<img src="trigger.png">

### verify it 

```
salesforce@spinnaker ~]$ kubectl  get  po -n spinnaker 
NAME                                READY   STATUS    RESTARTS       AGE
spin-clouddriver-855b56d75f-j5sww   1/1     Running   0              60m
spin-deck-85bb5b7975-nq9vs          1/1     Running   3 (3h3m ago)   19h
spin-echo-6c75749dbd-mrtkv          1/1     Running   1 (3h3m ago)   4h45m
spin-front50-797bbb5d79-wvzpp       1/1     Running   1 (3h3m ago)   4h45m
spin-gate-548d4f75-wqj9r            1/1     Running   1 (3h3m ago)   4h45m
spin-igor-6f5d6998f-t4rhb           1/1     Running   1 (3h3m ago)   4h41m
spin-orca-57b49cf44-8r54f           1/1     Running   1 (3h3m ago)   4h45m
spin-redis-869dfc57c5-jv52l         1/1     Running   4 (3h3m ago)   24h
spin-rosco-6c485687d-5wdt5          1/1     Running   1 (3h3m ago)   4h45m
[salesforce@spinnaker ~]$ kubectl  get  svc  -n spinnaker 
NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
spin-clouddriver   ClusterIP   10.101.53.124    <none>        7002/TCP         24h
spin-deck          NodePort    10.101.175.70    <none>        9000:31595/TCP   24h
spin-echo          ClusterIP   10.103.87.98     <none>        8089/TCP         24h
spin-front50       ClusterIP   10.105.196.153   <none>        8080/TCP         24h
spin-gate          NodePort    10.97.52.168     <none>        8084:32570/TCP   24h
spin-igor          ClusterIP   10.105.55.223    <none>        8088/TCP         4h46m
spin-orca          ClusterIP   10.104.160.143   <none>        8083/TCP         24h
spin-redis         ClusterIP   10.96.195.111    <none>        6379/TCP         24h
spin-rosco         ClusterIP   10.107.131.39    <none>        8087/TCP         24h
[salesforce@spinnaker ~]$ kubectl  get  ep spin-igor  -n spinnaker 
NAME        ENDPOINTS             AGE
spin-igor   192.168.104.45:8088   4h46m
[salesforce@spinnaker ~]$ kubectl  get  po   spin-igor-6f5d6998f-t4rhb -o wide -n spinnaker 
NAME                        READY   STATUS    RESTARTS       AGE     IP               NODE    NOMINATED NODE   READINESS GATES
spin-igor-6f5d6998f-t4rhb   1/1     Running   1 (3h5m ago)   4h42m   192.168.104.45   node2   <none>           <none>
[salesforce@spinnaker ~]$ 

```

### adding github account to halyard config 

```
bash-5.0$ echo "Mto1L7k1gxskdjlsdfjsldkf" >/tmp/ashu_token
bash-5.0$ 
bash-5.0$ hal config artifact github enable 
+ Get current deployment
  Success
+ Edit the github provider
  Success
+ Successfully enabled github
bash-5.0$ 
bash-5.0$ hal config artifact github account  add ashu-git-artifact-account  --token-file /tmp/ashu_token 
+ Get current deployment
  Success
+ Add the ashu-git-artifact-account artifact account
  Success
+ Successfully added artifact account ashu-git-artifact-account for
  artifact provider github.
```

### lets apply changes 

```
bash-5.0$ hal deploy apply 
+ Get current deployment
  Success
+ Prep deployment
  Success
Validation in default.features:
- WARNING Field Features.artifacts not supported for Spinnaker
  version 1.26.7: Artifacts are now enabled by default.
? You no longer need this.

Validation in default.stats:
- INFO Stats are currently ENABLED. Usage statistics are being
  collected. Thank you! These stats inform improvements to the product, and that
  helps the community. To disable, run `hal config stats disable`. To learn more
  about what and how stats data is used, please see
  https://spinnaker.io/docs/community/stay-informed/stats.

+ Preparation complete... deploying Spinnaker
+ Get current deployment
  Success
+ Apply deployment
  Success
+ Deploy spin-redis
  Success
+ Deploy spin-clouddriver
  Success
+ Deploy spin-front50
  Success
+ Deploy spin-orca
  Success
+ Deploy spin-deck
  Success
+ Deploy spin-echo
  Success
+ Deploy spin-gate
  Success
+ Deploy spin-igor
  Success
+ Deploy spin-rosco
  Success
+ Run `hal deploy connect` to connect to Spinnaker.
```


