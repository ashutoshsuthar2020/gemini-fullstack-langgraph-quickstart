# Deploying Gemini Fullstack LangGraph on Kubernetes (Minikube)

This guide will help you (or any new contributor) deploy the Gemini Fullstack LangGraph application on a local Kubernetes cluster using Minikube.

---

## Prerequisites

- [Docker](https://www.docker.com/) installed and running
- [kubectl](https://kubernetes.io/docs/tasks/tools/) installed
- [Minikube](https://minikube.sigs.k8s.io/docs/) installed
- Google Gemini API key and LangSmith API key

---

## 1. Build the Docker Image

From the project root, build the Docker image:
```sh
docker build -t gemini-fullstack-langgraph:latest -f Dockerfile .
```

---

## 2. Set Up Kubernetes Secrets

Edit `k8s/secrets.yaml` and add your actual API keys:
```yaml
stringData:
  gemini-api-key: <your-gemini-key>
  langsmith-api-key: <your-langsmith-key>
  postgres-password: "postgres"
```

Apply the secrets:
```sh
kubectl apply -f k8s/secrets.yaml
```

---

## 3. Deploy Dependencies (Postgres & Redis)

```sh
kubectl apply -f k8s/dependencies.yaml
```

---

## 4. Deploy the Application

Apply the service and deployment manifests:
```sh
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/deployment.yaml
```

---

## 5. Expose the Application

Start the Minikube tunnel in a new terminal:
```sh
minikube tunnel
```

---

## 6. Access the Application

Once the tunnel is running, open your browser and go to:
```
http://localhost:8000/app/
```
This will load the frontend. The backend API is available at `http://localhost:8000`.

---

## 7. Troubleshooting

- Check pod and service status:
  ```sh
  kubectl get pods
  kubectl get svc
  ```
- View backend logs:
  ```sh
  kubectl logs deployment/gemini-app
  ```
- If you see 404s or connection errors, ensure your frontend is using the correct API path and port (`/threads` or `/api/...` on port 8000).

---

## 8. Clean Up

To remove all resources:
```sh
kubectl delete -f k8s/
```

---

**Note:**  
- The default setup serves both frontend and backend from the same container and port (8000).
- If you want to change the backend port, update both the deployment and service YAMLs, and ensure your frontend calls the correct port. 