# xLink

> [!WARNING]
> 本项目仅供学习与技术研究，请勿用于违法用途。使用时请遵守所在国家或地区的法律法规。

原生部署 Xray VLESS + REALITY + Vision、rathole Noise 和 HAProxy。
直接使用 VPS 公网 IP，无需自有域名或证书。

## 在线安装

替换为实际公网 IP，在使用 systemd 的 Linux 服务器上执行：

```bash
curl -fsSL https://github.com/nekoimi-labs/xLink-Releases/releases/latest/download/install.sh | sudo bash -s -- --public-host 你的VPS公网IP -y
```

支持 Debian 12+、Ubuntu 22.04+、RHEL 系 9+（暂不支持 SELinux Enforcing），amd64 / arm64。
放行 TCP 443、8443、2333。IPv6 地址不带方括号，IPv6 入口不保证 IPv4 同时可用。

## 压缩包安装

从同一 Release 下载 `xlink-install.tar.gz` 和 `SHA256SUMS`，校验后解压：

```bash
sha256sum --check --ignore-missing SHA256SUMS
mkdir xlink-install
tar -xzf xlink-install.tar.gz -C xlink-install
sudo bash xlink-install/install.sh --public-host 你的VPS公网IP -y
```

安装仍需访问系统软件源和上游 Xray/rathole 的 GitHub Releases。
包内 `install.sh`、`native/app.py` 和 `native/download.py` 是本次发布对应的安装程序源码，许可见 `LICENSE`（AGPL-3.0）。

## 使用

```bash
sudo xlink info                  # VLESS 链接
sudo xlink clash                 # Mihomo / Clash Meta 配置
sudo xlink status                # 服务状态
sudo xlink check                 # 健康检查
sudo journalctl -u 'xlink-*' -f   # 日志
sudo xlink restart              # 应用 /etc/xlink/xlink.env 配置
```

节点和隧道身份保存在 `/var/lib/xlink/identity.json`，重跑安装时保留。配置和备份含凭据，请勿公开。

内网隧道使用 rathole v0.5.0；安全复制 `/var/lib/xlink/rathole/client.toml` 到内网机器，设为权限 600，按需修改 `local_addr` 后运行 `rathole --client client.toml`。
业务链路是 `公网 :443 → HAProxy → 127.0.0.1:2443 → Noise → 内网入口`。
**内网入口必须先解析 PROXY protocol v2，再处理 TLS**；内网应用的证书自行配置，隧道只转发 TCP。
