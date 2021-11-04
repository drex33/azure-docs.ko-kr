---
title: AKS(Azure Kubernetes Service용 Dapr 확장)(미리 보기)
description: Dapr 클러스터 확장을 사용하여 AKS(Azure Kubernetes Service) 클러스터에 Dapr을 설치하고 구성합니다.
author: greenie-msft
ms.author: nigreenf
ms.service: container-service
ms.topic: article
ms.date: 10/15/2021
ms.custom: devx-track-azurecli, ignite-fall-2021
ms.openlocfilehash: a8ba281a1061643cc582e6de4fc75a8c43fae71c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447433"
---
# <a name="dapr-extension-for-azure-kubernetes-service-aks-preview"></a>AKS(Azure Kubernetes Service용 Dapr 확장)(미리 보기)

[Dapr은](https://dapr.io/) 모든 개발자가 클라우드 및 에지에서 실행되고 언어 및 개발자 프레임워크의 다양성을 수용하는 복원력 있는 상태 비저장 및 상태 비저장 애플리케이션을 쉽게 빌드할 수 있게 해주는 이식 가능한 이벤트 기반 런타임입니다. 사이드카 아키텍처의 이점을 활용하여 Dapr은 마이크로 서비스 구축과 관련하여 겪는 문제를 해결하고 코드 플랫폼에 구애받지 않도록 합니다. 특히 다른 서비스를 안정적이고 안전하게 호출하는 서비스와 관련된 문제를 해결하고, pub-sub를 통해 이벤트 기반 앱을 빌드하고, 여러 클라우드 서비스 및 호스트(예: Kubernetes 및 VM)에서 이식 가능한 애플리케이션을 빌드하는 데 도움이 됩니다.

AKS Dapr 확장을 사용하여 AKS 클러스터에서 Dapr을 프로비전하면 Dapr 도구를 다운로드하고 AKS 클러스터에 런타임을 수동으로 설치 및 관리하는 오버헤드를 없앨 수 있습니다. 또한 확장은 간단한 명령줄 인수를 통해 모든 [네이티브 Dapr 구성 기능을 지원합니다.][dapr-configuration-options]

> [!NOTE]
> Kubernetes 프로덕션 환경에 Dapr을 설치하려는 경우 프로덕션 [사용 설명서에 대한 Dapr 지침][kubernetes-production] 페이지를 참조하세요.

## <a name="how-it-works"></a>작동 방식

AKS Dapr 확장은 Azure CLI 사용하여 AKS 클러스터에서 Dapr 제어 평면을 프로비전합니다. 이렇게 하면 다음이 생성됩니다.

- **dapr 연산자:** Dapr(상태 저장소, pub/sub 등)에 대한 구성 요소 업데이트 및 Kubernetes 서비스 엔드포인트를 관리합니다.
- **dapr-sidecar-injector:** 주석이 추가된 배포 Pod에 Dapr을 삽입하고 환경 변수를 추가하고 `DAPR_HTTP_PORT` `DAPR_GRPC_PORT` 사용자 정의 애플리케이션이 Dapr 포트 값을 하드 코딩하지 않고도 Dapr과 쉽게 통신할 수 있도록 합니다.
- **dapr-placement:** 행위자만 사용됩니다. 행위자 인스턴스를 Pod에 매핑하는 매핑 테이블을 만듭니다.
- **dapr-sentry:** 서비스 간의 mTLS를 관리하고 인증 기관 역할을 합니다. 자세한 내용은 [보안 개요를 참조하세요.][dapr-security]

Dapr이 AKS 클러스터에 설치되면 애플리케이션 서비스에 Dapr 사이드카가 함께 실행됩니다. 이를 통해 Dapr 구성 블록 API를 즉시 사용할 수 있습니다. 구성 요소 API 및 이를 가장 잘 사용하는 방법에 대한 자세한 개요는 [Dapr 구성 요소 개요를][building-blocks-concepts]참조하세요.

> [!WARNING]
> AKS 확장을 통해 Dapr을 설치하는 경우 Dapr CLI 대신 향후 Dapr 관리를 위해 확장을 계속 사용하는 것이 좋습니다. 두 도구를 결합하면 충돌이 발생하여 원치 않는 동작이 발생할 수 있습니다.

## <a name="supported-kubernetes-versions"></a>지원되는 Kubernetes 버전

Dapr 확장은 AKS와 동일한 지원 기간을 사용합니다. 자세한 내용은 [Kubernetes 버전 지원 정책][k8s-version-support-policy]를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항 

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli-windows) 및 *aks-preview 확장을 설치합니다.*
- 없는 경우 [AKS 클러스터][deploy-cluster]를 만들어야 합니다.


### <a name="register-the-aks-extensionmanager-and-aks-dapr-preview-features"></a>및 `AKS-ExtensionManager` 미리 보기 기능 등록 `AKS-Dapr`

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Dapr 확장을 사용할 수 있는 AKS 클러스터를 만들려면 구독에서 및 기능 플래그를 사용하도록 설정해야 `AKS-ExtensionManager` `AKS-Dapr` 합니다.

