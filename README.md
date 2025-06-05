# Heimdallr

[![PFM-Upstream-Sync](https://github.com/PFM-PowerForMe/heimdallr/actions/workflows/fork-sync.yml/badge.svg)](https://github.com/PFM-PowerForMe/heimdallr/actions/workflows/fork-sync.yml)

## 简介
Heimdallr 是一个非常轻量的通知网关，可以聚合各种推送渠道，使用 Serverless 部署，几乎零成本运行。

## 如何部署?

1. 前置条件:
```shell
mkdir -p /etc/containers/systemd
```

2. 部署:
```shell
mkdir -p /opt/podman-data/env
nvim /opt/podman-data/env/heimdallr.env
```

```
# group Group-A
Group-A_TOKEN=推送TOKEN
Group-A_ENABLED_CHANNELS=PFM-Push

ENABLED_GROUPS=Group-A

# channel PFM-Push
PFM-Push_TYPE=wecom_app
PFM-Push_WECOM_CORP_ID=微信企业ID
PFM-Push_WECOM_AGENT_ID=应用ID
PFM-Push_WECOM_SECRET=应用密钥
```
---

```shell
nvim /etc/containers/systemd/heimdallr.container
```

```
# /etc/containers/systemd/heimdallr.container

[Unit]
Description=The heimdallr container
Wants=network-online.target
After=network-online.target

[Container]
AutoUpdate=registry
ContainerName=heimdallr
Timezone=local
EnvironmentFile=/opt/podman-data/env/heimdallr.env
PublishPort=127.0.0.1:6001:9000
Image=ghcr.io/pfm-powerforme/heimdallr:latest

[Service]
Restart=on-failure
RestartSec=30s
StartLimitInterval=30
TimeoutStartSec=900
TimeoutStopSec=70

[Install]
WantedBy=multi-user.target default.target
```

## 测试
```
curl -X POST 'https://127.0.0.1:6001/push' \
-H "Content-Type: application/json" \
-d '{
    "key": "you_key",
    "title": "*Hello*",
    "body": "测试\n换行\n换行",
    "msg_type": "text",
    "attach": ""
}'
```