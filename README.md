### Preparing minio:
#### First an alias must be maden:
```
docker exec minio mc alias set mycloud http://localhost:9000 minioadminuser minioadminpassword
or
docker exec minio mc alias set mycloud1 https://io.miniosync.<url>/ minioadminuser minioadminpassword
 
```
#### Then generate the config with following command:
```
docker exec minio mc admin prometheus generate mycloud
```
#### the output: 

```
scrape_configs:
- job_name: minio-job
  bearer_token: <token>
  metrics_path: /minio/v2/metrics/cluster
  scheme: http
  static_configs:
  - targets: ['localhost:9000']
```

### Preparing rabbitmq:
### just run following command:
```
docker exec rabbit-container rabbitmq-plugins enable rabbitmq_prometheus
```
#### these labels must be added to the rabbitmq agent:
#### labels:
       - "traefik.enable=true"
       - "traefik.docker.network=web_net"
       - 
       - "traefik.http.routers.rabbit.entrypoints=http"
       - "traefik.http.routers.rabbit.rule=Host(`${RABBIT}.${DOMAIN_ADDRESS}`)"
       - "traefik.http.routers.rabbit.middlewares=https-redirect"
       - "traefik.http.middlewares.https-redirect.redirectscheme.scheme=https"
       - "traefik.http.routers.rabbit-secure.entrypoints=https"
       - "traefik.http.routers.rabbit-secure.rule=Host(`${RABBIT}.${DOMAIN_ADDRESS}`)"
       - "traefik.http.routers.rabbit-secure.tls=true"
       - "traefik.http.routers.rabbit-secure.tls.options=default"
       - "traefik.http.routers.rabbit-secure.tls.certresolver=mycert"
       - "traefik.http.routers.rabbit-secure.service=rabbit"
       - "traefik.http.services.rabbit.loadbalancer.server.port=15672"
       - 
       - "traefik.http.routers.rabbitmetrics.entrypoints=http"
       - "traefik.http.routers.rabbitmetrics.rule=Host(`${RABBITMETRICS}.${DOMAIN_ADDRESS}`)"
       - "traefik.http.routers.rabbitmetrics.middlewares=https-redirect"
       - "traefik.http.middlewares.https-redirect.redirectscheme.scheme=https"
       - "traefik.http.routers.rabbitmetrics-secure.entrypoints=https"
       - "traefik.http.routers.rabbitmetrics-secure.rule=Host(`${RABBITMETRICS}.${DOMAIN_ADDRESS}`)"
       - "traefik.http.routers.rabbitmetrics-secure.tls=true"
       - "traefik.http.routers.rabbitmetrics-secure.tls.options=default"
       - "traefik.http.routers.push-secure.middlewares=web-auth"
       - "traefik.http.routers.rabbitmetrics-secure.tls.certresolver=mycert"
       - "traefik.http.routers.rabbitmetrics-secure.service=rabbitmetrics"
       - "traefik.http.services.rabbitmetrics.loadbalancer.server.port=15692"

#### for tpcms:
#### labels:
            - "traefik.enable=true"
            - "traefik.docker.network=web_net"
            - "traefik.http.routers.rabbitmq-ui.entrypoints=http"
            - "traefik.http.routers.rabbitmq-ui.rule=Host(`rmq.staging.tpcms.ir`)"
            - "traefik.http.routers.rabbitmq-ui.service=rabbitmq-ui"
            - "traefik.http.services.rabbitmq-ui.loadbalancer.server.port=15672"
            
            
            - ###########################################################
            - "traefik.http.routers.rabbitmetrics.entrypoints=http"
            - "traefik.http.routers.rabbitmetrics.rule=Host(`rabbitmetrics.staging.tpcms.ir`)"
            - "traefik.http.routers.rabbitmetrics.middlewares=web-auth"
            - "traefik.http.routers.rabbitmetrics.service=rabbitmetrics"
            - "traefik.http.services.rabbitmetrics.loadbalancer.server.port=15692"

#### for production:
#### labels:
            #### labels:
            labels:
            - "traefik.enable=true"
            - "traefik.docker.network=web_net"
            - "traefik.http.routers.rabbitmq-ui.entrypoints=http"
            - "traefik.http.routers.rabbitmq-ui.rule=Host(`rmq.project.ir`)"
            - "traefik.http.services.rabbitmq-ui.loadbalancer.server.port=15672"
            - "traefik.http.routers.rabbitmq-secure.entrypoints=https"
            - "traefik.http.routers.rabbitmq-secure.rule=Host(`rmq.project.ir`)"
            - "traefik.http.routers.rabbitmq-secure.tls=true"
            - "traefik.http.routers.rabbitmq-secure.tls.options=default"
            - "traefik.http.routers.rabbitmq-secure.tls.certresolver=mycert"
            - "traefik.http.routers.rabbitmq-ui.middlewares=https-redirect"
            - "traefik.http.middlewares.https-redirect.redirectscheme.scheme=https"
            - "traefik.http.routers.rabbitmq-secure.service=rabbitmq-ui"
            - "traefik.http.routers.rabbitmetrics.entrypoints=http"
            - "traefik.http.routers.rabbitmetrics.rule=Host(`rabbitmetrics.monitoring.project.ir`)"
            - "traefik.http.routers.rabbitmetrics.middlewares=https-redirect"
            - "traefik.http.middlewares.https-redirect.redirectscheme.scheme=https"
            - "traefik.http.routers.rabbitmetrics-secure.entrypoints=https"
            - "traefik.http.routers.rabbitmetrics-secure.rule=Host(`rabbitmetrics.monitoring.project.ir`)"
            - "traefik.http.routers.rabbitmetrics-secure.tls=true"
            - "traefik.http.routers.rabbitmetrics-secure.tls.options=default"
            - "traefik.http.routers.rabbitmetrics-secure.middlewares=web-auth"
            - "traefik.http.routers.rabbitmetrics-secure.tls.certresolver=mycert"
            - "traefik.http.routers.rabbitmetrics-secure.service=rabbitmetrics"
            - "traefik.http.services.rabbitmetrics.loadbalancer.server.port=15692"

