#### 1.1 实战 Prometheus 搭建监控系统

参考文献：https://www.cnblogs.com/chenqionghe/p/10494868.html

参考文献: https://www.aneasystone.com/archives/2018/11/prometheus-in-action.html

```shell
$ docker pull prom/prometheus 
$ docker run -d -p 9090:9090 prom/prometheus
# web访问
http://localhost:9090/
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

查看配置文件:

```shell
$ docker inspect 56316e25cc5c
```

```jso
docker inspect 56316e25cc5c
[
    {
        "Id": "56316e25cc5ce63f9706d1ae6e633fcef447eb7a9500ca144b5e7888a510c918",
        "Created": "2020-01-21T03:01:29.6623581Z",
        "Path": "/bin/prometheus",
        "Args": [
            "--config.file=/etc/prometheus/prometheus.yml",
            "--storage.tsdb.path=/prometheus",
            "--web.console.libraries=/usr/share/prometheus/console_libraries",
            "--web.console.templates=/usr/share/prometheus/consoles"
        ],
```

#### 1.3 Prometheus 默认的配置文件

全局配置:

```yaml
global:
  scrape_interval:     15s # 拉取目标的时间间隔
  evaluation_interval: 15s # 执行规则的时间间隔
  crape_timeout: 10s   # 拉取一个target的超时时间
    external_labels: 	# 与外部系统通信时添加到任何时间序列或警报的标签（联合，远程存储，Alertma# nager）.即添加到拉取的数据并存到数据库中
      monitor: 'sass-monitor'
```

突破配置:

```yaml
alerting:
  alertmanagers: # 用于动态发现Alertmanager的配置
    - scheme: http
      static_configs:
        - targets:
            - "alertmanager:9093"
```

抓取配置:

```yaml
scrape_configs:
  - job_name: 'prometheus'
	honor_timestamps: true
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /metrics
  scheme: http
  static_configs:
  	- targets:
  	 - 192.168.0.199:8080 #192.168.0.199为本机ip， 本机127.0.0.1在容器中无法访问到
```

规则配置:

```yaml
rule_files:
  - 'alert.rules'
```

#### 1.4 安装Grafana

```shell
docker pull grafana
docker run -d -p 3000:3000 --name=grafana grafana/grafana
```

#### 1.5 完整案例

```yaml
global:
  scrape_interval: 15s
  scrape_timeout: 10s
  evaluation_interval: 15s
alerting:
  alertmanagers:
  - static_configs:
    - targets: []
    scheme: http
    timeout: 10s
scrape_configs:
- job_name: "sass-prometheus"
  honor_timestamps: true
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /metrics
  scheme: http
  static_configs:
  - targets:
    - 192.168.0.199:8080   #192.168.0.199为本机ip， 本机127.0.0.1在容器中无法访问到
```

