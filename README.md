# 🎨 Nginx Load Balancer Demo

A simple demonstration of nginx load balancing between 3 colored web containers (Red, Blue, Green).

## 📋 Project Structure

```
nginx-loadbalancer-demo/
├── docker-compose.yml
├── nginx.conf
├── red/
│   ├── Dockerfile
│   └── index.html
├── blue/
│   ├── Dockerfile
│   └── index.html
└── green/
    ├── Dockerfile
    └── index.html
```

## 🚀 Quick Start

### Prerequisites
- Docker
- Docker Compose

### Run the Project

1. Navigate to the project directory:
```bash
cd nginx-loadbalancer-demo
```

2. Start all containers:
```bash
docker-compose up -d
```

3. Open your browser and go to:
```
http://localhost:8080
```

4. **Refresh the page** multiple times to see the load balancer routing traffic between the three colored containers!

## 🎯 How It Works

- **3 Backend Containers**: Each serves a simple HTML page with a different color (Red 🔴, Blue 🔵, Green 🟢)
- **1 Nginx Load Balancer**: Distributes incoming requests across the 3 backend containers using round-robin algorithm
- **Port 8080**: The main entry point to access the application

## 🔄 Load Balancing

Nginx uses **round-robin** load balancing by default:
- Request 1 → Red Container
- Request 2 → Blue Container
- Request 3 → Green Container
- Request 4 → Red Container (cycle repeats)

## 🛠️ Useful Commands

### View running containers
```bash
docker-compose ps
```

### View logs
```bash
docker-compose logs -f
```

### Stop all containers
```bash
docker-compose down
```

### Rebuild and restart
```bash
docker-compose up -d --build
```

### Test load balancing with curl
```bash
for i in {1..9}; do curl http://localhost:8080 && sleep 1; done
```

## 📊 Architecture

```
                    ┌─────────────────┐
                    │   Browser       │
                    │ localhost:8080  │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  Nginx Load     │
                    │   Balancer      │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
         ┌────▼────┐    ┌───▼────┐    ┌───▼────┐
         │  Red    │    │ Blue   │    │ Green  │
         │Container│    │Container    │Container
         │  :80    │    │  :80   │    │  :80   │
         └─────────┘    └────────┘    └────────┘
```

## 🎨 Customization

To add more backend servers, edit `nginx.conf`:

```nginx
upstream backend {
    server red-app:80;
    server blue-app:80;
    server green-app:80;
    server your-new-app:80;  # Add here
}
```

And add the service to `docker-compose.yml`.

## 📝 Notes

- Each container runs nginx serving a static HTML page
- The load balancer uses Docker's internal DNS to resolve container names
- All containers are on the same bridge network (`app-network`)

---

**Made with ❤️ for learning load balancing concepts**
# nginx-loadbalancer-demo
