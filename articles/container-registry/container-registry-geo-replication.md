---
title: 레지스트리 지역 복제
description: 레지스트리가 다중 마스터 지역 복제본을 사용하여 여러 지역에 서비스를 제공할 수 있는 지리적 복제 Azure 컨테이너 레지스트리 만들기 및 관리를 시작합니다. 지역 복제는 프리미엄 서비스 계층 기능입니다.
author: stevelas
ms.topic: article
ms.date: 06/28/2021
ms.author: stevelas
ms.openlocfilehash: c616c3e196547d72825759de94792cc6573a12d9
ms.sourcegitcommit: 40dfa64d5e220882450d16dcc2ebef186df1699f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113037979"
---
# <a name="geo-replication-in-azure-container-registry"></a>Azure Container Registry의 지리적 복제

로컬 상태나 핫 백업이 필요한 기업은 둘 이상의 Azure 지역에서 서비스를 실행하도록 선택할 수 있습니다. 이미지가 실행되는 각 지역에 컨테이너 레지스트리를 배치하면 네트워크와 가까운 곳에서 작업하여 이미지 레이어를 빠르고 안정적으로 전송할 수 있습니다. 지리적 복제를 사용하면 Azure Container Registry가 단일 레지스트리로 기능하여 다중 마스터 지역 레지스트리가 있는 둘 이상의 지역에 서비스를 제공할 수 있습니다. 

지리적 복제된 레지스트리는 다음과 같은 이점을 제공합니다.

* 여러 지역에서 단일 레지스트리, 이미지, 태그 이름 사용 가능
* 네트워크 닫기 레지스트리 액세스를 사용하여 지역 배포의 성능 및 안정성 향상
* 컨테이너 호스트와 동일하거나 가까운 지역의 복제된 로컬 레지스트리에서 이미지 계층을 풀하여 데이터 전송 비용을 줄입니다.
* 여러 지역의 레지스트리를 단일 지점에서 관리
* 지역 가동 중단이 발생하는 경우 레지스트리 복원력

> [!NOTE]
> * 둘 이상의 Azure 컨테이너 레지스트리에서 컨테이너 이미지 복사본을 유지해야 하는 경우 Azure Container Registry가 [이미지 가져오기](container-registry-import-images.md)도 지원합니다. 예를 들어 Docker 명령을 사용할 필요 없이 DevOps 워크플로에서 개발 레지스트리의 이미지를 프로덕션 레지스트리로 가져올 수 있습니다.
> * 레지스트리를 지역 복제하는 대신 레지스트리를 다른 Azure 지역으로 이동하려면 [컨테이너 레지스트리를 다른 지역으로 수동 이동](manual-regional-move.md)을 참조하세요.

## <a name="example-use-case"></a>사용 사례
Contoso는 미국, 캐나다, 유럽에서 공개 웹 사이트를 운영하고 있습니다. Contoso는 미국, 캐나다, 유럽 시장에 네트워크와 가까운 곳에 위치한 로컬 콘텐츠를 제공하기 위해 미국 서부, 미국 동부, 캐나다 중부 및 서유럽에서 [Azure Kubernetes Service](../aks/index.yml)(AKS) 클러스터를 실행합니다. Docker 이미지로서 배포된 웹 사이트 애플리케이션은 모든 지역에서 동일한 코드와 이미지를 사용합니다. 해당 지역의 로컬 콘텐츠는 지역별로 고유하게 프로비전된 데이터베이스에서 가져옵니다. 각 지역별 배포는 로컬 데이터베이스와 리소스에 대해 고유한 구성을 갖습니다.

워싱턴주 시애틀에 있는 개발팀은 미국 서부 데이터 센터를 이용합니다.

![복수의 레지스트리에 밀어넣기](media/container-registry-geo-replication/before-geo-replicate.png)<br />*복수의 레지스트리에 밀어넣기*

