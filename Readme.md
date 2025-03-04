sudo yum update -y
sudo yum install -y git docker mysql
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user
exit
ssh again 
delete aws config & create it again (mkdir ~/.aws)

# installing kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Install kind
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/

# Create a configuration file for kind
cat <<EOF > kind-config.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 30000
    hostPort: 30000
    protocol: TCP
EOF

# Create the cluster using the config
kind create cluster --config kind-config.yaml --name k8s-assignment

kubectl cluster-info

aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com

docker pull your-account-id.dkr.ecr.your-region.amazonaws.com/your-image:tag (mysql)

docker pull your-account-id.dkr.ecr.your-region.amazonaws.com/your-image:tag (webapp)

docker tag your-account-id.dkr.ecr.your-region.amazonaws.com/your-image:mysql:v1 (mysql)

docker tag your-account-id.dkr.ecr.your-region.amazonaws.com/your-image:webapp:v1 (webapp)

kind load docker-image your-image:tag --name k8s-assignment (for both)


kubectl get pods -n namespace(webapp or mysql)   [for checking]

# for creating a secret
kubectl create secret docker-registry ecr-secret -n ()\
  --docker-server=166147787843.dkr.ecr.us-east-1.amazonaws.com \
  --docker-username=AWS \
  --docker-password=$(aws ecr get-login-password --region us-east-1) 

kubectl apply -f namespaces.yaml
kubectl apply -f mysql_pod.yaml 
kubectl apply -f mysql_service.yaml

kubectl get svc -n mysql


kubectl apply -f webapp_pod.yaml

curl (in browser 8080 port) 

kubectl apply -f webapp_service.yaml


kubectl get svc -n webapp

kubectl apply -f mysql_replicaset.yaml 
kubectl apply -f webapp_replicaset.yaml 


kubectl apply -f mysql_deployment.yaml
kubectl apply -f webapp_deployment.yaml

kubectl get pods -n webapp -l app=employees
