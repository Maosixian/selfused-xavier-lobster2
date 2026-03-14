# 恢复、部署与迁移指南

## 一、误卸载后恢复方法

### 1. 基础恢复
```bash
# 1. 克隆仓库
cd openclaw-workspace

# 2. 安装依赖
npm install -g clawhub

# 3. 安装所有技能
clawhub install self-improving
clawhub install ontology
clawhub install proactive-agent
clawhub install skill-vetter
clawhub install humanizer
clawhub install auto-updater
clawhub install openclaw-youtube-transcript
clawhub install elite-longterm-memory
clawhub install browser-use --force

# 4. 启动OpenClaw
openclaw gateway start
```

### 2. 完整恢复（包含配置）
```bash
# 备份当前配置（如有）
cp -r ~/.openclaw ~/.openclaw.backup

# 克隆仓库

# 恢复配置
cp -r ~/.openclaw/workspace/.openclaw/* ~/.openclaw/

# 重启网关
openclaw gateway restart
```

## 二、Docker部署方法

### 1. Dockerfile示例
```dockerfile
FROM openclaw/openclaw:latest

# 安装依赖
RUN npm install -g clawhub

# 复制工作区
COPY . /home/node/.openclaw/workspace

# 安装技能
RUN clawhub install self-improving && \
    clawhub install ontology && \
    clawhub install proactive-agent && \
    clawhub install skill-vetter && \
    clawhub install humanizer && \
    clawhub install auto-updater && \
    clawhub install openclaw-youtube-transcript && \
    clawhub install elite-longterm-memory && \
    clawhub install browser-use --force

# 暴露端口
EXPOSE 18783

# 启动命令
CMD ["openclaw", "gateway", "start", "--bind", "0.0.0.0"]
```

### 2. Docker Compose示例
```yaml
version: '3'
services:
  openclaw:
    build: .
    ports:
      - "18783:18783"
    volumes:
      - ./workspace:/home/node/.openclaw/workspace
      - ~/.openclaw:/home/node/.openclaw
    environment:
      - OPENCLAW_TOKEN=msx1221
    restart: always
```

## 三、迁移方法

### 1. 同机器迁移
```bash
# 1. 停止服务
openclaw gateway stop

# 2. 复制整个目录
cp -r ~/.openclaw /新的路径/.openclaw

# 3. 设置环境变量
export OPENCLAW_HOME=/新的路径/.openclaw

# 4. 重启服务
openclaw gateway start
```

### 2. 跨机器迁移
```bash
# 源机器操作
tar -zcvf openclaw-backup.tar.gz ~/.openclaw
scp openclaw-backup.tar.gz 目标机器:~

# 目标机器操作
tar -zxvf openclaw-backup.tar.gz -C ~
openclaw gateway start
```

## 四、同步规则
1. 每次技能安装、配置修改后自动同步到仓库
2. 提交信息格式：`[更新类型] 内容描述`（如：`[技能安装] 新增browser-use技能`）
3. 敏感信息（如密钥、token）不会提交到仓库
