# Next.js DevOps Assessment

A containerized Next.js application with automated CI/CD pipeline and Kubernetes deployment.

## Project Overview

This project demonstrates DevOps best practices including containerization, continuous integration/deployment, and orchestration using modern tools and technologies.

## Technologies Used

- **Next.js 15** - React framework for production
- **TypeScript** - Type-safe JavaScript
- **Docker** - Containerization platform
- **GitHub Actions** - CI/CD automation
- **GitHub Container Registry (GHCR)** - Container image registry
- **Kubernetes** - Container orchestration
- **Minikube** - Local Kubernetes development

## Prerequisites

Before running this project, ensure you have:

- Node.js 18+ and npm
- Docker Desktop
- Minikube
- kubectl
- Git

## Local Development

### Run Development Server

```bash
# Install dependencies
npm install

# Start development server
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

### Build Production Version

```bash
npm run build
npm start
```

## Docker

### Build Docker Image Locally

```bash
docker build -t nextjs-devops-app:latest .
```

### Run Docker Container

```bash
docker run -p 3000:3000 nextjs-devops-app:latest
```

Access the application at [http://localhost:3000](http://localhost:3000)

### Docker Configuration Highlights

- **Multi-stage build** - Separates build dependencies from runtime
- **Alpine Linux base** - Minimal image size (~50MB vs ~900MB)
- **Non-root user** - Enhanced security posture
- **Standalone output** - Optimized for containerized environments
- **Layer caching** - Faster subsequent builds

## CI/CD Pipeline

### GitHub Actions Workflow

The CI/CD pipeline automatically triggers on every push to the `main` branch:

**Workflow Steps:**
1. Checkout source code
2. Authenticate with GitHub Container Registry
3. Extract Docker metadata and tags
4. Set up Docker Buildx for advanced builds
5. Build Docker image with layer caching
6. Push image to GHCR with multiple tags

**Image Tags:**
- `latest` - Most recent build from main branch
- `main` - Latest main branch build
- `main-<commit-sha>` - Specific commit version

### Container Registry

Image available at:
```
ghcr.io/rajkaran-08/wexi-ai_assessment:latest
```

The image is publicly accessible and ready for deployment.

## Kubernetes Deployment

### Start Minikube

```bash
# Start local Kubernetes cluster
minikube start

# Verify cluster is running
kubectl cluster-info
minikube status
```

### Deploy Application

```bash
# Apply Kubernetes manifests
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml

# Verify deployment
kubectl get deployments
kubectl get pods
kubectl get services
```

### Access the Application

**Option 1: Minikube Service Command (Easiest)**
```bash
minikube service nextjs-service
```

**Option 2: Port Forwarding**
```bash
kubectl port-forward service/nextjs-service 3000:3000
```
Then access at [http://localhost:3000](http://localhost:3000)

**Option 3: NodePort Access**
```bash
# Get Minikube IP
minikube ip

# Access at http://<minikube-ip>:30080
```

### Kubernetes Configuration Details

**Deployment Features:**
- **Replicas:** 3 pods for high availability
- **Liveness Probe:** HTTP GET on `/` (checks if app is alive)
- **Readiness Probe:** HTTP GET on `/` (checks if app is ready for traffic)
- **Resource Limits:**
  - Memory: 256Mi limit, 128Mi request
  - CPU: 200m limit, 100m request
- **Rolling Updates:** 25% max unavailable during updates

**Service Configuration:**
- **Type:** NodePort (accessible outside cluster)
- **Port:** 3000 (internal)
- **NodePort:** 30080 (external access)

### Useful Kubernetes Commands

```bash
# View pod logs
kubectl logs -l app=nextjs-app

# View detailed pod information
kubectl describe pod <pod-name>

# Scale deployment
kubectl scale deployment nextjs-app --replicas=5

# Update image
kubectl set image deployment/nextjs-app nextjs=ghcr.io/rajkaran-08/wexi-ai_assessment:latest

# Restart deployment
kubectl rollout restart deployment nextjs-app

# View rollout status
kubectl rollout status deployment nextjs-app