다음 `AKS-ExtensionManager` `AKS-Dapr` 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 및 기능 플래그를 등록합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ExtensionManager"
az feature register --namespace "Microsoft.ContainerService" --name "AKS-Dapr"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ExtensionManager')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-Dapr')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *Microsoft.KubernetesConfiguration* 및 *Microsoft.ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ContainerService
```

### <a name="setup-the-azure-cli-extension-for-cluster-extensions"></a>클러스터 확장에 대한 Azure CLI 확장 설정

`k8s-extension`Azure CLI 확장도 필요합니다. 다음 명령을 실행하여 설치합니다.
  
```azurecli-interactive
az extension add --name k8s-extension
```

`k8s-extension` 확장이 이미 설치되어 있는 경우 다음 명령을 사용하여 최신 버전으로 업데이트할 수 있습니다.

```azurecli-interactive
az extension update --name k8s-extension
```

## <a name="create-the-extension-and-install-dapr-on-your-aks-cluster"></a>확장을 만들고 AKS 클러스터에 Dapr 설치

Kubernetes 확장을 사용하도록 구독이 등록되면 DApr 확장을 만들어 AKS 클러스터에 Dapr을 설치할 수 있습니다. 예를 들어:

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
```

매개 변수를 지정하고 값을 로 설정하여 Dapr이 부 버전을 자동으로 업데이트하도록 허용하는 옵션이 `--auto-upgrade-minor-version` 있습니다. `true`

```azure-cli-interactive
--auto-upgrade-minor-version true
```

## <a name="configuration-settings"></a>구성 설정

확장을 사용하면 매개 변수를 사용하여 Dapr 구성 옵션을 설정할 수 `--configuration-settings` 있습니다. 예를 들어 HA(고가용성)를 사용하도록 설정된 Dapr을 프로비전하려면 `global.ha.enabled` 매개 변수를 로 설정합니다. `true`

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \  
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2" \
```

> [!NOTE]
> 구성 설정이 중요하고 보호해야 하는 경우(예: 인증서 관련 정보) `--configuration-protected-settings` 매개 변수를 전달하면 값이 읽지 않도록 보호됩니다.

구성 설정이 전달되지 않으면 Dapr 구성의 기본값은 다음과 같습니다.

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

사용 가능한 옵션 목록은 [Dapr 구성][dapr-configuration-options]을 참조하세요.

## <a name="targeting-a-specific-dapr-version"></a>특정 Dapr 버전 대상 지정

> [!NOTE]
> Dapr은 현재 및 이전 버전만 포함하여 롤링 창에서 지원됩니다. 이러한 지원되는 버전을 최신 상태로 유지하는 것은 사용자의 운영 책임입니다. 이전 버전의 Dapr이 있는 경우 중간 업그레이드를 수행하여 지원되는 버전으로 돌아가야 할 수 있습니다.

동일한 명령줄 인수는 특정 버전의 Dapr을 설치하거나 이전 버전으로 롤백하는 데 사용됩니다. 를 로 설정하고 `--auto-upgrade-minor-version` `false` `--version` 설치하려는 Dapr 버전으로 설정합니다. 매개 `version` 변수를 생략하면 확장에서 최신 버전의 Dapr을 설치합니다. 예를 들어 Dapr X.X.X를 사용하려면 다음을 수행합니다. 

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup 
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version false \
--version X.X.X \
```

## <a name="show-current-configuration-settings"></a>현재 구성 설정 표시

명령을 사용하여 `az k8s-extension show` 현재 Dapr 구성 설정을 표시합니다.  

```azure-cli-interactive
az k8s-extension show --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension
```

## <a name="update-configuration-settings"></a>구성 설정 업데이트

> [!IMPORTANT]
> 일부 구성 옵션은 만든 후 수정할 수 없습니다. 이러한 옵션을 조정하려면 확장을 삭제하고 다시 수행해야 합니다. 이 설정은 다음 설정에 적용됩니다.
> * `global.ha.*`
> * `dapr_placement.*`

> [!NOTE]
> HA(고가용성)는 언제든지 사용하도록 설정할 수 있습니다. 그러나 사용하도록 설정되면 확장을 삭제하고 다시 설정해야 합니다. 사용 사례에 고가용성이 필요한지 확실하지 않은 경우 중단을 최소화하기 위해 사용하지 않도록 설정하여 시작하는 것이 좋습니다.

Dapr 구성 설정을 업데이트하려면 원하는 상태로 확장을 다시 만들기만 하면 됩니다. 예를 들어 다음 구성을 사용하여 이전에 확장을 만들고 설치했다고 가정합니다.

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

`dapr_operator.replicaCount`를 2에서 3으로 업데이트하려면 다음을 사용합니다.

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

확장을 만들거나 업데이트하지 못한 경우 명령을 실행하여 확장 만들기가 실패한 위치를 검사할 수 `az k8s-extension list` 있습니다. 예를 들어 구성 설정에 잘못된 키가 사용되는 경우(예: `global.ha=false` `global.ha.enabled=false` 대신) 

```azure-cli-interactive
az k8s-extension list --cluster-type managedClusters --cluster-name myAKSCluster --resource-group myResourceGroup
```

아래 JSON이 반환되고 오류 메시지가 `message` 속성에 캡처됩니다.

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

확장을 삭제하고 AKS 클러스터에서 Dapr을 제거해야 하는 경우 다음 명령을 사용할 수 있습니다. 

```azure-cli-interactive
az k8s-extension delete --resource-group myResourceGroup --cluster-name myAKSCluster --cluster-type managedClusters --name myDaprExtension
```

## <a name="next-steps"></a>다음 단계

- AKS 클러스터에서 Dapr을 성공적으로 프로비전했으면 [샘플 애플리케이션][sample-application]을 배포해 보세요.

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
