---
title: 고가용성 및 재해 복구
titleSuffix: Azure Digital Twins
description: 재해 복구 기능을 사용하여 고가용성 Azure IoT 솔루션을 빌드할 수 있도록 지원하는 Azure Digital Twins 기능을 설명합니다.
author: baanders
ms.author: baanders
ms.date: 9/16/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d021bc7643cfab1bbe805b424623c4ba129f6b1e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642817"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital Twins 고가용성 및 재해 복구

복원 IoT 솔루션 고려 사항의 핵심 영역은 비즈니스 연속성 및 재해 복구입니다. **HA(고가용성)** 및 **DR(재해 복구)** 을 위한 설계를 통해 솔루션에 대한 적절한 가동 시간 목표를 정의하고 달성할 수 있습니다.

이 문서에서는 특히 Azure Digital Twins 서비스에서 제공하는 HA 및 DR 기능을 설명합니다.

Azure Digital Twins는 다음과 같은 기능 옵션을 지원합니다.
* *지역 내 HA* – 서비스의 작동 시간을 제공하는 기본 제공 중복성
* 지역 간 DR – 예기치 않은 데이터 센터 오류가 발생하는 경우 지리적으로 쌍을 이루는 Azure 지역으로 장애 조치(Failover)

[모범 사례](#best-practices) 섹션에서도 HA/DR을 위한 설계에 대한 일반적인 Azure 지침을 확인할 수 있습니다.

## <a name="intra-region-ha"></a>역내 HA
 
Azure Digital Twins는 서비스 내에서 중복을 구현하여 지역 내 HA를 제공합니다. 이 기능은 작동 시간에 대한 [서비스 SLA](https://azure.microsoft.com/support/legal/sla/digital-twins)에 반영됩니다. **Azure Digital Twins 솔루션 개발자는 추가적인 작업 없이 이러한 HA 기능을 활용할 수 있습니다.** Azure Digital Twins는 비교적 높은 가동 시간을 보장하지만 분산 컴퓨팅 플랫폼에서와 마찬가지로 일시적인 오류는 여전히 발생합니다. 일시적 오류 처리를 위해 클라우드 애플리케이션과 상호 작용하는 구성 요소에 적절한 다시 시도 정책을 구축해야 합니다.

## <a name="cross-region-dr"></a>지역 간 DR

데이터 센터에서 정전 또는 기타 지역 이벤트로 인해 중단 시간이 늘어나는 경우도 드물지만 있을 수 있습니다. 이러한 이벤트는 드물며, 위에서 설명한 지역 내 HA 기능이 도움이 되지 못할 수도 있습니다. Azure Digital Twins는 Microsoft에서 시작한 장애 조치(failover)를 통해 이 시나리오를 해결합니다.

**Microsoft 시작 장애 조치(failover)** 는 해당 지역의 모든 Azure Digital Twins 인스턴스를 상응하는 [지역 쌍 지역](../best-practices-availability-paired-regions.md)으로 장애 조치(failover)하는 드문 상황에서 실행됩니다. 이 프로세스는 기본 옵션(사용자가 옵트아웃할 수 없음)이며 사용자 개입이 필요하지 않습니다. Microsoft는 이 옵션을 실행할 시기를 판단할 권리를 보유합니다. 이 메커니즘에는 사용자의 인스턴스가 장애 조치(failover)되기 전 사용자 동의가 포함되지 않습니다.

>[!NOTE]
> 일부 Azure 서비스는 **고객이 시작한 장애 조치(failover)** 라는 추가 옵션을 제공하여 고객이 DR 드릴 실행과 같은 인스턴스에 대해서만 장애 조치(failover)를 시작할 수 있도록 합니다. 이 메커니즘은 현재 Azure Digital Twins에서 **지원되지 않습니다**. 

모든 데이터를 특정 지리적 영역 내에 유지하는 것이 중요한 경우 인스턴스를 만드는 지역의 [지역 쌍 지역](../best-practices-availability-paired-regions.md#azure-regional-pairs) 위치를 확인하여 데이터 상주 요구 사항을 충족하는지 확인하세요.

>[!NOTE]
> 일부 Azure 서비스는 사용자가 데이터 상주 요구 사항을 충족하는 방법으로 장애 조치(failover)를 위해 다른 지역을 구성할 수 있는 옵션을 제공합니다. 이 기능은 현재 Azure Digital Twins에서 **지원되지 않습니다**. 

## <a name="monitor-service-health"></a>서비스 상태 모니터링

Azure Digital Twins 인스턴스가 장애 조치(failover)되고 복구되면 [Azure Service Health](../service-health/service-health-overview.md) 도구를 사용하여 프로세스를 모니터링할 수 있습니다. Service Health는 서로 다른 지역 및 구독에서 Azure 서비스의 상태를 추적하고 중단 및 가동 중지 시간에 대해 서비스에 영향을 주는 통신을 공유합니다.

장애 조치(failover) 이벤트 중 Service Health는 서비스가 다운된 시기와 백업 시점을 표시할 수 있습니다.

Service Health 이벤트를 보려면...
1. Azure Portal의 [Service Health](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues)로 이동합니다(이 링크를 사용하거나 포털 검색 창을 사용하여 검색할 수 있음).
1. 왼쪽 메뉴를 사용하여 *상태 기록* 페이지로 전환합니다.
1. **Azure Digital Twins** 로 시작하는 *문제 이름* 을 찾아 선택합니다.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="'상태 기록' 페이지를 보여주는 Azure Portal의 스크린샷. 'Azure Digital Twins - West Europe - Mitigated'라는 문제가 강조 표시되어 있습니다." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. 작동 중단에 대한 일반 정보를 보려면 *요약* 탭을 확인합니다.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="'요약' 탭이 강조 표시된 '상태 기록' 페이지를 보여주는 Azure Portal의 스크린샷. 탭에는 일반 정보가 표시됩니다." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. 시간에 따른 문제에 대한 자세한 내용 및 업데이트는 *문제 업데이트* 탭을 참조하세요.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="'문제 업데이트' 탭이 강조 표시된 '상태 기록' 페이지를 보여주는 Azure Portal 스크린샷. 탭에는 항목의 상태가 표시됩니다." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


이 도구에 표시된 정보는 하나의 Azure 디지털 인스턴스에만 국한되지 않습니다. Service Health를 사용하여 특정 지역 또는 구독에서 Azure Digital Twins 서비스의 진행 상황을 이해한 후에는 [Resource Health 도구](troubleshoot-resource-health.md)를 사용하여 특정 인스턴스로 드릴다운하고 영향을 받는지 여부를 확인하여 단계를 추가로 모니터링할 수 있습니다.

## <a name="best-practices"></a>모범 사례

HA/DR에 대한 모범 사례는 이 항목에 대한 다음 Azure 지침을 참조하세요. 
* [신뢰할 수 있는 Azure 애플리케이션 디자인](/azure/architecture/framework/resiliency/app-design) 문서에서는 비즈니스 연속성과 재해 복구에 대해 고민해 볼 수 있는 일반 프레임워크를 설명합니다. 
* [Azure 애플리케이션에 대한 재해 복구 및 고가용성](/azure/architecture/framework/resiliency/backup-and-recovery) 문서는 Azure 애플리케이션에서 HA(고가용성) 및 DR(재해 복구)을 수행하는 전략에 대한 아키텍처 지침을 제공합니다.

## <a name="next-steps"></a>다음 단계 

Azure Digital Twins 솔루션 시작하기에 대해 자세히 알아봅니다.
 
* [Azure Digital Twins란?](overview.md)
* [Azure Digital Twins Explorer 시작](quickstart-azure-digital-twins-explorer.md)
