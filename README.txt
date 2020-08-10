


----------------MINI-PROJET: Deployez Wordpress à l'aide de manifests (et non par helm)------------------


1/• Déployez wordpress en suivant les étapes suivantes
   • Créez un deployment mysql avec un seul replicat
   • Créez un service de type clusterIP pour exposer vos pods mysql
   • Créez un deployment wordpress avec les bonnes variables d’environnement pour se connecter à la base
     de données mysql
   • Votre deployment devra stocker les données de wordpress sur un volume mounté dans le /data de votre 
     nœud
   • Créez un service de type nodeport pour exposer le frontend wordpress
• Nous vous conseillons d’utiliser les manifests pour réaliser cet exercice
• Grâce à ce travail vous comprendrez mieux comment les fichiers contenu dans le chart wordpress
• A la fin de votre travail, poussez vos manifests sur github et envoyez nous le lien de votre repo à
   eazytrainingfr@gmail.com et nous vous dirons si votre solution respecte les bonnes pratiques et si 
   votre solution bonne. Nous vous proposerons aussi notre solution/


        
------------------------------------------------------------------------------------
------------------------------------------------------------------------------------

                             CORRECTION FINAL DU MINI PROJET

-------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------


----------------------------PREREQUIS: INSTALLATION MINIKUBE-------------------------

yum -y update
yum -y install epel-release
yum -y install libvirt qemu-kvm virt-install virt-top libguestfs-tools bridge-utils
yum install socat -y
yum install -y conntrack
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
usermod -aG docker centos
systemctl start docker
yum -y install wget
wget https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube-linux-amd64
mv minikube-linux-amd64 /usr/bin/minikube
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x kubectl
mv kubectl /usr/bin/
echo ‘1’ > /proc/sys/net/bridge/bridge-nf-call-iptables
systemctl enable docker.service

minikube start –vm-driver=none

systemctl enable kubelet.service


--------commande de vérification---------

$ kubectl get nodes
NAME                            STATUS   ROLES    AGE   VERSION
ip-172-31-67-197.ec2.internal   Ready    master   18h   v1.18.3

$ docker ps

CONTAINER ID        IMAGE                                     COMMAND                  CREATED             STATUS              PORTS               NAMES
1f4a6a704361        wordpress                                 "docker-entrypoint.s…"   18 hours ago        Up 18 hours                             k8s_wordpress_wordpress-deployment-5bdb55fc9d-sczn6_default_91d6eb68-48f1-470d-92fb-c3af1c9c74af_0
312de6c8b89c        k8s.gcr.io/pause:3.2                      "/pause"                 18 hours ago        Up 18 hours                             k8s_POD_wordpress-deployment-5bdb55fc9d-sczn6_default_91d6eb68-48f1-470d-92fb-c3af1c9c74af_0
d529f60f940e        mysql                                     "docker-entrypoint.s…"   18 hours ago        Up 18 hours                             k8s_mysql-db_mysql-deployment-fd4fcbd97-r9mmm_default_bdd39080-084e-43b0-b341-0864bf26b97c_0
7efd4bdde926        k8s.gcr.io/pause:3.2                      "/pause"                 18 hours ago        Up 18 hours                             k8s_POD_mysql-deployment-fd4fcbd97-r9mmm_default_bdd39080-084e-43b0-b341-0864bf26b97c_0
4aa0091a923b        gcr.io/k8s-minikube/storage-provisioner   "/storage-provisioner"   18 hours ago        Up 18 hours                             k8s_storage-provisioner_storage-provisioner_kube-system_84d7c9e2-1fa2-4b7f-ac6a-533af980e4a4_0
4ceb066b988d        67da37a9a360                              "/coredns -conf /etc…"   18 hours ago        Up 18 hours                             k8s_coredns_coredns-66bff467f8-l2znf_kube-system_84de00b4-6a55-4900-a739-1ea332753805_0
c65e3f4722dd        3439b7546f29                              "/usr/local/bin/kube…"   18 hours ago        Up 18 hours                             k8s_kube-proxy_kube-proxy-p5rq2_kube-system_638c340b-7653-4db8-99da-148cf2107658_0
ade95ed7f278        k8s.gcr.io/pause:3.2                      "/pause"                 18 hours ago        Up 18 hours                             k8s_POD_coredns-66bff467f8-l2znf_kube-system_84de00b4-6a55-4900-a739-1ea332753805_0
c0e02bd82bc8        k8s.gcr.io/pause:3.2                      "/pause"                 18 hours ago        Up 18 hours                             k8s_POD_storage-provisioner_kube-system_84d7c9e2-1fa2-4b7f-ac6a-533af980e4a4_0
a899e1e1dafa        k8s.gcr.io/pause:3.2                      "/pause"                 18 hours ago        Up 18 hours                             k8s_POD_kube-proxy-p5rq2_kube-system_638c340b-7653-4db8-99da-148cf2107658_0
f93f6c8a6bdb        303ce5db0e90                              "etcd --advertise-cl…"   18 hours ago        Up 18 hours                             k8s_etcd_etcd-ip-172-31-67-197.ec2.internal_kube-system_ea3e76f7a52242d2fb4c92ad9f96dbb2_0
ecfb455f9866        da26705ccb4b                              "kube-controller-man…"   18 hours ago        Up 18 hours                             k8s_kube-controller-manager_kube-controller-manager-ip-172-31-67-197.ec2.internal_kube-system_4693faf278aca6042fceca58ef2aabee_0
3ad2307a2618        76216c34ed0c                              "kube-scheduler --au…"   18 hours ago        Up 18 hours                             k8s_kube-scheduler_kube-scheduler-ip-172-31-67-197.ec2.internal_kube-system_dcddbd0cc8c89e2cbf4de5d3cca8769f_0
5943695bd0f9        7e28efa976bd                              "kube-apiserver --ad…"   18 hours ago        Up 18 hours                             k8s_kube-apiserver_kube-apiserver-ip-172-31-67-197.ec2.internal_kube-system_fb2cb5bad6dfcc36689af07cd4aadf0a_0
fe8c1507cf95        k8s.gcr.io/pause:3.2                      "/pause"                 18 hours ago        Up 18 hours                             k8s_POD_kube-scheduler-ip-172-31-67-197.ec2.internal_kube-system_dcddbd0cc8c89e2cbf4de5d3cca8769f_0
4203fb4db98f        k8s.gcr.io/pause:3.2                      "/pause"                 18 hours ago        Up 18 hours                             k8s_POD_kube-controller-manager-ip-172-31-67-197.ec2.internal_kube-system_4693faf278aca6042fceca58ef2aabee_0
cf19cd0045c1        k8s.gcr.io/pause:3.2                      "/pause"                 18 hours ago        Up 18 hours                             k8s_POD_kube-apiserver-ip-172-31-67-197.ec2.internal_kube-system_fb2cb5bad6dfcc36689af07cd4aadf0a_0
51339107aa45        k8s.gcr.io/pause:3.2                      "/pause"                 18 hours ago        Up 18 hours                             k8s_POD_etcd-ip-172-31-67-197.ec2.internal_kube-system_ea3e76f7a52242d2fb4c92ad9f96dbb2_0


