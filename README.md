# flask-web-app

通过 **GitHub Actions** 实现 Flask Web 项目的自动化构建、测试与部署，最终部署至头歌（EduCoder）平台服务器。

## 项目结构

```
flask-web-app/
├── src/
│   ├── app.py               # Flask 核心应用代码
│   └── __init__.py          # 空文件
├── tests/
│   └── test_app.py          # 测试用例
├── .github/
│   └── workflows/
│       └── cicd.yml         # GitHub Actions 工作流配置
├── requirements.txt         # 项目依赖清单
└── README.md
```

## 本地运行

```bash
# 安装依赖
pip install -r requirements.txt

# 运行单元测试
pip install pytest
pytest tests/ -v

# 启动应用（默认 http://127.0.0.1:5000）
python src/app.py
```

## CI/CD 流程

推送到 `main` 分支后，`.github/workflows/cicd.yml` 自动执行：

| 阶段 | 内容 |
|---|---|
| **自动化测试** | 拉取代码 → 配置 Python 3.11 → 安装依赖 → `pytest tests/ -v` |
| **自动化部署** | 通过 SSH 连接头歌服务器 → `git pull` 拉取最新代码 → 更新依赖 → `supervisorctl restart flask-web-app` |

部署依赖两个仓库密钥（Settings → Secrets and variables → Actions）：

| Secret | 内容 |
|---|---|
| `TOUGE_SSH_PRIVATE_KEY` | 本地 `~/.ssh/id_rsa` 的完整内容 |
| `TOUGE_SERVER_IP` | 头歌服务器公网 IP |

## 服务器端

应用由 supervisor 托管（头歌服务器不支持 systemd），配置文件位于
`/etc/supervisor/conf.d/flask-web-app.conf`，项目目录为 `/root/flask-web-app`。

```bash
supervisorctl status flask-web-app    # 查看状态
supervisorctl restart flask-web-app   # 重启应用
cat /root/flask-web-app/app.log       # 查看日志
```
