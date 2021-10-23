---
title: 서비스 할당량 및 제한
description: 기본 Azure Batch 할당량, 한도 및 제약 조건에 대해 알아보고 할당량 증가를 요청하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 07/20/2021
ms.custom: seodec18
ms.openlocfilehash: 799258258e1215977aea5b176d133bd121624bd6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239118"
---
# <a name="batch-service-quotas-and-limits"></a>Batch 서비스 할당량 및 제한

다른 Azure 서비스와 마찬가지로 Batch 서비스와 관련하여 특정 리소스에 대한 제한이 있습니다. 이러한 제한 대부분은 Azure 구독 또는 계정 수준에서 적용되는 기본 할당량입니다.

Batch 워크로드를 설계 및 강화할 때 이 할당량에 주의합니다. 예를 들어, 풀이 지정한 컴퓨팅 노드의 대상 수에 도달하지 않는 경우 배치 계정의 주요 할당량 한도에 도달했을 수 있습니다.

단일 Batch 계정에서 여러 Batch 워크로드를 실행하거나 다른 Azure 지역이 아닌 동일한 구독에 있는 Batch 계정 간에 워크로드를 배포할 수 있습니다.

Batch에서 프로덕션 작업을 실행하려고 계획하는 경우, 위 기본값의 할당량 중 두 개 이상을 늘려야 할 수 있습니다. 할당량을 늘리기 위해 무료로 [할당량 증가를 요청](#increase-a-quota)할 수 있습니다.

## <a name="resource-quotas"></a>리소스 할당량

할당량은 용량 보장이 아니라 한도입니다. 대규모 용량이 필요한 경우 Azure 지원에 문의하세요.

또한 할당량이 보장되는 값은 아닙니다. 할당량은 Batch 서비스로부터의 변경 또는 사용자의 할당량 값 변경 요청에 따라 달라질 수 있습니다.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>코어 할당량

### <a name="cores-quotas-in-batch-service-mode"></a>Batch 서비스 모드의 코어 할당량

Batch에서 지원하는 각 VM 시리즈에 대한 코어 할당량이 있습니다. 이 코어 할당량은 Azure Portal의 **할당량** 페이지에 표시됩니다. VM 시리즈 할당량 한도는 아래 설명된 대로 지원 요청으로 업데이트할 수 있습니다. 전용 노드의 경우 Batch는 각 VM 시리즈의 코어 할당량 한도 및 전체 배치 계정의 총 코어 할당량 한도를 적용합니다. 우선 순위가 낮은 노드의 경우 Batch는 서로 다른 VM 시리즈를 구분하지 않고 배치 계정의 총 코어 할당량만 적용합니다.

### <a name="cores-quotas-in-user-subscription-mode"></a>사용자 구독 모드에서 코어 할당량

풀 할당 모드가 **사용자 구독** 으로 설정된 [배치 계정](accounts.md)을 만든 경우 Batch VM 및 기타 리소스는 풀이 생성되거나 풀 크기가 조정될 때 구독에 직접 생성됩니다. Azure Batch 코어 할당량이 적용되지 않으며 지역 컴퓨팅 코어, 시리즈별 컴퓨팅 코어 및 기타 리소스에 대한 구독의 할당량이 사용되고 적용됩니다.

이러한 할당량에 대해 자세히 알아보려면 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.

## <a name="pool-size-limits"></a>풀 크기 제한

풀 크기 제한은 Batch 서비스에 의해 설정됩니다. [리소스 할당량](#resource-quotas)과 달리 이 값은 변경할 수 없습니다. 노드 간 통신 및 사용자 지정 이미지를 포함하는 풀에만 표준 할당량과 다른 제한이 있습니다.

| **리소스** | **최대 제한** |
| --- | --- |
| **[노드 간 통신 사용 풀](batch-mpi.md)의 컴퓨팅 노드**  ||
| Batch 서비스 풀 할당 모드 | 100 |
| Batch 구독 풀 할당 모드 | 80 |
| **[관리 이미지 리소스를 사용하여 만든 풀](batch-custom-images.md)의 컴퓨팅 노드**<sup>1</sup> ||
| 전용 노드 | 2000 |
| 우선 순위가 낮은 노드 | 1000 |

<sup>1</sup> 노드 간 통신을 사용하도록 설정되지 않은 풀입니다.

## <a name="other-limits"></a>기타 제한

이 추가 한도는 Batch 서비스에서 설정됩니다. [리소스 할당량](#resource-quotas)과 달리 이 값은 변경할 수 없습니다.

| **리소스** | **최대 제한** |
| --- | --- |
| 컴퓨팅 노드당 [동시 작업](batch-parallel-node-tasks.md) | 4 x 노드 코어 수 |
| [애플리케이션](batch-application-packages.md) | 200 |
| 애플리케이션당 애플리케이션 패키지 | 40 |
| 풀당 애플리케이션 패키지 | 10 |
| 최대 작업 수명 | 180일<sup>1</sup> |
| 컴퓨팅 노드당 [탑재](virtual-file-mount.md) | 10 |
| 풀당 인증서 | 12 |

<sup>1</sup> 태스크의 최대 수명(태스크가 작업에 추가되는 시점부터 완료되는 시점까지)은 180일입니다. 완료된 태스크는 7일 동안 지속됩니다. 최대 수명 이내에 완료되지 않은 태스크에 대한 데이터에는 액세스할 수 없습니다.

## <a name="view-batch-quotas"></a>Batch 할당량 보기

[Azure Portal](https://portal.azure.com)에서 Batch 계정 할당량을 보려면

1. **Batch 계정** 을 선택한 다음, 관심 있는 Batch 계정을 선택합니다.
1. 배치 계정의 메뉴에서 **할당량** 을 선택합니다.
1. Batch 계정에 현재 적용된 할당량을 표시합니다.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Azure Portal에서 배치 계정 할당량을 보여 주는 스크린샷":::

## <a name="increase-a-quota"></a>할당량 증가

[Azure Portal](https://portal.azure.com)을 사용하거나 [Azure 할당량 REST API](#azure-quota-rest-api)를 사용하여 배치 계정 또는 구독에 대해 할당량 증가를 요청할 수 있습니다.

할당량 증가 유형은 Batch 계정의 풀 할당 모드에 따라 다릅니다. 할당량 증가를 요청하려면 할당량을 늘릴 VM 시리즈를 포함해야 합니다. 할당량 증가가 적용되는 경우 모든 VM 시리즈에 적용됩니다.

지원 요청을 제출하면 Azure 지원 팀에서 연락을 드릴 것입니다. 할당량 요청은 몇 분에서 최대 2일(영업일 기준) 이내에 완료될 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. **할당량** 페이지에서 **할당량 증가 요청** 을 선택합니다. 또는 포털 대시보드에서 **도움말 + 지원** 타일(또는 포털의 오른쪽 위에 있는 물음표( **?** ))을 선택한 다음, **새 지원 요청** 을 선택할 수 있습니다.

1. **기본 사항** 에서

    1. **문제 유형** 에 대해 **서비스 및 구독 제한(할당량)** 을 선택합니다.
    1. 구독을 선택합니다.
    1. **할당량 유형** 에서 **Batch** 를 선택합니다.
    1. 계속하려면 **다음** 을 선택합니다.

1. **세부 정보** 에서 다음을 수행합니다.

    1. **세부 정보 제공** 섹션에서 위치, 할당량 유형, 배치 계정(해당하는 경우)을 지정한 다음, 늘릴 할당량을 선택합니다.

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="할당량 증가를 요청할 때 할당량 세부 정보 화면의 스크린샷":::

       할당량 유형에는 다음이 포함됩니다.

       - **Batch 계정당**  
         이 옵션을 사용하여 전용 코어, 우선 순위가 낮은 코어, 작업 및 풀 수를 포함하여 단일 배치 계정에 특정한 할당량 증가를 요청합니다.

         이 옵션을 선택하는 경우 해당 요청을 적용할 배치 계정을 지정한 다음, 업데이트할 할당량을 선택합니다. 각 리소스에 대해 요청하는 새 한도를 제공합니다.

         낮은 우선 순위 할당량은 모든 VM 시리즈에 걸친 단일 값입니다. 제한된 SKU가 필요한 경우 **우선 순위가 낮은 코어** 를 선택하고 요청할 VM 제품군을 포함해야 합니다.

       - **이 지역의 모든 계정**  
         이 옵션을 사용하여 구독당 지역당 배치 계정 수와 같이 지역의 모든 배치 계정에 적용되는 할당량 증가를 요청합니다.

    1. **지원 방법** 에서 [비즈니스 영향](https://aka.ms/supportseverity)에 따라 **심각도** 를 선택하고 원하는 연락 방법 및 지원 언어를 선택합니다.

    1. **연락처 정보** 에서 필요한 연락처 세부 정보를 확인하고 입력합니다.

1. **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택하여 지원 요청을 제출합니다.

### <a name="azure-quota-rest-api"></a>Azure 할당량 REST API

Azure 할당량 REST API를 사용하여 구독 수준 또는 배치 계정 수준에서 할당량 증가를 요청할 수 있습니다.

자세한 내용과 예제는 [Azure 지원 REST API를 사용하여 할당량 증가 요청](/rest/api/support/quota-payload#azure-batch)을 참조하세요.

## <a name="related-quotas-for-vm-pools"></a>VM 풀에 대한 관련 할당량

[Azure 가상 네트워크에 배포된 Virtual Machine 구성의 Batch 풀](batch-virtual-network.md)은 추가 Azure 네트워킹 리소스를 자동으로 할당합니다. 이 리소스는 Batch 풀을 만들 때 제공되는 가상 네트워크가 포함된 구독에 생성됩니다.

가상 네트워크의 100개 풀 노드 각각에 대해 다음 리소스가 생성됩니다.

- [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#network-security-groups) 1개
- [공용 IP 주소](../virtual-network/ip-services/public-ip-addresses.md) 1개
- [부하 분산 장치](../load-balancer/load-balancer-overview.md) 1개

이러한 리소스는 구독의 [리소스 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md)으로 제한됩니다. 가상 네트워크에서 대규모 풀 배포를 계획하는 경우 해당 리소스 중 하나 이상에 대해 할당량 증가를 요청해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업 및 태스크와 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)에 대해 알아봅니다.