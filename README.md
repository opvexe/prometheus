#### 1.1 实战 Prometheus 搭建监控系统

```shell
$ docker pull prom/prometheus 
$ docker run -d -p 9090:9090 prom/prometheus
```

#### 1.2 配置prometheus.yaml

```shell
$ docker exec -it vibrant_hypatia /bin/sh
# 将宿主机的/etc/prometheus/
docker cp 72edfdb4b90b:/etc/prometheus/ home/data/
```

如果报错:

```shell
OCI runtime exec failed: exec failed: container_linux.go:345: starting container process caused "exec: \"bash\": executable file not found in $PATH": unknown
```

问题原因及解决方案:

```shell
$ docker exec -it vibrant_hypatia /bn/bash 
# docker exec 报错是因为 /bin/bash 文件不存在，可以尝试 /bin/sh 等
```

