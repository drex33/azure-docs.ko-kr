---
title: Helm을 사용하여 AKS (Azure Kubernetes Service) 개발
description: AKS 및 Azure Container Registry와 함께 Helm을 사용하여 클러스터에서 애플리케이션 컨테이너를 패키지하고 실행합니다.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/15/2021
ms.author: zarhoads
ms.openlocfilehash: cc060b0b23cbcef0551ec2660856d453b8a3b0e2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114454151"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>빠른 시작: Helm을 사용하여 AKS (Azure Kubernetes Service) 개발

[Helm][helm]은 Kubernetes 애플리케이션을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다. *APT* 및 *Yum* 과 같은 Linux 패키지 관리자와 마찬가지로 사전 구성된 Kubernetes 리소스의 패키지인 Kubernetes 차트 관리에 Helm을 사용합니다.

빠른 시작에서 Helm을 사용하여 AKS에서 애플리케이션을 패키지하고 실행합니다. Helm을 사용하여 기존 애플리케이션을 설치하는 방법에 대한 자세한 내용은 [AKS에서 Helm을 사용하여 기존 애플리케이션 설치하기][helm-existing]를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치](/cli/azure/install-azure-cli)
* [Helm v3 설치][helm-install]

## <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기
AKS 클러스터에서 Helm을 사용하여 애플리케이션을 실행하려면 컨테이너 이미지를 ACR (Azure Container Registry)에 저장해야 합니다. 5-50 영숫자 문자를 포함하며, Azure 내에서 고유한 사용자 자신의 레지스트리 이름을 제공합니다. *기본* SKU는 개발을 위해 비용에 최적화된 진입점으로, 스토리지와 처리량의 균형을 적절하게 맞추었습니다.

아래 예제에서는 [az acr create][az-acr-create]를 사용하여 *기본* SKU로 *Myresourcegroup* 의 *MyHelmACR* 이라는 ACR을 만듭니다.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

출력은 다음 예제와 비슷합니다. ACR의 *loginServer* 값은 이후 단계에서 사용되므로 기록해 두세요. 아래 예제에서 *myhelmacr.azurecr.io* 는 *MyHelmACR* 의 *loginServer* 입니다.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

새 AKS 클러스터는 컨테이너 이미지를 풀해서 실행하기 위해 ACR에 액세스해야 합니다. 다음과 같은 작업에 다음 명령을 사용합니다.
* *MyAKS* 라는 AKS 클러스터를 만들고 *MyHelmACR* 을 연결합니다.
* *MyAKS* 클러스터에 *MyHelmACR* ACR에 대한 액세스 권한을 부여합니다.


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>AKS 클러스터에 연결

Kubernetes 클러스터를 로컬로 연결하려면 Kubernetes 명령줄 클라이언트인 [kubectl][kubectl]을 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. 

1. `az aks install-cli` 명령을 사용하여 `kubectl`을 로컬로 설치합니다.

    ```azurecli
    az aks install-cli
    ```

2. `az aks get-credentials` 명령을 사용하여 Kubernetes 클러스터에 연결하도록 `kubectl`을 구성합니다. 다음 명령 예제에서는 *MyResourceGroup* 에서 AKS 클러스터 이름 *MyAKS* 에 대한 자격 증명을 가져옵니다.  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

이 빠른 시작에서는 [Azure Vote 애플리케이션][azure-vote-app]을 사용합니다. GitHub에서 애플리케이션을 복제하고 `azure-vote` 디렉터리로 이동합니다.

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
cd azure-voting-app-redis/azure-vote/
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>애플리케이션 예제를 빌드하여 ACR로 푸시합니다.

앞의 Dockerfile을 사용하여 [az acr build][az-acr-build] 명령을 실행하여 이미지를 빌드하여 레지스트리로 푸시합니다. 명령 끝부분에 있는 `.`는 Dockerfile의 위치(이 경우에는 현재 디렉터리)를 설정합니다.

