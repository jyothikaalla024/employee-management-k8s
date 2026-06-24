# Employee Management K8s Project
employee-management/
│
├── backend/
│   ├── src/
│   │   └── main/
│   │       ├── java/com/example/employee/
│   │       │   ├── EmployeeApplication.java
│   │       │   ├── controller/
│   │       │   │   └── EmployeeController.java
│   │       │   ├── entity/
│   │       │   │   └── Employee.java
│   │       │   ├── repository/
│   │       │   │   └── EmployeeRepository.java
│   │       │   └── service/
│   │       │       └── EmployeeService.java
│   │       │
│   │       └── resources/
│   │           └── application.properties
│   │
│   ├── Dockerfile
│   └── pom.xml
│
└── k8s/
    ├── mysql-secret.yaml
    ├── mysql-pvc.yaml
    ├── mysql-deployment.yaml
    ├── mysql-service.yaml
    ├── app-deployment.yaml
    └── app-service.yaml

Deploy to Kubernetes
kubectl apply -f mysql-secret.yaml

kubectl apply -f mysql-pvc.yaml

kubectl apply -f mysql-deployment.yaml

kubectl apply -f mysql-service.yaml

kubectl apply -f app-deployment.yaml

kubectl apply -f app-service.yaml

Verify
kubectl get pods

kubectl get svc

kubectl get pvc

terminal process 


k8s $ aws iam list-attached-role-policies \
>   --role-name AmazonEKSAutoClusterRole \
>   --output table
--------------------------------------------------------------------------------------------
|                                 ListAttachedRolePolicies                                 |
+------------------------------------------------------------------------------------------+
||                                    AttachedPolicies                                    ||
|+-------------------------------------------------------+--------------------------------+|
||                       PolicyArn                       |          PolicyName            ||
|+-------------------------------------------------------+--------------------------------+|
||  arn:aws:iam::aws:policy/AmazonEKSClusterPolicy       |  AmazonEKSClusterPolicy        ||
||  arn:aws:iam::aws:policy/AmazonEKSNetworkingPolicy    |  AmazonEKSNetworkingPolicy     ||
||  arn:aws:iam::aws:policy/AmazonEKSComputePolicy       |  AmazonEKSComputePolicy        ||
||  arn:aws:iam::aws:policy/AmazonEKSBlockStoragePolicy  |  AmazonEKSBlockStoragePolicy   ||
||  arn:aws:iam::aws:policy/AmazonEKSLoadBalancingPolicy |  AmazonEKSLoadBalancingPolicy  ||
|+-------------------------------------------------------+--------------------------------+|
k8s $ aws iam list-attached-role-policies \
>   --role-name AmazonEKSAutoClusterRole > policies.json
k8s $ kubectl rollout restart deployment employee-app
deployment.apps/employee-app restarted

k8s $ TOKEN=$(aws ecr get-login-password --region us-east-1)
k8s $ kubectl create secret docker-registry regcred \
>   --docker-server=986119050506.dkr.ecr.us-east-1.amazonaws.com \
>   --docker-username=AWS \
>   --docker-password="$TOKEN"
secret/regcred created
k8s $ kubectl delete secret regcred
secret "regcred" deleted from default namespace
k8s $ aws ecr get-login-password --region us-east-1 | \
>   kubectl create secret docker-registry regcred \
>     --docker-server=986119050506.dkr.ecr.us-east-1.amazonaws.com \
>     --docker-username=AWS \
>     --docker-password-stdin
error: unknown flag: --docker-password-stdin
See 'kubectl create secret docker-registry --help' for usage.
Exception ignored while flushing sys.stdout:
BrokenPipeError: [Errno 32] Broken pipe
k8s $ TOKEN=$(aws ecr get-login-password --region us-east-1)
k8s $ kubectl create secret docker-registry regcred \
>   --docker-server=986119050506.dkr.ecr.us-east-1.amazonaws.com \
>   --docker-username=AWS \
>   --docker-password="$TOKEN"
secret/regcred created
k8s $ kubectl patch deployment employee-app \
>   -p '{"spec":{"template":{"spec":{"imagePullSecrets":[{"name":"regcred"}]}}}}'
deployment.apps/employee-app patched
k8s $ kubectl get pods -w
NAME                            READY   STATUS                       RESTARTS   AGE
employee-app-75958bbdf5-8q49j   0/1     ImagePullBackOff             0          66m
employee-app-7bd7f5f686-trdkh   0/1     CreateContainerConfigError   0          28s
employee-app-9c785ccf6-vmcwl    0/1     ImagePullBackOff             0          12m
nginx-test-6ff8854996-zbh6s     1/1     Running                      0          33m
kubectl describe pod -l app=employee-app | grep -A 20 "Events:"
employee-app-75958bbdf5-8q49j   0/1     ErrImagePull                 0          67m
employee-app-75958bbdf5-8q49j   0/1     ImagePullBackOff             0          67m
^Ck8s $ kubectl describe pod -l app=employee-app | grep -A 20 "Events:"
Events:
  Type     Reason   Age                   From     Message
  ----     ------   ----                  ----     -------
  Normal   BackOff  3m5s (x285 over 68m)  kubelet  Back-off pulling image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest"
  Warning  Failed   3m5s (x285 over 68m)  kubelet  Error: ImagePullBackOff


