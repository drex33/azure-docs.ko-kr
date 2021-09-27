---
title: 레지스트리 서비스 계층 및 기능
description: Azure Container Registry의 기본, 표준 및 프리미엄 서비스 계층(SKU)에 있는 기능 및 제한(할당량)에 대해 알아봅니다.
ms.topic: article
ms.date: 08/12/2021
ms.openlocfilehash: 7f9fe5d461dede4510d3fc8069f42e7950803984
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582342"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry 서비스 계층

Azure Container Registry는 다중 서비스 계층(SKU라고도 함)에서 사용 가능합니다. 해당 계층은 예측 가능한 가격 책정과 Azure에서 프라이빗 Docker 레지스트리의 용량 및 사용량 패턴에 맞추기 위한 여러 옵션을 제공합니다.

| 계층 | Description |
| --- | ----------- |
| **기본** | 개발자가 Azure Container Registry에 대해 배울 수 있는 비용 최적화된 진입점입니다. 기본 레지스트리는 표준 및 프리미엄(예: Azure Active Directory [인증 통합](container-registry-authentication.md#individual-login-with-azure-ad), [이미지 삭제][container-registry-delete] 및 [웹후크][container-registry-webhook])과 동일한 프로그래밍 성능을 가집니다. 그렇지만 포함된 스토리지 및 이미지 처리량은 더 낮은 사용량 시나리오에 가장 적합합니다. |
| **Standard** | Standard 레지스트리는 Basic과 동일한 성능을 제공하되, 포함된 스토리지 제한과 이미지 처리량은 더 높습니다. Standard 레지스트리는 대부분의 프로덕션 시나리오 요구를 충족합니다. |
| **Premium** | Premium 레지스트리는 포함된 스토리지 및 동시 작업 수가 가장 높으며 고용량 시나리오를 지원합니다. 프리미엄은 많은 이미지 처리량을 제공하는 것 외에도, 여러 지역에서 단일 레지스트리를 관리하기 위한 [지역 복제][container-registry-geo-replication], 이미지 태그 서명을 위한 [콘텐츠 신뢰](container-registry-content-trust.md), 레지스트리에 대한 액세스를 제한하기 위한 [프라이빗 엔드포인트를 사용한 프라이빗 링크](container-registry-private-link.md)와 같은 기능을 추가로 제공합니다. |

기본, 표준 및 프리미엄 계층은 모두 동일한 프로그래밍 성능을 제공합니다. 또한 Azure에서 완전히 관리하는 [이미지 스토리지][container-registry-storage]를 완벽하게 활용합니다. 더 높은 수준의 계층을 선택하면 향상된 성능과 규모를 제공합니다. 여러 서비스 계층을 사용하여 Basic로 시작한 다음, 레지스트리 사용량 증가에 따라 Standard 및 Premium으로 변환할 수 있습니다.

## <a name="service-tier-features-and-limits"></a>서비스 계층 기능 및 한도

다음 표에서는 기본, 표준 및 프리미엄 서비스 계층의 기능 및 레지스트리 제한에 대해 자세히 설명합니다.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="registry-throughput-and-throttling"></a>레지스트리 처리량 및 제한

### <a name="throughput"></a>처리량 

높은 비율의 레지스트리 작업을 생성할 때 예상되는 최대 처리량에 대한 지침으로 읽기와 쓰기 작업 및 대역폭에 대한 서비스 계층의 제한을 사용합니다. 이러한 제한은 이미지 및 기타 아티팩트 나열, 삭제, 푸시 및 끌어오기와 같은 데이터 평면 작업에 영향을 줍니다.

특히 이미지 끌어오기 및 푸시의 처리량을 예측하려면 레지스트리 제한과 다음 요소를 고려합니다. 

* 이미지 계층의 수 및 크기
* 이미지 전체에서 레이어 또는 기본 이미지 다시 사용
* 각 끌어오기 또는 푸시에 필요할 수 있는 추가 API 호출

자세한 내용은 [Docker HTTP API V2](https://docs.docker.com/registry/spec/api/) 설명서를 참조하세요.

레지스트리 처리량을 평가하거나 문제를 해결할 때 클라이언트 환경의 구성도 고려합니다.

* 동시 작업에 대한 Docker 디먼 구성
* 레지스트리의 데이터 엔드포인트(또는 레지스트리가 [지역 복제](container-registry-geo-replication.md)된 경우 엔드포인트)에 대한 네트워크 연결입니다.

레지스트리의 처리량에 문제가 발생하는 경우 레지스트리 [성능 문제 해결](container-registry-troubleshoot-performance.md)을 참조하세요. 

#### <a name="example"></a>예

단일 133MB `nginx:latest` 이미지를 Azure Container Registry에 푸시하려면 이미지의 5개 계층에 대해 여러 읽기 및 쓰기 작업이 필요합니다. 

* 레지스트리에 있는 경우 이미지 매니페스트를 읽기 위한 읽기 작업
* 이미지의 구성 Blob을 작성하는 쓰기 작업
* 이미지 매니페스트를 작성하는 쓰기 작업

### <a name="throttling"></a>제한

레지스트리에서 요청 속도가 레지스트리의 서비스 계층에 허용되는 제한을 초과하는 것으로 확인되면 끌어오기 또는 푸시 작업이 제한될 수 있습니다. `Too many requests`와 유사한 HTTP 429 오류가 표시될 수 있습니다.

읽기 및 쓰기 작업의 평균 속도가 레지스트리 제한 내에 있는 경우에도 매우 짧은 기간 내에 이미지 끌어오기 또는 푸시 작업의 버스트를 생성할 때 제한이 일시적으로 발생할 수 있습니다. 코드에서 일부 백오프를 사용하여 재시도 논리를 구현하거나 레지스트리에 대한 최대 요청 속도를 줄여야 할 수 있습니다.

## <a name="show-registry-usage"></a>레지스트리 사용 표시

[Az acr show 사용](/cli/az/acr#az_acr_show_usage) 명령 또는 [목록](/rest/api/containerregstry/registries/list-usages) 사용 REST API를 사용 하 여 레지스트리의 현재 저장소 및 기타 리소스 사용량에 대 한 스냅숏을 가져옵니다. 해당 레지스트리의 서비스 계층에 대 한 제한과 비교해 볼 수 있습니다. 포털의 레지스트리의 **개요** 페이지에도 Storage 사용이 표시 됩니다.

사용 정보는 레지스트리가 한도에 도달 하는 경우 [서비스 계층 변경](#changing-tiers) 에 대 한 결정을 내리는 데 도움이 됩니다. 이 정보는 소비를 [관리](container-registry-best-practices.md#manage-registry-size)하는 데도 도움이 됩니다. 

> [!NOTE]
> 레지스트리의 저장소 사용은 가이드로만 사용 해야 하며 최근 레지스트리 작업을 반영 하지 않을 수 있습니다. 최신 데이터에 대해 레지스트리의 [Storageused 메트릭을](monitor-service-reference.md#container-registry-metrics) 모니터링 합니다. 

레지스트리의 서비스 계층에 따라 사용 정보에는 다음의 일부 또는 전체 및 해당 계층의 제한이 포함 됩니다.

* 바이트<sup>1</sup> 에서 사용 된 Storage
* [웹 후크](container-registry-webhook.md) 수
* [지역에서 복제](container-registry-geo-replication.md) (홈 복제본 포함)의 수
* [개인 끝점](container-registry-private-link.md) 수
* [IP 액세스 규칙](container-registry-access-selected-networks.md) 수
* [가상 네트워크 규칙](container-registry-vnet.md) 수

<sup>1</sup> 지역에서 복제 된 레지스트리에서는 홈 지역에 대해 저장소 사용량이 표시 됩니다. 사용 된 총 저장소 수에 대 한 복제 수를 곱합니다.

## <a name="changing-tiers"></a>계층 변경

Azure CLI를 사용하거나 Azure Portal에서 레지스트리의 서비스 계층을 변경할 수 있습니다. 전환하는 계층에 필요한 최대 스토리지 용량이 있는 한 계층 간에 자유롭게 이동할 수 있습니다. 

서비스 계층 간에 이동할 때 레지스트리 가동 중지 시간이나 레지스트리 작업에 영향을 주지 않습니다.

### <a name="azure-cli"></a>Azure CLI

Azure CLI에서 서비스 계층 간에 이동하려면 [az acr update][az-acr-update] 명령을 사용합니다. 예를 들어 Premium으로 전환하려면:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure portal

Azure Portal의 컨테이너 레지스트리 **개요** 에서 **업데이트** 를 선택한 다음, SKU 드롭다운에서 새로운 **SKU** 를 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 SKU 업데이트][update-registry-sku]

## <a name="pricing"></a>가격 책정

각 Azure Container Registry 서비스 계층의 가격 책정 정보는 [Container Registry 가격 책정][container-registry-pricing]을 참조하세요.

데이터 전송 가격 책정에 대한 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

**Azure Container Registry 로드맵**

GitHub에서 [ACR 로드맵][acr-roadmap]에 방문하여 예정된 서비스 기능에 대한 정보를 알아 보세요.

**Azure Container Registry UserVoice**

[ACR UserVoice][container-registry-uservoice]에서 새로운 기능 제안 사항을 제출하고 투표합니다.

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
