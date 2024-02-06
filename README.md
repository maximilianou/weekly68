# weekly68

FluxCD, git, linux,  kubernetes

---

### Install Lightweight Kubernetes k3s

https://docs.k3s.io/quick-start


```sh
curl -sfL https://get.k3s.io | sh -
..
[INFO]  Installing k3s to /usr/local/bin/k3s
[INFO]  Skipping installation of SELinux RPM
[INFO]  Creating /usr/local/bin/kubectl symlink to k3s
[INFO]  Creating /usr/local/bin/crictl symlink to k3s
[INFO]  Skipping /usr/local/bin/ctr symlink to k3s, command exists in PATH at /usr/bin/ctr
[INFO]  Creating killall script /usr/local/bin/k3s-killall.sh
[INFO]  Creating uninstall script /usr/local/bin/k3s-uninstall.sh
[INFO]  env: Creating environment file /etc/systemd/system/k3s.service.env
[INFO]  systemd: Creating service file /etc/systemd/system/k3s.service
[INFO]  systemd: Enabling k3s unit
Created symlink /etc/systemd/system/multi-user.target.wants/k3s.service → /etc/systemd/system/k3s.service.
[INFO]  systemd: Starting k3s
```

```sh
┌──(root㉿kali)-[/home/kali/projects]
└─# cat /etc/rancher/k3s/k3s.yaml >> /home/kali/.kube/config

┌──(root㉿kali)-[/home/kali/projects]
└─# chown kali:kali /home/kali/.kube/config  

```

```sh
┌──(kali㉿kali)-[~/projects]
└─$ kubectl get all 
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   13m
```


```sh
┌──(kali㉿kali)-[~/projects]
└─$ kubectl get all --all-namespaces
NAMESPACE     NAME                                          READY   STATUS             RESTARTS   AGE
kube-system   pod/helm-install-traefik-crd-59trg            0/1     Completed          0          13m
kube-system   pod/helm-install-traefik-clh9n                0/1     Completed          2          13m
kube-system   pod/svclb-traefik-b4aa3510-qgrwl              2/2     Running            0          11m
kube-system   pod/traefik-f4564c4f4-tkv45                   1/1     Running            0          11m
kube-system   pod/coredns-6799fbcd5-qdqkh                   0/1     ImagePullBackOff   0          13m
kube-system   pod/metrics-server-67c658944b-wx542           0/1     ImagePullBackOff   0          13m
kube-system   pod/local-path-provisioner-84db5d44d9-hbpqq   0/1     ImagePullBackOff   0          13m

NAMESPACE     NAME                     TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)                      AGE
default       service/kubernetes       ClusterIP      10.43.0.1       <none>            443/TCP                      13m
kube-system   service/kube-dns         ClusterIP      10.43.0.10      <none>            53/UDP,53/TCP,9153/TCP       13m
kube-system   service/metrics-server   ClusterIP      10.43.231.191   <none>            443/TCP                      13m
kube-system   service/traefik          LoadBalancer   10.43.108.153   192.168.1.134     80:31042/TCP,443:30918/TCP   11m

NAMESPACE     NAME                                    DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
kube-system   daemonset.apps/svclb-traefik-b4aa3510   1         1         1       1            1           <none>          11m

NAMESPACE     NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/traefik                  1/1     1            1           11m
kube-system   deployment.apps/metrics-server           0/1     1            0           13m
kube-system   deployment.apps/local-path-provisioner   0/1     1            0           13m
kube-system   deployment.apps/coredns                  0/1     1            0           13m

NAMESPACE     NAME                                                DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/local-path-provisioner-84db5d44d9   1         1         0       13m
kube-system   replicaset.apps/coredns-6799fbcd5                   1         1         0       13m
kube-system   replicaset.apps/metrics-server-67c658944b           1         1         0       13m
kube-system   replicaset.apps/traefik-f4564c4f4                   1         1         1       11m

NAMESPACE     NAME                                 COMPLETIONS   DURATION   AGE
kube-system   job.batch/helm-install-traefik-crd   1/1           72s        13m
kube-system   job.batch/helm-install-traefik       1/1           87s        13m
```


---


### Install fluxcd

https://fluxcd.io/flux/get-started/

```sh
curl -s https://fluxcd.io/install.sh | bash
```

### Deploy Flux on a cluster the GitOps way.

https://fluxcd.io/flux/cmd/flux_bootstrap/

https://fluxcd.io/flux/cmd/flux_bootstrap_github/

