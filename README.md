==Core Concepts==

1. PODs
-List pod
kubectl get pods

-List pod with watch
kubectl get pods --watch

-List pod with more information
kubectl get pods -o wide

-Create pod with declarative way
spec:
	containers:
	- name: <container_name>
	  image: <image>
	  ports:
		- containerPort: <container_port>

-Create pod with imperative way
kubectl run <pod_name> --image=<image>

-Edit pod
kubectl edit pod <pod_name>
kubectl replace --force -f <yaml_file>

-Delete pod
kubectl delete pod <pod_name>

-Delete all pods
kubectl delete pod --all

-Inspect pod
kubectl describe pod <pod_name>

		
=============================================================

2. ReplicaSet

-Create replication controller with declarative way
apiVersion: v1
kind: ReplicationController
metadata:
	name: <name>
spec:
	template:
		// pod_definition
		metadata:
			name: <pod_name>
		spec:
			containers:
			- name: <container_name>
			  image: <image>
	replicas: <replicas>
	
-List replication controller
kubectl get replicationcontroller
			  
-Create replica set with declarative way
apiVersion: apps/v1
kind: ReplicaSet
metadata:
	name: <name>
spec:
	template:
		// pod_definition
		metadata:
			name: <pod_name>
			<pod_label_key>: <pod_label_value>
		spec:
			containers:
			- name: <container_name>
			  image: <image>
	replicas: <replicas>
	selector:
		matchLabels:
			<pod_label_key>: <pod_label_value>

-List replica set controller
kubectl get replicaset		

-Scale replica set
kubectl scale --replicas=<replicas> replicaset <replicaset_name>
kubectl scale --replicas=<replicas> -f <yaml_file>	
=============================================================

3. Deployments

-Create deployment with imperative way
kubectl create deployment <deploy_name> --image=<image> --replicas=<replicas>

-Edit deployment
kubectl edit deployment <deploy_name>

-Scale deployment
kubectl scale deployment --replica=<replicas> <deploy_name>
=============================================================

4. Namespaces

-List pod with namespace
kubectl get pods --namespace=<namespace_name>

-List pod for all namespace
kubectl get pods --all-namespaces

-Create pod with namespace
kubectl create -f <yaml_file> --namespace=<namespace_name>
	
-Create namespace with declarative way
apiVersion: v1
kind: Namespace
metadata:
	name: <namespace_name>
	
-Create namespace with imperative way
kubectl create namespace <namespace_name>

-Use the namespace in pod
apiVersion: v1
kind: Pod
metadata:
	name: <pod_name>
	namespace: <namespace_name>

-Create resource quata for namespace
apiVersion: v1
kind: ResourceQuata
metadata:
	name: [quata_name]
	namespace: <namespace_name>
spec:
	pods: <number of pod>
	requests.cpu: <cpu>
	requests.memory: <memory>
	limits.cpu: <cpu>
	limits.memory: <memory>
	
=============================================================

==Configuration==

5. Command & Argument