# Delete resources
kubectl delete -f k8s/
```

## Project Structure

```
.
├── .github/
│   └── workflows/
│       └── docker-build-push.yml    # CI/CD pipeline configuration
├── app/                             # Next.js app directory
│   ├── layout.tsx
│   ├── page.tsx
│   └── globals.css
├── k8s/
│   ├── deployment.yaml              # Kubernetes Deployment
│   └── service.yaml                 # Kubernetes Service
├── public/                          # Static assets
├── .dockerignore                    # Docker build exclusions
├── .gitignore                       # Git exclusions
├── Dockerfile                       # Multi-stage Docker build
├── next.config.ts                   # Next.js configuration
├── package.json                     # Dependencies and scripts
├── README.md                        # This file
└── tsconfig.json                    # TypeScript configuration
```

## Docker Optimization Techniques

1. **Multi-stage builds** - Separate dependency installation, build, and runtime stages
2. **Minimal base image** - Alpine Linux reduces image size by 95%
3. **Layer caching** - Package files copied before source code for better cache utilization
4. **Production dependencies only** - Runtime stage includes only production dependencies
5. **Non-root user execution** - Security best practice
6. **Standalone output** - Next.js optimization for containerized deployments

## Assessment Checklist

- ✅ Next.js application created and functional
- ✅ Dockerfile with multi-stage build and best practices
- ✅ .dockerignore for build optimization
- ✅ GitHub Actions workflow for automated CI/CD
- ✅ Docker image automatically pushed to GHCR
- ✅ Proper image tagging strategy implemented
- ✅ Kubernetes Deployment with 3 replicas
- ✅ Health checks configured (liveness and readiness)
- ✅ Resource limits and requests defined
- ✅ Kubernetes Service for application exposure
- ✅ Comprehensive documentation

## Troubleshooting

### Docker Issues

```bash
# Clean Docker build cache
docker system prune -af
docker builder prune -af

# Rebuild without cache
docker build --no-cache -t nextjs-devops-app:latest .
```

### Minikube Issues

```bash
# Restart Minikube
minikube stop
minikube start

# Delete and recreate cluster
minikube delete
minikube start

# Check cluster status
minikube status
kubectl get nodes
```

### Pod Issues

```bash
# Check pod status
kubectl get pods

# View pod logs
kubectl logs <pod-name>

# Describe pod for events
kubectl describe pod <pod-name>

# Get pod details
kubectl get pod <pod-name> -o yaml
```

### Image Pull Issues

```bash
# Verify image exists
docker pull ghcr.io/rajkaran-08/wexi-ai_assessment:latest

# Check image pull policy in deployment
kubectl describe deployment nextjs-app
```

## Security Considerations

- Non-root user in Docker container
- Minimal attack surface with Alpine base image
- Resource limits prevent resource exhaustion
- Health checks ensure failed containers are restarted
- No sensitive data in container images

## Performance Optimizations

- Multi-stage Docker builds reduce final image size
- Next.js standalone output optimizes for production
- Layer caching speeds up builds
- Resource requests/limits optimize cluster utilization
- Multiple replicas provide load distribution

## Future Improvements

- Add Horizontal Pod Autoscaler (HPA)
- Implement Ingress for advanced routing
- Add monitoring with Prometheus/Grafana
- Implement ConfigMaps for environment configuration
- Add Secrets management for sensitive data
- Set up staging and production environments
- Implement blue-green or canary deployments

## License

MIT

## Author

**Rajkaran Singh**

- GitHub: [@Rajkaran-08](https://github.com/Rajkaran-08)
- Repository: [wexi-ai_assessment](https://github.com/Rajkaran-08/wexi-ai_assessment)
- GHCR Image: [ghcr.io/rajkaran-08/wexi-ai_assessment](https://ghcr.io/rajkaran-08/wexi-ai_assessment)

---

**DevOps Internship Assessment Submission**

This project demonstrates proficiency in:
- Container orchestration and management
- CI/CD pipeline implementation
- Infrastructure as Code
- Cloud-native application deployment
- DevOps best practices and tooling