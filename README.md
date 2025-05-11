# **SIT323/SIT737- Cloud Native Application Development - Task 9.1P: Adding a database to your application** 

### **Containerised Registration App with MongoDB Integration on Kubernetes**
### Student Name: Inwang Ubong Marshal
### Student ID: 222093271

## **Project Overview**

The purpose of this project is to show how a simple Node.js microservice application that uses MongoDB Atlas in the cloud is developed and deployed. It was a project created as a practical evaluation for SIT323/SIT737-2025 and demonstrates a complete DevOps workflow of Docker, Kubernetes, and database integration in a microservice model.

The main objective of the application is to make the user registration process easy, users can enter their name, email, password and phone number, which are saved safely on MongoDB. One of the main things of the project is the integration of persistent storage, environment-specific configuration handled with Kubernetes secrets and containerised deployment configuration for increased scalability and portability.

The key objectives are:
* To design a functional Node.js/Express backend with MongoDB integration.
* To containerise the application using Docker.
* To deploy the application to a Kubernetes cluster with service exposure via NodePort.
* To secure sensitive credentials using Kubernetes secrets.
* To verify CRUD functionality and MongoDB connectivity using a frontend registration form.
* To ensure operational resilience through health checks and optional alerting.

This set of configuration files includes `Dockerfile`, `docker-compose.yaml`, configuration manifests (e.g., deployment manifests), and complete documentation to recreate the environment from scratch.


## **Tools and Technologies**

For the development, deployment and management of a scalable microservice application, a cloud-native toolset is used in this project. Every technology factor in to ensure optimal performance, reliability, and ease of maintenance.

* **Node.js**: Node.js sits at the heart of the registration application’s backend architecture. The ability of Node.js to act in a non-blocking and event-driven form makes it suitable for the efficient undertaking of I/O functions such as database transactions. Express.js makes the development of RESTful APIs easier because it manages the route mapping and largely takes care of the configuration of middleware.

* **MongoDB Atlas**: This cloud-based NoSQL database can flexibly store document-based information. Atlas automates the tasks related to infrastructure, which means that the application can concentrate on processing the information about the user’s registration. Atlas allows flexibility in scaling, incorporation performance monitoring capabilities, and incorporates security measures.

* **Docker**: Docker allows for application portability through packaging the backend and dependencies, and the environment inside individual isolated containers. This normalises performance across environments and makes it easy to deploy the service on multiple platforms.

* **Kubernetes**: Thanks to Kubernetes, containerised applications are effectively managed, scaled, and deployed on a large scale. Declarative manifests set the path of deployment, service discovery, and auto-restarts. Via a NodePort service, Browser users are able to access the application.

* **MongoDB Compass / Mongosh**: These tools allow developers to visually inspect or interact with the MongoDB database. They are essential for verifying data insertion, structure, and connectivity.

* **GitHub**: GitHub serves as the version control platform to host all code and configuration files, enabling collaboration, documentation, and submission tracking.


## **Folder Structure**

The project is organised to reflect a standard Node.js web application structure integrated with Kubernetes and Docker configurations. This layout promotes modularity, reusability, and ease of deployment.
```
.

├── node_modules/                # Node.js dependencies
├── public/                     # Static frontend files
│   ├── index.html              # HTML form for registration
│   ├── script.js               # JavaScript for client-side logic
│   └── style.css               # Styling for the form
├── Screenshots/               # Screenshots used for documentation
├── .env                       # Environment variables (excluded from GitHub)
├── app-deployment.yaml       # Kubernetes Deployment & Service
├── application.js            # Main server logic and DB integration
├── docker-compose.yaml       # Optional local Docker Compose file
├── Dockerfile                # Docker instructions to containerise app
├── mongo-secret.yaml         # Kubernetes Secret for MongoDB URI
├── package.json              # Node.js dependencies and scripts
├── package-lock.json         # Dependency lockfile
```
This structure helps separate concerns across different layers—UI, server logic, environment configurations, deployment artifacts, and documentation.


## **MongoDB Atlas Setup and Integration (with MongoDB Compass)**