-Dockerfile
FROM ubuntu
ENTRYPOINT ["sleep] //startup
CMD ["5"]			//argument

-Override command and argument in definition file
apiVersion: v1
kind: Pod
metadata:
	name: <pod_name>
spec:
	containers:
	- name: <container_name>
	  image: <image>
	  command: ["sleep"]	//replace entrypoint value in Dockerfile
	  args: ["10"]			//replace cmd value in Dockerfile
	  

-Create pod with command & args with imperative way
kubectl run <name> --image=<image> --command -- <command> <args>

-Create pod with args by imperative way
kubectl run <name> --image=<image> -- <args>

=============================================================

6. Environment Variables

-Define env in pod
apiVersion: v1
kind: Pod
metadata:
spec:
	containers:
		- name: <name>
		  image: <image>
		  env:
			- name: <env_name>
			  value: <env_value>

-Set value from config map
env:
	- name: <name>
	  valueFrom:
		configMapKeyRef:
		
-Set value from secret
env:
	- name: <name>
	  valueFrom:
		secretKeyRef:

=============================================================

7. ConfigMaps

-Create configmap with imperative way
kubectl create configmap --from-litteral=<key>=<value>
kubectl create configmap --from-file=<configmap_file>

-Create configmap with declarative way
apiVersion: v1
kind: ConfigMaps
metadata:
	name: <name>
data:
	<key>: <value>

kubectl create -f <yaml_file>

-List configmap
kubectl get configmaps

-Inspect configmap
kubectl describe configmap <name>

-Inject all configmap with env to Pod
envFrom:
- configMapRef:
	name: <name>

-Inject single configmap with env to Pod
env:
- name: [env_name]
  valueFrom:
	configMapKeyRef:
		name: [config_name]
		key: [config_value]

-Inject configmap with volume to Pod
volumes:
- name: <name>
  configMap:
	name: [config_name]

=============================================================

8. Secrets

-Create secret with imperative way
kubectl create secret generic --from-litteral=<key>=<value>
kubectl create secret generic --from-file=<secret_file>

-Create secret with declarative way
apiVersion: v1
kind: Secret
metadata:
	name: <name>
data:
	<key>: <value>
	
kubectl create -f <yaml_file>

-List secret
kubectl get secrets

-Inspect secret
kubectl describe secret <name>

-Inject all secret with env to Pod
envFrom:
	- secretRef:
		name: <name>
		
-Inject single secret with env to Pod
env:
	- name: <name>
	  valueFrom:
		secretKeyRef:
			name: <seret_name>
			key: <secret_key>
	
-Inject secret with Volume to Pod
volumes
- name: <name>
  secret:
	secretName: <secret_name>
=============================================================

9. Security Contexts

-Use security context in pod level
spec:
	securityContext:
		runAsUser: <user>
	containers:

-Use security context in container level
spec:
	containers:
		- name: <name>
		  image: <image>
		  securityContext:
			runAsUser: <user>
			capabilities:				//support only container
				add: ["MAC_ADMIN"]
			
-Check user who am I
kubectl exec <pod> -- whoami
=============================================================

10. Service Account

-Create service account
kubectl create serviceaccount <name>

-List service account
kubectl get serviceaccount

-Inspect
kubectl describe serviceaccount <name>

-User service account in pod
spec:
	containers:
	- name: <name>
	  image: <image>
	serviceAccountName: [service account name]
	
-Disable auto mount service account
spec:
	containers:
	- name: <container_name>
	  image: <image>
	automountSeriviceAccountToken: false
	
=============================================================

11. Resource Requirement

-Declaration in pod
spec:
	containers:
	- name: <container_name>
	  image: <image>
	  resources:
		requests:
			memory: <memory>
			cpu: <cpu>
		limits:
			memory: <memory>
			cpu: <cpu>
			
-memory unit 
GMK, 1G=1,000,000 bytes, 1Gi=1,073,741,824 bytes
=============================================================

12. Taints and Tolerations

-Create node
kubectl create node <node>

-List node
kubectl get nodes

-Set taint to node
kubectl taint nodes <name> <key>=<value>:<effect>	//NoSchedule, PreferNoSchedule, NoExecute

-Remove taint from node
-kubectl taint node <name> <taint_value>-

-Set toleration to pod
spec:
	containers:
	tolerations:
	- key: "<key>"
	  operator: "<operator>"	//Equal
	  value: "<value>"
	  effect: "<effect>"

=============================================================

13. Node Selectors

-Set label to node
kubectl lable nodes <name> <key>=<value>

-Set node selector in pod
spec:
	containers:
	nodeSelector:
		<key>: <value>

=============================================================

14. Node Affinity

-Set node affinity in pod
spec:
	containers:
	affinity:
		nodeAffinity:
			requiredDuringSchedulingIgnoredDuringExecution:
				nodeSelectorTerms:
				- matchExpressions:
					- key: <key>
					  operator: In
					  values:
					  - <value>
		
-List pod with node
kubectl get pods -o wide		
=============================================================

==Multi-Container PODs==

15. Multi-Container

-Declaration
spec:
	containers:
		- name: <name>
		  image: <image>
		- name: <name>
		  image: <image>
		  
=============================================================

16. Init Container

-Declaration
spec:
  containers:
  - name: <name>
    image: <image>
  initContainers:
  - name: <name>
    image: <image>

=============================================================

==Observability==

17. Readness Probes

-Readiness Probes with http
spec:
	containers:
	- name: <name>
	  image: <image>
	  readinessProbe:
		httpGet:
			path: <path>
			port: <port>
		initialDelaySeconds: <delay>
		periodSeconds: <period>
		failureThreshold: <attemp>

-Readiness Probes with tcp
spec:
	containers:
	- name: <name>
	  image: <image>
	  readinessProbe:
		tcpSocket:
			port: <port>
			
-Readiness Probes with command
spec:
	containers:
	- name: <name>
	  image: <image>
	  readinessProbe:
		exec:
			command: <command>

=============================================================

18. Liveness Probes

-Liveness Probes with http
spec:
	containers:
	- name: <name>
	  image: <image>
	  livenessProbe:
		httpGet:
			path: <path>
			port: <port>
		initialDelaySeconds: <delay>
		periodSeconds: <period>
		failureThreshold: <attemp>

-Liveness Probes with tcp
spec:
	containers:
	- name: <name>
	  image: <image>
	  livenessProbe:
		tcpSocket:
			port: <port>
			
-Liveness Probes with command
spec:
	containers:
	- name: <name>
	  image: <image>
	  livenessProbe:
		exec:
			command: <command>
=============================================================

19. Container Logging

-Show log
kubectl logs -f <pod>

-Show log with specific container
kubectl logs -f <pod> <container>

-Read log file
kubectl exec -it -n <pod> -- cat /log/app.log

=============================================================

20. Monitor and Debug Applications

-Check memory consumption
kubectl top node

-Check pod consumption
kubectl top pod

=============================================================

==POD Design==

21. Labels, Selectors and Annotations

-Declaration
apiVersion: v1
kind: Pod
metadata:
	name: <name>
	labels:
		<key>: <value>

-List pod with selector
kubectl get pods --selector <key=value>,<key=value>

-Use with ReplicaSet
apiVersion: v1
kind: Pod
metadata:
	name: <name>
	labels:
		<key>: <value>
spec:
	selector:
		matchLabels:
			<key>: <value>
			
-Annotations
apiVersion: v1
kind: Pod
metadata:
	name: <name>
	labels:
	annotations:
		<key>: <value>
=============================================================

22. Rolling Updates and Rollback

-Deployment strategry
Recreate, RollingUpdate

-Check status rollout
kubectl rollout status deployment/<name>

-Check revision rollout
kubectl rollout history deployment/<name>

-Rollback
kubectl rollout undo deployment/<name>

-Change image in deployment
kubectl set image deployment/<name> <container_name>=<image>

=============================================================

23. Job and Conjobs

-Create job
kubectl create -f <yaml_file>

-List job
kubectl get jobs

-Get pod of job
kubectl get pods

-Check log from pod
kubectl logs <pod>

-Delete job
kubectl delete job <name>

-job definition
apiVersion: batch/v1
kind: Job
metadata:
	name: <name>
spec:
	completions: <desire job to complete>
	parallelism: <number of parallelism>
	backoffLimit: <number of failure>
	template:
		spec:
			containers:
			restartPolicy: Never
			
-Conjob definition
apiVersion: batch/v1beta1
kind: CronJob
metadata:
	name: <name>
spec:
	schedule: "<schedule>"
	jobTemplate:
		spec:
			completions: <desire job to complete>
			parallelism: <number of parallelism>
			
-List cronjob
kubectl get cronjob
=============================================================

==Service & Networking==

24. Services

-Definition of NodePort
apiVersion: v1
kind: Service
metadata:
	name: <name>
spec:
	type: NodePort
	ports:
		- targetPort: <port>
		  port: <port>
		  nodePort: <external_port>
	selector:
		<key>: <value>
		
-Definition of ClusterIP
apiVersion: v1
kind: Service
metadata:
	name: <name>
spec:
	type: ClusterIP
	ports:
		- targetPort: <port>
		  port: <port>
	selector:
		<key>: <value>
		
-Create by imperative way
kubectl expose pod <name> --type=[type] --port=<port> --name=<service_name>	--dry-run=client -o yaml	
kubectl create service [type] <name> --tcp=<port:port> --dry-run=client -o yaml	
		
		
-List service
kubectl get services
=============================================================

25. Ingress Networking

-Ingress Resource Definition
apiVersion: extensions/vi1beta1
kind: Ingress
metadata:
	name: <name>
spec:
	backend:
		serviceName: <name>
		servicePort: <port>
			

-Ingress Resource Definition with Path
apiVersion: extensions/vi1beta1
kind: Ingress
metadata:
	name: <name>
spec:
	rules:
	- http:
		paths:
			- path: <path1>
			  backend:
				serviceName: <name>
				servicePort: <port>
			- path: <path2>
			  backend:
				serviceName: <name>
				servicePort: <port>
	
-Ingress Resource Definition with Host
apiVersion: extensions/vi1beta1
kind: Ingress
metadata:
	name: <name>
spec:
	rules:
	- host: <host1>
		- http:
			paths:
				- backend:
					serviceName: <name>
					servicePort: <port>
	- host: [host2]
		- http:
			paths:
				- backend:
					serviceName: <name>
					servicePort: <port>
					
-Create from imperative way
kubectl create ingress <name> --rule="host/path=service:port"

-List ingress
kubectl get ingress
=============================================================

26. Network Policies

-Definition
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
	name: <name>
spec:
	podSelector:
		matchLabels:
			<key>: <value>
			
	policyTypes:
	- Ingress
	- Egress
	
	ingress
	- from:
		- podSelector:
			mactchLabels:
				name: <name>
				
		  namespaceSelector:
			mactchLabels:
				name: <name>
				
		- ipBlock:
			cidr: <ip>/<range>
		  
		ports:
			- protocol: TCP
			  port: <port>
			  
	egress
	- to:
		- podSelector:
			mactchLabels:
				name: <name>
		ports:
			- protocol: TCP
			  port: <port>
			  
-List network policy
kubectl get networkpolicies
=============================================================

==State Persistence==

27. Volumes

-Volume & Mount
apiVersion: v1
kind: Pod
metadata:
	name: <pod_name>
spec:
	containers:
	- image: <image>
	  name: <name>
	  volumeMounts:
	  - mountPath: <container_path>
	    name: <volume_name>
		
	volumes:
	- name: <volume_name>
	  hostPath:
		path: <node_path>
		type: Directory


=============================================================

28. Persistent Volumes

-Create persistent volume by declarative way
apiVersion: v1
kind: PersistentVolume
metadata:
	name: <pv_name>
spec:
	accessModes:
		- ReadWriteOnce
	capacity:
		storage: 1Gi
	hostPath:
		path: <path>
	persistentVolumeReclaimPolicy: <policy>	//Recycle/Delete/Retain
	
-List persistent volume
kubectl get persistentvolume
	
=============================================================

29. Persistent Volumes Claims

-Create persistent volume claim by declarative way
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
	name: <name>
spec:
	accessModes:
		- ReadWriteOnce
	resources:
		requests:
			storage: 500Mi
	
-Use PVC in pod
apiVersion: v1
kind: Pod
metadata:
  name: <pod_name>
spec:
  containers:
    - name: <container_name>
      image: <image>
      volumeMounts:
      - mountPath: <path>
        name: <volume_name>
  volumes:
    - name: <volume_name>
      persistentVolumeClaim:
        claimName: <pvc_name>
			
-List persistent volume claim
kubectl get persistentvolumeclaim

-Delete persistent volume claim
kubectl delete persistentvolumeclaim <pvc_name>
=============================================================

30. Authentication

-Create user with static password file
ExecStart=/usr/local/bin/kube-apiserver \\
--basic-auth-file=user-details.csv

-Authen user with user/password
curl -v -k https://master-node-ip:6443/api/v1/pods -u "<user>:<password>"

-Create user with static token file
ExecStart=/usr/local/bin/kube-apiserver \\
--token-auth-file=user-details.csv

-Authen user with user/password
curl -v -k https://master-node-ip:6443/api/v1/pods --header "Authorization: Bearer <token>"


=============================================================

31. KubeConfig

-Declaration
apiVersion: v1
kind: Config
current-context: <user>@<context>

clusters:
- name: [cluster_name]
  cluster:
		certificate-authority: ca.crt
		server: https://my-kube-playground:6443


contexts:
- name: [user@cluster]
  context:
		cluster: <cluster_name>
		user: <user_name>

users:
- name: [user_name]
  user:
		client-certificate: admin.crt
		client-key: admin.key


-View config
kubectl config view			//file is in home default $HOME/.kube/config
kubectl config view --kubeconfig=<config_name>

-Update current context
kubectl config use-context <user>@<cluster>

=============================================================

32. API Group

-List api group
curl -k https://kube-master:6443

-Check version
curl https://kube-master:6443/version

-List pod
curl https://kube-master:6443/api/vi/pods

-/api
	/v1
		/namespaces, /pods, /rc, /events, /endpoints, /nodes
		/bindings, /PV, /PVC, /configmaps, /secrets, /services

-/apis
	/apps/v1
		/deployments
		/replicasets
		/statefullsets
	/extensions
	/networking.k8s.io/v1
	/networkpolicies
	/storage.k8s.io
	/authentication.k8s.io
	/certificate.k8s.io

-Use proxy
kubectl proxy
curl https://localhost:8001/api/vi/pods

=============================================================

33. Authorization

-Mode
Node, ABAC, RBAC, WEBHOOK, AlwaysAllow, AlwaysDeny

-Set mode
ExecStart=/usr/local/bin/kube-apiserver \\
--authorization-mode=Node,RBAC

-Check api server setting
cat /etc/kubernetes/manifests/kube-apiserver.yaml
ps -aux | grep authorization

=============================================================

34. Role and RoleBinding

-Create role with declarative way
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
	name: <name>
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "get", "create"]
  resourceNames: ["<pod_name>"]
  
