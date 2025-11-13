# 🎨 Load Balancer Demo (Nginx & Traefik)

A simple demonstration of load balancing between 3 colored web containers (Red, Blue, Green) using **Nginx** or **Traefik**.

## 📋 Project Structure

```
nginx-loadbalancer-demo/
├── docker-compose.yml              # Nginx setup
├── docker-compose-traefik.yml      # Traefik setup
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

Choose your load balancer:

### Option 1: Nginx (Traditional)

**Start:**
```bash
docker-compose up -d
```

**Access:** http://localhost:8080

### Option 2: Traefik (Modern & Auto-Discovery)

**Start:**
```bash
docker-compose -f docker-compose-traefik.yml up -d
```

**Access:**
- **App:** http://localhost:9080
- **Dashboard:** http://localhost:9081

## 🆚 Nginx vs Traefik

| Feature | Nginx | Traefik |
|---------|-------|---------|
| **Configuration** | Static file (`nginx.conf`) | Docker labels (auto-discovery) |
| **Health Checks** | Basic (passive) | Advanced (active probing) |
| **Dashboard** | ❌ No | ✅ Built-in web UI |
| **Add Containers** | Edit config + restart | Automatic detection |
| **Best For** | Traditional, high performance | Cloud-native, microservices |
| **Access** | Port 8080 | Port 9080 |

## 🎯 How It Works

### Nginx Approach
- Static configuration in `nginx.conf`
- Manual server list
- Round-robin load balancing
- Requires restart for changes

### Traefik Approach
- Watches Docker socket for new containers
- Auto-discovers services via labels
- Dynamic routing without restart
- Built-in health checks every 5 seconds

## 📊 Load Balancing
- Request 1 → Red Container
- Request 2 → Blue Container
- Request 3 → Green Container
- Request 4 → Red Container (cycle repeats)

## 🛠️ Useful Commands

### Nginx Commands

**View running containers:**
```bash
docker-compose ps
```

**View logs:**
```bash
docker-compose logs -f
```

**Stop all containers:**
```bash
docker-compose down
```

**Rebuild and restart:**
```bash
docker-compose up -d --build
```

### Traefik Commands

**View running containers:**
```bash
docker-compose -f docker-compose-traefik.yml ps
```

**View logs:**
```bash
docker-compose -f docker-compose-traefik.yml logs -f
```

**Stop all containers:**
```bash
docker-compose -f docker-compose-traefik.yml down
```

**Restart Traefik only:**
```bash
docker-compose -f docker-compose-traefik.yml restart traefik
```

### Test Load Balancing

**Test Nginx:**
```bash
for i in {1..9}; do curl http://localhost:8080 && sleep 1; done
```

**Test Traefik:**
```bash
for i in {1..9}; do curl http://localhost:9080 && sleep 1; done
```

## 🧪 Testing Failover

### With Nginx:
```bash
# Stop a container
docker stop red-app

# Nginx detects failure after 2 attempts (takes ~5 seconds)
# Traffic routes to blue and green only

# Restart container
docker start red-app

# Auto-recovers within 5 seconds
```

### With Traefik:
```bash
# Stop a container
docker stop red-app

# Traefik health check detects failure in 5 seconds
# Automatically removes from pool
# Watch in dashboard: http://localhost:9081

# Restart container
docker start red-app

# Traefik auto-discovers and adds back in 5 seconds
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

### Adding Containers with Nginx

1. Edit `nginx.conf`:
```nginx
upstream backend {
    server red-app:80;
    server blue-app:80;
    server green-app:80;
    server yellow-app:80;  # Add here
}
```

2. Add service to `docker-compose.yml`
3. Restart nginx: `docker-compose restart nginx-lb`

### Adding Containers with Traefik

1. Create container with same labels:
```yaml
yellow-app:
  build: ./yellow
  labels:
    - "traefik.enable=true"
    - "traefik.http.services.backend.loadbalancer.server.port=80"
```

2. Start it: `docker-compose -f docker-compose-traefik.yml up -d yellow-app`
3. **That's it!** Traefik auto-discovers and adds it instantly.

## ⚙️ Configuration Details

### Nginx Health Checks
```nginx
server red-app:80 max_fails=2 fail_timeout=5s;
```
- `max_fails=2`: Mark down after 2 failures
- `fail_timeout=5s`: Wait 5s before retry

### Traefik Health Checks
```yaml
- "traefik.http.services.backend.loadbalancer.healthcheck.path=/"
- "traefik.http.services.backend.loadbalancer.healthcheck.interval=5s"
- "traefik.http.services.backend.loadbalancer.healthcheck.timeout=3s"
```
- Active probing every 5 seconds
- 3 second timeout per check

## 📝 Notes

- Each container runs nginx serving a static HTML page
- Load balancers use Docker's internal DNS to resolve container names
- All containers are on the same bridge network (`app-network`)
- Both nginx and Traefik can run simultaneously (different ports)
- Traefik requires access to Docker socket for auto-discovery

## 📚 Learn More

- **Nginx Config:** `nginx.conf` - Traditional upstream configuration
- **Traefik Config:** `docker-compose-traefik.yml` - Label-based routing
- **Traefik Docs:** See `TRAEFIK-README.md` for detailed Traefik guide

## 🎓 Key Concepts Demonstrated

- Round-robin load balancing
- Health checks and failover
- Container networking
- Service discovery
- Zero-downtime deployments (Traefik)
- Static vs Dynamic configuration

---

**Made with ❤️ for learning load balancing concepts**
