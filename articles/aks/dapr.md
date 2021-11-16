---
title: AKS (Azure Kubernetes Service) 용 4apr 확장 (미리 보기)
description: Eapr 클러스터 확장을 사용 하 여 AKS (Azure Kubernetes Service) 클러스터에 Eapr를 설치 하 고 구성 합니다.
author: greenie-msft
ms.author: nigreenf
ms.service: container-service
ms.topic: article
ms.date: 10/15/2021
ms.custom: devx-track-azurecli, ignite-fall-2021
ms.openlocfilehash: 9d7a263a17d117aea162b39682fa5bd9ec4c1fde
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485758"
---
# <a name="dapr-extension-for-azure-kubernetes-service-aks-preview"></a>AKS (Azure Kubernetes Service) 용 4apr 확장 (미리 보기)

[6Apr](https://dapr.io/) 는 개발자가 클라우드 및에 지에서 실행 되 고 다양 한 언어 및 개발자 프레임 워크를 채택 하는 복원 력, 상태 비저장 및 상태 저장 응용 프로그램을 쉽게 빌드할 수 있도록 하는 이식 가능한 이벤트 기반 런타임입니다. 사이드카 아키텍처의 이점을 활용 하는 경우에는 아키텍처의 이점을 활용 하 여 마이크로 서비스를 구축 하 고 코드 플랫폼을 독립적으로 유지 하면서 발생 하는 문제를 해결할 수 있습니다. 특히, 다른 서비스를 안정적이 고 안전 하 게 호출 하는 서비스와 관련 된 문제를 해결 하 고, pub-sub를 사용 하 여 이벤트 구동 앱을 빌드하고, 여러 클라우드 서비스 및 호스트 (예: Kubernetes 및 VM)에서 이식 가능한 응용 프로그램을 구축 하는 데 도움이 됩니다.

AKS Eapr 확장을 사용 하 여 AKS 클러스터에 대 한 d 4를 프로 비전 하면, AKS 클러스터에서 런타임 도구를 다운로드 하 고 수동으로 설치 하 고 관리 하는 오버 헤드를 없앨 수 있습니다. 또한 확장은 간단한 명령줄 인수를 통해 모든 [기본 Eapr 구성 기능][dapr-configuration-options] 에 대 한 지원을 제공 합니다.

> [!NOTE]
> Kubernetes 프로덕션 환경에 Eapr를 설치 하려는 경우 [프로덕션 사용 설명서에 대 한 자세한 내용은 4 단계][kubernetes-production] 설명서 페이지를 참조 하세요.

## <a name="how-it-works"></a>작동 방식

AKS Eapr 확장은 Azure CLI를 사용 하 여 AKS 클러스터에 대 한 4Apr 제어 평면을 프로 비전 합니다. 다음을 만듭니다.

- Kubernetes **-operator**: eapr (상태 저장소, 게시/구독 등)에 대 한 구성 요소 업데이트 및 서비스 끝점을 관리 합니다.
- **사이드카-인젝터**: 주석이 추가 된 배포 pod에 d 4를 삽입 하 고 환경 변수를 추가 하 고 `DAPR_HTTP_PORT` `DAPR_GRPC_PORT` 사용자 정의 응용 프로그램에서 사용자 정의 응용 프로그램을 사용 하 여 포트 값을 하드 코딩 하지 않고 i apr와 쉽게 통신할 수 있도록 합니다.
- d **apr-배치**: 행위자에만 사용 됩니다. 행위자 인스턴스를 pod에 매핑하는 매핑 테이블을 만듭니다.
- **4apr-sentry**: 서비스 간 mTLS를 관리 하 고 인증 기관 역할을 합니다. 자세한 내용은 [보안 개요][dapr-security]를 참조 하세요.

AKS 클러스터에 Eapr가 설치 되 면 이제 응용 프로그램 서비스에는 함께 실행 되는 Eapr 사이드카가 있습니다. 이를 통해 사용자는 처음에는 Eapr 빌딩 블록 Api를 사용할 수 있습니다. 빌딩 블록 Api에 대 한 자세한 개요와 이러한 Api를 사용 하는 방법에 대 한 자세한 내용은 (영문) [문서 블록 개요][building-blocks-concepts]를 참조 하세요.

> [!WARNING]
> AKS 확장을 통해 d 4를 설치 하는 경우에는 더 이상 확장을 사용 하 여 eapr CLI 대신 Eapr를 관리 하는 것이 좋습니다. 두 도구를 결합 하면 충돌이 발생 하 여 원치 않는 동작이 발생할 수 있습니다.

## <a name="supported-kubernetes-versions"></a>지원되는 Kubernetes 버전

AKS 확장은 동일한 지원 기간을 사용 합니다. 자세한 내용은 [Kubernetes 버전 지원 정책][k8s-version-support-policy]을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항 

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli-windows) 및 *aks-preview* 확장을 설치 합니다.
- 아직 없는 경우 [AKS 클러스터][deploy-cluster]를 만들어야 합니다.


