# Deploy Nginx Container on Application Server

### On Application Server 1 create a container named nginx_1 using the nginx image with the alpine tag. Ensure container is in a running state.

```bash
docker run -d --name nginx_1 nginx:alpine

docker ps
```