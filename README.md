```bash
docker build -f deploy/Dockerfile . -t harbor.cluster.teramesh.cn/library/kaniko:v1.6.0-idrc
docker push harbor.cluster.teramesh.cn/library/kaniko:v1.6.0-idrc
```
