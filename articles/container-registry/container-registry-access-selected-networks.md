---
title: 공용 레지스트리 액세스 구성
description: 선택한 공용 IP 주소나 주소 범위에서 Azure 컨테이너 레지스트리에 대한 액세스를 사용하기 위한 IP 규칙을 구성합니다.
ms.topic: article
ms.date: 07/30/2021
ms.openlocfilehash: 3a4a4a28dfbcd859cf97be6799e24a8110add436
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627010"
---
# <a name="configure-public-ip-network-rules"></a>공용 IP 네트워크 규칙 구성

기본적으로 Azure Container Registry는 모든 네트워크에 있는 호스트로부터의 인터넷 연결을 수락합니다. 이 문서에서는 특정 공용 IP 주소 또는 주소 범위로부터만 액세스를 허용하도록 컨테이너 레지스트리를 구성하는 방법을 살펴봅니다. Azure CLI 및 Azure Portal 사용에 해당하는 단계가 제공됩니다.

IP 네트워크 규칙은 공용 레지스트리 엔드포인트에 구성됩니다. IP 네트워크 규칙은 [프라이빗 링크](container-registry-private-link.md)로 구성된 프라이빗 엔드포인트에는 적용되지 않음

IP 액세스 규칙 구성은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry 계층](container-registry-skus.md)을 참조하세요.

각 레지스트리는 최대 100 IP 액세스 규칙을 지원 합니다.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>선택한 공용 네트워크에서 액세스 - CLI

### <a name="change-default-network-access-to-registry"></a>레지스트리에 대한 기본 네트워크 액세스 변경

선택한 공용 네트워크에 대한 액세스를 제한하려면 먼저 액세스를 거부하도록 기본 작업을 변경합니다. 다음 [az acr update][az-acr-update] 명령에서 레지스트리 이름을 대체합니다.

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>레지스트리에 네트워크 규칙 추가

공용 IP 주소나 범위에서의 액세스를 허용하는 네트워크 규칙을 [az acr network-rule add][az-acr-network-rule-add] 명령을 사용하여 레지스트리에 추가합니다. 예를 들어 가상 네트워크에서 컨테이너 레지스트리의 이름과 VM의 공용 IP 주소를 대체합니다.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> 규칙을 추가한 후 규칙이 적용되는 데 몇 분 정도 걸립니다.

## <a name="access-from-selected-public-network---portal"></a>선택한 공용 네트워크에서 액세스-포털

1. 포털에서 컨테이너 레지스트리로 이동합니다.
1. **설정** 에서 **네트워킹** 을 선택합니다.
1. **공용 액세스** 탭에서 **선택된 네트워크** 에서 공용 액세스를 허용하도록 선택합니다.
1. **방화벽** 에서 가상 네트워크에 있는 VM의 공용 IP 주소와 같은 공용 IP 주소를 입력합니다. 또는 VM의 IP 주소를 포함하는 CIDR 표기법으로 주소 범위를 입력합니다.
1. **저장** 을 선택합니다.

![컨테이너 레지스트리에 대한 방화벽 규칙 구성][acr-access-selected-networks]

> [!NOTE]
> 규칙을 추가한 후 규칙이 적용되는 데 몇 분 정도 걸립니다.

> [!TIP]
> 선택적으로 로컬 클라이언트 컴퓨터나 IP 주소 범위에서 레지스트리 액세스를 사용합니다. 이 액세스를 허용하려면 컴퓨터의 공용 IPv4 주소가 필요합니다. 인터넷 브라우저에서 "내 IP 주소"를 검색하여 이 주소를 찾을 수 있습니다. 포털의 **네트워킹** 페이지에서 방화벽 설정을 구성할 때 현재 클라이언트 IPv4 주소도 자동으로 표시됩니다.

## <a name="disable-public-network-access"></a>공용 네트워크 액세스 사용 안 함

필요에 따라 레지스트리에서 퍼블릭 엔드포인트를 사용하지 않게 설정합니다. 퍼블릭 엔드포인트를 사용하지 않게 설정하면 모든 방화벽 구성이 재정의됩니다. 예를 들어 [프라이빗 링크](container-registry-private-link.md)를 사용하여 가상 네트워크에서 보호되는 레지스트리에 대한 공용 액세스를 사용하지 않게 설정할 수 있습니다.

> [!NOTE]
> 레지스트리가 [서비스 엔드포인트](container-registry-vnet.md)가 있는 가상 네트워크에 설정된 경우 레지스트리의 공용 엔드포인트에 대한 액세스를 사용하지 않도록 설정하면 가상 네트워크 내의 레지스트리에 대한 액세스도 사용하지 않도록 설정됩니다.

### <a name="disable-public-access---cli"></a>공용 액세스 사용 안 함 - CLI

Azure CLI를 통해 공용 액세스를 사용하지 않으려면 [az acr update][az-acr-update]를 실행하고 `--public-network-enabled`를 `false`로 설정합니다. `public-network-enabled` 인수에는 Azure CLI 2.6.0 이상이 필요합니다. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>공용 액세스 사용 안 함 - 포털

