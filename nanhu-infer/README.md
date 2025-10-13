# NanHuInfer-artifacthub

[NanHuInfer-artifacthub project](https://github.com/nanhu-tech/NanHuInfer-artifacthub)

## 一、仓库介绍

本仓库是 `nanhu-infer` 系列 AI 推理服务 Helm Chart 的官方存储源，专注于为 Kubernetes 集群提供便捷、可定制的 AI 推理部署方案。支持图像识别、文本生成等多种推理任务，所有 Chart 均经过兼容性测试，适配主流 Kubernetes 版本（1.24+），可灵活应用于开发调试、测试验证及生产环境部署场景。

## 二、包含的 Helm Chart 列表



| Chart 名称      | 最新版本  | 核心功能描述                                                 |
| ------------- | ----- | ------------------------------------------------------ |
| `nanhu-infer` | 0.0.5 | 核心推理服务部署包，包含推理引擎 Deployment、模型存储挂载、Service 网络暴露、资源弹性配置 |

## 三、快速开始

### 1. 环境准备



* 已安装 Helm 3.8+（查看版本：`helm version`）

* 已配置 Kubernetes 集群访问权限（验证：`kubectl cluster-info`）

* 若需持久化存储（模型文件），集群需提前创建 StorageClass

### 2. 添加并更新 Helm 仓库



```
# 添加 nanhu-infer 仓库到本地 Helm 客户端

helm repo add nanhu-infer https://nanhu-tech.github.io/NanHuInfer-artifacthub/

# 更新仓库索引，确保获取最新 Chart 版本

helm repo update
```

### 3. 搜索与查看 Chart 信息



```
# 搜索仓库内所有可用 Chart

helm search repo nanhu-infer/

# 查看 \`nanhu-infer\` Chart 的详细元数据（版本、依赖、描述）

helm show chart nanhu-infer/nanhu-infer

# 查看 Chart 的默认配置项（便于自定义修改）

helm show values nanhu-infer/nanhu-infer --version 0.0.5
```

### 4. 安装 Chart

#### 4.1 基础安装（默认配置，适合快速测试）



```
# 安装 \`nanhu-infer\` Chart，指定释放名为 "nanhu-infer-test"，版本 0.0.5

helm install nanhu-infer-test nanhu-infer/nanhu-infer --version 0.0.5
```

#### 4.2 自定义配置安装（生产环境推荐）

通过 `--set` 参数或自定义 `values.yaml` 文件，调整模型路径、资源限制、服务暴露方式等关键配置。

**示例 1：指定模型路径与资源限制**



```
helm install nanhu-infer-prod nanhu-infer/nanhu-infer \\

  --version 0.0.5 \\ helm version

  --set image.tag=v0.0.5 \                     # 推理服务镜像版本

  --set model.path=/data/models/llama-2-7b \   # 容器内模型文件挂载路径

  --set persistence.enabled=true \             # 启用持久化存储（存储模型文件）

  --set persistence.size=50Gi \                # 持久化存储容量

  --set persistence.storageClass=standard \    # 指定集群中的 StorageClass

  --set resources.limits.cpu=8 \               # CPU 资源上限

  --set resources.limits.memory=32Gi \         # 内存资源上限

  --set service.type=LoadBalancer \            # 用 LoadBalancer 暴露服务（云环境适用）

  --set service.port=8080                      # 服务暴露端口
```

**示例 2：使用自定义 values 文件安装**



```
# 1. 下载默认 values.yaml 到本地

helm show values nanhu-infer/nanhu-infer --version 0.0.5 > custom-values.yaml

# 2. 编辑 custom-values.yaml（根据需求修改配置，如镜像地址、模型路径）

vim custom-values.yaml

# 3. 基于自定义配置文件安装

helm install nanhu-infer-prod nanhu-infer/nanhu-infer --version 0.0.5 -f custom-values.yaml
```

### 5. 验证安装结果



```
# 查看 Helm 释放状态（确认安装是否成功）

helm status nanhu-infer-prod

# 查看推理服务 Pod 运行状态（确保状态为 Running）

kubectl get pods -l app.kubernetes.io/name=nanhu-infer

# 查看 Service 信息（获取访问地址/端口）

kubectl get svc nanhu-infer-prod-nanhu-infer

```

## 四、核心配置项说明



| 配置项                        | 默认值                 | 说明                                                                             |
| -------------------------- | ------------------- | ------------------------------------------------------------------------------ |
| `image.repository`         | `3ziye/nanhu-infer` | 推理服务镜像仓库地址                                                         |
| `image.tag`                | `v0.0.5`            | 镜像标签（需与实际部署的应用版本匹配）                                                            |
| `image.pullPolicy`         | `IfNotPresent`      | 镜像拉取策略（`Always`/`IfNotPresent`/`Never`）                                        |
| `model.path`               | `/models/default`   | 容器内模型文件挂载路径（需与持久化存储的挂载路径一致）                                                    |
| `persistence.enabled`      | `false`             | 是否启用持久化存储（生产环境建议开启，避免模型文件丢失）                                                   |
| `persistence.size`         | `10Gi`              | 持久化存储容量（根据模型文件大小调整，如 7B 模型建议 50Gi+）                                            |
| `persistence.storageClass` | `""`                | 存储类（需填写集群中已存在的 StorageClass 名称）                                                |
| `service.type`             | `ClusterIP`         | 服务暴露类型：- `ClusterIP`：仅集群内访问 - `NodePort`：节点端口访问 - `LoadBalancer`：云厂商负载均衡（公网访问） |
| `service.port`             | `80`                | 服务暴露端口（需与容器内推理服务监听端口一致）                                                        |
| `resources.limits.cpu`     | `2`                 | CPU 资源上限（推理任务建议 4+，根据模型复杂度调整）                                                  |
| `resources.limits.memory`  | `8Gi`               | 内存资源上限（7B 模型建议 16Gi+，避免 OOM）                                                   |
| `nodeSelector`             | `{}`                | 节点选择器（可指定推理服务部署到 GPU 节点，如 `gpu: "true"`）                                       |

> 完整配置项可通过

`helm show values nanhu-infer/nanhu-infer --version 0.0.5`

查看。

## 五、更新与卸载

### 1. 升级已安装的 Chart



```
# 方法 1：升级到仓库中最新版本（保留原配置）

helm upgrade nanhu-infer-prod nanhu-infer/nanhu-infer

# 方法 2：指定版本升级，并应用新配置

helm upgrade nanhu-infer-prod nanhu-infer/nanhu-infer --version 0.0.2 -f custom-values-v2.yaml
```

### 2. 回滚到历史版本

若升级后出现问题，可回滚到之前的稳定版本：



```
# 查看释放的历史版本

helm history nanhu-infer-prod

# 回滚到指定版本（如版本 1）

helm rollback nanhu-infer-prod 1
```

### 3. 卸载 Chart



```
# 卸载释放（删除所有相关 Kubernetes 资源：Deployment、Service、PVC 等）

helm uninstall nanhu-infer-prod

# （可选）删除 nanhu-infer 仓库（若不再使用）

helm repo remove nanhu-infer
```

## 六、常见问题（FAQ）

### 1. 安装后 Pod 一直处于 Pending 状态？



* **原因 1**：资源不足（CPU / 内存 / GPU 不够）。

  解决：通过 `kubectl describe pod <Pod-Name>` 查看具体错误（如 `Insufficient cpu`），调整 `resources.limits` 配置或增加集群节点资源。

* **原因 2**：持久化存储配置错误。

  解决：确认 `persistence.storageClass` 是集群中存在的存储类（通过 `kubectl get sc` 查看），或关闭持久化（`--set persistence.enabled=false`，仅测试用）。

### 2. 无法访问推理服务接口？



1. 确认 Pod 正常运行：`kubectl get pods <Pod-Name>`（状态为 `Running`）；

2. 检查 Service 配置：`kubectl get svc <Service-Name>`，确认 `TYPE` 和 `PORT(S)` 正确；

3. 测试集群内访问：在集群节点或其他 Pod 中执行 `curl <Service-IP>:<Port>/health`；

4. 若用 `LoadBalancer` 类型，确认云厂商已分配外部 IP（可能需要几分钟）。

### 3. 模型加载失败？



* 确认 `model.path` 与持久化存储的挂载路径一致（查看 `values.yaml` 中 `persistence.mountPath` 配置）；

* 检查 PVC 是否正常挂载：`kubectl describe pod <Pod-Name>` 查看 `Volumes` 和 `Mounts` 部分；

* 确认模型文件格式正确（如 `.bin`/`.pt` 等，与推理服务预期格式匹配）。

## 七、维护与支持



* **仓库更新频率**：暂无；

* **问题反馈**：通过 GitHub Issues 提交问题或需求，仓库地址：[https://github.com/nanhu-tech/NanHuInfer-artifacthub/issues](https://github.com/nanhu-tech/NanHuInfer-artifacthub/issues)；

* **联系支持**：若需紧急技术支持，可发送邮件至：support.nanhuinfer@zhejianglab.org

## 八、许可证

本仓库所有 Helm Chart 及相关文档基于 **Apache License 2.0** 开源.