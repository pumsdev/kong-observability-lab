# Kong Observability Labs

## Observability Pillar

1. Metrics ตัวเลข + เวลา เอาไปทำ graph
2. Logs ตัวอักษร + เวลา เเสดงถึง event ในระบบ
3. Traces ข้อมูลการ request ว่าไปแตะส่วนไหนของ system บ้าง

## Tools

- Prometheus plugin (metrics)
- Httplog plugin (logs)
- OpenTelemetry plugin (trace)

## HandOn lab

[https://github.com/pumsdev/kong-observability-lab](https://github.com/pumsdev/kong-observability-lab)

### Start Kong traditional

```bash
docker compose -f kong-traditional/docker-compose.yaml up -d
```

apply initial declarative config

```bash
deck gateway sync kong-traditional/declarative-config.yaml
```

ทดสอบ kong

เปิด Kong manager ผ่าน web browser โดยเข้าไปที่ localhost:8002/manager

- default workspace
- service ต้องมี httpbin-service
- plugins มี opentelemetry, prometheus ติดตั้งแล้วเรียบร้อย

ลอง curl check service

```bash
curl localhost:8000/bin/headers
```

ผลลัพธ์ที่ควรเป็นคือ service response headers ของ request คล้าย ๆ ด้านล่างนี้

```bash
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "httpbin",
    "Traceparent": "00-056a057c25da9bbec1818c7de990e6a0-a925d76dacbe90e9-01",
    "User-Agent": "curl/8.1.2",
    "X-Forwarded-Host": "localhost",
    "X-Forwarded-Path": "/bin/headers",
    "X-Forwarded-Prefix": "/bin",
    "X-Kong-Request-Id": "3b11264241245b1e3226580733253617"
}

```

### Start Observability tools

- grafana → visualize
- prometheus → metrics backend (database)
- fluent-bit → log collector
- opensearch → log backend (database)
- opensearch dashboard → log visualize
- jaeger-collector → trace collector
- jaeger-ui → trace visualize

```bash
docker compose -f observability-tools/docker-compose.yaml up -d
```

recheck on browser

- grafana → [localhost:3000](http://localhost:3000) (admin:grafana)
- prometheus → localhost:9090
- opensearch-dashboard → [localhost:5601](http://localhost:5601) (admin:Opensearch@1)
- jaeger → localhost:16686

### use wrk to make load to Kong

```bash
wrk -t1 -c1 -d180s http://localhost:8000/bin/headers
```

### 

# REF: เพิ่มเติม

[https://www.cncf.io/online-programs/cncf-on-demand-webinar-instrumenting-observability-in-kong-using-fluent-bit-and-opentelemetry/](https://www.cncf.io/online-programs/cncf-on-demand-webinar-instrumenting-observability-in-kong-using-fluent-bit-and-opentelemetry/)


[https://github.com/fluent/fluent-bit-observability-demo/tree/main](https://github.com/fluent/fluent-bit-observability-demo/tree/main)

[https://tech.aufomm.com/kong-observability-with-grafana-a-unified-view-for-logs-metrics-and-traces/](https://tech.aufomm.com/kong-observability-with-grafana-a-unified-view-for-logs-metrics-and-traces/)