-Create role with imperative way
kubectl create role <name> --verb=list,create,delete --resource=pods
  
-Create role binding
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
	name: <rolebinding_name>
subjects:
- kind: User
  name: <user_name>
  apiGroup: rbac.authorization.k8s.io
roleRef: 
  kind: Role
  name: <role_name>
  apiGroup: rbac.authorization.k8s.io
  
-Create role binding with imperative way
kubectl create rolebinding <name> --role=<role_name> --user=<user_name>
  
-List role
kubectl get roles

-List role binding
kubectl get rolebindings

-Check access with user
kubectl auth can-i <verb> <type>
kubectl auth can-i <verb> <type> --as <user>

=============================================================

35. ClusterRole and ClusterRoleBinding

-Create cluster role with declarative way
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
	name: <name>
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["list", "get", "create"]
  
-Create cluster role with imperative way
kubectl create clusterrole <name> --verb=list,create,delete --resource=pods

-Create cluster role binding with declarative way
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
	name: <name>
subjects:
- kind: User
  name: <user_name>
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: <clusterrole_name>
  apiGroup: rbac.authorization.k8s.io
  
 -Create role binding with imperative way
kubectl create clusterrolebinding <name> --clusterrole=<role_name> --user=<user_name>

=============================================================

36. API Resource

