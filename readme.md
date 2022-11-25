
# Kuberentes-Mongo-Express

Deploying a sample Mongo-Express (Client) and MongoDB Stack in Kuberentes Cluster. We will do it in two ways:
-  Stateless Way
-  StatefulSets Way (Recommended)

## Get Started 
Clone the repository and navigate to the project root directory. Make sure you the following requirements:
- A running Kubernetes Cluster in cloud or Minikube (if testing locally)
- If you are using cloud provider, make sure to download the kubeconfig.yaml file and export it  your enviroment as:

```
export KUBECONFIG=path-to-your-kubeconfig-file
```


## 1.  Stateless Way 
We will use the following components:
- Deployment - MondoDB and MongoExpress Pod
- Internal Service - MongoDB
- External Service (NodePort) - Mongo Express
- Config Map - For Database URLs
- Secret - For storing DB User and Password
  
### Generate MongoDB Secrets
- AS, Kubernets Secrets are base64 encoded. We need to encode your credentials before applying to secret. 

We can use any 3rd party tool for Windows. For Mac/Linux, run the following command and paste the resulting values in mongodb-sercret.yaml file: 

```bash
echo "mongodb-root-username" | base64

echo "your mongodb-root-password" | base64
```

### Apply kubectl commands in the following order
```bash   
kubectl apply -f mongo-db-secret.yaml
kubectl apply -f mongo-db.yaml
kubectl apply -f mongo-db-configmap.yaml 
kubectl apply -f mongo-express.yaml
```
### View the resources status using these kubectl get commands
```bash
kubectl get pod
kubectl get pod -o wide
kubectl get service
kubectl get secret
kubectl get configmap
```

### (Minikube Users Only): Give a URL to external service in minikube
```bash
minikube service mongo-express-service
```
You will get a service IP address you can use to access the application. Hurray!

# StatefulSets Way: MongoDB- Mongo Express via Helm Charts

We will use Linode for this purpose. However, you can do the same with other cloud providers. Just make sure to change the attributes accordingly. 
-  We will Utilize Linode StorageClass for PersistantVolume (to persist our data from MongoDB)
-  Use Linode NodeBalancer as the entrypoint to our cluster which sends the request to internal Ingress Controller and then finally to the Pods via Services
  
1. Setup a Linode Kubernetes Engine. Follow steps here
2. Navigate to */helm-ingress-statefulsets-linode* directory
3. Download kubeconfig and run the following commands:
```bash
  export KUBECONFIG={your-linode-kuberenetes-engine-kubeconfig-file}.yaml
  chmod o-r ~./k8s-mongo-express-kubeconfig.yaml
  chmod g-r ~./k8s-mongo-express-kubeconfig.yaml

```

4. Install [Helm](https://helm.sh/docs/intro/install/) if you haven't already. The following commands will install the mongodb StatefulSet with values from *mongodb-helm-values.yaml*. This also creates Persistent Volume in Linode for each statefulsets.
```
  helm repo add bitnami https://charts.bitnami.com/bitnami
```
```
  helm install mongodb --values=mongodb-helm-values.yaml bitnami/mongodb
```
5. Create MongoExpress Deployment and Service
```
  kubectl apply -f mongo-express.yaml
```

6.  Finaly, let's intall ingress-nginx controller in our cluster. This also creates Linode NodeBalancer cutomatically
```
  helm repo add nginx-stable https://helm.nginx.com/stable
```
```
  helm install nginx-ingres ingress-nginx/ingress-nginx 
```

7. Go to your Linode Dashbaord->LoadBalancer and grab the hostname url. In ingress.yaml file, paste the value under spec->rules->host
```
  kubectl apply -f ingress.yaml 
```

8. To delete the resources, you can simply use:
```bash 
  helm delete mongodb //for statefulsets resources
```
```bash 
  helm delete ingress //for ingress resources
```
That's it! Our MongoDB StatefulSets and MongoExpress Application is deployed!

## Contributions

Contributions are highly welcomed. Please send a Pull Request with suggested changes or open an Issue to get things started!