```azurecli
az acr build --image azure-vote-front:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

> [!NOTE]
> 컨테이너 이미지를 ACR로 가져오는 것 외에도 Helm 차트를 ACR로 가져올 수도 있습니다. 자세한 내용은 [Azure Container Registry에 Helm 차트 푸시 및 가져오기][acr-helm]를 참조하세요.

## <a name="create-your-helm-chart"></a>Helm 차트 만들기

`helm create` 명령을 사용하여 Helm 차트를 생성합니다.

```console
helm create azure-vote-front
```

*azure-vote-front/Chart.yaml* 을 업데이트하여 `https://charts.bitnami.com/bitnami` 차트 리포지토리의 *redis* 차트에 대한 종속성을 추가하고 `appVersion`을 `v1`로 업데이트합니다. 예를 들면 다음과 같습니다.

```yml
apiVersion: v2
name: azure-vote-front
description: A Helm chart for Kubernetes

dependencies:
  - name: redis
    version: 14.7.1
    repository: https://charts.bitnami.com/bitnami

...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

`helm dependency update`를 사용하여 Helm 차트 종속성을 업데이트합니다.

```console
helm dependency update azure-vote-front
```

*azure-vote-front/values.yaml* 업데이트:
* *redis* 섹션을 추가하여 이미지 세부 정보, 컨테이너 포트 및 배포 이름을 설정합니다.
* 프런트 엔드 부분을 *redis* 배포에 연결하기 위한 *backendName* 을 추가합니다.
* *image.repository* 를 `<loginServer>/azure-vote-front`로 변경합니다.
* *image.tag* 를 `v1`로 변경합니다.
* *service.type* 을 *LoadBalancer* 로 변경합니다.

예를 들면 다음과 같습니다.

```yml
# Default values for azure-vote-front.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1
backendName: azure-vote-backend-master
redis:
  image:
    registry: mcr.microsoft.com
    repository: oss/bitnami/redis
    tag: 6.0.8
  fullnameOverride: azure-vote-backend
  auth:
    enabled: false

image:
  repository: myhelmacr.azurecr.io/azure-vote-front
  pullPolicy: IfNotPresent
  tag: "v1"
...
service:
  type: LoadBalancer
  port: 80
...
```

*redis* 배포의 이름을 전달하기 위해 *azure-vote-front/templates/deployment.yaml* 에 `env` 섹션을 추가합니다.

```yml
...
      containers:
        - name: {{ .Chart.Name }}
          securityContext:
            {{- toYaml .Values.securityContext | nindent 12 }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
          - name: REDIS
            value: {{ .Values.backendName }}
...
```

## <a name="run-your-helm-chart"></a>Helm 차트 실행

`helm install` 명령을 사용하여 Helm 차트를 사용하여 애플리케이션을 설치합니다.

```console
helm install azure-vote-front azure-vote-front/
```

서비스가 공인 IP 주소를 반환하는 데 몇 분이 걸릴 수 있습니다. `--watch` 인수와 함께 `kubectl get service` 명령을 사용하여 진행 상황을 모니터링합니다.

```console
$ kubectl get service azure-vote-front --watch
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.18.228   <pending>       80:32021/TCP   6s
...
azure-vote-front   LoadBalancer   10.0.18.228   52.188.140.81   80:32021/TCP   2m6s
```

`<EXTERNAL-IP>`을 사용하여 브라우저에서 애플리케이션의 부하 분산 장치로 이동하여 애플리케이션 예제를 확인합니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

[az group delete][az-group-delete] 명령을 사용하여 리소스 그룹, AKS 클러스터, 컨테이너 레지스트리, ACR에 저장된 컨테이너 이미지 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요.
> 
> 관리 ID를 사용하는 경우 ID는 플랫폼에 의해 관리되며 제거할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

Helm을 사용하는 방법에 대한 자세한 내용은 Helm 설명서를 참조하세요.

> [!div class="nextstepaction"]
> [Helm 설명서][helm-documentation]

[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-group-delete]: /cli/azure/group#az_group_delete
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[acr-helm]: ../container-registry/container-registry-helm-repos.md