Config
```
POSTGRES_PASSWORD=ImMich431onL1N3
UPLOAD_LOCATION=/mnt/data/immich
/usr/lib/immich/pgdata
v1.132.3
```
Check docker-compose.yml
```
docker.io/valkey/valkey:8-bookworm@sha256:42cba146593a5ea9a622002c1b7cba5da7be248650cbb64ecb9c6c33d29794b1
docker.io/tensorchord/pgvecto-rs:pg14-v0.2.0@sha256:739cdd626151ff1f796dc95a6591b55a714f341c737e27f045019ceabf8e8c52
```

```
podman network create immich-network
```

```
podman run -d --replace \
  --name immich-postgres \
  --hostname database \
  --network immich-network \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=ImMich431onL1N3 \
  -e POSTGRES_DB=immich \
  -v /usr/lib/immich/pgdata:/var/lib/postgresql/data \
  -p 5432:5432 \
  docker.io/tensorchord/pgvecto-rs:pg14-v0.2.0@sha256:739cdd626151ff1f796dc95a6591b55a714f341c737e27f045019ceabf8e8c52
```

```
podman run -d --replace \
  --hostname redis \
  --name immich-redis \
  --network immich-network \
  -p 6379:6379 \
  docker.io/valkey/valkey:8-bookworm@sha256:42cba146593a5ea9a622002c1b7cba5da7be248650cbb64ecb9c6c33d29794b1 
```

```
podman run -d --replace \
  --name immich-server \
  --hostname immich-server \
  --network immich-network \
  -e DB_HOST=database \
  -e DB_PORT=5432 \
  -e DB_USERNAME=postgres \
  -e DB_PASSWORD=ImMich431onL1N3 \
  -e DB_DATABASE_NAME=immich \
  -e REDIS_HOST=redis \
  -e UPLOAD_LOCATION=/mnt/data/immich \
  -e TZ=Asia/Jakarta \
  -p 2283:2283 \
  ghcr.io/immich-app/immich-server:v1.132.3
```

```
podman run -d --replace \
  --name immich-machine-learning \
  --hostname immich-machine-learning \
  --network immich-network \
  -e DB_HOST=database \
  -e DB_PORT=5432 \
  -e DB_USERNAME=postgres \
  -e DB_PASSWORD=ImMich431onL1N3 \
  -e DB_DATABASE_NAME=immich \
  -e REDIS_HOST=redis \
  -p 3003:3003 \
  ghcr.io/immich-app/immich-machine-learning:v1.132.3
```

Access IMMICH
http://localhost:2283

```
podman exec -it immich-postgres psql -U postgres -d immich
```
```
podman exec -it immich-redis redis-cli ping
```