1. 포털에서 컨테이너 레지스트리로 이동하고 **설정 > 네트워킹** 을 선택합니다.
1. **공용 액세스** 탭의 **공용 네트워크 액세스 허용** 에서 **사용 안 함** 을 선택합니다. 그런 다음 **저장** 을 선택합니다.

![공용 액세스를 사용하지 않음][acr-access-disabled]


## <a name="restore-public-network-access"></a>공용 네트워크 액세스 복원

퍼블릭 엔드포인트를 다시 사용하려면 네트워크 설정을 업데이트하여 공용 액세스를 허용합니다. 퍼블릭 엔드포인트를 사용하도록 설정하면 모든 방화벽 구성이 재정의됩니다. 

### <a name="restore-public-access---cli"></a>공용 액세스 복원 - CLI

[az acr update][az-acr-update]를 실행하고 `--public-network-enabled`를 `true`로 설정합니다. 

> [!NOTE]
> `public-network-enabled` 인수에는 Azure CLI 2.6.0 이상이 필요합니다. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>공용 액세스 복원 - 포털

1. 포털에서 컨테이너 레지스트리로 이동하고 **설정 > 네트워킹** 을 선택합니다.
1. **공용 액세스** 탭의 **공용 네트워크 액세스 허용** 에서 **모든 네트워크** 를 선택합니다. 그런 다음 **저장** 을 선택합니다.

![모든 네트워크에서 공용 액세스][acr-access-all-networks]

## <a name="troubleshoot"></a>문제 해결

### <a name="access-behind-https-proxy"></a>HTTPS 프록시를 사용하여 액세스

공용 네트워크 규칙이 설정되거나 레지스트리에 대한 공용 액세스가 거부된 경우 허용되지 않은 공용 네트워크에서 레지스트리에 로그인하려는 시도가 실패합니다. 프록시에 대한 액세스 규칙이 설정되지 않은 경우 HTTPS 프록시 뒤에서의 클라이언트 액세스도 실패합니다. `Error response from daemon: login attempt failed with status: 403 Forbidden` 또는 `Looks like you don't have access to registry`와 유사한 오류 메시지가 표시됩니다.

이러한 오류는 네트워크 액세스 규칙에서 허용하는 HTTPS 프록시를 사용하지만 프록시가 클라이언트 환경에서 제대로 구성되지 않은 경우에도 발생할 수 있습니다. Docker 클라이언트와 Docker 데몬이 모두 프록시 동작에 대해 구성되어 있는지 확인합니다. 자세한 내용은 Docker 문서의 [HTTP/HTTPS 프록시](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)를 참조하세요.

### <a name="access-from-azure-pipelines"></a>Azure Pipelines에서 액세스

특정 IP 주소에 대한 액세스를 제한하는 Azure 컨테이너 레지스트리에서 Azure Pipelines를 사용하는 경우 파이프라인의 아웃바운드 IP 주소가 고정되지 않았으므로 파이프라인이 레지스트리에 액세스하지 못할 수 있습니다. 기본적으로 파이프라인은 변경되는 IP 주소 세트가 있는 가상 머신 풀에서 Microsoft 호스팅 [에이전트](/azure/devops/pipelines/agents/agents)를 사용하여 작업을 실행합니다.

한 가지 해결 방법은 파이프라인을 실행하는 데 사용되는 에이전트를 Microsoft 호스팅 에이전트에서 자체 호스팅 에이전트로 변경하는 것입니다. 관리하는 [Windows](/azure/devops/pipelines/agents/v2-windows) 또는 [Linux](/azure/devops/pipelines/agents/v2-linux) 머신에서 실행되는 자체 호스팅 에이전트를 사용하면 파이프라인의 아웃바운드 IP 주소를 제어하고 이 주소를 레지스트리 IP 액세스 규칙에 추가할 수 있습니다.

### <a name="access-from-aks"></a>AKS에서 액세스

특정 IP 주소에 대한 액세스를 제한하는 Azure 컨테이너 레지스트리에서 AKS(Azure Kubernetes Service)를 사용하는 경우 기본적으로 고정 AKS IP 주소를 구성할 수 없습니다. AKS 클러스터의 송신 IP 주소가 임의로 할당됩니다.

AKS 클러스터가 레지스트리에 액세스하도록 허용하려면 다음 옵션을 사용할 수 있습니다.

* Azure 기본 Load Balancer를 사용하는 경우 AKS 클러스터의 [고정 IP 주소](../aks/egress.md)를 설정합니다. 
* Azure 표준 Load Balancer를 사용하는 경우 클러스터에서 [송신 트래픽을 제어](../aks/limit-egress-traffic.md)하는 방법에 대한 지침을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 가상 네트워크에서 프라이빗 엔드포인트를 사용하여 레지스트리에 대한 액세스를 제한하려면 [Azure 컨테이너에 대한 Azure Private Link 구성](container-registry-private-link.md)을 참조하세요.
* 클라이언트 방화벽 뒤에서 레지스트리 액세스 규칙을 설정해야 할 경우 [방화벽 뒤의 Azure Container Registry에 액세스하기 위한 규칙 구성](container-registry-firewall-access-rules.md)을 참조하세요.
* 자세한 문제 해결 지침은 [레지스트리를 사용하여 네트워크 문제 해결](container-registry-troubleshoot-access.md)을 참조하세요.

[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
