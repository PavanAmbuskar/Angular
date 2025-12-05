## 🛠 Technologies Used

- Angular
- Node.js (Build stage)
- Nginx (Serving stage)
- Docker & DockerHub
- Jenkins CI/CD
- Linux (Ubuntu / CentOS)

---

# 🧱 **1. Build Setup**

### Install dependencies
```bash
npm ci

npm run build --configuration production

dist/angular-frontend/

docker build -t pavanambuskar/angular-frontend .

docker run -d -p 80:80 --name angular-frontend pavanambuskar/angular-frontend

docker login
docker push pavanambuskar/angular-frontend:latest
