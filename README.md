# Docker 镜像同步与资源分发

这个项目用于定期同步 Docker 镜像到阿里云容器镜像服务，并提供镜像下载功能。

## 项目结构

.
├── .github/
│ └── workflows/
│ └── resources-sync.yml # GitHub Actions 工作流
├── resources.list # 镜像同步清单
└── README.md # 项目说明


## 功能特性

- 🔄 **自动镜像同步**: 定期将 Docker 镜像同步到阿里云容器镜像服务
- 📦 **镜像打包下载**: 提供压缩格式的镜像文件下载
- 🔐 **安全存储**: 使用 GitHub Secrets 管理敏感信息
- 🏷️ **多平台支持**: 支持不同架构的 Docker 镜像
- 🚀 **一键发布**: 自动创建 GitHub Release 并提供下载

## 使用方法

### 1. 配置镜像列表

在 `resources.list` 文件中添加需要同步的镜像，示例如下：
    标准镜像
    nginx:latest
    redis:alpine

    多平台镜像
    --platform=linux/amd64 ubuntu:20.04
    --platform=linux/arm64/v8 python:3.9

    带命名空间的镜像
    registry.k8s.io/pause:3.9
    docker.io/library/mysql:8.0

### 2. 设置环境变量

在 GitHub 仓库的 Settings → Secrets and variables → Actions 中配置以下变量：

| 变量名 | 说明 | 示例 |
|--------|------|------|
| `ALIYUN_REGISTRY` | 阿里云 Registry 地址 | `registry.cn-hangzhou.aliyuncs.com` |
| `ALIYUN_NAME_SPACE` | 命名空间 | `your-namespace` |
| `ALIYUN_REGISTRY_USER` | 阿里云用户名 | `your-username` |
| `ALIYUN_REGISTRY_PASSWORD` | 阿里云密码 | `your-password` |

### 3. 触发同步

- **自动触发**: 每年1月1日自动执行
- **手动触发**: 在 GitHub Actions 页面手动运行工作流

## 工作流程

1. **镜像同步阶段**:
   - 从 `resources.list` 读取镜像列表
   - 拉取原始镜像
   - 重新标记并推送到阿里云
   - 清理本地镜像

2. **资源下载阶段**:
   - 从阿里云拉取已同步的镜像
   - 打包为 `.tar.gz` 格式
   - 创建 GitHub Release 并提供下载

## 输出文件

同步完成后，在 GitHub Release 中可以获得：
- `{platform}_{namespace}_{image}_{tag}.tar.gz` 格式的镜像包
- 例如: `linux_amd64_library_nginx_latest.tar.gz`

## 注意事项

### ⚠️ GitHub 免费额度使用提示

本工作流配置为**每年1月1日执行一次**，对免费额度影响极小：

| 资源类型 | 免费额度 | 预计使用量 | 使用率 |
|----------|----------|------------|--------|
| 运行时间 | 2,000分钟/月 | 3-9分钟/月 | 0.15%-0.45% |
| 存储空间 | 500MB/月 | 可忽略不计 | 接近0% |

### 🔧 如需调整执行频率

如需更频繁的执行，可修改 `.github/workflows/resources-sync.yml` 中的 cron 表达式：

```yaml
# 每月执行一次
cron: '0 0 1 * *'

# 每季度执行一次  
cron: '0 0 1 */3 *'

# 每半年执行一次
cron: '0 0 1 */6 *'
```
📊 监控用量
建议定期检查用量：

访问仓库 Settings → Actions → Usage

查看 Storage 使用情况

根据实际需求调整执行频率

## 许可证
本项目采用 MIT 许可证。
