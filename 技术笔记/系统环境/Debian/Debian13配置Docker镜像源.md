## 一. 创建 / 修改 docker 加速配置

```bash
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://huecker.io",
    "https://dockerhub.timeweb.cloud",
    "https://noohochai.d.1panel.live"
  ]
}
```

EOF

## 二. 重载配置、重启 docker

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 三. 验证加速是否生效

```
bash
docker info
```

看到 Registry Mirrors 列出上面地址代表成功。

## 四. 再次测试

```bash
sudo docker run hello-world
```
