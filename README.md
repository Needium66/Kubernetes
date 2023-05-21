# Kubernetes
everything k8s
Sources: https://kubernetes.io/docs/home/
https://www.youtube.com/watch?v=s_o8dwzRlu4
https://minikube.sigs.k8s.io/docs/start/
https://minikube.sigs.k8s.io/docs/drivers/docker/
https://docs.docker.com/desktop/install/windows-install/
kubernetes.io/docs/home/
https://kubernetes.io/docs/concepts/services-networking/service/  --- service config
https://hub.docker.com/_/mongo   --credentials for mongodb (i.e environment variables)

Some K8s components/concepts
Pods: Smallest unit in K8s, usually 1 app per pod, a particular interface where you can perform some certain tasks for a container and each pod always have its own IP address.
Service: Static or permanent IP address that can be attached to each pod. Also a load balancer; forwards requests.
External Service: has node ip, ingress, does forwarding to the internal service (e.g db service).
Internal Service: Protected, attached to a database or an app or web app for example.
ConfigMap: External config to your application, it has the configuration of data that you will want to remain static e.g url of your database, service, endpoint to prevent consant change that comes with end of life of pod or any another component in K8.
Secrets: To store secret data e.g credentials, password, certs. store in base 4 encrypted format. encrypted with 3rd party tools.
Volume: For persistence storage. Attached to a pod.
Deployment: Blueprints for pods, where replicas are specified to allow for persistence of pods; pods don't die, no downtime, can be replicated easily. Ensures the persistence of pods. Not creating pods, but deployments. Can easily scaleup or down. Distributed systems at its best.
Statefulset: Ensures the persistence of database. No downtime for replacement of database. Clone or replica of an existing database.
K8s Config: Will have a KIND od deployment, service etc.Spec, with the number of replicas of what to be created e.g my-app, based on a container with an image running inside. Environment variables and port config of what pod should be. It has 3 parts: (1)Metadata part:It has the name, labels, etc (2) Specifications: Every config to be applied goes in here. Has attributes like replicas, selector, template or port based on the kind e.g deployment kind will have replicas, selector, template while service kind will have selector, port. (3) Status: Automatically generated and edited by K8s. Compared desired state and actual state to generate this. Basis of self-healing process that K8s does. Updates the state of your deployment continuously. Knows what to do from data obtained from etcd.
One master node with multiple workers node or multiple master nodes with multiple workers nodes. Control Plane of your master node will have these 4 components: api server, scheduler, controller manager and etcd. Workers node: will ahve pod, db, node, service etc
Minicube: Production like cluster for individuals. Can easily set up your K8s test lab with this. It has 1 node cluster where both master processes and workers processes run on 1 node as against (multiple nodes in a production-like environment). It has a docker container runtime pre-installed. So i can install a minicuve on my local machine, it will act as my K8s VM.
Kubectl: Used to interact with a cluster. To create pods or do any other thing, you need to use this. To interact with api server in the master's node of K8 or K8 as a whole, you need kubectl (CLI). It is the most powerful. You can use it to do anything that you want. It submits commands to api servers to create components, delete components etc. The work processes on minikube or real K8s cluster will be executing the command to deploy a pod, create a service etc.
DRAW                                                                                                                                      MASTER                  Api server                            UI, API, CLI (Kubectl)                                                                                                                         Worker Processes                       Service, Secret, Config Map
Minikube utilities (Start it as a container or virtual machine(easier)) i.e needs to install either a container e.g docker or vmware on our laptop. For Windows, Docker (VM + container preferred). Hyper-V PREFERRED TOO. Hosting minikube on our local machine as docker container. There is docker in minikube to run our app containers. Minikube runs as a docker container too (docker in your laptop)
brew install minikube - to install minikube on a macos
minikube start - to start your minikube cluster (Not necesarily)
Install minikube through the link in roject resources for windows-
Install docker through the link above-
Start your docker daemon by double clicking on it from where it is saved on your local computer and ensures that you see a green pane at the bottom left pane that indicates that it is running.
Run this command on your command prompt on whatever terminal you are using to start the minikube cluster and pass docker as the driver optiton- "minikube start --driver docker" put the sss here. It will create a local K8s cluster on your machine
Run this command- "minikube status" to check the status of your machine
Start using kubectl as CLI (It has been installed as a dependency to minikube)- the last line of the installation shows that.
Run this command- "kubectl get node" - it will display all the nodes in the cluster. This shows that we have a k8s cluster running locally on your laptop.
minikube is just for start up and deleting the cluster. from now on, you should be using kubectl to interact and whenever you don't need to minkube cluter on your laptop anymore, run the minikube delete to delete it.
Project: Create a web-app with a mongo-db database.
Steps
Deploy web-app
Deploy mongo-db
Service
Config map
Secrets
Ingress(node-ip: 301300)
Create 4 config files as listed below
Create a Config Map with mongo db endpoint.
Create a secret with mongo db credentials
Create a config file for deployment & service (mongo db app + inetrnal service)
Create a config file for  deployment & service (webapp + external service)
Create ConfigMap: - you can reference the K8s documentation for configmap for a blueprint- https://kubernetes.io/docs/concepts/configuration/configmap/. Get the part that you need and paste in your file locally. you can modify what to suit your specification. Indicate the key value pairs as external configuration in your data block e.g mongo-url as key an service as the value.
Create secret config file: reference documentation for the config- https://kubernetes.io/docs/concepts/configuration/secret/. You can use "opaque" as type of your secret. There are various types. Encode your credentials by running command for base64 e.g "echo -n jide-user | base64" get the values and paste them in for your username and passowrd in the secret file.
Create file for deployment and service. Reference the documentation for a config example- https://kubernetes.io/docs/concepts/workloads/controllers/deployment/. Template is the blueprint of what you desire. Template has its own metadata and spec, just like deployment too. We have definition of containers for pod in the template. It is where you define the image to create the pod and the port to reference. We need mongo image for this, go and search for it in docker hub. Click on the mongo image and click on tags-https://hub.docker.com/_/mongo/tags. Select your preferred tag e.g latest or 4.0 or whatever. Use 6.0.
We have the port where the container will listen to. You have to look at your image in the docker hub to know your "containerPort". For example, for this image, it is 27017. The container block is to configure the deployment to create pods with mongo 5.0. 
We have labels attributes and matchLabels attributes. Theey are key-value pairs that need to be meaningful identifier for users. Multiple pods can share the same labels. Label selector i.e label under "Selector" block is very important. Because it is the one that will identify resources eventually. It is the one that is used to identify resources during deployment e.g all the pods that match this label belongs to this deployment e.g app: nginx. What is common in k8s is to use app key label. So change the value for app from nginx to mongo.
Replicas: Defines how many pods you want to create. 1 will be good here, because it is a database and we are using a deployment and not a statefulset here, so using statefulset will be easier to use to scale.
Service: service configuartion can be in the same yaml file with deployment configuration. You can seperate the two with 3 dots. Go and search for an example of service in k8s documentation. Modify the config to suit your purpose. Change the "name" to be in sync with your deployment e.g change the name to "mongo-service". This value for the name is the endpoint(mongo-service) that will be used to access the deployment. It can be found in the mongo-configmap yaml file- it must match the value that the url carries there e.g it is jide-mongo-service. So i need to change in the value in the service to jide-mongo-service too.
Then we have the spec attributes next. We need selector here because service needs to forward the reuest that it gets to its endpoint by using the label selector (app: mongo) of the pod that will belong to the service, which is mongo.
Then the port config- service is accessible within the cluster using its own ip address and the port e.g port 80 or whatever we want. Then we have the "targetPort", which is the port of the pod that belongs to the service. Logically enough the targetPort should always be the same as the containerPort.( the port in service is the port of the service itself in the cluster, to make it accessible within the cluster while the targetPort is the port of the pod in the cluster that points the pod to service (connects the pod to the service- should have the same port as containerPort in the deployment file)) .   So the service forward request to the pod through the targetPort. The service sends request from the targetPort to the containerPod in the pod. So both ports have 27017. The port and targetPort in the service file can be different or they could be the same to keep things simple and consistent all through. You can change the port value in service to 27017 too.
Web application and service file: Copy the mongo.yaml file into webapp file and modify to suit the purpose. Create another file called webapp.yaml and change the values there. Go get the corrct image of web app from docker hub e.g nanajanashia/k8s-demo-app:v1.0
It is a simple nodejs application. It starts on port 3000. So change the port value to 3000 on the webapp file i.e the containerPort value. The containerPort will be the same as targetPort and can be the same as port in the service too. All three ports can be 3000
Next: Pass the data defined in the config and secret components
Need to set username and password for the mongodb app. These credentials can be configured during startup with environment variables in k8s documenttaion. They are required fields in most of the databases. Otherwise, we will not be able to access them. We pass the credentials through the env attributes (for environment variable). List of environment variables with names and values. Go and copy the syntax for the values of name and value from the documentation. We can either set the value of the username in there or go and reference it from the secret components in the configmap. So instead of hardcoding e.g myuser as the value for the environment variable user, lets change the value key to "valueFrom" key from the secret, by adding the secKeyRef tag. Under that we have the name of the secret, which is called "jide-secret" in my secret file. Then, the key for the secret in the secret file. In mine, it is called "jide-user", but you can call it "mongo-user" or whatever. Configure for password the same way.
Next: When our webapp starts, it needs to connect to the database. Need to give the webapp information about the database endpoint; where can it access the database. Which username and password to use, to authenticate with the database. So we need to pass them as environment variable in the web application too. Go to your webapp yaml file and add the environment variables after the containerPort. For the "USER_NAME", copy the block from the mong0 yaml file for username and paste it under. Do the same for password too. Our webapp needs the database endpoint, which will be DB_URL value for the name key. The value for it will be in the configmap file. Do it the same way as the config in secret, but change the ref  key to configMapKeyRef.
Next: Make it accessible from the browser: Create an external service. You can copy the service block from mongo file into webapp file to create the external service needed. Under the spec, define the type of service. By default the external service is a "ClusterIP". So if you don't specify, it will be clusterIP by default. But i will be specifying nodePort here. It is another type of external service. However, it reuires a 3rd port after the port, and targetPort,called nodePort. nodePort port is "the port that will opened on the k8s nodes on which the application will be accessible". On the nodeIP (the IP in the web browser) address and nodePort configuration, we will be able to access the external service, which will then access our webapp. This is a range that can be used for nodePort in Kubernetes. From 30000 to 32767- you can choose your nodePort value within this range. Lets choose 30300
Deploy your app through the minikube
run "kubectl get pod" to check if you have pods
check the ss
Create the external configurations first, because they need to be there before the app deployment
so secret and mongo config first
run " kubectl apply -f mongo-configmap.yaml" - , the -f flag means file. You can cd into any folder that has your configmap, for me, it is my :Kubernetes" folder.
Create the secret next: run the below command.
kubectl apply -f mongo-secret.yaml
No base24 for me. "echo -n jide-user | base64" and "echo -n jide-password | base64"
It is created after converting to base64
Next- deployment of the database. run the below command:
kubectl apply -f mongo.yaml  . 
check the below ss for the successful deployment. You can see that the deployment and service were created from the execution.
Deploy the webapp, by running the below command:
kubectl apply -f webapp.yaml
You can see from the ss, that it did not take my nodeport in the external service for the IP. It was demanding for clusterIP to be used. The error is actually in the syntax. it should be "NodePort" instead of "nodePort that i had in the config previously.
Check the new ss. You can see from the output that it has created a service successfully now. Note also, that it is saying that my webapp deployment is unchanged and the inetrnal service deployment is unchanged too.
Lets run some more commands to interact with the cluster.
Run "kubectl get all" ---- This gives you all the components that are created inside the cluster e.g  pods, deployments, service and replicaset.
Check the ss. There are 4 pods deployments, 3 repeated mongo pod and 1 webapp pod deploymnets with 1 replica each. We have services mongodb and webapp. The webapp service is of NodePort type, which means it can be accessed externally.
Run "kubectl get pod | configmap | secret |" to get the configmap and secret if you want OR
Just run "kubectl get configmap" and "kubectl get secret"
Check the ss for the outputs
kubectl get "components" gets you a list of the particular component e.g kubectl get pod--- gets you a list of pods. Check the ss.
kubectl help -- to get a list of commands that you can run
kubectl get --help -- to get a list of sub commands for get that you can run
Check the ss
kubectl describe "component" will give you more details about a particular deployment. e.g "kubectl describe service webapp-service"
"kubectl describe deployment webapp-deployment"  i.e kubectl describe "component" "name of the component". It will give you info about the "status", "the container configuration" "labels" etc
To check for logs for debugging. Do kubectl logs "name of the component" e.g name of a pod. You see the ports it is listening at and other information. logs of the container etc
To check if the app is accessible from the browser, run "kubectl get svc". Get the "NodePort" port from here
We need the IP where it will be accessible, for me it is the IP of the minikube that i set up. To get the ip of the minikube, run:
minikube ip OR
Run "kubectl get node" , then "kubectl get node -o wide" i.e get node of wide output. The internal ip from the output is your ip
You can use "-o wide" to get additional informational for any component e.g "kubectl get pod -o wide".
Grab the ip and the port where the app is accessible- for me it is 30300 and run them a browser.