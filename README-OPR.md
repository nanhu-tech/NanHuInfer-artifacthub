nanhu-infer-temp

[![Artifact Hub](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/nanhu-infer)](https://artifacthub.io/packages/search?repo=nanhu-infer)

[nanhu-infer-temp project](https://github.com/nanhu-tech/NanHuInfer-artifacthub)

nanhu-infer/ 是独立的 Chart 目录，内部包含：
- Chart.yaml（必填）
- values.yaml（必填）
- templates/（模板目录，必填）
- crds/（可选，自定义资源）

```
nanhu-infer/
├─ Chart.yaml       # 必填：Chart 元数据（名称、版本等）
├─ values.yaml      # 必填：默认配置值
├─ templates/       # 必填：K8s 资源模板（Deployment、Service 等）
├─ crds/            # 可选：自定义资源定义
└─ README.md        # 可选：使用说明
```

```
helm package ./nanhu-infer

helm repo index . --url https://nanhu-tech.github.io/NanHuInfer-artifacthub/
```