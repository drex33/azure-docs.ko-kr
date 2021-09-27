---
title: Advisor로 운영 우수성 개선
description: Azure Advisor를 사용하여 Azure 구독의 운영 우수성을 최적화하고 완성합니다.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 151edab6c389e32abaaa73fec3012fa3bdcdf75a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650423"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Azure Advisor를 사용하여 운영 우수성 달성

Azure Advisor의 운영 우수성 권장 사항은 다음에 도움이 될 수 있습니다. 
- 프로세스 및 워크플로 효율성
- 리소스 관리 효율성
- 배포 모범 사례 

이러한 권장 사항은 Advisor 대시보드의 **운영 우수성** 탭에서 가져올 수 있습니다.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Azure 문제가 영향을 미칠 때 알림을 받도록 Azure Service Health 경고 만들기

Azure 서비스 문제가 영향을 미칠 때 알림을 받을 수 있도록 Azure Service Health 경고를 설정하는 것이 좋습니다. [Azure Service Health](https://azure.microsoft.com/features/service-health/)는 Azure 서비스 문제가 영향을 미칠 때 개인화된 지침과 지원을 제공하는 무료 서비스입니다. Advisor는 경고가 구성되지 않은 구독을 식별하고 구독을 구성하도록 권장합니다.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>최대 구독 한도에 도달하지 않도록 스토리지 계정 설계

Azure 지역 하나는 구독당 최대 250개의 스토리지 계정을 지원할 수 있습니다. 이 한도에 도달하면 해당 지역/구독 조합에서 스토리지 계정을 만들 수 없습니다. Advisor는 구독을 확인하고 최대 한도에 근접한 지역/구독의 스토리지 계정 수를 줄여 설계하도록 권장 사항을 제공합니다.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>필요할 때 Azure 클라우드 전문가에 대한 액세스 권한이 있는지 확인

중요 비즈니스용 워크로드를 실행하는 경우 필요할 때 기술 지원에 액세스할 수 있어야 합니다. Advisor는 지원 플랜에 기술 지원이 포함되지 않은 잠재적인 중요 비즈니스용 구독을 식별합니다. 기술 지원이 포함된 옵션으로 업그레이드하는 것이 좋습니다.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>풀을 삭제하고 다시 만들어 사용되지 않는 내부 구성 요소 제거

사용되지 않는 내부 구성 요소가 풀에서 사용 중인 경우 안정성 및 성능 향상을 위해 풀을 삭제하고 다시 만듭니다.

## <a name="repair-invalid-log-alert-rules"></a>잘못된 로그 경고 규칙 복구

Azure Advisor는 조건 섹션에 잘못된 쿼리가 지정된 경고 규칙을 검색합니다. Azure Monitor에서 로그 경고 규칙을 만들어 지정된 간격으로 분석 쿼리를 실행하는 데 사용할 수 있습니다. 쿼리 결과에 따라 경고를 트리거할 것인지 여부가 결정됩니다. 시간이 지나면서 참조되는 리소스, 테이블 또는 명령의 변경 때문에 분석 쿼리가 잘못될 수 있습니다. Advisor는 경고 규칙이 자동으로 사용 중지되지 않도록 경고 규칙의 쿼리를 수정하고, Azure의 리소스 범위를 모니터링하도록 권장합니다. [경고 규칙 문제 해결에 대해 자세히 알아보세요.](../azure-monitor/alerts/alerts-troubleshoot-log.md)

## <a name="use-azure-policy-recommendations"></a>Azure Policy 권장 사항 사용

Azure Policy는 정책을 만들고, 할당하고, 관리하는 데 사용할 수 있는 Azure의 서비스입니다. 이러한 정책은 리소스에 대해 규칙과 효과를 적용합니다. 다음 Azure Policy 권장 사항은 운영 우수성을 달성하는 데 도움이 될 수 있습니다. 

**태그를 관리합니다.** 이 정책은 리소스를 만들거나 업데이트할 때 지정된 태그 및 값을 추가하거나 바꿉니다. 수정 작업을 트리거하여 기존 리소스를 수정할 수 있습니다. 이 정책은 리소스 그룹에 대한 태그를 수정하지 않습니다.

**지역 규정 준수 요구 사항을 적용합니다.** 이 정책을 사용하면 조직에서 리소스를 배포할 때 지정할 수 있는 위치를 제한할 수 있습니다. 

**배포에 허용되는 가상 머신 SKU를 지정합니다.** 이 정책을 통해 조직에서 배포할 수 있는 가상 컴퓨터 SKU 집합을 지정할 수 있습니다.

**관리 디스크를 사용하지 않는 VM 감사를 적용합니다.**

**리소스 그룹에서 태그 상속을 사용하도록 설정합니다.** 이 정책은 리소스를 만들거나 업데이트할 때 상위 리소스 그룹의 지정된 태그 및 값을 추가하거나 바꿉니다. 수정 작업을 트리거하여 기존 리소스를 수정할 수 있습니다.

Advisor는 고객이 모범 사례를 채택하여 운영 우수성을 달성하는 데 도움이 되는 몇 가지 개별 Azure 정책을 권장합니다. 고객이 권장 정책을 할당하기로 결정하면 권장 사항이 표시되지 않습니다. 고객이 나중에 정책을 제거하기로 결정하면 Advisor는 제거를 다음의 강력한 신호로 해석하므로 권장 사항을 계속 표시하지 않습니다.

1.  고객이 Advisor의 권장 사항에도 불구하고 고객의 특정 사용 사례에 해당되지 않기 때문에 정책을 제거했습니다. 
2.  고객이 정책 할당 및 제거 후 정책에 대해 잘 알고 있으며 나중에 사용 사례와 관련이 있는 경우 지침 없이 필요에 따라 다시 할당하거나 제거할 수 있습니다. 동일한 정책을 다시 할당하는 것이 유리하다고 판단하는 고객은 Advisor의 권장 사항이 없어도 Azure Policy에서 정책을 할당할 수 있습니다. 이 논리는 특히 운영 우수성 범주의 정책 권장 사항에 적용됩니다. 이러한 규칙은 보안 권장 사항에는 적용되지 않습니다.  


## <a name="no-validation-environment-enabled"></a>유효성 검사 환경을 사용하지 않음
Azure Advisor에서 현재 구독에 사용하도록 설정된 유효성 검사 환경이 없다고 결정합니다. 호스트 풀을 만들 때 속성 탭에서 \"유효성 검사 환경\"에 대해 \"아니요\"를 선택했습니다. 유효성 검사 환경을 사용하도록 설정한 호스트 풀이 하나 이상 있으면 잠재적인 문제를 조기에 검색하여 Windows Virtual Desktop 서비스 배포를 통해 비즈니스 연속성을 보장합니다. [자세히 알아보기](../virtual-desktop/create-validation-host-pool.md)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>안정적인 기능의 혜택에 대한 프로덕션(유효성 검사 아님) 환경 확인
Azure Advisor에서 유효성 검사 환경을 사용하도록 설정된 호스트 풀이 너무 많다고 검색합니다. 유효성 검사 환경을 용도에 가장 적합하게 사용하려면 유효성 검사 환경에 호스트 풀이 하나 이상 있어야 하지만 호스트 풀 수의 절반을 넘지 않아야 합니다. 유효성 검사 환경을 사용하도록 설정한 호스트 풀과 사용하지 않도록 설정한 호스트 풀 간에 정상적인 균형을 유지하여 Windows Virtual Desktop이 특정 업데이트를 제공하는 다단계 배포의 혜택을 최대한 활용할 수 있습니다. 이 문제를 해결하려면 호스트 풀의 속성을 열고 \"유효성 검사 환경\" 설정 옆의 \"아니요\"를 선택합니다.

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>트래픽 분석을 사용하여 Azure 리소스에서 트래픽 패턴에 대한 인사이트를 볼 수 있음
트래픽 분석은 Azure의 사용자 및 애플리케이션 작업에 대한 가시성을 제공하는 클라우드 기반 솔루션입니다. 트래픽 분석은 NSG(Network Watcher 네트워크 보안 그룹) 흐름 로그를 분석하여 트래픽 흐름에 대한 인사이트를 제공합니다. 트래픽 분석을 사용하면 Azure 및 비 Azure 배포 전반에서 상위 토커를 확인하고, 사용자 환경에서 열린 포트, 프로토콜 및 악성 흐름을 조사하고, 네트워크 배포를 성능에 맞게 최적화할 수 있습니다. 10분 및 60분의 처리 간격으로 흐름 로그를 처리하여 트래픽에 대한 분석을 더 빠르게 할 수 있습니다. Azure 리소스에 트래픽 분석을 사용하도록 설정하는 것이 좋습니다. 

## <a name="increase-vcpu-limits-for-your-deployments-for-pay-as-you-go-subscription-preview"></a>종 량 제 구독에 대 한 배포에 대 한 vCPU 한도 늘리기 (미리 보기)
이 환경은 증가 하는 요구에 도움이 되 고 할당량 제한으로 인 한 배포 문제를 방지 하기 위해 할당량을 늘릴 수 있는 쉬운 방법을 제공 하기 위해 만들어졌습니다. 제한 된 구독에 대해 "빠른 수정" 옵션을 사용 하도록 설정 했습니다 .이 옵션을 사용 하면 한 번의 클릭으로 10에서 20 까지의 vCPUs 할당량을 늘릴 수 있습니다. 이 간소화 된 방법은 할당량을 늘리기 위해 사용자를 대신 하 여 [할당량 REST API](https://techcommunity.microsoft.com/t5/azure-governance-and-management/using-the-new-quota-rest-api/ba-p/2183670) 를 호출 합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 안정성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