Before deploying our microservice application with database integration, it was essential to establish a secure and scalable cloud database. For this project, MongoDB Atlas was chosen as the cloud-hosted NoSQL solution due to its managed infrastructure, built-in scalability, security features, and ease of integration with Kubernetes and Node.js applications.

### Step 1: Creating an Atlas Account and Logging In

The process began by visiting MongoDB Atlas and creating a free-tier account. Once signed in, we accessed the dashboard and selected “Create New Project”. Projects in Atlas act as containers for clusters, users, and databases.

![alt text](/Screenshots/CreateNewProject.jpg)

### Step 2: Creating a Free Cluster

Inside the new project, we created a Free M0 Cluster by selecting AWS as the cloud provider and ap-southeast-2 (Sydney) as the region for optimised latency.
This cluster provides 512 MB storage, and a replica set with 3 nodes, sufficient for testing and development purposes.
 
![alt text](/Screenshots/Cluster%20Created.jpg)

### Step 3: Creating the Database and User

Once the cluster was ready, we created a database named registerdb and added a collection users.
To interact securely, we created a new MongoDB user appuser with password apppassword123, and assigned the Atlas Admin role to manage all database activities. 

![alt text](/Screenshots/Mongosh.png)

### Step 4: Connecting via MongoDB Compass and URI Generation

To manage data visually and perform manual verifications, we used MongoDB Compass, a GUI client for MongoDB.
In Atlas, we clicked “Connect → Connect with MongoDB Compass” and copied the generated connection string:

```
mongodb+srv://appuser:apppassword123@cluster123.2ldn7g1.mongodb.net/registerdb?retryWrites=true&w=majority
```

This URI was tested and verified in both Compass and Mongosh shell for CRUD operations.

![alt text](/Screenshots/MongoCompass.png)

### Step 5: Adding the URI to Kubernetes Secret
This URI was added to Kubernetes as a secret using mongo-secret.yaml:

```
stringData:
     MONGO_URI: "mongodb+srv://appuser:apppassword123@cluster123.2ldn7g1.mongodb.net/registerdb?retryWrites=true&w=majority"
```

By using secrets, sensitive credentials were kept out of the public Docker image and configuration files, ensuring security and modularity in our deployment.

### Justification and Importance

Using MongoDB Atlas removed the complexity of self-managing databases within Kubernetes. It offered:

* High Availability via a replica set
* Persistent Storage managed by Atlas (no need for separate PV/PVC setup)
* Easy Monitoring and Alerts with built-in dashboards and integration
* Secure Access Control with user authentication and IP whitelisting

This setup allowed the registration microservice to seamlessly connect to registerdb, persist user data, and provide reliable backend support—laying a solid foundation for scalable and secure deployments.

![alt text](/Screenshots/DatabaseMongosh.png)

## **Step-by-Step Process**

### Step 1: Develop Node.js Application

The first step is to build the core functionality using Express and MongoDB. The backend logic is defined in `application.js`, and frontend assets are stored inside the `public/` folder.

**HTML Frontend**:

The `index.html` contains a simple form to capture user details such as name, email, password, and phone number. This form is styled using `style.css` and made interactive using `script.js`.

**Express Server (`application.js`)**:

The backend uses `Express.js` to serve static files, handle form submission, and connect to MongoDB Atlas. The registration route listens for POST requests and stores data securely.

**MongoDB Schema**:

In `application.js` User schema and model is typically used to define the Mongoose schema. It ensures data validation before saving into MongoDB.

![alt text](Screenshots/image.png)

![alt text](Screenshots/LocalHost:3000.png)

### Step 2: Prepare Environment Variables

Use environment variables to manage sensitive configurations like the MongoDB connection string.

Create a `.env` file with:

```
# .env
MONGO_URI=mongodb+srv://appuser:apppassword123@cluster123.2ldn7g1.mongodb.net/registerdb?retryWrites=true&w=majority
PORT=3000
```

### Step 3: Dockerise the Application

Use Docker to containerise the app, ensuring consistency across all environments.

Create a `Dockerfile`:

```
FROM node:18
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
CMD ["node", "application.js"]
```

Build and push the image in Docker Hub through terminal:
```
docker build -t registration-app:latest .
docker tag registration-app:latest 222093271/registration-app:latest
docker push 222093271/registration-app:latest
```

