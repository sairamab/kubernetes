####
##  Automation of process run ##
Ensures a process is running continously, which helps is continous execution of tasks.

# scenario #
Let us assume, we have linux servers on which we install an 'Agent' which runs with specific process id. We run our daily tasks on Agent, which is hosted by Linux server. There might be situation when due to load of tasks, agent doesn't work or goes down(sleep mode). So we specify Shutdown and Startup scripts to make Agent run smoothly and seamlessly.


If Agent goes down at non-working time, all the process that runs at that time will be stopped which delays further processes.

In that case, our solution should make sure that agent will restart(shutdown and startup) once agent process is not running.
In some cases, even if agent goes down, process might be still showing up, So it is better to check agent logs and if log reports error, then we shall restart agent with scripts mentioned.
If agent log is still showing error, then there might be issues with host- the linux server, for which we shall escalate server team.

# solution #
We can run a kubernetes "deployment", which checks process on certain interval and executes startup scripts accordingly.


k8s cluster is on another server, so we proceeded with sshpass authentication, and stored password in secrets for better understanding of k8s resources (It is good practice to store sensitive info like passwords, tokens in "secrets")

We built all these commands on top of open image- alpine. How does it fetched cluster information of secrets?
Here comes the usage of k8s resources- "service account", "roles" and "rolebindings"
 ServiceAccount is an identity used by pods to interact with the Kubernetes API.
 A Role defines a set of permissions (RBAC rules) for resources within a specific namespace. ClusterRole applies across all namespaces
 A RoleBinding attaches a Role to a ServiceAccount within a specific namespace. ClusterRoleBinding attaches a ClusterRole to a      ServiceAccount across all namespaces.

  🛠 How They Work Together?
  
  1️⃣ A pod runs with a ServiceAccount.
  
  2️⃣ The ServiceAccount needs permissions to perform actions (e.g., list pods).
  
  3️⃣ A Role defines the needed permissions within a namespace.
  
  4️⃣ A RoleBinding links the Role to the ServiceAccount.

**secret creation**:
 secret can be created declarative(using yaml files like we did for role, rolebinding and deployment as above) way as well as imperative way(we shall use this for creating secret now which store password of a user)

_kubectl create secret secret-type secret-name --from-literal=key=value --namespace=namespace_  

Secret Type	        :     Description

  generic	             :     Used for storing any kind of secret data (default type).

  docker-registry	     :    Used for storing Docker credentials.

  tls	Stores            :    TLS certificates and keys.

  basic-auth	           :    Stores username-password credentials.


we used generic type in this project
**kubectl create secret generic my-secret --from-literal=username=user --from-literal=password=xxxxxxx**