Name:             employee-app-7bd7f5f686-trdkh
Namespace:        default
Priority:         0
Service Account:  default
Node:             i-0cbbf0a8ee0fee6ff/172.31.31.120
Start Time:       Wed, 24 Jun 2026 16:01:38 +0000
Labels:           app=employee-app
                  pod-template-hash=7bd7f5f686
                  topology.kubernetes.io/region=us-east-1
                  topology.kubernetes.io/zone=us-east-1c
Annotations:      kubectl.kubernetes.io/restartedAt: 2026-06-24T15:49:54Z
Status:           Pending
IP:               172.31.27.80
IPs:
--
Events:
  Type     Reason     Age                 From               Message
  ----     ------     ----                ----               -------
  Normal   Scheduled  2m9s                default-scheduler  Successfully assigned default/employee-app-7bd7f5f686-trdkh to i-0cbbf0a8ee0fee6ff
  Normal   Pulled     2m                  kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 5.158s (5.158s including waiting). Image size: 209903068 bytes.
  Normal   Pulled     119s                kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 108ms (108ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     107s                kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 116ms (116ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     92s                 kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 185ms (185ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     80s                 kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 100ms (100ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     68s                 kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 120ms (120ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     41s                 kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 148ms (148ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     16s (x2 over 53s)   kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 127ms (127ms including waiting). Image size: 209903068 bytes.
  Normal   Pulling    2s (x11 over 2m5s)  kubelet            Pulling image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest"
  Warning  Failed     2s (x11 over 2m)    kubelet            Error: secret "mysql-secret" not found
  Normal   Pulled     2s (x2 over 28s)    kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 118ms (118ms including waiting). Image size: 209903068 bytes.


Name:             employee-app-9c785ccf6-vmcwl
Namespace:        default
Priority:         0
Service Account:  default
--
Events:
  Type     Reason     Age                   From               Message
  ----     ------     ----                  ----               -------
  Type     Reason     Age                   From               Message
  ----     ------     ----                  ----               -------
  Normal   Scheduled  13m                   default-scheduler  Successfully assigned default/employee-app-9c785ccf6-vmcwl to i-0cbbf0a8ee0fee6ff
  Normal   Pulling    10m (x5 over 13m)     kubelet            Pulling image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest"
  Warning  Failed     10m (x5 over 13m)     kubelet            Failed to pull image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest": failed to pull and unpack image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest": failed to resolve image: pull access denied, repository does not exist or may require authorization: authorization failed: no basic auth credentials
  Warning  Failed     10m (x5 over 13m)     kubelet            Error: ErrImagePull
  Normal   BackOff    3m44s (x42 over 13m)  kubelet            Back-off pulling image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest"
  Warning  Failed     3m44s (x42 over 13m)  kubelet            Error: ImagePullBackOff
k8s $ kubectl create secret generic mysql-secret \
>   --from-literal=MYSQL_HOST=<your-db-host> \
>   --from-literal=MYSQL_USER=<your-db-user> \
>   --from-literal=MYSQL_PASSWORD=<your-db-password> \
>   --from-literal=MYSQL_DATABASE=<your-db-name>
-bash: syntax error near unexpected token `newline'
k8s $ aws rds create-db-instance \
>   --db-instance-identifier employee-db \
>   --db-instance-class db.t3.micro \
>   --engine mysql \
>   --master-username admin \
>   --master-user-password yourpassword \
>   --allocated-storage 20 \
>   --db-name employeedb \
>   --region us-east-1
{
    "DBInstance": {
        "DBInstanceIdentifier": "employee-db",
        "DBInstanceClass": "db.t3.micro",
        "Engine": "mysql",
        "DBInstanceStatus": "creating",
        "MasterUsername": "admin",
        "DBName": "employeedb",
        "AllocatedStorage": 20,
        "PreferredBackupWindow": "05:28-05:58",
        "BackupRetentionPeriod": 1,
        "DBSecurityGroups": [],
        "VpcSecurityGroups": [
            {
                "VpcSecurityGroupId": "sg-0e36b20ba95ab0b91",
                "Status": "active"
k8s $ aws rds describe-db-instances \
>   --db-instance-identifier employee-db \
>   --query 'DBInstances[0].Endpoint.Address' \
>   --output text
employee-db.c692eyi481od.us-east-1.rds.amazonaws.com
k8s $ kubectl create secret generic mysql-secret \
>   --from-literal=MYSQL_HOST=<rds-endpoint> \
>   --from-literal=MYSQL_USER=admin \
>   --from-literal=MYSQL_PASSWORD=yourpassword \
>   --from-literal=MYSQL_DATABASE=employeedb
-bash: rds-endpoint: No such file or directory
k8s $ aws rds modify-db-instance \
>   --db-instance-identifier employee-db \
>   --master-user-password NewPassword123! \
>   --apply-immediately \
>   --region us-east-1

{
{
    "DBInstance": {
        "DBInstanceIdentifier": "employee-db",
        "DBInstanceClass": "db.t3.micro",
        "Engine": "mysql",
        "DBInstanceStatus": "available",
        "MasterUsername": "admin",
        "DBName": "employeedb",
        "Endpoint": {
            "Address": "employee-db.c692eyi481od.us-east-1.rds.amazonaws.com",
            "Port": 3306,
            "HostedZoneId": "Z2R2ITUGPM61AM"
        },
        "AllocatedStorage": 20,
        "InstanceCreateTime": "2026-06-24T16:14:46.279000+00:00",
        "PreferredBackupWindow": "05:28-05:58",
k8s $ kubectl create secret generic mysql-secret \
>   --from-literal=MYSQL_HOST=employee-db.c692eyi481od.us-east-1.rds.amazonaws.com \
>   --from-literal=MYSQL_USER=admin \
>   --from-literal=MYSQL_PASSWORD=NewPassword123! \
>   --from-literal=MYSQL_DATABASE=employeedb
secret/mysql-secret created
k8s $ kubectl rollout restart deployment employee-app
deployment.apps/employee-app restarted
k8s $ kubectl get pods -w
NAME                            READY   STATUS                       RESTARTS   AGE
employee-app-646fbd89f5-vjfjr   0/1     CreateContainerConfigError   0          32s
employee-app-7bd7f5f686-trdkh   0/1     CreateContainerConfigError   0          29m
employee-app-9c785ccf6-vmcwl    0/1     ImagePullBackOff             0          40m
nginx-test-6ff8854996-zbh6s     1/1     Running                      0          61m
^Ck8s $ kubectl get pods -w
NAME                            READY   STATUS                       RESTARTS   AGE
employee-app-646fbd89f5-vjfjr   0/1     CreateContainerConfigError   0          77s
employee-app-7bd7f5f686-trdkh   0/1     CreateContainerConfigError   0          29m
employee-app-9c785ccf6-vmcwl    0/1     ImagePullBackOff             0          41m
nginx-test-6ff8854996-zbh6s     1/1     Running                      0          62m
employee-app-9c785ccf6-vmcwl    0/1     ErrImagePull                 0          41m
employee-app-9c785ccf6-vmcwl    0/1     ImagePullBackOff             0          42m
^Ck8s $ kubectl describe pod employee-app-646fbd89f5-vjfjr | grep -A 5 "Events:"
Events:
  Type     Reason     Age                 From               Message
  ----     ------     ----                ----               -------
  Normal   Scheduled  114s                default-scheduler  Successfully assigned default/employee-app-646fbd89f5-vjfjr to i-0cbbf0a8ee0fee6ff
  Normal   Pulled     109s                kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 222ms (222ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     109s                kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 127ms (127ms including waiting). Image size: 209903068 bytes.
k8s $ kubectl describe pod employee-app-646fbd89f5-vjfjr
Name:             employee-app-646fbd89f5-vjfjr
Namespace:        default
Priority:         0
Service Account:  default
Node:             i-0cbbf0a8ee0fee6ff/172.31.31.120
Start Time:       Wed, 24 Jun 2026 16:30:12 +0000
Labels:           app=employee-app
                  pod-template-hash=646fbd89f5
                  topology.kubernetes.io/region=us-east-1
                  topology.kubernetes.io/zone=us-east-1c
Annotations:      kubectl.kubernetes.io/restartedAt: 2026-06-24T16:30:12Z
Status:           Pending
IP:               172.31.27.81
IPs:
  IP:           172.31.27.81
Controlled By:  ReplicaSet/employee-app-646fbd89f5
Containers:
  employee-app:
    Container ID:   
    Image:          986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest
    Image ID:       
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       CreateContainerConfigError
    Ready:          False
    Restart Count:  0
    Environment:
      SPRING_DATASOURCE_URL:       jdbc:mysql://mysql-service:3306/employeedb
      SPRING_DATASOURCE_USERNAME:  root
      SPRING_DATASOURCE_PASSWORD:  <set to the key 'mysql-root-password' in secret 'mysql-secret'>  Optional: false
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-gh9gj (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  kube-api-access-gh9gj:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                   From               Message
  ----     ------     ----                  ----               -------
  Normal   Scheduled  2m48s                 default-scheduler  Successfully assigned default/employee-app-646fbd89f5-vjfjr to i-0cbbf0a8ee0fee6ff
  Normal   Pulled     2m43s                 kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 222ms (222ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     2m43s                 kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 127ms (127ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     2m31s                 kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 152ms (152ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     2m17s                 kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 143ms (143ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     2m2s                  kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 156ms (156ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     109s                  kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 121ms (121ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     97s                   kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 175ms (175ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     85s                   kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 147ms (147ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     71s                   kubelet            Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 140ms (140ms including waiting). Image size: 209903068 bytes.
  Normal   Pulled     34s (x3 over 58s)     kubelet            (combined from similar events): Successfully pulled image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest" in 116ms (116ms including waiting). Image size: 209903068 bytes.
  Normal   Pulling    23s (x13 over 2m44s)  kubelet            Pulling image "986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest"
  Warning  Failed     11s (x14 over 2m43s)  kubelet            Error: couldn't find key mysql-root-password in Secret default/mysql-secret
k8s $ kubectl delete secret mysql-secret
secret "mysql-secret" deleted from default namespace
k8s $ kubectl create secret generic mysql-secret \
>   --from-literal=mysql-root-password=NewPassword123!
secret/mysql-secret created
k8s $ kubectl rollout restart deployment employee-app
deployment.apps/employee-app restarted
cat: Dockerfile: No such file or directory
k8s $ cd ~/employee-management-k8s/k8s
k8s $ ls
app-deployment.yaml  app-service.yaml  mysql-deployment.yaml  mysql-pvc.yaml  mysql-secret.yaml  mysql-service.yaml  policies.json
k8s $ cd
~ $ ls
employee-management-k8s
~ $ ls -l
total 4
drwxr-xr-x. 5 cloudshell-user cloudshell-user 4096 Jun 24 07:05 employee-management-k8s
~ $ cd employee-management-k8s
employee-management-k8s $ ls -l
total 12
drwxr-xr-x. 4 cloudshell-user cloudshell-user 4096 Jun 24 14:15 backend
drwxr-xr-x. 2 cloudshell-user cloudshell-user 4096 Jun 24 15:32 k8s
-rw-r--r--. 1 cloudshell-user cloudshell-user   34 Jun 24 07:05 README.md
employee-management-k8s $ cd backend
backend $ ls
Dockerfile  pom.xml  src  target
backend $ cat Dockerfile
backend $ nano pom.xml
backend $ vim pom.xml
backend $ mvn clean package -DskipTests

Login Succeeded
backend $ docker push 986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app:latest
The push refers to repository [986119050506.dkr.ecr.us-east-1.amazonaws.com/employee-app]
af05ab3ff132: Pushed 
e8565951e2e1: Layer already exists 
cf585fb050ee: Layer already exists 
6ade00acd3da: Layer already exists 
73000d5df0aa: Layer already exists 
d5a103a78bcb: Layer already exists 
ab0ae19b58df: Layer already exists 
e8c084c1b320: Layer already exists 
latest: digest: sha256:5db694d5c0437634e268643b2109274c2a67bd264133c6fbe558e36f86dff74b size: 1995
backend $ kubectl rollout restart deployment employee-app
deployment.apps/employee-app restarted
nginx-test-6ff8854996-zbh6s     1/1     Running   0          89m
employee-app-74d696cbb5-9qvdd   0/1     Error     0          14s
employee-app-74d696cbb5-9qvdd   1/1     Running   1 (1s ago)   15s
employee-app-74d696cbb5-k2z96   0/1     Error     0            10s
employee-app-74d696cbb5-k2z96   1/1     Running   1 (1s ago)   11s
employee-app-74d696cbb5-9qvdd   0/1     Error     1 (10s ago)   24s
employee-app-74d696cbb5-k2z96   0/1     Error     1 (9s ago)    19s
employee-app-74d696cbb5-9qvdd   0/1     CrashLoopBackOff   1 (14s ago)   37s
employee-app-74d696cbb5-9qvdd   1/1     Running            2 (15s ago)   38s
employee-app-74d696cbb5-k2z96   0/1     CrashLoopBackOff   1 (15s ago)   34s
employee-app-74d696cbb5-k2z96   1/1     Running            2 (16s ago)   35s
employee-app-74d696cbb5-9qvdd   0/1     Error              2 (23s ago)   46s
employee-app-74d696cbb5-k2z96   0/1     Error              2 (24s ago)   43s
employee-app-74d696cbb5-9qvdd   0/1     CrashLoopBackOff   2 (23s ago)   68s
^[aemployee-app-74d696cbb5-9qvdd   1/1     Running            3 (24s ago)   69s
employee-app-74d696cbb5-9qvdd   0/1     Error              3 (30s ago)   75s
employee-app-74d696cbb5-k2z96   0/1     CrashLoopBackOff   2 (29s ago)   71s
employee-app-74d696cbb5-k2z96   1/1     Running            3 (30s ago)   72s
employee-app-74d696cbb5-k2z96   0/1     Error              3 (36s ago)   78s
employee-app-74d696cbb5-k2z96   0/1     CrashLoopBackOff   3 (42s ago)   119s
employee-app-74d696cbb5-k2z96   1/1     Running            4 (43s ago)   2m
employee-app-74d696cbb5-k2z96   0/1     Error              4 (49s ago)   2m6s
employee-app-74d696cbb5-9qvdd   0/1     CrashLoopBackOff   3 (59s ago)   2m13s
employee-app-74d696cbb5-9qvdd   1/1     Running            4 (60s ago)   2m14s
employee-app-74d696cbb5-9qvdd   0/1     Error              4 (66s ago)   2m20s
backend $ kubectl get svc 
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   5h37m
<img width="1165" height="536" alt="Screenshot 2026-06-24 at 22 36 31" src="https://github.com/user-attachments/assets/7aa017e3-aa2a-4d03-b489-99a418485191" />