-List resource
kubectl api-resources

=============================================================

37. Admission Controller

-View enable admission controller
1) kube-apiserver -h | grep enable-admission-plugins

2) kubectl exec -it kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep 'enable-admission-plugins'

3) vi /etc/kubernetes/manifests/kube-apiserver.yaml

4) ps -ef | grep kube-apiserver | grep admission-plugins

-Enable admission controller
1) kube-apiserver.service
ExecStart=/usr/local/bin/kube-apiserver \\
--enable-admission-plugins=NodeRestriction,NamespaceAutoPrivision

2) /etc/kubernetes/manifests/kube-apiserver.yaml
apiVersion: v1
kind: Pod
metadata:
	name: kube-apiserver
spec:
	containers:
	- command:
	  - kube-apiserver
	  - --enable-admission-plugins=NodeRestriction,NamespaceAutoPrivision


=============================================================

38. API version

-Check version of object
kubectl explain <type>

-Get preferred version
kubectl proxy 8001
curl localhost:8001/apis/batch

-Enable/Disable api group
ExecStart=/usr/local/bin/kube-apiserver \\
--runtime-config=batch/v2alpha1

-List short name
kubecutl api-resources

=============================================================

39. Deployment strategy

-Recreate strategy
Deployed aV1 bV1
Stop aV1 bV1
Deploy aV2 bV2
 
-RollingUpdate strategy
Deployed aV1 bV1
Stop aV1 Deploy aV2
Stop bV1 Deploy bV2

-Blue/Green strategy
Deployed aV1 bV1
Deployed aV2 bV2
If V2 OK, swith traffic to V2 

-Canary strategy
Deployed aV1 bV1
Deployed aV2 bV2
Switch small traffic to V2 by using replicas=x

=============================================================

==Tip==
-Show help
<command> --help

-Get declarative file in yaml
kubectl get [type] <name> -o yaml

-Dry run
kubectl get [type] <name> --dry-run=client

-Export to the file
kubectl get [type] <name> --dry-run=client -o yaml > <yaml_file>

-Encode/Decode base64
echo "test" | base64
echo "xyz" | base64 --decode

-Count line
kubectl get pods --no-headers | wc -l

-List all objects
kubectl get all

-List object for all name spaces
kubectl get <type> -A

-Find value from file
grep <keyword> <file>