![alt text](/Screenshots/Docker%20Image.png)

![alt text](/Screenshots/Docker%20Hub.png)

### Step 4: Create MongoDB Secret in Kubernetes

Secure the MongoDB URI using Kubernetes Secrets to avoid hardcoding it in your code.

* mongo-secret.yaml: 

```
apiVersion: v1
kind: Secret
metadata:
  name: mongodb-uri-secret
  namespace: default
  labels:
    app: registration-app
stringData:
  MONGO_URI: "mongodb+srv://appuser:apppassword123@cluster123.2ldn7g1.mongodb.net/registerdb?retryWrites=true&w=majority"
```

* Apply the secret:
```
kubectl apply -f mongo-secret.yaml  
```
![alt text](/Screenshots/Mongo-secret.png)

```
apiVersion: v1
data:
  MONGO_URI: bW9uZ29kYitzcnY6Ly9hcHB1c2VyOmFwcHBhc3N3b3JkMTIzQGNsdXN0ZXIxMjMuMmxkbjdnMS5tb25nb2RiLm5ldC9yZWdpc3RlcmRiP3JldHJ5V3JpdGVzPXRydWUmdz1tYWpvcml0eSZhcHBOYW1lPUNsdXN0ZXIxMjM=
kind: Secret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Secret","metadata":{"annotations":{},"name":"mongodb-uri-secret","namespace":"default"},"stringData":{"MONGO_URI":"mongodb+srv://appuser:apppassword123@cluster123.2ldn7g1.mongodb.net/registerdb?retryWrites=true\u0026w=majority\u0026appName=Cluster123"},"type":"Opaque"}
  creationTimestamp: "2025-05-10T05:48:47Z"
  name: mongodb-uri-secret
  namespace: default
  resourceVersion: "14658"
  uid: 9a49f628-81b4-4017-b385-4b2c3d2428c1
type: Opaque
```

### Step 5: Kubernetes Deployment and Service

Deploy the app using Kubernetes and expose it via a NodePort service.

* app-deployment.yaml:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: registration-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: registration-app
  template:
    metadata:
      labels:
        app: registration-app
    spec:
      containers:
        - name: registration-app
          image: 222093271/registration-app:latest
          ports:
            - containerPort: 3000
          env:
            - name: MONGO_URI
              valueFrom:
                secretKeyRef:
                  name: mongodb-uri-secret
                  key: MONGO_URI
---
apiVersion: v1
kind: Service
metadata:
  name: registration-service
spec:
  type: NodePort
  selector:
    app: registration-app
  ports:
    - port: 80
      targetPort: 3000
      nodePort: 30080
```

* Apply deployment:

```
kubectl apply -f app-deployment.yaml
```

![alt text](/Screenshots/Kubectl%20Pods.png)

![alt text](/Screenshots/Kubectl%20Logs.png)

### Step 6: Test and Verify Application

Once deployed:

* Visit: http://localhost:30080
* Fill the form and click submit.
* Check MongoDB Compass or Mongosh to verify data is inserted into the registerdb.users collection.

![alt text](/Screenshots/Frontend.jpg)

![alt text](/Screenshots/Mongodb%20Compass.png)

## **Testing CRUD Operations and Setting Up Backups**

### Step 1: Verifying CRUD Operations with MongoDB Atlas

After successfully deploying the Node.js application using Docker and Kubernetes, and configuring the database URI via `mongo-secret.yaml`, the final critical step was to test whether the application could perform essential CRUD (Create, Read, Update, Delete) operations on the MongoDB Atlas database.

✅ **Create (Insert)**

Upon accessing the web application via `http://localhost:30080`, users are presented with a registration form. When the form is submitted:

A `POST` request is made to `/register`

The form data (name, email, password, phone) is validated and stored in the `users` collection of the `registerdb` database on MongoDB Atlas via the Mongoose schema

Verification:

* Opened MongoDB Compass
* Navigated to: `registerdb` > `users`
* Verified that the submitted document appeared in the collection

![alt text](/Screenshots/CreateDB.jpg)

✅ **Read**

