---
title: AKS(Azure Kubernetes Service)용 Dapr 클러스터 확장(미리 보기)으로 애플리케이션 배포
description: AKS(Azure Kubernetes Service)용 Dapr 클러스터 확장(미리 보기)을 사용하여 애플리케이션 배포
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: quickstart
ms.date: 11/01/2021
ms.custom: template-quickstart
ms.openlocfilehash: 1590bcc48260e4b6729cd12cb80b047ba9fe4acb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479731"
---
# <a name="quickstart-deploy-an-application-using-the-dapr-cluster-extension-preview-for-azure-kubernetes-service-aks"></a>빠른 시작: AKS(Azure Kubernetes Service)용 Dapr 클러스터 확장(미리 보기)을 사용하여 애플리케이션 배포

이 빠른 시작에서는 AKS 클러스터에서 [Dapr 클러스터 확장][dapr-overview]을 사용하는 방법을 익힙니다. 메시지를 생성하는 Python 애플리케이션과 이 메시지를 사용하고 유지하는 Node 애플리케이션으로 구성된 hello world 예제를 배포합니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치](/cli/azure/install-azure-cli)
* [Dapr 클러스터 확장][dapr-overview]을 사용하도록 설정된 AKS 클러스터

## <a name="clone-the-repository"></a>리포지토리 복제

샘플 애플리케이션을 배포하는 데 사용할 파일을 가져오려면 다음과 같이 [빠른 시작 리포지토리][hello-world-gh]를 복제하고 `hello-kubernetes` 디렉터리로 변경합니다.

```bash
git clone https://github.com/dapr/quickstarts.git
cd quickstarts/hello-kubernetes
```

## <a name="create-and-configure-a-state-store"></a>상태 저장소를 만들고 구성

Dapr은 Redis, CosmosDB, DynamoDB, Cassandra 등의 여러 가지 상태 저장소를 사용하여 상태를 유지하고 검색할 수 있습니다. 이 예제에서는 Redis를 사용합니다.

### <a name="create-a-redis-store"></a>Redis 저장소 만들기

1. [Azure Portal][azure-portal-cache]을 열고 Azure Redis Cache 만들기 흐름을 시작합니다.
2. 필요한 정보를 입력합니다.
3. "만들기"를 클릭하여 Redis 인스턴스 배포를 시작합니다.
4. Redis 인스턴스의 호스트 이름을 적어 둡니다. Azure의 "개요"에서 검색할 수 있습니다. `xxxxxx.redis.cache.windows.net:6380` 형식입니다.
5. 인스턴스를 만든 후에는 액세스 키를 얻어야 합니다. "설정" 아래의 "액세스 키"로 이동한 다음, Redis 암호를 저장할 Kubernetes 비밀을 만듭니다.

```bash
kubectl create secret generic redis --from-literal=redis-password=<your-redis-password>
```

### <a name="configure-the-dapr-components"></a>Dapr 구성 요소 구성

저장소를 만든 후에는 Hello World 리포지토리의 배포 디렉터리에 있는 redis.yaml 파일에 키를 추가해야 합니다. `redisHost` 값을 해당하는 Redis 마스터 주소로 바꾸고, `redisPassword`를 해당하는 비밀로 바꿉니다. [여기][dapr-component-secrets]에서 자세히 알아볼 수 있습니다.

또한 다음 두 줄을 `redisPassword` 아래에 추가하여 TLS를 통한 연결을 설정해야 합니다.

```yml
- name: redisPassword
    secretKeyRef:
      name: redis
      key: redis-password
- name: enableTLS
  value: true
```

### <a name="apply-the-configuration"></a>구성 적용

다음과 같이 `redis.yaml` 파일을 적용합니다.

```bash
kubectl apply -f ./deploy/redis.yaml
``` 

그리고 출력에서 상태 저장소가 성공적으로 구성되었는지 확인합니다.

```bash
component.dapr.io/statestore created
```

## <a name="deploy-the-nodejs-app-with-the-dapr-sidecar"></a>Dapr 사이드카를 사용하여 Node.js 앱 배포

다음과 같이 클러스터에 Node.js 앱 배포를 적용합니다.

```bash
kubectl apply -f ./deploy/node.yaml
```

> [!NOTE]
> Kubernetes 배포는 비동기식입니다. 즉, 배포가 완료될 때까지 기다렸다가 다음 단계를 진행해야 합니다. 이렇게 하려면 다음 명령을 사용합니다.
> ```bash
> kubectl rollout status deploy/nodeapp
> ```

그러면 Node.js 앱이 Kubernetes에 배포됩니다. Dapr 컨트롤 플레인은 Dapr 사이드카를 Pod에 삽입합니다. `node.yaml` 파일을 잘 보면 Dapr이 어떻게 해당 배포에 사용하도록 설정되는지 알 수 있습니다.

* `dapr.io/enabled: true` - 이 배포에 사이드카를 삽입하도록 Dapr 컨트롤 플레인에 지시합니다.

