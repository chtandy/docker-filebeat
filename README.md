# docker-filebeat
docker-filebeat
```
chown root filebeat/filebeat.yml
chmod 644 filebeat/filebeat.yml
```
```
version: '3'
services:
  docker-filebeat:
    container_name: docker-filebeat
    image: docker.elastic.co/beats/filebeat:6.2.3
    restart: unless-stopped
    user: root
    volumes:
      - ./filebeat/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "15"
```
- filebeat.yml
```
filebeat.prospectors:
- type: docker
  containers.ids:
    - "*"
#- type: log
#  paths:
#    - /var/lib/docker/containers/*/*.log
  processors:
  - add_docker_metadata: ~

output.kafka:
  enabled: true
  hosts: ["kafka1.mlytics.co:9092"]

  topic: echowave
  partition.round_robin:
    reachable_only: false

  required_acks: 1
  codec.json:
    pretty: true
```