#### now in prometheus.yml file add the config:
```
- job_name: 'rabbitmq-details-exporter'
    scrape_interval: 15s
    metrics_path: '/metrics'
    static_configs:
      - targets: ['YOUR_AGENT_ADDRESS]
```
### Preparing traefik labels:

#### labels:
      - "traefik.enable=true"
      - "traefik.docker.network=web_net"
      - "traefik.http.routers.traefik.entrypoints=http"
      - "traefik.http.routers.traefik.rule=Host(`${SUB}.${DOMAIN_ADDR}`)"
      - "traefik.http.middlewares.web-auth.basicauth.users=${WEB_AUTH_USER}:${WEB_AUTH_PASS}"
      - "traefik.http.routers.traefik.middlewares=https-redirect"
      - "traefik.http.middlewares.https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.traefik-secure.entrypoints=https"
      - "traefik.http.routers.traefik-secure.rule=Host(`${SUB}.${DOMAIN_ADDR}`)"
      - "traefik.http.routers.traefik-secure.tls=true"
      - "traefik.http.routers.traefik-secure.tls.options=default"
      - "traefik.http.routers.traefik-secure.middlewares=web-auth"
      - "traefik.http.routers.traefik-secure.service=traefik"
      - "traefik.http.routers.traefik-secure.tls.certresolver=mycert"
      - "traefik.http.services.traefik.loadbalancer.server.port=8080"
# metrics:
      - "traefik.http.routers.traefik-monitoring.entrypoints=http"
      - "traefik.http.routers.traefik-monitoring.rule=Host(`${METRIC}.${DOMAIN_ADDR}`)"
      - "traefik.http.routers.traefik-monitoring.middlewares=https-redirect"
      - "traefik.http.middlewares.https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.traefik-monitoring-secure.entrypoints=https"
      - "traefik.http.routers.traefik-monitoring-secure.rule=Host(`${METRIC}.${DOMAIN_ADDR}`)"
      - "traefik.http.routers.traefik-monitoring-secure.tls=true"
      - "traefik.http.routers.traefik-monitoring-secure.tls.options=default"
      - "traefik.http.routers.traefik-monitoring-secure.middlewares=web-auth"
      - "traefik.http.routers.traefik-monitoring-secure.tls.certresolver=mycert"
     # .service must be decleared
      - "traefik.http.routers.traefik-monitoring-secure.service=traefik-monitoring"
      - "traefik.http.services.traefik-monitoring.loadbalancer.server.port=8082"
#### for  tpcms:

##### labels:
            - "traefik.enable=true"
            - "traefik.docker.network=web_net"
            - "traefik.http.routers.traefik.entrypoints=http"
            - "traefik.http.routers.traefik.rule=Host(`web.staging.tpcms.ir`)"
            - "traefik.http.middlewares.web-auth.basicauth.users=username:password"
            - "traefik.http.routers.traefik.middlewares=web-auth"
            - "traefik.http.services.traefik.loadbalancer.server.port=8080"

            - ###################################################
            - "traefik.http.routers.traefik.service=traefik"
            - "traefik.http.routers.traefik-monitoring.entrypoints=http"
            - "traefik.http.routers.traefik-monitoring.rule=Host(`trmetrics.staging.tpcms.ir`)"
            - "traefik.http.routers.traefik-monitoring.middlewares=web-auth"
            - "traefik.http.routers.traefik-monitoring.service=traefik-monitoring"
            - "traefik.http.services.traefik-monitoring.loadbalancer.server.port=8082"

#### for  production:
##### labels:
            - "traefik.enable=true"
            - "traefik.docker.network=web_net"
            - "traefik.http.routers.traefik.entrypoints=http"
            - "traefik.http.routers.traefik.rule=Host(`web.project.ir`)"
            - "traefik.http.middlewares.web-auth.basicauth.users=username:password"
            - "traefik.http.routers.traefik.middlewares=web-auth"
            - "traefik.http.services.traefik.loadbalancer.server.port=8080"


            - #####################################################################
            - "traefik.http.routers.traefik-secure.service=traefik"
            - "traefik.http.routers.traefik-monitoring.entrypoints=http"
            - "traefik.http.routers.traefik-monitoring.rule=Host(`trmetrics.project.ir`)"
            - "traefik.http.routers.traefik-monitoring.middlewares=https-redirect"
            - "traefik.http.middlewares.https-redirect.redirectscheme.scheme=https"
            - "traefik.http.routers.traefik-monitoring-secure.entrypoints=https"
            - "traefik.http.routers.traefik-monitoring-secure.rule=Host(`trmetrics.project.ir`)"
            - "traefik.http.routers.traefik-monitoring-secure.tls=true"
            - "traefik.http.routers.traefik-monitoring-secure.tls.options=default"
            - "traefik.http.routers.traefik-monitoring-secure.middlewares=web-auth"
            - "traefik.http.routers.traefik-monitoring-secure.tls.certresolver=mycert"
            - "traefik.http.routers.traefik-monitoring-secure.service=traefik-monitoring"
            - "traefik.http.services.traefik-monitoring.loadbalancer.server.port=8082"

