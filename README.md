# Tymoe Traefik Gateway

Traefik反向代理网关配置，用于Tymoe微服务架构的统一入口和集中鉴权。

## 🎯 功能
- **统一入口**: 所有微服务通过Traefik访问
- **集中鉴权**: ForwardAuth中间件调用auth-service验证JWT
- **自动发现**: 通过Docker标签自动配置路由
- **健康检查**: 支持服务健康状态监控

## 🚀 快速开始

### 1. 创建Docker网络
```bash
docker network create tymoe-network
```

### 2. 启动Traefik
```bash
docker-compose up -d
```

### 3. 访问控制面板
- Traefik Dashboard: http://localhost:8080
- 服务通过: http://localhost

## 🔧 配置说明

### 主要文件
- `docker-compose.yml` - Traefik容器配置
- `config/dynamic_conf.yml` - 路由规则和中间件配置

### 路由规则
- `auth-service`: `/api/auth-service/*`
- `subscription-service`: `subscriptions.localhost` 或 `/api/subscription-service/*`
- 健康检查: `/health` 或 `/healthz` (无需认证)

### ForwardAuth中间件
所有受保护请求通过 `auth-service` 的 `/auth/gateway-check` 接口验证JWT，返回用户信息header。

## 📋 微服务集成

### 1. 确保服务在 `tymoe-network` 中
```yaml
# docker-compose.yml
networks:
  default:
    name: tymoe-network
    external: true
```

### 2. 添加Traefik标签
```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.<service-name>.rule=Host(`<hostname>.localhost`)"
  - "traefik.http.routers.<service-name>.entrypoints=web"
  - "traefik.http.routers.<service-name>.middlewares=tymoe-auth-gateway"
  - "traefik.http.services.<service-name>.loadbalancer.server.port=<port>"
```

## 🧪 测试

### 验证Traefik运行
```bash
curl http://localhost/api/subscription-service/health
```

### 测试Gateway鉴权
```bash
curl -H "Authorization: Bearer <JWT>" \
  http://localhost/api/subscription-service/v1/subscriptions/test-org
```

## 🔍 故障排除

### 检查服务发现
```bash
# 查看Traefik发现的服务
curl http://localhost:8080/api/http/routers | jq .
```

### 查看日志
```bash
docker logs tymoe-gateway
```

### 检查网络
```bash
docker network inspect tymoe-network
```

## 📞 支持
遇到问题请检查：
1. 服务是否在 `tymoe-network` 中
2. Traefik标签是否正确
3. auth-service的 `/auth/gateway-check` 接口是否可用
4. 防火墙/端口是否开放