To ensure data was stored correctly, a `find()` query was executed using Mongosh or MongoDB Compass:

```
db.users.find().pretty()
```

The expected documents were retrieved, confirming that read operations were functioning properly.

![alt text](/Screenshots/ReadDB.jpg)

✅ **Update**

Using either Compass or CLI, documents were updated. For example:

```
db.users.updateOne({ email: "testuser123@gmail.com" }, { $set: { phone: "0123456789" } })
```

Verification confirmed the phone field was modified for the matching document.

![alt text](/Screenshots/UpdateDB.jpg)

✅ **Delete**
To confirm delete functionality, this command was used:

```
db.users.deleteOne({ email: "testuser123@deakin.edu.au" })
```

The deletion was validated as the document no longer appeared in query results.

![alt text](/Screenshots/DeleteDB.jpg)

### Step 2: Setting Up Database Backups and Disaster Recovery

Although MongoDB Atlas Free Tier (M0) does not support automated backups, best practices and preparedness were still applied:

🔒 Atlas Snapshot Feature (Available on Paid Plans)

In a paid setup (M2+), continuous or scheduled cloud backups can be enabled via the Backup tab in MongoDB Atlas. These allow for point-in-time recovery and disaster protection.

![alt text](/Screenshots/BackupDisable.jpg)

### Download MongoDB Database Tools

Go to the official MongoDB Database Tools page:

🔗 https://www.mongodb.com/try/download/database-tools

* Choose your OS (e.g., Windows)

* Download the .zip or .msi installer

* Extract & Set Environment Variable

If you downloaded the ZIP:

1. Extract the ZIP to a folder (e.g., C:\mongodb-tools)

2. Copy the path of the bin folder, e.g., C:\mongodb-tools\bin

3. Add this path to your system environment variables:

    * Open System Properties > Environment Variables

    * Under System Variables, find Path → Click Edit

    * Click New → Paste the path

    * Click OK to save

### Manual Backup Strategy (For Free Tier)

![alt text](/Screenshots/Cluster-FreeTier.jpg)

To simulate a backup process in the free-tier setup:

1. Open MongoDB Compass

2. Go to the users collection

3. Click EXPORT DATA

4. Choose JSON or CSV

5. Save the file securely

This provides a snapshot that can be imported later if recovery is needed.

```
mongoimport --uri "mongodb+srv://appuser:apppassword123@cluster123.2ldn7g1.mongodb.net/registerdb?retryWrites=true&w=majority&appName=Cluster123" --collection users --file users.json --jsonArray
```

![alt text](/Screenshots/UserCOllection.png)

### Disaster Recovery Measures

* **Secrets Managed via Kubernetes**: The MongoDB URI is stored as a Kubernetes secret (mongodb-uri-secret.yaml) to allow seamless recovery or redeployment

* **Docker Images Pushed to Docker Hub**: Ensures application version control and redeployment

* **GitHub Repository**: Acts as the single source of truth for app logic, Dockerfiles, and YAMLs

These practices ensure that in the event of a failure, the application can be re-deployed with the same state and data restored using backups.

## **Monitoring and Health Checks**

To ensure the deployed Node.js registration application remains stable and performs efficiently, proactive monitoring and robust health checks were integrated. This section explains both aspects in detail, along with the implemented configurations and the observed system behavior through MongoDB Atlas.

### Kubernetes Health Checks

The application deployment was enhanced with two critical Kubernetes health probes: livenessProbe and readinessProbe, defined within the app-deployment.yaml file. These health checks automatically verify the operational state of the container and its readiness to serve requests.

```
livenessProbe:
  httpGet:
    path: /
    port: 3000
  initialDelaySeconds: 15
  periodSeconds: 20
  failureThreshold: 3
readinessProbe:
  httpGet:
    path: /
    port: 3000
  initialDelaySeconds: 5
  periodSeconds: 10
  failureThreshold: 3
```

Liveness Probe: It verifies that the application is never stuck or dead. If the check fails three times consecutively, Kubernetes is going to restart the container.

Readiness Probe: It attests that this is the status of the application with respect to receiving and serving requests. The pod stays outside of the service endpoint list until the check succeeds.

