### Port-forward to apisix-admin service:

kubectl port-forward -n apisix svc/apisix-admin 9180:9180


### Get Admin API Key:

kubectl get configmap -n apisix apisix -o yaml | grep -A5 admin_key


### igrp-platform-access-management-backend

1️⃣ Create the Upstream
curl -k http://127.0.0.1:9180/apisix/admin/upstreams/1 \
  -X PUT \
  -H "X-API-KEY: edd1c9f034335f136f87ad84b625c8f1" \
  -H "Content-Type: application/json" \
  -d '{
        "name": "access-management-backend",
        "type": "roundrobin",
        "nodes": {
          "igrp-platform-access-management-backend-service.igrp-platform-access-management-backend.svc.cluster.local:8080": 1
        }
      }'

2️⃣ Create a Route
curl -k http://127.0.0.1:9180/apisix/admin/routes/1 \
  -X PUT \
  -H "X-API-KEY: edd1c9f034335f136f87ad84b625c8f1" \
  -H "Content-Type: application/json" \
  -d '{
        "uri": "/access-management/*",
        "methods": ["GET", "POST", "PUT", "DELETE"],
        "upstream_id": 1
      }'

3️⃣ Verify Upstream
curl -k http://127.0.0.1:9180/apisix/admin/upstreams/1 \
  -H "X-API-KEY: edd1c9f034335f136f87ad84b625c8f1"

4️⃣ Add proxy-rewrite to strip /access-management prefix
curl -k http://127.0.0.1:9180/apisix/admin/routes/1 \
  -X PATCH \
  -H "X-API-KEY: edd1c9f034335f136f87ad84b625c8f1" \
  -H "Content-Type: application/json" \
  -d '{
        "plugins": {
          "proxy-rewrite": {
            "regex_uri": ["^/access-management(/.*)$","$1"],
            "scheme": "http"
          }
        }
      }'


### igrp-process-runtime-backend    

1️⃣ Create the Upstream
curl -k http://127.0.0.1:9180/apisix/admin/upstreams/2 \
  -X PUT \
  -H "X-API-KEY: edd1c9f034335f136f87ad84b625c8f1" \
  -H "Content-Type: application/json" \
  -d '{
        "name": "process-runtime",
        "type": "roundrobin",
        "nodes": {
          "igrp-process-runtime-backend-service.igrp-process-runtime-backend.svc.cluster.local:8080": 1
        }
      }'


2️⃣ Create a Route
curl -k http://127.0.0.1:9180/apisix/admin/routes/2 \
  -X PUT \
  -H "X-API-KEY: edd1c9f034335f136f87ad84b625c8f1" \
  -H "Content-Type: application/json" \
  -d '{
        "uri": "/process-runtime/*",
        "methods": ["GET", "POST", "PUT", "DELETE"],
        "upstream_id": 2
      }'


3️⃣ Verify Upstream
curl -k http://127.0.0.1:9180/apisix/admin/upstreams/2 \
  -H "X-API-KEY: edd1c9f034335f136f87ad84b625c8f1"


4️⃣ Add proxy-rewrite to strip /process-runtime prefix
curl -k http://127.0.0.1:9180/apisix/admin/routes/2 \
  -X PATCH \
  -H "X-API-KEY: edd1c9f034335f136f87ad84b625c8f1" \
  -H "Content-Type: application/json" \
  -d '{
        "plugins": {
          "proxy-rewrite": {
            "regex_uri": ["^/process-runtime(/.*)$","$1"],
            "scheme": "http"
          }
        }
      }'


### igrp-process-studio-backend


1️⃣ Create the Upstream
curl -k http://127.0.0.1:9180/apisix/admin/upstreams/3 \
  -X PUT \
  -H "X-API-KEY: edd1c9f034335f136f87ad84b625c8f1" \
  -H "Content-Type: application/json" \
  -d '{
        "name": "process-studio",
        "type": "roundrobin",
        "nodes": {
          "igrp-process-studio-backend-service.igrp-process-studio-backend.svc.cluster.local:8080": 1
        }
      }'

2️⃣ Create a Route
curl -k http://127.0.0.1:9180/apisix/admin/routes/3 \
  -X PUT \
  -H "X-API-KEY: edd1c9f034335f136f87ad84b625c8f1" \
  -H "Content-Type: application/json" \
  -d '{
        "uri": "/process-studio/*",
        "methods": ["GET", "POST", "PUT", "DELETE"],
        "upstream_id": 3
      }'

3️⃣ Verify Upstream
curl -k http://127.0.0.1:9180/apisix/admin/upstreams/3 \
  -H "X-API-KEY: edd1c9f034335f136f87ad84b625c8f1"

4️⃣ Add proxy-rewrite to strip /process-studio prefix
curl -k http://127.0.0.1:9180/apisix/admin/routes/3 \
  -X PATCH \
  -H "X-API-KEY: edd1c9f034335f136f87ad84b625c8f1" \
  -H "Content-Type: application/json" \
  -d '{
        "plugins": {
          "proxy-rewrite": {
            "regex_uri": ["^/process-studio(/.*)$","$1"],
            "scheme": "http"
          }
        }
      }'


### Test the endpoints:

curl -v https://apisix.zingdevelopers.com/process-runtime/actuator/health   -H "Host: apisix.zingdevelopers.com"
curl -v https://apisix.zingdevelopers.com/access-management/actuator/health   -H "Host: apisix.zingdevelopers.com"
curl -v https://apisix.zingdevelopers.com/process-studio/actuator/health   -H "Host: apisix.zingdevelopers.com"