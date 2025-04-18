# 🚀 Cloud Native Node.js Application with Kubernetes

This project demonstrates how to containerize a simple Node.js application and deploy it on a Kubernetes cluster using deployments and services.

---

## 📁 Project Structure

project-root/ │ ├── app/ │ ├── server.js │ └── package.json │ ├── k8s/ │ ├── deployment.yaml │ └── service.yaml │ └── Dockerfile

yaml
Copy
Edit

---

## 🔧 1. Create Your Node.js App

**app/server.js**
```js
const express = require('express');
const app = express();
const PORT = 3000;

app.get('/', (req, res) => {
  res.send('Hello from Kubernetes Node.js App!');
});

app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
📦 2. Initialize Node.js and Add Dependencies
Navigate into the app/ folder and run:

bash
Copy
Edit
cd app
npm init -y
npm install express
This will generate a package.json file with express as a dependency.

🐳 3. Create Dockerfile
Dockerfile (in the project root):

Dockerfile
Copy
Edit
# Use official Node.js image
FROM node:18

# Create app directory
WORKDIR /usr/src/app

# Copy app files
COPY app/package*.json ./
RUN npm install

COPY app/ .

# Expose app port
EXPOSE 3000

# Start the app
CMD ["node", "server.js"]
🛠️ 4. Build and Push Docker Image
Make sure Docker is running and you're logged into Docker Hub.

bash
Copy
Edit
docker build -t srii0305/k8s-node-app:v1 .
docker push srii0305/k8s-node-app:v1
☸️ 5. Kubernetes Deployment
k8s/deployment.yaml

yaml
Copy
Edit
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nodejs-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nodejs
  template:
    metadata:
      labels:
        app: nodejs
    spec:
      containers:
        - name: nodejs-container
          image: srii0305/k8s-node-app:v1
          ports:
            - containerPort: 3000
🌐 6. Kubernetes Service
k8s/service.yaml

yaml
Copy
Edit
apiVersion: v1
kind: Service
metadata:
  name: nodejs-service
spec:
  selector:
    app: nodejs
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
  type: ClusterIP
📥 7. Apply Kubernetes Configuration
bash
Copy
Edit
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
Check if your pods and service are running:

bash
Copy
Edit
kubectl get pods
kubectl get services
🌐 8. Access the Application
Port forward the service to access it in your browser:

bash
Copy
Edit
kubectl port-forward service/nodejs-service 3000:3000
Then visit http://localhost:3000 to see your app.

🔁 Update Application (Part II)
1️⃣ Modify the App
Update server.js with a new message, for example:

js
Copy
Edit
res.send('Hello from Kubernetes v2!');
2️⃣ Rebuild and Push New Docker Image
bash
Copy
Edit
docker build -t srii0305/k8s-node-app:v2 .
docker push srii0305/k8s-node-app:v2
3️⃣ Update Deployment
Edit k8s/deployment.yaml to use the new image:

yaml
Copy
Edit
image: srii0305/k8s-node-app:v2
Apply the changes:

bash
Copy
Edit
kubectl apply -f k8s/deployment.yaml
4️⃣ Verify the Update
bash
Copy
Edit
kubectl get pods
Make sure new pods are running the updated version.

5️⃣ Access Updated App
If port forwarding is not running, do:

bash
Copy
Edit
kubectl port-forward service/nodejs-service 3000:3000
Then check the updated message at http://localhost:3000

✅ Summary
✅ Built a Node.js app

✅ Containerized with Docker

✅ Deployed with Kubernetes

✅ Updated with zero downtime using versioned images

