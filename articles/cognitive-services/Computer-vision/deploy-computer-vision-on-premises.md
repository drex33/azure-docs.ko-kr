---
title: Kubernetes 및 Helm에서 Computer Vision 컨테이너 사용
titleSuffix: Azure Cognitive Services
description: Kubernetes 및 Helm을 사용하여 Computer Vision 컨테이너를 배포하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aahi
ms.openlocfilehash: 18099d4de55423bdef2a5c05f3aecfa34a653bd0
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426367"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Kubernetes 및 Helm에서 Computer Vision 컨테이너 사용

Computer Vision 컨테이너 온-프레미스를 관리하는 한 가지 옵션은 Kubernetes 및 Helm을 사용하는 것입니다. Kubernetes 및 Helm을 사용하여 Computer Vision 컨테이너 이미지를 정의하면서 Kubernetes 패키지를 만듭니다. 이 패키지는 Kubernetes 클러스터 온-프레미스에 배포됩니다. 마지막으로 배포된 서비스를 테스트하는 방법을 살펴보겠습니다. Kubernetes 오케스트레이션을 사용하지 않고 Docker 컨테이너를 실행하는 방법에 대한 자세한 내용은 [Computer Vision 컨테이너 설치 및 실행](computer-vision-how-to-install-containers.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

Computer Vision 컨테이너 온-프레미스를 사용하기 전에 다음 필수 조건을 충족해야 합니다.

| 필수 | 목적 |
|----------|---------|
| Azure 계정 | Azure 구독이 아직 없는 경우, 시작하기 전에 [체험 계정][free-azure-account]을 만듭니다. |
| Kubernetes CLI | [Kubernetes CLI][kubernetes-cli]는 컨테이너 레지스트리에서 공유 자격 증명을 관리하는 데 필요합니다. Kubernetes는 Kubernetes 패키지 관리자인 Helm 이전에도 필요합니다. |
| Helm CLI | Helm 차트(컨테이너 패키지 정의)를 설치하는 데 사용되는 [Helm CLI][helm-install]를 설치합니다. |
| Computer Vision 리소스 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>Azure **Computer Vision** 리소스 및 관련 API 키는 엔드포인트 URI입니다. 두 값은 모두 리소스의 개요 및 키 페이지에서 제공되며 컨테이너를 시작하는 데 필요합니다.<br><br>**{API_KEY}** : **키** 페이지에 제공된 두 개의 리소스 키 중 하나<br><br>**{ENDPOINT_URI}** : **개요** 페이지에 제공된 엔드포인트|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Kubernetes 클러스터에 연결

호스트 컴퓨터에 사용 가능한 Kubernetes 클러스터가 있어야 합니다. Kubernetes 클러스터를 호스트 컴퓨터에 배포하는 방법에 대한 개념을 이해하려면 [Kubernetes 클러스터 배포](../../aks/tutorial-kubernetes-deploy-cluster.md)에 대한 이 자습서를 참조하세요. [Kubernetes 설명서](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)에서 배포에 대한 자세한 내용을 확인할 수 있습니다.

## <a name="configure-helm-chart-values-for-deployment"></a>배포에 대한 Helm 차트 값 구성

먼저 *read* 라는 폴더를 만듭니다. 그런 다음, `chart.yaml`이라는 새 파일에 다음 YAML 콘텐츠를 붙여 넣습니다.

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the Read OCR container to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

Helm 차트 기본값을 구성하려면 다음 YAML을 복사하여 `values.yaml` 파일에 붙여 넣습니다. `# {ENDPOINT_URI}` 및 `# {API_KEY}` 주석을 원하는 값으로 바꿉니다. 필요한 경우 resultExpirationPeriod, Redis 및 RabbitMQ를 구성합니다.

```yaml
# These settings are deployment specific and users can provide customizations
read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  mcr.microsoft.com/
    repository: azure-cognitive-services/vision/read
    tag: 3.2-preview.1
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read OCR container to store result records.
      # A cache is required if multiple read OCR containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read OCR containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - `billing` 및 `apikey` 값이 제공되지 않은 경우 서비스는 15분 후에 만료됩니다. 마찬가지로 서비스를 사용할 수 없기 때문에 확인에 실패합니다.
> 
> - 예를 들어 Docker Compose 또는 Kubernetes에서 부하 분산 장치 뒤에 여러 Read OCR 컨테이너를 배포하는 경우 외부 캐시가 있어야 합니다. 처리 컨테이너와 GET 요청 컨테이너는 다를 수 있기 때문에 외부 캐시는 결과를 저장하고 컨테이너 간에 결과를 공유합니다. 캐시 설정에 대한 자세한 내용은 [Computer Vision Docker 컨테이너 구성](./computer-vision-resource-container-config.md)을 참조하세요.
>

*read* 디렉터리 아래에 *templates* 폴더를 만듭니다. 다음 YAML을 복사하여 `deployment.yaml` 파일에 붙여넣습니다. `deployment.yaml` 파일은 Helm 템플릿 역할을 합니다.

> 템플릿은 Kubernetes에서 이해할 수 있는 YAML 형식의 리소스 설명인 매니페스트 파일을 생성합니다. [- Helm 차트 템플릿 가이드][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  template:
    metadata:
      labels:
        app: read-app       
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

동일한 *templates* 폴더에서 다음 도우미 함수를 복사하여 `helpers.tpl`에 붙여 넣습니다. `helpers.tpl`은 Helm 템플릿을 생성하는 데 도움이 되는 유용한 함수를 정의합니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 종속 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMain := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostReplica := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMain $hostReplica $passWord $connTail -}}
{{- end -}}
```
템플릿은 부하 분산 장치 서비스와 읽기를 위한 컨테이너/이미지 배포를 지정합니다.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 패키지(Helm 차트)

*Helm 차트* 에는 `mcr.microsoft.com` 컨테이너 레지스트리에서 끌어올 Docker 이미지의 구성이 포함되어 있습니다.

> [Helm 차트][helm-charts]는 Kubernetes 리소스의 관련 집합을 설명하는 파일 컬렉션입니다. 단일 차트를 사용하여 Memcached Pod와 같이 단순한 항목이나, HTTP 서버, 데이터베이스, 캐시 등이 포함된 전체 웹앱 스택과 같이 복잡한 항목을 배포할 수 있습니다.

제공된 *Helm 차트* 는 Computer Vision 서비스의 Docker 이미지와 `mcr.microsoft.com` 컨테이너 레지스트리에서 해당하는 서비스를 끌어옵니다.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Kubernetes 클러스터에 Helm 차트 설치

*Helm 차트* 를 설치하려면 [`helm install`][helm-install-cmd] 명령을 실행해야 합니다. `read` 폴더 위의 디렉터리에서 install 명령을 실행해야 합니다.

```console
helm install read ./read
```

성공적인 설치 실행에서 볼 수 있는 출력의 예는 다음과 같습니다.

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Kubernetes 배포를 완료하는 데 몇 분 정도 걸릴 수 있습니다. Pod와 서비스가 올바르게 배포되고 사용 가능한지 확인하려면 다음 명령을 실행합니다.

```console
kubectl get all
```

다음과 비슷하게 표시됩니다.

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```

## <a name="deploy-multiple-v3-containers-on-the-kubernetes-cluster"></a>Kubernetes 클러스터에 여러 v3 컨테이너 배포

컨테이너의 v3부터 작업 및 페이지 수준 모두에서 컨테이너를 병렬로 사용할 수 있습니다.

의도적으로 각 v3 컨테이너에는 디스패처와 인식 작업자가 있습니다. 디스패처는 다중 페이지 작업을 여러 단일 페이지 하위 작업으로 분할하는 작업을 담당합니다. 인식 작업자는 단일 페이지 문서를 인식하는 데 최적화되어 있습니다. 페이지 수준 병렬화를 달성하려면 부하 분산 장치 뒤에 여러 v3 컨테이너를 배포하고 컨테이너가 범용 스토리지 및 큐를 공유하도록 합니다. 

> [!NOTE] 
> 현재 Azure Storage 및 Azure Queue만 지원됩니다. 

요청을 수신하는 컨테이너는 작업을 단일 페이지 하위 작업으로 분할하고, 범용 큐에 추가할 수 있습니다. 사용량이 적은 컨테이너의 모든 인식 작업자는 큐의 단일 페이지 하위 작업을 사용하고, 인식을 수행하고, 결과를 스토리지에 업로드할 수 있습니다. 처리량은 배포된 컨테이너 수에 따라 최대 `n`배까지 향상될 수 있습니다.

v3 컨테이너는 `/ContainerLiveness` 경로 아래에 활동성 프로브 API를 노출합니다. 다음 배포 예제를 사용하여 Kubernetes에 대한 활동성 프로브를 구성합니다. 

다음 YAML을 복사하여 `deployment.yaml` 파일에 붙여넣습니다. `# {ENDPOINT_URI}` 및 `# {API_KEY}` 주석을 원하는 값으로 바꿉니다. `# {AZURE_STORAGE_CONNECTION_STRING}` 주석을 Azure Storage 연결 문자열로 대체합니다. `replicas`를 원하는 수로 구성합니다. 이 번호는 다음 예제에서 `3`으로 설정됩니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  replicas: # {NUMBER_OF_READ_CONTAINERS}
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: cognitive-services-read
        image: mcr.microsoft.com/azure-cognitive-services/vision/read
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: accept
        - name: billing
          value: # {ENDPOINT_URI}
        - name: apikey
          value: # {API_KEY}
        - name: Storage__ObjectStore__AzureBlob__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        - name: Queue__Azure__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        livenessProbe:
          httpGet:
            path: /ContainerLiveness
            port: 5000
          initialDelaySeconds: 60
          periodSeconds: 60
          timeoutSeconds: 20
--- 
apiVersion: v1
kind: Service
metadata:
  name: azure-cognitive-service-read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort: 5000
  selector:
    app: read-app
```

다음 명령을 실행합니다. 

```console
kubectl apply -f deployment.yaml
```

다음은 성공적인 배포 실행에서 볼 수 있는 예제 출력입니다.

```console
deployment.apps/read created
service/azure-cognitive-service-read created
```

Kubernetes 배포를 완료하는 데 몇 분 정도 걸릴 수 있습니다. Pod와 서비스가 올바르게 배포되고 사용 가능한지 확인하려면 다음 명령을 실행합니다.

```console
kubectl get all
```

다음과 유사한 콘솔 출력이 표시됩니다.

```console
kubectl get all
NAME                       READY   STATUS    RESTARTS   AGE
pod/read-6cbbb6678-58s9t   1/1     Running   0          3s
pod/read-6cbbb6678-kz7v4   1/1     Running   0          3s
pod/read-6cbbb6678-s2pct   1/1     Running   0          3s

NAME                                   TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)          AGE
service/azure-cognitive-service-read   LoadBalancer   10.0.134.0   <none>         5000:30846/TCP   17h
service/kubernetes                     ClusterIP      10.0.0.1     <none>         443/TCP          78d

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   3/3     3            3           3s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/read-6cbbb6678   3         3         3       3s
```

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>다음 단계

AKS(Azure Kubernetes Service)에서 Helm을 사용하여 애플리케이션을 설치하는 방법에 대한 자세한 내용은 [여기를 참조][installing-helm-apps-in-aks]하세요.

> [!div class="nextstepaction"]
> [Cognitive Services 컨테이너][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
