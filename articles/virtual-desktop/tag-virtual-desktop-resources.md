---
title: Azure Virtual Desktop 리소스 태그 - Azure
description: 태그 지정이란 무엇이며, 이를 사용하여 Azure Virtual Desktop에서 Azure 서비스 비용을 관리하는 방법입니다.
author: heidilohr
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1c77e7b2ec767cd843cbf34b7f3a44d074d20ab5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404679"
---
# <a name="tag-azure-virtual-desktop-resources-to-manage-costs"></a>비용을 관리하기 위해 Azure Virtual Desktop 리소스에 태그를 추가합니다.

태그 지정은 Azure 구독 내에서 리소스를 구성하는 데 도움이 되는 Azure 서비스에서 사용할 수 있는 도구입니다. 리소스를 구성하면 여러 서비스에서 비용을 더 쉽게 추적할 수 있습니다. 또한 태그는 청구 주기당 Azure 리소스의 각 그룹화 비용을 이해하는 데 도움이 됩니다. 일반적으로 태그 지정에 대해 자세히 알아보려면 [태그를 사용하여 Azure 리소스 및 관리 계층 구조 구성을 참조하세요.](../azure-resource-manager/management/tag-resources.md) Azure 태그를 사용하는 몇 가지 다른 방법에 대한 [빠른 비디오를](https://www.youtube.com/watch?v=dUft4FZ40O8) 시청할 수도 있습니다.

## <a name="how-tagging-works"></a>태그 지정의 작동 방식

Azure Portal 또는 PowerShell을 통해 관리하는 Azure 서비스에 태그를 지정할 수 있습니다. 태그는 텍스트의 키-값 쌍으로 표시됩니다. 태그가 지정된 Azure 리소스를 사용하면 연결된 태그 키-값 쌍이 리소스 사용에 연결됩니다.

배포에서 태그가 지정된 사용량 정보를 [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md)보고하면 태그 지정 구조를 사용하여 비용 데이터를 필터링할 수 있습니다. Azure Cost Management 태그로 필터링하는 방법을 알아보려면 빠른 [시작: 비용 분석을 사용하여 비용 탐색 및 분석을 참조하세요.](../cost-management-billing/costs/quick-acm-cost-analysis.md)

### <a name="add-edit-or-delete-tags"></a>태그 추가, 편집 또는 삭제

리소스에 새 태그를 적용하면 연결된 Azure 리소스가 활동을 보고할 때까지 Azure Cost Management 표시되지 않습니다. 리소스에 기존 태그를 적용하는 경우 Azure 리소스가 작업을 보고할 때까지 이 변경 내용은 Azure Cost Management 표시되지 않습니다.

태그 이름을 편집하는 경우 연결된 리소스는 이제 비용을 새 키-값 쌍과 연결합니다. 이전 태그를 통해 데이터를 필터링할 수 있지만 변경 후의 모든 새 데이터는 새 태그를 통해 보고됩니다.

태그를 삭제하면 Azure Virtual Desktop은 삭제된 태그와 연결된 데이터를 더 이상 Azure Cost Management 보고하지 않습니다. 태그를 삭제하기 전에 보고된 데이터에 대해 삭제된 태그로 필터링할 수 있습니다.

>[!IMPORTANT]
>태그를 새로 추가하거나 업데이트한 이후에 활성화되지 않은 태그가 지정된 Azure 리소스는 변경된 태그와 연결된 활동을 Azure Cost Management 보고하지 않습니다. 관련 활동이 서비스에서 Azure Cost Management 보고될 때까지 특정 태그를 필터링할 수 없습니다.

### <a name="view-all-existing-tags"></a>모든 기존 태그 보기

Azure Portal 클릭한 다음 태그 탭을 열어 Azure 서비스에 대한 모든 기존 태그를 볼 수 [ **있습니다.**](https://ms.portal.azure.com/#blade/HubsExtension/TagsBlade) 태그 탭에는 액세스할 수 있는 개체의 모든 태그가 표시됩니다. 많은 수의 태그를 동시에 빠르게 업데이트해야 할 때마다 키 또는 값으로 태그를 정렬할 수도 있습니다.

### <a name="what-tags-can-and-cant-do"></a>태그가 수행할 수 있는 것과 수행할 수 없는 일

태그는 직접 할당된 Azure 리소스에 대한 사용량 및 비용 데이터만 보고합니다. 다른 리소스에 태그를 지정하지 않고 리소스에 태그를 지정한 경우 Azure Virtual Desktop은 최상위 태그가 지정된 리소스와 관련된 활동만 보고합니다. 또한 청구 데이터를 정확하게 유지하려면 해당 최상위 리소스 아래의 모든 리소스에 태그를 추가해야 합니다.

Azure Cost Management 태그가 작동하는 방식에 대한 자세한 내용은 [비용 및 사용량 현황 데이터에 태그를 사용하는 방법을 참조하세요.](../cost-management-billing/costs/understand-cost-mgt-data.md#how-tags-are-used-in-cost-and-usage-data)

알려진 Azure 태그 제한 사항 목록은 [태그를 사용하여 Azure 리소스 및 관리 계층 구조 구성을 참조하세요.](../azure-resource-manager/management/tag-resources.md#limitations)

## <a name="using-tags-in-azure-virtual-desktop"></a>Azure Virtual Desktop에서 태그 사용

이제 Azure 태그의 기본을 이해하게 됐으므로 Azure Virtual Desktop에서 사용하는 방법을 살펴보겠습니다.

Azure 태그를 사용하여 고객과 사용자를 위한 가상화된 환경을 만들고, 관리하고, 배포하는 비용을 구성할 수 있습니다. 태그 지정은 Azure Virtual Desktop 및 Azure Virtual Desktop 배포에 연결된 다른 Azure 서비스를 통해 직접 구매하는 리소스를 추적하는 데 도움이 될 수도 있습니다.

## <a name="suggested-tags-for-azure-virtual-desktop"></a>Azure Virtual Desktop에 대해 제안된 태그

Azure Virtual Desktop은 다른 Azure 서비스와 함께 배포를 지원할 수 있으므로 배포 리소스에 태그를 지정하는 범용 시스템은 없습니다. 가장 중요한 것은 사용자와 조직에 적합한 전략을 개발한다는 것입니다. 그러나 특히 Azure를 사용하는 경우 도움이 될 수 있는 몇 가지 제안이 있습니다. 

다음 제안은 모든 Azure Virtual Desktop 배포에 적용됩니다.

- 태그를 만들려는 범위를 이해할 수 있도록 구매한 Azure 서비스에 익숙해집니다. Azure Portal 사용하는 방법을 알아보면 태그를 적용할 수 있는 서비스 그룹 및 개체 목록을 유지합니다. 추적해야 하는 일부 리소스에는 리소스 그룹, 가상 머신, 디스크 및 NIC(네트워크 인터페이스 카드)가 포함됩니다. 태그를 지정할 수 있는 비용 생성 서비스 구성 요소의 보다 포괄적인 목록은 [총 Azure Virtual Desktop 배포 비용 이해를](./remote-app-streaming/total-costs.md)참조하세요.

- 비용 보고 집계를 만들어 태그를 구성합니다. [일반적인 태그 지정 패턴을 따르거나](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging.md) 조직의 요구 사항을 충족하는 새 패턴을 만들 수 있습니다.

- 태그를 적용할 때마다 태그를 일관되게 유지합니다. 가장 작은 오타도 데이터 보고에 영향을 줄 수 있으므로 나중에 조회하려는 정확한 키-값 쌍을 추가해야 합니다.

- 업데이트하거나 편집하는 태그의 레코드를 유지합니다. 이 레코드를 사용하면 필요에 따라 각 태그의 기록 데이터를 결합할 수 있습니다. 태그를 편집하거나 업데이트할 때 변경된 태그를 포함하는 모든 리소스에 이러한 변경 내용을 적용해야 합니다.

## <a name="suggested-tags-for-azure-virtual-desktop-host-pools"></a>Azure Virtual Desktop 호스트 풀에 대해 제안된 태그

Azure Virtual Desktop 호스트 풀의 모든 가상 머신은 비용 생성 구문을 만듭니다. 호스트 풀은 Azure Virtual Desktop 배포의 기초이므로 VM은 일반적으로 Azure Virtual Desktop 배포에 대한 주요 비용 원본입니다. 태그 지정 시스템을 설정하려면 배포의 모든 호스트 풀에 태그를 지정하여 VM 컴퓨팅 비용을 추적하는 것이 좋습니다. 호스트 풀에 태그를 지정하면 Azure Cost Management 필터링을 사용하여 이러한 VM 비용을 식별할 수 있습니다.

[일반적인 제안과](#suggested-tags-for-azure-virtual-desktop)마찬가지로 호스트 풀에 태그를 지정하는 범용 시스템은 없습니다. 그러나 호스트 풀 태그를 구성하는 데 도움이 되는 몇 가지 제안이 있습니다.

- 호스트 풀을 만드는 동안 태그를 지정하는 것은 선택 사항이지만 생성 프로세스 중에 태그를 지정하면 나중에 Azure Cost Management 태그가 지정된 모든 사용량을 더 쉽게 볼 수 있습니다. 호스트 풀 태그는 호스트 풀 내에서 세션 호스트의 비용 생성 구성 요소를 모두 따릅니다. 총 Azure Virtual Desktop 배포 비용 [이해에서](./remote-app-streaming/total-costs.md)세션 호스트 관련 비용에 대해 자세히 알아보세요.

- Azure Portal 사용하여 새 호스트 풀을 만드는 경우 만들기 워크플로를 통해 기존 태그를 추가할 수 있습니다. 이러한 태그는 호스트 풀 생성 프로세스 중에 만드는 모든 리소스에 전달됩니다. 태그는 Azure Portal 기존 호스트 풀에 추가하는 모든 세션 호스트에도 적용됩니다. 그러나 새 세션 호스트를 추가할 때마다 태그를 수동으로 입력해야 합니다.

- 구성 옵션은 제한이 없고 각 고객에게 고유하기 때문에 호스트 풀을 사용하여 작업하는 모든 지원 Azure 서비스에 대한 전체 비용 보고서를 얻을 가능성은 거의 없습니다. Azure Virtual Desktop 배포와 연결된 모든 Azure 서비스에서 비용을 얼마나 근접하게 추적할 것인지는 사용자가 결정해야 합니다. 태그를 지정하여 이러한 비용을 더 철저하게 추적할수록 월별 Azure Virtual Desktop 비용 보고서가 더 정확해집니다.

- 호스트 풀 주위에 태그 지정 시스템을 빌드하는 경우 나중에 다른 Azure 서비스에 추가하는 것이 적합한 키-값 쌍을 사용해야 합니다.

## <a name="suggested-tags-for-other-azure-virtual-desktop-resources"></a>다른 Azure Virtual Desktop 리소스에 대해 제안된 태그

대부분의 Azure Virtual Desktop 고객은 배포를 지원하기 위해 다른 Azure 서비스를 배포합니다. 비용 보고서에 이러한 추가 서비스의 비용을 포함하려면 다음 제안을 고려해야 합니다.

- Azure Virtual Desktop 배포에 통합하려는 Azure 서비스 또는 리소스를 이미 구매한 경우 다음 두 가지 옵션이 있습니다.
   
   - 구매한 Azure 서비스를 서로 다른 Azure 구독 간에 구분합니다.
   - 동일한 구독에서 구매한 모든 Azure 서비스를 Azure Virtual Desktop 태그와 결합합니다.

   서비스를 분리하면 각 서비스에 대한 비용을 보다 명확하게 파악할 수 있지만 결국 비용이 더 많이 들 수 있습니다. Azure Virtual Desktop에 지정된 자체 스토리지가 있는지 확인하려면 이러한 서비스에 대한 추가 스토리지를 구입해야 할 수 있습니다. 

   구매한 서비스를 결합하는 것은 비용이 적게 들지만 공유 리소스에 대한 사용량 현황 데이터가 정확하지 않기 때문에 비용 보고서가 증가될 수 있습니다. 정확도의 부족을 확인하기 위해 리소스에 여러 태그를 추가하여 다양한 요인을 추적하는 필터를 통해 공유 비용을 확인할 수 있습니다.

- 다른 Azure 서비스를 사용하여 태그 지정 시스템을 빌드하기 시작한 경우 만든 키-값 쌍을 나중에 Azure Virtual Desktop 배포 또는 다른 서비스에 적용할 수 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

일반적인 Azure Virtual Desktop 관련 비용에 대해 자세히 알아보려면 [총 Azure Virtual Desktop 배포 비용 이해를](./remote-app-streaming/total-costs.md)확인하세요.

Azure 태그에 대해 자세히 알아보려면 다음 리소스를 확인하세요.

- [태그를 사용하여 Azure 리소스 및 관리 계층 구조 구성](../azure-resource-manager/management/tag-resources.md)

- [Azure 태그 사용의 가치를 설명하는 비디오](https://www.youtube.com/watch?v=dUft4FZ40O8)

- [비용 및 사용량 데이터에서 태그를 사용하는 방법](../cost-management-billing/costs/understand-cost-mgt-data.md#how-tags-are-used-in-cost-and-usage-data)

- [Azure 리소스에 대한 명명 및 태그 지정 전략 개발](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

- [태그 지정 전략 정의](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging)

- [리소스 명명 및 태그 지정 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/resource-tagging)

Azure Cost Management 대해 자세히 알아보려면 다음 문서를 확인하세요.

- [Azure Cost Management + 청구란?](../cost-management-billing/cost-management-billing-overview.md)

- [빠른 시작: 비용 분석을 사용하여 비용 탐색 및 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md)
