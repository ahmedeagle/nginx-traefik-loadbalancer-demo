# 🎨 Traefik Load Balancer Demo

Modern load balancing with Traefik - automatic service discovery and built-in health checks!

## 🚀 Quick Start with Traefik

### Run with Traefik:
```bash
docker-compose -f docker-compose-traefik.yml up -d
```

### Access Points:
- **Application**: http://localhost:8080
- **Traefik Dashboard**: http://localhost:8081

## ✨ Traefik Advantages

### 1. **Auto-Discovery** 🔍
- Automatically detects new containers
- No config file edits needed
- Add/remove containers on the fly

### 2. **Built-in Health Checks** 💚
- Checks every 5 seconds
- Auto-removes unhealthy containers
- Auto-adds when they recover

### 3. **Real-time Dashboard** 📊
- Visual service monitoring
- See active routes and backends
- Monitor health status live

### 4. **Zero Downtime** ⚡
- Instant failover
- No manual intervention
- Graceful container updates

## 🧪 Test Health Checks

### Stop a container:
```bash
docker stop red-app
```
- Traefik detects failure in **5 seconds**
- Traffic routes only to blue & green
- Check dashboard to see red marked as unhealthy

### Restart it:
```bash
docker start red-app
```
- Auto-detected in **5 seconds**
- Immediately added back to rotation
- Dashboard shows green status

## 📊 Traefik Dashboard

Open http://localhost:8081 to see:
- **HTTP Routers**: Your routing rules
- **HTTP Services**: Backend pool (3 servers)
- **HTTP Middlewares**: (none in this setup)
- **Health Status**: Real-time server health

## 🎯 How It Works

### Labels Configuration:
```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.services.backend.loadbalancer.server.port=80"
  - "traefik.http.services.backend.loadbalancer.healthcheck.interval=5s"
```

All 3 containers use the **same service name** (`backend`), so Traefik automatically:
1. Groups them into one load balancer pool
2. Distributes traffic using round-robin
3. Health checks every 5 seconds
4. Removes/adds servers automatically

## 🔄 Load Balancing Algorithms

### Change to Weighted Round Robin:
```yaml
labels:
  - "traefik.http.services.backend.loadbalancer.server.port=80"
  - "traefik.http.services.backend.loadbalancer.server.weight=3"  # Red gets 3x traffic
```

### Use Least Connections:
Add to Traefik command:
```yaml
command:
  - "--providers.docker.defaultLoadBalancer=wrr"
```

## 🆚 Traefik vs Nginx

| Feature | Traefik | Nginx |
|---------|---------|-------|
| **Auto-discovery** | ✅ Yes | ❌ Manual config |
| **Health checks** | ✅ Built-in | ⚠️ Basic |
| **Dashboard** | ✅ Beautiful UI | ❌ No built-in |
| **Zero config** | ✅ Labels only | ❌ Conf files |
| **Learning curve** | Medium | Steep |
| **Performance** | Very Good | Excellent |

## 🛠️ Useful Commands

### View all services:
```bash
docker-compose -f docker-compose-traefik.yml ps
```

### View Traefik logs:
```bash
docker logs traefik-lb -f
```

### Scale services:
```bash
docker-compose -f docker-compose-traefik.yml up -d --scale red-app=2
```

### Stop all:
```bash
docker-compose -f docker-compose-traefik.yml down
```

## 🎨 Add More Containers

Just add a new service with the same labels:
```yaml
yellow-app:
  build: ./yellow
  labels:
    - "traefik.enable=true"
    - "traefik.http.services.backend.loadbalancer.server.port=80"
```

**That's it!** Traefik auto-discovers it and adds it to the pool.

## 🔒 Production Tips

1. **Secure the dashboard:**
```yaml
- "--api.insecure=false"
- "--api.dashboard=true"
```

2. **Add authentication:**
```yaml
labels:
  - "traefik.http.middlewares.auth.basicauth.users=admin:$$apr1$$..."
```

3. **Enable HTTPS:**
```yaml
- "--entrypoints.websecure.address=:443"
- "--certificatesresolvers.myresolver.acme.email=you@email.com"
```

## 📝 Configuration Files

- **Nginx version**: `docker-compose.yml` + `nginx.conf`
- **Traefik version**: `docker-compose-traefik.yml` (no config file needed!)

---

**Traefik = Modern, Auto-discovering, Cloud-native Load Balancer** 🚀