### <a name="register-the-aks-extensionmanager-and-aks-dapr-preview-features"></a>`AKS-ExtensionManager`및 `AKS-Dapr` 미리 보기 기능 등록

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

AKS 확장을 사용할 수 있는 클러스터를 만들려면 `AKS-ExtensionManager` 구독에서 및 기능 플래그를 사용 하도록 설정 해야 합니다 `AKS-Dapr` .

`AKS-ExtensionManager` `AKS-Dapr` 다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용 하 여 및 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ExtensionManager"
az feature register --namespace "Microsoft.ContainerService" --name "AKS-Dapr"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ExtensionManager')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-Dapr')].{Name:name,State:properties.state}"
```

준비가 되 면 [az provider register][az-provider-register] 명령을 사용 하 여 *KubernetesConfiguration* 및 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ContainerService
```

### <a name="setup-the-azure-cli-extension-for-cluster-extensions"></a>클러스터 확장에 대 한 Azure CLI 확장 설정

Azure CLI 확장도 필요 합니다 `k8s-extension` . 다음 명령을 실행 하 여이를 설치 합니다.
  
```azurecli-interactive
az extension add --name k8s-extension
```

`k8s-extension` 확장이 이미 설치되어 있는 경우 다음 명령을 사용하여 최신 버전으로 업데이트할 수 있습니다.

```azurecli-interactive
az extension update --name k8s-extension
```

## <a name="create-the-extension-and-install-dapr-on-your-aks-cluster"></a>확장을 만들고 AKS 클러스터에 d 4를 설치 합니다.

Kubernetes 확장을 사용 하도록 구독을 등록 한 후에는 AKS 클러스터에 A4를 설치 하는 Eapr 확장을 만들 수 있습니다. 예:

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr
```

매개 변수를 지정 하 `--auto-upgrade-minor-version` 고 값을로 설정 하 여 d 4에서 해당 부 버전을 자동으로 업데이트 하도록 허용 하는 옵션이 있습니다 `true` .

```azure-cli-interactive
--auto-upgrade-minor-version true
```

## <a name="configuration-settings"></a>구성 설정

확장을 사용 하면 매개 변수를 사용 하 여 4Apr 구성 옵션을 설정할 수 있습니다 `--configuration-settings` . 예를 들어 HA (고가용성)를 사용 하도록 설정 된 Aapr를 프로 비전 하려면 `global.ha.enabled` 매개 변수를로 설정 합니다 `true` .

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2"
```

> [!NOTE]
> 구성 설정이 중요 하 고 보호 해야 하는 경우 (예: 인증서 관련 정보) 매개 변수를 전달 하면 `--configuration-protected-settings` 값을 읽지 못하도록 보호 됩니다.

구성 설정이 전달 되지 않은 경우에는 기본적으로 다음을 수행 합니다.

```yaml
  ha:
    enabled: true
    replicaCount: 3
    disruption:
      minimumAvailable: ""
      maximumUnavailable: "25%"
  prometheus:
    enabled: true
    port: 9090
  mtls:
    enabled: true
    workloadCertTTL: 24h
    allowedClockSkew: 15m
```

사용할 수 있는 옵션 목록은 [Eapr 구성][dapr-configuration-options]을 참조 하세요.

## <a name="targeting-a-specific-dapr-version"></a>특정 버전의 특정 버전을 대상으로 지정

> [!NOTE]
> 현재 버전과 이전 버전만을 포함 하는 롤링 창에서 지원 됩니다. 이러한 지원 되는 버전을 사용 하 여 최신 상태로 유지 해야 하는 운영 책임이 있습니다. 이전 버전의 d 4가 있는 경우 지원 되는 버전을 얻으려면 중간 업그레이드를 수행 해야 할 수 있습니다.

동일한 명령줄 인수를 사용 하 여 특정 버전의 버전을 설치 하거나 이전 버전으로 롤백합니다. 을 (를)로 설정 하 `--auto-upgrade-minor-version` 고를 `false` 설치 하려는 `--version` 버전의 버전으로 설정 합니다. `version`매개 변수를 생략 하면 확장에서 최신 버전의 d 4를 설치 합니다. 예를 들어, Eapr X. x. X를 사용 합니다. 

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version false \
--version X.X.X
```

## <a name="limiting-the-extension-to-certain-nodes-nodeselector"></a>확장을 특정 노드 ()로 제한 `nodeSelector`

일부 구성에서는 특정 노드에서만 6Apr를 실행 하려고 할 수 있습니다. 이는 확장 구성에서을 전달 하 여 수행할 수 있습니다 `nodeSelector` . 원하는에를 포함 하는 경우 `nodeSelector` `.` 셸과 확장에서 이스케이프 해야 합니다. 예를 들어 다음 구성은를 사용 하는 노드에만 C4를 설치 합니다 `kubernetes.io/os=linux` .

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2" \
--configuration-settings "global.nodeSelector.kubernetes\.io/os=linux"
```