This way, the application will always be highly available and resilient in the Kubernetes cluster.

![alt text](/Screenshots/HealthChecks.png)

### MongoDB Atlas Monitoring

MongoDB Atlas delivers a wide range of dashboards for monitoring the health of your hosted `registerdb` cluster. On the “Metrics” tab, users get live graphs that are ordered by nodes, also showing highly important details, such as connection activity, throttled operations, data throughput, query execution, and command operations.

![alt text](/Screenshots/AtlasMetrics.jpg)

![alt text](/Screenshots/Network-LogicalSize.jpg)

![alt text](/Screenshots/Insert-Query-Command.jpg)

![alt text](/Screenshots/Update-Delete.jpg)

![alt text](/Screenshots/ClusterOverview.jpg)

These visualisations are essential in identifying performance deficiencies, detecting non-analogous increases in the load, or indicating unanticipated system failure. An example is the connections graph, which tells the number of simultaneous database clients, an indicator for analysis of scalability requirements. Similarly, the operation throttling chart can show whether the cluster has arrived at the boundaries of usage.

### Alert Configuration in MongoDB Atlas

MongoDB Atlas provides inherent alerting capability that can enable you to monitor crucial system events and performance. For this project, an alert was established in the Cloud Backup to notify the users as soon as " Backup Compliance policy has been met”. An email alert occurs as soon as any successful completion of backup tasks occurs in real time. Such alerts are fundamental to the protection of data integrity, supporting the rapid disaster recovery strategies and the need to follow organisational or regulatory requirements.

![alt text](/Screenshots/ClusterAlert.png)

### Monitoring Usefulness

The usage of Kubernetes health checks plus Atlas performance metrics increases the value for the application in that:

* **Early issue detection**: Downtime or backend crashes are automatically rectified via liveness checks.

* **Visibility into usage trends**: Traffic and command volume trends support better resource allocation decisions.

* **Database insight**: Read/write latencies, query rates, and cluster resource utilisation guide optimisation strategies.

These combined tools not only ensure application stability but also provide developers and system administrators with actionable insights to plan for scale, improve reliability, and react to issues swiftly.

## **Final GitHub Submission**

We host all project files in GitHub to enable version control, collaboration, and transparent submission. It ensures reproducibility, allows instructors to verify code, configurations, and documentation, and supports CI/CD pipelines if extended. GitHub serves as a centralised, accessible, and verifiable source for all components of the deployment workflow.

![alt text](/Screenshots/GithubSubmission.png)

## **Conclusion**

In conclusion, this project successfully demonstrates the development, containerisation, deployment, and monitoring of a full-stack Node.js registration application connected to a MongoDB Atlas database within a Kubernetes environment. It integrates modern DevOps principles with cloud-native application deployment workflows, resulting in a production-ready, scalable, and observable microservice.

* The **Node.js application** provides a responsive registration interface and interacts with the `registerdb` database securely via Mongoose.

* **MongoDB Atlas** offers a flexible and scalable NoSQL backend with integrated performance monitoring and automated backups.

* **Docker** is used to containerise the entire application, ensuring consistency across development, testing, and deployment environments.

* **Kubernetes** orchestrates deployment, load balancing, health checks, and service exposure using `NodePort`.

* The application utilises **Secrets Management** to secure database credentials and `.env` variables, preserving confidentiality and integrity in cloud deployments.

* **Health probes** (liveness/readiness) ensure continuous availability and reliability of the service.

* Comprehensive **monitoring dashboards** from MongoDB Atlas allow for performance inspection at the replica set, query, and operation level.

This project reflects an understanding of real-world software delivery pipelines and cloud infrastructure management. It simulates how scalable microservices are built, connected to cloud-managed databases, and deployed with automation and observability.

The inclusion of detailed documentation, structured code organisation, YAML configuration files, and version-controlled assets in GitHub ensures that the project can be easily reused, improved, or scaled by any team. Future improvements could include enabling continuous deployment (CI/CD) using GitHub Actions, integrating unit tests, or deploying in a managed Kubernetes environment like GKE or EKS.

*📌 This GitHub repository serves as both the working directory and deployment blueprint for the registration application.*