$ docker images

REPOSITORY                                TAG                 IMAGE ID            CREATED             SIZE
wordpress                                 latest              2da59c54a06a        3 days ago          543MB
gcr.io/k8s-minikube/storage-provisioner   v2                  2d72ece3274d        5 days ago          31.6MB
mysql                                     latest              0d64f46acfd1        5 days ago          544MB
k8s.gcr.io/kube-proxy                     v1.18.3             3439b7546f29        2 months ago        117MB
k8s.gcr.io/kube-scheduler                 v1.18.3             76216c34ed0c        2 months ago        95.3MB
k8s.gcr.io/kube-controller-manager        v1.18.3             da26705ccb4b        2 months ago        162MB
k8s.gcr.io/kube-apiserver                 v1.18.3             7e28efa976bd        2 months ago        173MB
k8s.gcr.io/pause                          3.2                 80d28bedfe5d        5 months ago        683kB
k8s.gcr.io/coredns                        1.6.7               67da37a9a360        6 months ago        43.8MB
k8s.gcr.io/etcd                           3.4.3-0             303ce5db0e90        9 months ago        288MB




---------------------------CREATION DE VOLUME ET MANISFEST----------------------------

1/
$ yum install -y git 
$ mkdir /data_volume

$ ls 
get-docker.sh  mysql-deployment.yml  service-clusteIP.yml  service-nodePort.yml  wordpress-deployment.yml


$ vi mysql-deployment.yml
$ kubectl apply -f mysql-deployment.yml  

2/ 
$ vi service-clusteIP.yml
$ kubectl apply -f service-clusteIP.yml

3/
$ vi wordpress-deployment.yml
$ kubectl apply -f wordpress-deployment.yml

4/
$ vi service-nodePort.yml
$ kubectl apply -f service-nodePort.yml


------------Voir l'état des ressources --------------

$ kubectl get deployment -o wide   (voir l'état "deployment")
NAME                   READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES      SELECTOR
mysql-deployment       1/1     1            1           55m   mysql-db     mysql       app=mysql-db
wordpress-deployment   1/1     1            1           48m   wordpress    wordpress   app=wordpress


$ kubectl get pod (voir l'état "pod")
NAME                                    READY   STATUS    RESTARTS   AGE
mysql-deployment-fd4fcbd97-r9mmm        1/1     Running   0          57m
wordpress-deployment-5bdb55fc9d-sczn6   1/1     Running   0          30m


$ kubectl get replicaset -o wide  (voir l'état "replicaset")
mysql-deployment-fd4fcbd97        1         1         1       57m   mysql-db     mysql       app=mysql-db,pod-template-hash=fd4fcbd97
wordpress-deployment-5bdb55fc9d   1         1         1       31m   wordpress    wordpress   app=wordpress,pod-template-hash=5bdb55fc9d
wordpress-deployment-6d86f758cb   0         0         0       51m   wordpress    wordpress   app=wordpress,pod-template-hash=6d86f758cb


----Voir l'état du service----

$ kubectl get svc
kubernetes                   ClusterIP   10.96.0.1      <none>        443/TCP          56m
service-clusterip-mysql      ClusterIP   10.97.191.36   <none>        3306/TCP         45m
service-nodeport-wordpress   NodePort    10.103.91.53   <none>        8081:30016/TCP   41m

$ kubectl describe svc service-clusterip-mysql

Name:              service-clusterip-mysql
Namespace:         default
Labels:            <none>
Annotations:       Selector:  app=mysql-db
Type:              ClusterIP
IP:                10.97.191.36
Port:              <unset>  3306/TCP
TargetPort:        3306/TCP
Endpoints:         172.17.0.3:3306
Session Affinity:  None
Events:            <none>


$ kubectl describe svc service-nodeport-wordpress

Name:                     service-nodeport-wordpress
Namespace:                default
Labels:                   <none>
Annotations:              Selector:  app=wordpress
Type:                     NodePort
IP:                       10.103.91.53
Port:                     <unset>  8081/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30016/TCP
Endpoints:                172.17.0.4:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>



----------------------TEST DE L'APPLICATION----------------------------

Allez sur ce lien http://ec2-3-231-224-41.compute-1.amazonaws.com:30016 
pour pouvoir accès à wordpress.

























