## <a name="show-current-configuration-settings"></a>현재 구성 설정 표시

명령을 사용 `az k8s-extension show` 하 여 현재  

```azure-cli-interactive
az k8s-extension show --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension
```

## <a name="update-configuration-settings"></a>구성 설정 업데이트

> [!IMPORTANT]
> 일부 구성 옵션은 사후 생성을 수정할 수 없습니다. 이러한 옵션을 조정 하려면 확장을 삭제 하 고 다시 만들지 않아도 됩니다. 이는 다음 설정에 적용 됩니다.
> * `global.ha.*`
> * `dapr_placement.*`

> [!NOTE]
> HA (고가용성)는 언제 든 지 사용할 수 있습니다. 그러나 일단 사용 하도록 설정 하면 확장을 삭제 하 고 다시 만들어야 합니다. 사용 사례에 대해 고가용성이 필요한 지 확실 하지 않은 경우 중단을 최소화 하기 위해 사용 하지 않도록 설정 하는 것으로 시작 하는 것이 좋습니다.

Eapr 구성 설정을 업데이트 하려면 원하는 상태로 확장을 다시 만듭니다. 예를 들어 다음 구성을 사용 하 여 확장을 이전에 만들고 설치 했다고 가정 합니다.

```azurecli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \  
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2" 
```

를 `dapr_operator.replicaCount` 2에서 3으로 업데이트 하려면 다음을 사용 합니다.

```azurecli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=3"
```

## <a name="troubleshooting-extension-errors"></a>확장 오류 문제 해결

확장을 만들거나 업데이트 하지 못한 경우에는 명령을 실행 하 여 확장을 만들지 못한 위치를 검사할 수 있습니다 `az k8s-extension list` . 예를 들어 구성에서 잘못 된 키를 사용 하는 경우 (예 `global.ha=false` : 대신) `global.ha.enabled=false` 

```azure-cli-interactive
az k8s-extension list --cluster-type managedClusters --cluster-name myAKSCluster --resource-group myResourceGroup
```

아래 JSON이 반환 되 고, 오류 메시지가 속성에 캡처됩니다 `message` .

```json
"statuses": [
      {
        "code": "InstallationFailed",
        "displayStatus": null,
        "level": null,
        "message": "Error: {failed to install chart from path [] for release [dapr-1]: err [template: dapr/charts/dapr_sidecar_injector/templates/dapr_sidecar_injector_poddisruptionbudget.yaml:1:17: executing \"dapr/charts/dapr_sidecar_injector/templates/dapr_sidecar_injector_poddisruptionbudget.yaml\" at <.Values.global.ha.enabled>: can't evaluate field enabled in type interface {}]} occurred while doing the operation : {Installing the extension} on the config",
        "time": null
      }
],
```

## <a name="delete-the-extension"></a>확장 삭제

확장을 삭제 하 고 AKS 클러스터에서 Eapr를 제거 해야 하는 경우 다음 명령을 사용할 수 있습니다. 

```azure-cli-interactive
az k8s-extension delete --resource-group myResourceGroup --cluster-name myAKSCluster --cluster-type managedClusters --name myDaprExtension
```

## <a name="next-steps"></a>다음 단계

- AKS 클러스터에서 1Apr를 성공적으로 프로 비전 했으면 [샘플 응용 프로그램][sample-application]을 배포 해 보세요.

<!-- LINKS INTERNAL -->
[deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[sample-application]: ./quickstart-dapr.md
[k8s-version-support-policy]: ./supported-kubernetes-versions.md?tabs=azure-cli#kubernetes-version-support-policy

<!-- LINKS EXTERNAL -->
[kubernetes-production]: https://docs.dapr.io/operations/hosting/kubernetes/kubernetes-production
[building-blocks-concepts]: https://docs.dapr.io/developing-applications/building-blocks/
[dapr-configuration-options]: https://github.com/dapr/dapr/blob/master/charts/dapr/README.md#configuration
[sample-application]: https://github.com/dapr/quickstarts/tree/master/hello-kubernetes#step-2---create-and-configure-a-state-store
[dapr-security]: https://docs.dapr.io/concepts/security-concept/
