---
title: Azure 가상 데스크톱 리소스 태그-Azure
description: 태깅 이란 무엇 이며 Azure Virtual Desktop에서 Azure 서비스 비용을 관리 하는 데 사용할 수 있는 방법입니다.
author: heidilohr
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 4b6b6ec30217f5be2f1a78778b827ae8eb920163
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133569816"
---
# <a name="tag-azure-virtual-desktop-resources-to-manage-costs"></a>비용을 관리하기 위해 Azure Virtual Desktop 리소스에 태그 지정

태그 지정은 azure 구독 내에서 리소스를 구성 하는 데 도움이 되는 Azure 서비스에서 사용할 수 있는 도구입니다. 리소스를 구성 하면 여러 서비스에서 비용을 보다 쉽게 추적할 수 있습니다. 태그는 청구 주기 당 Azure 리소스의 각 그룹화 정도를 이해 하는 데에도 도움이 됩니다. 일반적인 태그 지정에 대해 자세히 알아보려면 [태그를 사용 하 여 Azure 리소스 및 관리 계층 구조 구성](../azure-resource-manager/management/tag-resources.md)을 참조 하세요. Azure 태그를 사용 하는 몇 가지 다른 방법에 대 한 [빠른 비디오](https://www.youtube.com/watch?v=dUft4FZ40O8) 를 볼 수도 있습니다.

## <a name="how-tagging-works"></a>태깅 작동 방법

Azure Portal 또는 PowerShell을 통해 관리 하는 Azure 서비스에 태그를 지정할 수 있습니다. 태그는 텍스트의 키-값 쌍으로 표시 됩니다. 태그가 지정 된 Azure 리소스를 사용 하는 경우 연결 된 태그 키-값 쌍이 리소스 사용에 연결 됩니다.

배포에서 [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md)에 태그가 지정 된 사용 정보를 보고 하면 태그 구조를 사용 하 여 비용 데이터를 필터링 할 수 있습니다. Azure Cost Management 태그를 기준으로 필터링 하는 방법에 대 한 자세한 내용은 [빠른 시작: 비용 분석을 사용 하 여 비용 탐색 및 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md)을 참조 하세요.

### <a name="add-edit-or-delete-tags"></a>태그 추가, 편집 또는 삭제

리소스에 새 태그를 적용 하면 연결 된 Azure 리소스에서 활동을 보고 하기 전까지 Azure Cost Management에 표시 되지 않습니다. 리소스에 기존 태그를 적용 하는 경우 Azure 리소스에서 활동을 보고 하기 전까지 Azure Cost Management에도이 변경 내용이 표시 되지 않습니다.

태그 이름을 편집 하면 연결 된 리소스가 새 키-값 쌍과 비용을 연결 하 게 됩니다. 여전히 이전 태그를 사용 하 여 데이터를 필터링 할 수 있지만 변경 후의 모든 새 데이터는 새 태그로 보고 됩니다.

태그를 삭제 하는 경우 Azure 가상 데스크톱은 더 이상 Azure Cost Management 삭제 된 태그와 연결 된 데이터를 보고 하지 않습니다. 태그를 삭제 하기 전에 보고 된 데이터에 대해 삭제 된 태그를 사용 하 여 필터링 할 수 있습니다.

>[!IMPORTANT]
>태그가 지정 된 Azure 리소스는 새 태그 또는 업데이트 된 태그를 적용 한 이후 활성화 되지 않았기 때문에 변경 된 태그와 관련 된 활동을 Azure Cost Management에 보고 하지 않습니다. 연결 된 활동이 서비스에서 Azure Cost Management 보고 될 때까지 특정 태그를 필터링 할 수 없습니다.

### <a name="view-all-existing-tags"></a>모든 기존 태그 보기

Azure Portal로 이동한 다음 [ **태그** 탭](https://ms.portal.azure.com/#blade/HubsExtension/TagsBlade)을 열어 Azure 서비스에 대 한 모든 기존 태그를 볼 수 있습니다. 태그 탭에는 사용자가 액세스할 수 있는 개체의 모든 태그가 표시 됩니다. 또한 많은 수의 태그를 동시에 신속 하 게 업데이트 해야 할 때마다 해당 키 또는 값을 기준으로 태그를 정렬할 수 있습니다.

### <a name="what-tags-can-and-cant-do"></a>어떤 태그가 가능 하 고 수행할 수 없음

태그는 직접 할당 된 Azure 리소스에 대 한 사용 현황 및 비용 데이터만 보고 합니다. 다른 리소스에 태그를 지정 하지 않고 리소스에 태그를 지정한 경우 Azure 가상 데스크톱은 최상위 태그가 지정 된 리소스와 관련 된 활동만 보고 합니다. 또한 청구 데이터를 정확 하 게 하려면 해당 최상위 리소스 아래의 모든 리소스에 태그를 적용 해야 합니다.

Azure Cost Management에서 태그의 작동 방식에 대 한 자세한 내용은 [비용 및 사용 현황 데이터에서 태그를 사용 하는 방법](../cost-management-billing/costs/understand-cost-mgt-data.md#how-tags-are-used-in-cost-and-usage-data)을 참조 하세요.

알려진 Azure 태그 제한 목록은 [태그를 사용 하 여 azure 리소스 및 관리 계층 구조 구성](../azure-resource-manager/management/tag-resources.md#limitations)을 참조 하세요.

## <a name="using-tags-in-azure-virtual-desktop"></a>Azure 가상 데스크톱에서 태그 사용

이제 azure 태그의 기본 사항을 이해 했으므로 Azure 가상 데스크톱에서 사용할 수 있는 방법을 살펴보겠습니다.

Azure 태그를 사용 하 여 고객과 사용자에 게 가상화 된 환경을 만들고, 관리 하 고, 배포 하는 비용을 구성할 수 있습니다. 태그를 지정 하면 azure 가상 데스크톱 및 Azure 가상 데스크톱 배포에 연결 된 기타 Azure 서비스를 통해 직접 구매한 리소스를 추적할 수도 있습니다.

## <a name="suggested-tags-for-azure-virtual-desktop"></a>Azure 가상 데스크톱에 대 한 제안 된 태그

Azure 가상 데스크톱은 다른 Azure 서비스와 함께 작동 하 여 해당 배포를 지원할 수 있으므로 배포 리소스에 태그를 지정 하기 위한 범용 시스템이 없습니다. 가장 중요 한 것은 사용자와 조직에서 작동 하는 전략을 개발 하는 것입니다. 그러나 특히 Azure를 처음 사용 하는 경우 도움이 될 수 있는 몇 가지 제안이 있습니다. 

다음 제안 사항은 모든 Azure 가상 데스크톱 배포에 적용 됩니다.

- 태그를 지정할 수 있는 범위를 이해 하기 위해 구매한 Azure 서비스에 익숙해져야 합니다. Azure Portal 사용 방법에 대 한 자세한 내용은 태그를 적용할 수 있는 서비스 그룹 및 개체 목록을 보관 합니다. 리소스 그룹, 가상 머신, 디스크 및 Nic (네트워크 인터페이스 카드)를 추적 해야 하는 일부 리소스에는 태그를 지정할 수 있는 비용 생성 서비스 구성 요소에 대 한 포괄적인 목록은 [총 Azure 가상 데스크톱 배포 비용 이해](./remote-app-streaming/total-costs.md)를 참조 하세요.

- 태그를 구성 하는 비용 보고 집계를 만듭니다. [일반적인 태깅 패턴을 따르거나](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging) 조직의 요구 사항을 충족 하는 새 패턴을 만들 수 있습니다.

- 태그를 적용 하는 모든 위치에서 태그를 일관 되 게 유지 합니다. 가장 작은 철자가 라도 데이터 보고에 영향을 줄 수 있으므로 나중에 조회할 정확한 키-값 쌍을 추가 해야 합니다.

- 업데이트 하거나 편집 하는 태그의 레코드를 유지 합니다. 이 레코드를 사용 하면 필요에 따라 각 태그의 기록 데이터를 결합할 수 있습니다. 태그를 편집 하거나 업데이트 하는 경우 변경 된 태그를 포함 하는 모든 리소스에도 해당 변경 내용을 적용 해야 합니다.

## <a name="suggested-tags-for-azure-virtual-desktop-host-pools"></a>Azure 가상 데스크톱 호스트 풀의 제안 된 태그

Azure 가상 데스크톱 호스트 풀의 모든 가상 컴퓨터는 비용 생성 구문을 만듭니다. 호스트 풀은 Azure 가상 데스크톱 배포의 기반 이므로 Vm은 일반적으로 Azure 가상 데스크톱 배포에 대 한 기본 비용의 원본입니다. 태깅 시스템을 설정 하려면 VM 계산 비용을 추적 하기 위해 배포에 포함 된 모든 호스트 풀에 태그를 지정 하는 것으로 시작 하는 것이 좋습니다. 호스트 풀에 태그를 지정 하면 Azure Cost Management에서 필터링을 사용 하 여 이러한 VM 비용을 식별 하는 데 도움이 됩니다.

[일반적인 제안과](#suggested-tags-for-azure-virtual-desktop)마찬가지로 호스트 풀에 태그를 지정 하기 위한 범용 시스템은 없습니다. 그러나 호스트 풀 태그를 구성 하는 데 도움이 되는 몇 가지 제안이 있습니다.

- 호스트 풀을 만드는 동안 태그를 지정 하는 것은 선택 사항 이지만 생성 프로세스 중에 태그를 지정 하면 나중에 Azure Cost Management에서 태그가 지정 된 모든 사용 현황을 쉽게 볼 수 있습니다. 호스트 풀 태그는 호스트 풀 내에서 세션 호스트의 모든 비용 생성 구성 요소를 따릅니다. [총 Azure 가상 데스크톱 배포 비용을 파악](./remote-app-streaming/total-costs.md)하 여 세션 호스트 관련 비용에 대해 자세히 알아보세요.

- Azure Portal를 사용 하 여 새 호스트 풀을 만드는 경우 만들기 워크플로는 기존 태그를 추가할 수 있는 기회를 제공 합니다. 이러한 태그는 호스트 풀을 만드는 동안 만드는 모든 리소스와 함께 전달 됩니다. 태그는 Azure Portal의 기존 호스트 풀에 추가 하는 모든 세션 호스트에도 적용 됩니다. 그러나 새 세션 호스트를 추가할 때마다 태그를 수동으로 입력 해야 합니다.

- 모든 지원 되는 Azure 서비스에 대 한 완전 한 비용 보고서는 모든 고객에 게 무제한으로 구성 되므로 호스트 풀을 사용 하 여 작업 하는 것은 거의 없습니다. Azure 가상 데스크톱 배포와 연결 된 모든 Azure 서비스에 대 한 비용을 얼마나 철저히 추적할 것인지 결정 해야 합니다. 태그를 지정 하 여 이러한 비용을 보다 철저 하 게 추적할 수 있습니다. 월별 Azure 가상 데스크톱 비용 보고서의 정확도가 향상 됩니다.

- 호스트 풀 주위에 태깅 시스템을 빌드하는 경우 나중에 다른 Azure 서비스에 추가 하는 데 적합 한 키-값 쌍을 사용 해야 합니다.

## <a name="suggested-tags-for-other-azure-virtual-desktop-resources"></a>다른 Azure 가상 데스크톱 리소스에 대 한 제안 된 태그

대부분의 Azure Virtual Desktop 고객은 다른 Azure 서비스를 배포 하 여 배포를 지원 합니다. 비용 보고서에 이러한 추가 서비스의 비용을 포함 하려는 경우 다음 제안 사항을 고려해 야 합니다.

- Azure 가상 데스크톱 배포에 통합 하려는 Azure 서비스 또는 리소스를 이미 구매한 경우 다음과 같은 두 가지 옵션을 사용할 수 있습니다.
   
   - 구매한 Azure 서비스를 서로 다른 Azure 구독 간에 분리 합니다.
   - 동일한 구독에서 구매한 모든 Azure 서비스를 Azure 가상 데스크톱 태그와 결합 합니다.

   서비스를 분리 하면 각 서비스에 대 한 비용을 명확 하 게 파악할 수 있지만 결국에는 비용이 더 많이 들 수 있습니다. 이러한 서비스에 대 한 추가 저장소를 구입 하 여 Azure 가상 데스크톱에 지정 된 저장소가 있는지 확인 해야 할 수 있습니다. 

   구매한 서비스를 결합 하는 것은 비용이 저렴 하지만, 공유 리소스에 대 한 사용 데이터가 정확 하지 않기 때문에 비용 보고서를 확장할 수 있습니다. 정확성을 높이기 위해 리소스에 여러 태그를 추가 하 여 다양 한 요인을 추적 하는 필터를 통해 공유 비용을 볼 수 있습니다.

- 다른 Azure 서비스를 사용 하 여 태깅 시스템 빌드를 시작한 경우에는 사용자가 만든 키-값 쌍을 Azure 가상 데스크톱 배포 또는 다른 서비스에 나중에 적용할 수 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

일반적인 Azure 가상 데스크톱 관련 비용에 대해 자세히 알아보려면 [총 Azure 가상 데스크톱 배포 비용 이해](./remote-app-streaming/total-costs.md)를 참조 하세요.

Azure 태그에 대해 자세히 알아보려면 다음 리소스를 확인 하세요.

- [태그를 사용하여 Azure 리소스 및 관리 계층 구조 구성](../azure-resource-manager/management/tag-resources.md)

- [Azure 태그를 사용 하는 값을 설명 하는 비디오](https://www.youtube.com/watch?v=dUft4FZ40O8)

- [비용 및 사용량 데이터에서 태그를 사용하는 방법](../cost-management-billing/costs/understand-cost-mgt-data.md#how-tags-are-used-in-cost-and-usage-data)

- [Azure 리소스에 대 한 명명 및 태깅 전략 개발](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

- [태그 지정 전략 정의](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging)

- [리소스 명명 및 태그 지정 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/resource-tagging)

Azure Cost Management에 대해 자세히 알아보려면 다음 문서를 확인 하세요.

- [Azure Cost Management + 청구란?](../cost-management-billing/cost-management-billing-overview.md)

- [빠른 시작: 비용 분석을 사용하여 비용 탐색 및 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md)