Contoso가 지리적 복제 기능을 시용하기 전에는 미국 서부 소재의 미국 기반 레지스트리와 추가로 서유럽 소재의 레지스트리가 있었습니다. 개발팀은 이렇게 여러 지역에 서비스를 제공하기 위해 두 개의 레지스트리로 이미지를 밀어넣었습니다.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![복수의 레지스트리에서 가져오기](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*복수의 레지스트리에서 가져오기*

둘 이상의 레지스트리가 있는 경우 다음과 같은 문제가 발생할 수 있습니다.

* 미국 동부, 미국 서부, 캐나다 중부 클러스터는 모두 미국 서부 레지스트리에서 콘텐츠를 가져왔는데, 이때 각 원격 컨테이너 호스트가 미국 서부 데이터 센서에서 이미지를 가져와야 했기 때문에 송신 요금이 발생했습니다.
* 개발팀은 미국 서부 레지스트리와 서유럽 레지스트리로 이미지를 밀어넣어야 했습니다.
* 개발팀은 로컬 레지스트리를 참조하는 이미지 이름을 사용하여 각 지역 배포를 구성하고 유지 관리해야 했습니다.
* 레지스트리 액세스를 지역별로 구성해야 했습니다.

## <a name="benefits-of-geo-replication"></a>지리적 복제의 이점

![지리적 복제된 레지스트리에서 가져오기](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Azure Container Registry의 지리적 복제 기능을 사용하면 다음과 같은 이점을 누릴 수 있습니다.

* 모든 지역에서 단일 레지스트리 관리: `contoso.azurecr.io`
* 모든 지역이 동일한 이미지 URL을 사용하므로 이미지 배포의 단일 구성을 관리합니다. `contoso.azurecr.io/public/products/web:1.2`
* 단일 레지스트리에 푸시하는 반면 ACR은 지역 복제를 자동으로 관리합니다. ACR은 고유한 레이어만 복제하므로 지역 간 데이터 전송이 줄어듭니다. 
* 특정 복제본 이벤트를 알리도록 지역 [웹후크](container-registry-webhook.md)를 구성할 수 있습니다.
* 지역 가동 중단에 대해 복원력이 있는 고가용성 레지스트리를 제공합니다.

또한 Azure Container Registry는 Azure 지역 내에서 복원력 있고 가용성이 높은 Azure 컨테이너 레지스트리를 만들 수 있도록 [가용성 영역](zone-redundancy.md)을 지원합니다. 지역 내 중복도에 대한 가용성 영역, 여러 지역 전반의 지역 복제를 조합하여 레지스트리의 안정성과 성능을 향상합니다.

## <a name="configure-geo-replication"></a>지역에서 복제 구성

지도에서 해당 지역을 클릭하여 간편하게 지리적 복제를 구성할 수 있습니다. Azure CLI에서 [az acr replication](/cli/azure/acr/replication) 명령을 비롯한 도구를 사용하여 지역 복제를 관리하거나 [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/container-registry-geo-replication/)을 사용하여 지역 복제가 가능한 레지스트리를 배포할 수도 있습니다.

지역 복제는 [프리미엄 레지스트리](container-registry-skus.md)의 기능입니다. Basic 및 Standard 레지스트리를 사용 중인 경우, [Azure Portal](https://portal.azure.com)에서 Premium으로 변경할 수 있습니다.

![Azure Portal에서 서비스 계층 전환](media/container-registry-skus/update-registry-sku.png)

프리미엄 레지스트리의 지리적 복제를 구성하려면 Azure Portal( https://portal.azure.com )에 로그인합니다.

Azure Container Registry로 이동하여 **복제**:

![Azure Portal 컨테이너 레지스트리 UI의 복제를 선택합니다.](media/container-registry-geo-replication/registry-services.png)

기존 Azure 지역이 모두 표시된 지도가 나타납니다.

 ![Azure Portal의 지역 지도](media/container-registry-geo-replication/registry-geo-map.png)

* 파란색 육각형은 기존 복제본을 나타냅니다.
* 녹색 육각형은 가능한 복제 지역을 나타냅니다.
* 회색 육각형은 복제를 사용할 수 없는 Azure 지역을 나타냅니다.

복제본을 구성하려면 녹색 육각형을 선택하고 **만들기** 를 선택합니다.

 ![Azure Portal에서 복제 UI 만들기](media/container-registry-geo-replication/create-replication.png)

추가로 복제본을 구성하려면 다른 지역의 녹색 육각형을 선택하고 **만들기** 를 클릭합니다.

ACR이 구성된 복제본 사이의 이미지 동기화를 시작합니다. 동기화가 완료되면 포털에 *준비* 라고 표시됩니다. 포털의 복제본 상태는 자동으로 업데이트되지 않습니다. 업데이트된 상태를 보려면 새로 고침 단추를 클릭합니다.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>지역 복제 레지스트리 사용 시 고려 사항

* 지역 복제 레지스트리의 각 Azure 지역은 일단 설정되면 서로 독립적입니다. 지역 복제된 각 Azure 지역에는 Azure Container Registry SLA가 적용됩니다.
* 지역 복제된 레지스트리에서 이미지를 푸시 또는 풀하면 백그라운드의 Azure Traffic Manager는 네트워크 지연 시간 측면에서 가장 가까운 지역에 있는 레지스트리로 요청을 보냅니다.
* 가장 가까운 Azure 지역에 이미지 또는 태그 업데이트를 푸시한 후 Azure Container Registry가 매니페스트 및 레이어를 사용자가 옵트인한 나머지 Azure 지역에 복제할 때까지 어느 정도 시간이 걸립니다. 큰 이미지는 작은 이미지보다 복제 시간이 오래 걸립니다. 이미지 및 태그는 최종 일관성 모델을 사용하여 복제 지역에서 동기화됩니다.
* 지역 복제 레지스트리에 푸시 업데이트를 사용하는 워크플로를 관리하려면 푸시 이벤트에 응답하는 [webhook](container-registry-webhook.md)를 구성하는 것이 좋습니다. 지역 복제된 Azure 지역에서 완료되는 푸시 이벤트를 추적하도록 지역 복제된 레지스트리 내부에 지역별 webhook를 설정할 수 있습니다.
* 콘텐츠 계층을 나타내는 블롭을 제공하기 위해 Azure Container Registry는 데이터 엔드포인트를 사용합니다. 각 레지스트리의 지역 복제된 지역에서 레지스트리에 대해 [전용 데이터 엔드포인트](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints)를 사용하도록 설정할 수 있습니다. 이러한 엔드포인트를 사용하면 엄격하게 범위가 지정된 방화벽 액세스 규칙을 구성할 수 있습니다. 문제 해결을 위해 복제된 데이터를 유지하는 동안 필요에 따라 [복제에 대한 라우팅 사용하지 않을](#temporarily-disable-routing-to-replication) 수 있습니다.
* 가상 네트워크의 프라이빗 엔드포인트를 사용하여 레지스트리에 [프라이빗 링크](container-registry-private-link.md)를 구성하는 경우 각 지역 복제된 지역에서 전용 데이터 엔드포인트가 기본적으로 사용되도록 설정됩니다. 

## <a name="considerations-for-high-availability"></a>고가용성 고려 사항

* 고가용성 및 복원력을 위해 [영역 중복을](zone-redundancy.md)사용하도록 지원하는 지역에 레지스트리를 만드는 것이 좋습니다. 각 복제본 지역에서 영역 중복을 사용하도록 설정하는 것도 좋습니다.
* 레지스트리의 홈 지역(생성된 지역) 또는 해당 복제본 지역 중 하나에서 중단이 발생하는 경우 지리적으로 복제된 레지스트리는 컨테이너 이미지 푸시 또는 풀링과 같은 데이터 평면 작업에 계속 사용할 수 있습니다. 
* 레지스트리의 홈 지역을 사용할 수 없게 되면 네트워크 규칙 구성, 가용성 영역 사용, 복제본 관리 등의 레지스트리 관리 작업을 수행할 수 없습니다.
* Azure Key Vault에 저장된 [고객 관리형 키로](container-registry-customer-managed-keys.md) 암호화된 지역 복제 레지스트리의 고가용성을 계획하려면 키 자격 증명 모음 [장애 조치 및 중복성](../key-vault/general/disaster-recovery-guidance.md)에 대한 지침을 검토하세요.

## <a name="delete-a-replica"></a>복제본 삭제

레지스트리에 대한 복제본을 구성한 후 더 이상 필요하지 않은 경우 언제든지 삭제할 수 있습니다. Azure Portal 또는 Azure CLI에서 [az acr replication delete](/cli/azure/acr/replication#az_acr_replication_delete) 명령과 같은 기타 도구를 사용하여 복제본을 삭제합니다.

Azure Portal에서 복제본을 삭제하려면 다음을 수행합니다.

1. Azure Container Registry로 이동하여 **복제** 를 선택합니다.
1. 복제본의 이름을 선택하고 **삭제** 를 선택합니다. 복제본을 삭제할지 확인합니다.

Azure CLI를 사용하여 미국 동부 지역에 있는 *myregistry* 의 복제본을 삭제하려면 다음을 수행합니다.

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>지역에서 복제 가격 책정

지역 복제는 Azure Container Registry의 [프리미엄 서비스 계층](container-registry-skus.md)의 기능입니다. 레지스트리를 원하는 지역으로 복제하면 각 지역별로 Premium 레지스트리 요금이 발생합니다.

앞의 예에서 Contoso는 미국 동부, 캐나다 중부, 서유럽에 복제본을 추가하여 두 개의 레지스트리를 하나로 통합했습니다. Contoso는 지금부터 매월 4배의 프리미엄 요금을 지불하게 되며, 구성 또는 관리에 추가로 부과되는 요금은 없습니다. 각 지역은 이제 로컬에서 이미지를 가져오기 때문에 성능과 안정성이 개선되며, 미국 서부에서 캐나다와 미국 동부로 네트워크 송신 요금이 발생하지 않습니다.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>지역에서 복제된 레지스트리를 사용하여 푸시 작업 문제 해결
 
지리적으로 복제된 레지스트리에 이미지를 푸시하는 Docker 클라이언트는 모든 이미지 레이어와 해당 매니페스트를 단일 복제 지역에 푸시할 수 없습니다. 이 문제는 Azure Traffic Manager가 레지스트리 요청을 네트워크에서 가장 가까운 복제 레지스트리로 라우팅하기 때문에 발생할 수 있습니다. 레지스트리에 두 개의 *인접* 복제 지역이 있는 경우 이미지 레이어와 매니페스트를 두 사이트에 배포할 수 있으며 매니페스트의 유효성을 검사할 때 푸시 작업이 실패합니다. 이 문제는 일부 Linux 호스트에서 레지스트리의 DNS 이름이 확인되기 때문에 발생합니다. 클라이언트 쪽 DNS 캐시를 제공하는 Windows에서는 이 문제가 발생하지 않습니다.
 
이 문제가 발생하는 경우 한 가지 해결 방법은 Linux 호스트에서 `dnsmasq`와 같은 클라이언트 쪽 DNS 캐시를 적용하는 것입니다. 이렇게 하면 레지스트리의 이름이 일관되게 확인됩니다. Azure에서 Linux VM을 사용하여 레지스트리에 푸시하는 경우 [Azure의 Linux 가상 머신에 대한 DNS 이름 확인 옵션](../virtual-machines/linux/azure-dns.md)의 옵션을 참조하세요.

이미지를 푸시할 때 가장 가까운 복제본에 대한 DNS 확인을 최적화하려면 Azure 외부에서 작업할 때 푸시 작업의 원본과 동일한 Azure 지역 또는 가장 가까운 지역의 지역 복제 레지스트리를 구성합니다.

### <a name="temporarily-disable-routing-to-replication"></a>일시적으로 복제에 대한 라우팅 사용을 비활성화

지역에서 복제된 레지스트리를 사용하여 작업 문제를 해결하려면, 하나 이상의 복제에 대한 Traffic Manager 라우팅을 일시적으로 사용하지 않도록 설정하는 것이 좋습니다. Azure CLI 버전 2.8부터 복제된 지역을 만들거나 업데이트할 때 `--region-endpoint-enabled` 옵션(미리 보기)을 구성할 수 있습니다. 복제의 `--region-endpoint-enabled` 옵션을 `false`로 설정할 때 Traffic Manager는 해당 지역으로 Docker 밀어넣기 또는 끌어오기 요청을 라우트하지 않습니다. 기본적으로 모든 복제에 대한 라우팅은 사용하도록 설정되며, 모든 복제에 대한 데이터 동기화는 라우팅의 사용 여부에 관계없이 수행됩니다.

기존 복제에 대한 라우팅을 사용하지 않도록 설정하려면 먼저 [az acr replication list][az-acr-replication-list]를 실행하여 레지스트리의 복제를 나열합니다. 그다음 특정 복제에 대한 [az acr replication update][az-acr-replication-update]를 실행하고 `--region-endpoint-enabled false`를 설정합니다. 예를 들어 *myregistry* 의 *westus* 복제에 대한 설정을 구성하려면 다음을 수행합니다.

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

복제에 대한 라우팅을 복원하려면 다음을 수행합니다.

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>다음 단계

총 3부로 구성된 자습서 시리즈 [Azure Container Registry의 지리적 복제](container-registry-tutorial-prepare-registry.md)를 확인하세요. 직접 지리적 복제된 레지스트리를 만들어 보고, 컨테이너를 빌드하고, `docker push` 명령 하나로 여러 지역별 Web Apps for Containers 인스턴스로 배포해 볼 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Container Registry의 지리적 복제](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az_acr_replication_list
[az-acr-replication-update]: /cli/azure/acr/replication#az_acr_replication_update
