
  

# GAI_final

生成式AI基礎與應用 期末專題

  

  

# 安裝步驟

  

```

# 下載 dify

git clone https://github.com/langgenius/dify.git --branch 0.15.3

  

# 下載 frp ( 主機從機都要下載 )

wget https://github.com/fatedier/frp/releases/download/v0.61.0/frp_0.61.0_linux_amd64.tar.gz

tar -xzvf frp_0.61.0_linux_amd64.tar.gz

```

  

# 啟動dify

  

```

cd dify\docker # 切換到dify\docker目錄

cp .env.example .env # 複製.env

cd docker compose up -d # 啟動dify

```

  

```

docker compose ps # 檢查是否順利開啟

# NAME IMAGE COMMAND SERVICE CREATED STATUS PORTS

# docker-api-1 langgenius/dify-api:0.6.13 "/bin/bash /entrypoi…" api About a minute ago Up About a minute 5001/tcp

# docker-db-1 postgres:15-alpine "docker-entrypoint.s…" db About a minute ago Up About a minute (healthy) 5432/tcp

# docker-nginx-1 nginx:latest "sh -c 'cp /docker-e…" nginx About a minute ago Up About a minute 0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp

# docker-redis-1 redis:6-alpine "docker-entrypoint.s…" redis About a minute ago Up About a minute (healthy) 6379/tcp

# docker-sandbox-1 langgenius/dify-sandbox:0.2.1 "/main" sandbox About a minute ago Up About a minute

# docker-ssrf_proxy-1 ubuntu/squid:latest "sh -c 'cp /docker-e…" ssrf_proxy About a minute ago Up About a minute 3128/tcp

# docker-weaviate-1 semitechnologies/weaviate:1.19.0 "/bin/weaviate --hos…" weaviate About a minute ago Up About a minute

# docker-web-1 langgenius/dify-web:0.6.13 "/bin/sh ./entrypoin…" web About a minute ago Up About a minute 3000/tcp

# docker-worker-1 langgenius/dify-api:0.6.13 "/bin/bash /entrypoi…" worker About a minute ago Up About a minute 5001/tcp

```

  

# 啟動frp

> 主機 ( frps )

```

cd ./frp_0.61.2_linux_amd64

nano frps.toml

```

```

# frps.toml

[common]

bind_port = 7000

token = YOUR_TOKEN

  

# dashboard setting

dashboard_port = 7500

dashboard_user = YOUR_USERNAME

dashboard_pwd = YOUR_PASSWORD

```

```

./frps -c frps.toml

```

  

> 從機 ( frpc )

```

cd ./frp_0.61.2_linux_amd64

nano frpc.toml

```

```

# frpc.toml

[common]

server_addr = HOST_IP # 主機的IP

server_port = 7000

token = YOUR_TOKEN

  

[dify]

type = tcp

local_ip = LOCAL_IP

local_port = LOCAL_PORT

remote_port = PORT_YOU_WANT_TO_CONNECT

```

```

./frpc -c frpc.toml

```

  

# 在Synology NAS ( DS423+ ) 啟動frp

1. 在套件中心下載container manager

2. 專案 > 新增

```

專案名稱: Name you like

路徑 : Path you like

來源 : 建立docker-compose.yaml

檔案 :

version: "3.8"

services:

ubuntu:

image: ubuntu:24.04

container_name: ubuntu_frps

tty: true

stdin_open: true

network_mode: "host"

volumes: - /path/to/your/frp:/frp

command: /frp/frp_0.61.1_linux_amd64/frps -c /frp/frp_0.61.1_linux_amd64/frps.toml

```

3. 建立完成

  
  

# 反向代理

> 在NAS中建立ubuntu24.04並建立frp後，可以直接用NAS自帶的反向代理伺服器進行反向代理

1. 控制台 > 登入入口 > 進階 > 反向代理伺服器 > 新增

```

反向代理名稱: dify

  

通訊協定: HTTPS

主機名稱: URL you like

連接埠: port you like

  

通訊協定: HTTP

主機名稱: NAS IP

連接埠: frpc.toml的remote_port

```

```

反向代理名稱: frps_dashboard

  

通訊協定: HTTPS

主機名稱: URL you like

連接埠: port you like

  

通訊協定: HTTP

主機名稱: NAS IP

連接埠: 7500

```

# 參考資料

dify: https://dify.ai/
FRP: https://github.com/fatedier/frp