* `dapr.io/app-id: nodeapp` - 다른 Dapr 앱으로 메시지를 보내고 통신할 수 있도록 Dapr 애플리케이션에 고유한 ID 또는 이름을 할당합니다.

서비스에 액세스하려면 다음과 같이 `kubectl`을 통해 `EXTERNAL-IP`를 가져오고 기록해 둡니다.

```bash
kubectl get svc nodeapp
```

### <a name="verify-the-service"></a>서비스 확인

서비스를 호출하려면 다음을 실행합니다.

```bash
curl $EXTERNAL_IP/ports
```

다음과 비슷한 내용이 출력됩니다.

```bash
{"DAPR_HTTP_PORT":"3500","DAPR_GRPC_PORT":"50001"}
```

다음으로 애플리케이션에 주문을 제출합니다.

```bash
curl --request POST --data "@sample.json" --header Content-Type:application/json $EXTERNAL_IP/neworder
```

주문을 요청하여 주문이 계속되고 있는지 확인합니다.

```bash
curl $EXTERNAL_IP/order
```

다음과 비슷한 내용이 출력됩니다.

```bash
{ "orderId": "42" }
```

> [!TIP]
> Dapr에서 실행되는 애플리케이션의 상태, 정보 및 로그를 확인할 수 있는 편리한 인터페이스인 Dapr 대시보드를 숙지할 수 있는 좋은 기회입니다. 다음 명령을 실행하면 `http://localhost:8080/`에서 사용할 수 있습니다.
> ```bash
> kubectl port-forward svc/dapr-dashboard -n dapr-system 8080:8080
> ```

## <a name="deploy-the-python-app-with-the-dapr-sidecar"></a>Dapr 사이드카를 사용하여 Python 앱 배포

Python 앱을 간략히 살펴보겠습니다. `hello-kubernetes` 빠른 시작에서 Python 앱 디렉터리로 이동하여 `app.py` 파일을 엽니다.

이 파일은 Dapr의 기본 수신 대기 포트인 `localhost:3500`에 JSON 메시지를 게시하는 기본 Python 앱입니다. `v1.0/invoke/nodeapp/method/neworder`에 게시하여 Node.js 애플리케이션의 `neworder` 엔드포인트를 호출할 수 있습니다. 메시지에는 다음과 같이 1초에 한 번씩 증가하는 `orderId`가 포함된 데이터가 있습니다.

```python
n = 0
while True:
    n += 1
    message = {"data": {"orderId": n}}

    try:
        response = requests.post(dapr_url, json=message)
    except Exception as e:
        print(e)

    time.sleep(1)
```

다음과 같이 Kubernetes 클러스터에 Python 앱을 배포합니다.

```bash
kubectl apply -f ./deploy/python.yaml
```

> [!NOTE]
> 위와 마찬가지로, 다음 명령은 배포가 완료될 때까지 기다립니다.
> ```bash
> kubectl rollout status deploy/pythonapp
> ```

## <a name="observe-messages-and-confirm-persistence"></a>메시지 관찰 및 지속성 확인

Node.js 및 Python 애플리케이션을 모두 배포했으므로, 메시지가 도착하는 것을 확인합니다.

다음과 같이 Node.js 앱의 로그를 가져옵니다.

```bash
kubectl logs --selector=app=node -c node --tail=-1
```

배포가 성공적으로 완료되면 다음과 같은 로그가 표시됩니다.

```bash
Got a new order! Order ID: 1
Successfully persisted state
Got a new order! Order ID: 2
Successfully persisted state
Got a new order! Order ID: 3
Successfully persisted state
```

Node.js 앱의 주문 엔드포인트를 호출하여 최신 주문을 가져옵니다. 다음과 같이 앞에서 저장해 둔 외부 IP 주소를 가져오고, "/order"를 추가하고 이에 대해 GET 요청을 수행합니다(브라우저에 입력하고 Postman 또는 `curl` 사용).

```bash
curl $EXTERNAL_IP/order
{"orderID":"42"}
```

응답에 최신 JSON이 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[az group delete][az-group-delete] 명령을 사용하여 리소스 그룹, AKS 클러스터, 네임스페이스 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 샘플 애플리케이션을 성공적으로 배포한 후에는
> [!div class="nextstepaction"]
> [다른 클러스터 확장에 대해 자세히 알아보세요][cluster-extensions].

<!-- LINKS -->
<!-- INTERNAL -->
[cluster-extensions]: ./cluster-extensions.md
[dapr-overview]: ./dapr.md
[az-group-delete]: /cli/azure/group#az_group_delete

<!-- EXTERNAL -->
[hello-world-gh]: https://github.com/dapr/quickstarts/tree/v1.4.0/hello-kubernetes
[azure-portal-cache]: https://ms.portal.azure.com/#create/Microsoft.Cache
[dapr-component-secrets]: https://docs.dapr.io/operations/components/component-secrets/