```sh
GITHUB_TOKEN=ght...

┌──(kali㉿kali)-[~/projects/weekly68]
└─$ echo $GITHUB_TOKEN | flux bootstrap github --owner=maximilianou --repository=weekly68-3 --path=kube01/default --personal
Please enter your GitHub personal access token (PAT): 
► connecting to github.com
✔ repository "https://github.com/maximilianou/weekly68-3" created
► cloning branch "main" from Git repository "https://github.com/maximilianou/weekly68-3.git"
✔ cloned repository
► generating component manifests
✔ generated component manifests
✔ committed sync manifests to "main" ("ccac60376d688349759adbb8fff40c28433ce256")
► pushing component manifests to "https://github.com/maximilianou/weekly68-3.git"
► installing components in "flux-system" namespace
✔ installed components
✔ reconciled components
► determining if source secret "flux-system/flux-system" exists
► generating source secret
✔ public key: ecdsa-sha2-nistp384 AAAAE2VjZHNhLXNoYTItbmlzdHAzODQAAAAIbmlzdHAzODQAAABhBCdzAXDb2R6ve7tKYg/g83klDu2QUh/Mu1xlt9SAHLT0cck54sxugTzenjSWbeICjoZm+Bf/V+Ch/Hv8A2Y2plqFn6P0Wh47IAnWS6kqwU0brEnfRPdpPXtC/iwLWQVKXg==
✔ configured deploy key "flux-system-main-flux-system-./kube01/default" for "https://github.com/maximilianou/weekly68-3"
► applying source secret "flux-system/flux-system"
✔ reconciled source secret
► generating sync manifests
✔ generated sync manifests
✔ committed sync manifests to "main" ("88f9d48560bb897e4c9ec1454769909873021757")
► pushing sync manifests to "https://github.com/maximilianou/weekly68-3.git"
► applying sync manifests
✔ reconciled sync configuration
◎ waiting for Kustomization "flux-system/flux-system" to be reconciled
✗ client rate limiter Wait returned an error: context deadline exceeded
► confirming components are healthy
✔ helm-controller: deployment ready
✔ kustomize-controller: deployment ready
✔ notification-controller: deployment ready
✗ source-controller: deployment not ready
✗ bootstrap failed with 2 health check failure(s)

```


```sh
┌──(kali㉿kali)-[~/projects/weekly68]
└─$ kubectl get all --namespace flux-system
NAME                                          READY   STATUS             RESTARTS   AGE
pod/notification-controller-76dc5d768-8x7vd   1/1     Running            0          16m
pod/kustomize-controller-9c588946c-sdlbn      1/1     Running            0          16m
pod/helm-controller-5f964c6579-psjnp          1/1     Running            0          16m
pod/source-controller-6c49485888-6tzc8        0/1     ImagePullBackOff   0          16m

NAME                              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/notification-controller   ClusterIP   10.43.162.48    <none>        80/TCP    16m
service/source-controller         ClusterIP   10.43.181.244   <none>        80/TCP    16m
service/webhook-receiver          ClusterIP   10.43.104.42    <none>        80/TCP    16m

NAME                                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/notification-controller   1/1     1            1           16m
deployment.apps/kustomize-controller      1/1     1            1           16m
deployment.apps/helm-controller           1/1     1            1           16m
deployment.apps/source-controller         0/1     1            0           16m

NAME                                                DESIRED   CURRENT   READY   AGE
replicaset.apps/source-controller-6c49485888        1         1         0       16m
replicaset.apps/notification-controller-76dc5d768   1         1         1       16m
replicaset.apps/kustomize-controller-9c588946c      1         1         1       16m
replicaset.apps/helm-controller-5f964c6579          1         1         1       16m
                                                                                         
```


```sh
┌──(kali㉿kali)-[~/projects]
└─$ git clone https://github.com/maximilianou/weekly68-3

┌──(kali㉿kali)-[~/projects/weekly68-3]
└─$ cd weekly68-3   

┌──(kali㉿kali)-[~/projects/weekly68-3]
└─$ tree                                                
.
└── kube01
    └── default
        └── flux-system
            ├── gotk-components.yaml
            ├── gotk-sync.yaml
            └── kustomization.yaml

4 directories, 3 files

┌──(kali㉿kali)-[~/projects/weekly68-3]
└─$ cat kube01/default/flux-system/kustomization.yaml 
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- gotk-components.yaml
- gotk-sync.yaml
                                                                                                                                                        
┌──(kali㉿kali)-[~/projects/weekly68-3]
└─$ cat kube01/default/flux-system/gotk-sync.yaml    
# This manifest was generated by flux. DO NOT EDIT.
---
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: flux-system
  namespace: flux-system
spec:
  interval: 1m0s
  ref:
    branch: main
  secretRef:
    name: flux-system
  url: ssh://git@github.com/maximilianou/weekly68-3
---
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: flux-system
  namespace: flux-system
spec:
  interval: 10m0s
  path: ./kube01/default
  prune: true
  sourceRef:
    kind: GitRepository
    name: flux-system
```

- infra.yml
  - infra/
    - ingress nginx
    - metallb
- app.yml
  - app/
    - app-a-ns.yml
    - app-a.yml
    - app-a-ingress.yml

---




---





