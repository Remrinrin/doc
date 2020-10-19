# k8s相关工具

---

**Helm Chart**

* Charts are packages of pre-configured Kubernetes resources.
* Helm is a tool that streamlines installing and managing Kubernetes applications.

**ChartMuseum**

* an open-source Helm Chart Repository 

```bash
docker run --rm -it \
  -p 8080:8080 \
  -v $(pwd)/charts:/charts \
  -e DEBUG=true \
  -e STORAGE=local \
  -e STORAGE_LOCAL_ROOTDIR=/charts \
  chartmuseum/chartmuseum:latest
# USING WITH LOCAL FILESYSTEM STORAGE
chartmuseum --debug --port=8080 \
  --storage="local" \
  --storage-local-rootdir="./chartstorage"
# USING WITH ALIBABA CLOUD OSS STORAGE
## set the following env vars: - ALIBABA_CLOUD_ACCESS_KEY_ID - ALIBABA_CLOUD_ACCESS_KEY_SECRET
chartmuseum --debug --port=8080 \
  --storage="alibaba" \
  --storage-alibaba-bucket="my-oss-bucket" \
  --storage-alibaba-prefix="" \
  --storage-alibaba-endpoint="oss-cn-beijing.aliyuncs.com"
```