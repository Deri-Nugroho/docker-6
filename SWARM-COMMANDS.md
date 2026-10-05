# Docker Swarm Command Reference

## Swarm Initialization

### Initialize Swarm (Manager Node)
```bash
docker swarm init --advertise-addr <IP-ADDRESS>
```

### Get Join Token for Worker
```bash
docker swarm join-token worker
```

### Get Join Token for Manager
```bash
docker swarm join-token manager
```

### Join Swarm (Worker Node)
```bash
docker swarm join --token <TOKEN> <MANAGER-IP>:2377
```

### Leave Swarm
```bash
docker swarm leave
```

### Leave Swarm (Manager Node - Force)
```bash
docker swarm leave --force
```

## Node Management

### List Nodes
```bash
docker node ls
```

### Inspect Node
```bash
docker node inspect <NODE-ID>
```

### Promote Worker to Manager
```bash
docker node promote <NODE-ID>
```

### Demote Manager to Worker
```bash
docker node demote <NODE-ID>
```

### Update Node Availability
```bash
docker node update --availability <active|pause|drain> <NODE-ID>
```

### Remove Node
```bash
docker node rm <NODE-ID>
```

## Service Management

### Create Service
```bash
docker service create --name <SERVICE-NAME> --replicas <N> <IMAGE>
```

### Create Service with Port Mapping
```bash
docker service create --name web --replicas 3 -p 8080:80 nginx
```

### List Services
```bash
docker service ls
```

### Inspect Service
```bash
docker service inspect <SERVICE-NAME>
```

### List Service Tasks
```bash
docker service ps <SERVICE-NAME>
```

### Scale Service
```bash
docker service scale <SERVICE-NAME>=<N>
```

### Update Service Image
```bash
docker service update --image <NEW-IMAGE> <SERVICE-NAME>
```

### Remove Service
```bash
docker service rm <SERVICE-NAME>
```

### View Service Logs
```bash
docker service logs <SERVICE-NAME>
```

### View Service Logs for Specific Task
```bash
docker service logs <SERVICE-NAME> --task <TASK-ID>
```

## Stack Management

### Deploy Stack from Compose File
```bash
docker stack deploy -c docker-compose.yml <STACK-NAME>
```

### List Stacks
```bash
docker stack ls
```

### List Services in Stack
```bash
docker stack services <STACK-NAME>
```

### List Tasks in Stack
```bash
docker stack ps <STACK-NAME>
```

### Remove Stack
```bash
docker stack rm <STACK-NAME>
```

## Network Management

### Create Overlay Network
```bash
docker network create --driver overlay <NETWORK-NAME>
```

### List Networks
```bash
docker network ls
```

### Inspect Network
```bash
docker network inspect <NETWORK-NAME>
```

### Remove Network
```bash
docker network rm <NETWORK-NAME>
```

## Volume Management

### Create Volume
```bash
docker volume create <VOLUME-NAME>
```

### List Volumes
```bash
docker volume ls
```

### Inspect Volume
```bash
docker volume inspect <VOLUME-NAME>
```

### Remove Volume
```bash
docker volume rm <VOLUME-NAME>
```

## Troubleshooting

### Check Swarm Status
```bash
docker info | grep Swarm
```

### View Swarm Events
```bash
docker events
```

### Check Service Replicas
```bash
docker service ps <SERVICE-NAME> --no-trunc
```

### Force Service Reconciliation
```bash
docker service update --force <SERVICE-NAME>
```

### Rollback Service Update
```bash
docker service rollback <SERVICE-NAME>
```

## Best Practices

1. **Always use odd number of manager nodes** (3, 5, 7) for Raft consensus
2. **Separate manager and worker roles** in production
3. **Use overlay networks** for multi-service communication
4. **Set resource limits** to prevent resource exhaustion
5. **Use health checks** for self-healing
6. **Use rolling updates** for zero-downtime deployments
7. **Monitor swarm health** regularly
8. **Backup configuration** and swarm tokens

## Example: Complete Workflow

```bash
# 1. Initialize swarm on manager
docker swarm init --advertise-addr 192.168.1.10

# 2. Get worker join token
docker swarm join-token worker

# 3. Join worker nodes
docker swarm join --token <TOKEN> 192.168.1.10:2377

# 4. Create overlay network
docker network create --driver overlay webnet

# 5. Deploy service
docker service create \
  --name web \
  --replicas 3 \
  --network webnet \
  -p 8080:80 \
  nginx:alpine

# 6. Scale service
docker service scale web=5

# 7. Update service
docker service update --image nginx:latest web

# 8. Check status
docker service ls
docker service ps web

# 9. Remove service
docker service rm web
```
