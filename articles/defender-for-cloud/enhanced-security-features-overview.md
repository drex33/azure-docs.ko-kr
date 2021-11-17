---
title: 클라우드용 Microsoft Defender의 향상된 보안 기능 이해
description: 클라우드용 Microsoft Defender에서 향상된 보안을 사용하도록 설정하면 얻을 수 있는 이점에 대해 알아보기
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: overview
ms.date: 11/14/2021
ms.openlocfilehash: 4f17d5228bdac13797aaef0fe4441f61d474042a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556925"
---
# <a name="microsoft-defender-for-clouds-enhanced-security-features"></a>클라우드용 Microsoft Defender의 향상된 보안 기능

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

향상된 보안 기능은 처음 30일 동안 무료로 사용할 수 있습니다. 30일 종료 시 서비스를 계속 사용하기로 선택하는 경우 사용량에 대한 요금이 자동으로 부과되기 시작합니다.

[빠른 시작: Azure Defender 사용](enable-enhanced-security.md)에 설명된 대로 **환경 설정** 페이지에서 업그레이드할 수 있습니다. 선택한 통화와 지역에 따른 가격 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

:::image type="content" source="media/enhanced-security-features-overview/defender-plans-top.png" alt-text="클라우드용 Microsoft Defender의 향상된 보안 기능을 사용합니다.":::

## <a name="what-are-the-benefits-of-enabling-enhanced-security-features"></a>향상된 보안 기능을 사용하면 어떤 이점이 있나요?

Defender for Cloud는 다음 두 가지 모드로 제공됩니다.

- **향상 된 보안 기능** (무료)을 사용 하지 않는 경우, 처음으로 Azure Portal에서 워크 로드 보호 대시보드를 방문 하거나 API를 통해 프로그래밍 방식으로 사용 하도록 설정한 경우 모든 Azure 구독에 대해 Defender for Cloud를 무료로 사용할 수 있습니다. 이 무료 모드에서는 Azure 리소스를 보호하는 데 도움이 되는 보안 점수 및 관련 기능(보안 정책, 지속적인 보안 평가 및 실행 가능한 보안 권장 사항)이 제공됩니다.

- **향상된 보안 기능을 사용한 Defender for Cloud** - 향상된 보안 기능을 사용하면 무료 모드의 기능이 프라이빗 클라우드 및 기타 퍼블릭 클라우드에서 실행되는 작업으로 확장 적용되며, 하이브리드 클라우드 작업 전반에 걸쳐 통합 보안 관리 및 위협 방지 기능이 제공됩니다. 몇 가지 이점은 다음과 같습니다.

    - **엔드포인트용 Microsoft Defender** - 서버용 Microsoft Defender에는 포괄적인 EDR(엔드포인트 검색 및 응답)을 위한 [엔드포인트용 Microsoft Defender](https://www.microsoft.com/microsoft-365/security/endpoint-defender)가 포함되어 있습니다. [Defender for Cloud의 통합 EDR 솔루션 사용](integration-defender-for-endpoint.md)에서 Defender for Cloud와 함께 엔드포인트용 Azure Defender를 사용하는 이점에 대해 자세히 알아봅니다.
    - **가상 컴퓨터, 컨테이너 레지스트리 및 SQL 리소스에 대 한 취약성 평가** -취약성 평가 솔루션을 쉽게 사용 하 여 취약성을 검색, 관리 및 해결할 수 있습니다. 클라우드 용 Defender 내에서 직접 결과를 확인, 조사 및 수정 합니다.
    - **다중 클라우드 보안** - AWS(Amazon Web Services) 및 GCP(Google Cloud Platform)의 계정을 연결하여 다양한 클라우드용 Microsoft Defender 보안 기능으로 해당 플랫폼의 리소스와 워크로드를 보호합니다.
    - **하이브리드 보안** – 모든 온-프레미스 및 클라우드 작업에 걸쳐 보안을 통합 확인할 수 있습니다. 또한 보안 정책을 적용하고 하이브리드 클라우드 작업의 보안을 지속적으로 평가하여 보안 표준을 준수할 수 있습니다. 뿐만 아니라 방화벽 및 기타 파트너 솔루션을 포함한 여러 소스에서 보안 데이터를 수집, 검색 및 분석할 수 있습니다.
    - **위협 방지 경고** - 고급 동작 분석 및 Microsoft Intelligent Security Graph가 갈수록 발전하는 사이버 공격에 효율적으로 대응합니다. 기본 제공 행동 분석 및 기계 학습은 공격 및 제로 데이 익스플로잇을 식별할 수 있습니다. 네트워크, 컴퓨터, 데이터 저장소 SQL (azure, azure SQL 데이터베이스, azure SQL Managed Instance, Azure Storage) 및 클라우드 서비스를 모니터링 하 여 들어오는 공격과 위반 후 활동을 모니터링 합니다. 대화형 도구 및 상황에 맞는 위협 인텔리전스를 사용하면 조사를 손쉽게 수행할 수 있습니다.
    - **다양한 표준으로 규정 준수 추적** - Defender for Cloud는 하이브리드 클라우드 환경을 지속적으로 평가하여 [Azure Security 벤치마크](/security/benchmark/azure/introduction)의 제어와 모범 사례에 따라 위험 요인을 분석합니다. 향상된 보안 기능을 사용하면 조직의 요구 사항에 따라 다양한 업계 표준, 규정 표준 및 벤치마크를 적용할 수 있습니다. 표준을 추가하고 [규정 준수 대시보드](update-regulatory-compliance-packages.md)에서 이러한 표준을 사용하여 규정 준수를 추적합니다.
    - **액세스 및 응용 프로그램 제어** -특정 워크 로드에 맞게 조정 된 machine learning 지원 권장 사항을 적용 하 여 허용 및 차단 목록을 만들어 맬웨어 및 기타 원치 않는 응용 프로그램을 차단 합니다. Azure VM의 관리 포트에 대한 액세스를 JIT(Just-In-Time) 방식으로 제어하여 네트워크 공격 표면을 줄입니다. 액세스 및 애플리케이션 제어는 무차별 암호 대입 및 기타 네트워크 공격에 대한 노출을 크게 줄입니다.
    - **컨테이너 보안 기능** - 컨테이너화된 환경에서 취약성 관리 및 실시간 위협 방지를 활용할 수 있습니다. 요금은 연결된 레지스트리에 푸시된 고유한 컨테이너 이미지 수를 기반으로 청구됩니다. 한 번 검사한 이미지는 수정하여 한 번 더 푸시하지 않는 한 요금이 다시 청구되지 않습니다.
    - 모든 리소스에 공통 되는 azure 서비스에 대 한 azure 클라우드 기본 위협 방지에 **연결 된 리소스에 대 한 범위 위협 방지** : Azure Resource Manager, Azure DNS, azure 네트워크 계층 및 Azure Key Vault. Defender for Cloud는 Azure 관리 계층과 Azure DNS 계층에 대한 고유한 가시성을 제공하므로 이들 계층에 연결된 클라우드 리소스를 보호할 수 있습니다.


## <a name="faq---pricing-and-billing"></a>FAQ - 가격 책정 및 요금 청구 

- [조직에서 누가 Defender for Cloud에서 Microsoft Defender 플랜을 사용했는지 추적하려면 어떻게 해야 하나요?](#how-can-i-track-who-in-my-organization-enabled-a-microsoft-defender-plan-in-defender-for-cloud)
- [Defender for Cloud에서 제공하는 플랜은 무엇인가요?](#what-are-the-plans-offered-by-defender-for-cloud)
- [내 구독에 Defender for Cloud의 향상된 보안을 사용하려면 어떻게 해야 하나요?](#how-do-i-enable-defender-for-clouds-enhanced-security-for-my-subscription)
- [내 구독의 서버 하위 집합에서 서버에 대해 Microsoft Defender를 사용하도록 설정할 수 있나요?](#can-i-enable-microsoft-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [엔드포인트용 Microsoft Defender 라이선스가 이미 있는 경우 서버용 Defender에 대해 할인을 받을 수 있나요?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-defender-for-servers)
- [내 구독에서 서버용 Microsoft Defender를 사용하도록 설정했습니다. 실행되지 않는 서버에 대해 비용을 지불하나요?](#my-subscription-has-microsoft-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Log Analytics 에이전트가 설치되지 않은 컴퓨터에 대해 요금이 청구되나요?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Log Analytics 에이전트에서 여러 작업 영역에 보고하는 경우 요금이 두 번 청구되나요?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Log Analytics 에이전트에서 여러 작업 영역에 보고하는 경우 모든 작업 영역에서 500MB의 무료 데이터 수집을 사용할 수 있나요?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [500MB의 무료 데이터 수집이 전체 작업 영역에 계산됩니까, 아니면 머신별로 계산됩니까?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [하루에 500MB 데이터를 허용하는 데이터 형식은 무엇입니까?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-a-microsoft-defender-plan-in-defender-for-cloud"></a>조직에서 누가 Defender for Cloud에서 Microsoft Defender 플랜을 사용했는지 추적하려면 어떻게 해야 하나요?
Azure 구독에는 가격 책정 설정을 변경할 수 있는 권한이 있는 여러 관리자가 있을 수 있습니다. 변경한 사용자를 확인하려면 Azure 활동 로그를 사용합니다.

:::image type="content" source="media/enhanced-security-features-overview/logged-change-to-pricing.png" alt-text="가격 책정 변경 이벤트를 보여주는 Azure 활동 로그.":::

사용자 정보가 **이벤트를 시작한 사람** 열에 나열되지 않는 경우 관련 세부 정보를 보려면 이벤트의 JSON을 살펴봅니다.

:::image type="content" source="media/enhanced-security-features-overview/tracking-pricing-changes-in-activity-log.png" alt-text="Azure 활동 로그 JSON 탐색기.":::


### <a name="what-are-the-plans-offered-by-defender-for-cloud"></a>Defender for Cloud에서 제공하는 플랜은 무엇인가요? 
클라우드용 Microsoft Defender의 무료 제품은 보안 점수 및 관련 도구를 제공합니다. 향상된 보안을 사용하면 Azure, 하이브리드 및 다중 클라우드 환경의 모든 리소스에 대해 다양한 보안 이점을 제공하는 모든 Microsoft Defender 플랜이 활성화됩니다.  

### <a name="how-do-i-enable-defender-for-clouds-enhanced-security-for-my-subscription"></a>내 구독에 Defender for Cloud의 향상된 보안을 사용하려면 어떻게 해야 하나요? 
다음 방법을 사용하여 구독에 향상된 보안을 사용할 수 있습니다. 

| 메서드                                          | 지침                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Portal의 Defender for Cloud 페이지    | [향상된 보호 사용](enable-enhanced-security.md)                                                                                         |
| REST API                                        | [가격 책정 API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [가격 책정 번들](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-microsoft-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>내 구독의 서버 하위 집합에서 서버에 대해 Microsoft Defender를 사용하도록 설정할 수 있나요?
아니요. 구독에서 [서버용 Microsoft Defender](defender-for-servers-introduction.md)를 사용하도록 설정하면 구독의 모든 컴퓨터는 서버용 Defender에서 보호됩니다.

대안은 Log Analytics 작업 영역 수준에서 Microsoft Defender를 서버에 사용하도록 설정하는 것입니다. 이렇게 하면 해당 작업 영역에 보고하는 서버만 보호되고 요금이 청구됩니다. 그러나 몇 가지 기능은 사용할 수 없습니다. 여기에는 Just-In-Time VM 액세스, 네트워크 탐지, 규정 준수, 적응형 네트워크 강화, 적응형 애플리케이션 제어 등이 포함됩니다. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-defender-for-servers"></a>엔드포인트용 Microsoft Defender 라이선스가 이미 있는 경우 서버용 Defender에 대해 할인을 받을 수 있나요?
엔드포인트용 Microsoft Defender 라이선스가 이미 있는 경우 서버용 Defender 라이선스의 해당 부분에 대해 비용을 지불할 필요가 없습니다.

할인을 요청하려면 Defender for Cloud에 문의하고 지정된 작업 영역의 컴퓨터에 적용된 엔드포인트용 Microsoft Defender 라이선스의 관련 작업 영역 ID, 지역 및 개수를 제공합니다.

할인은 승인 날짜부터 적용되며 소급해서 적용되지 않습니다.

### <a name="my-subscription-has-microsoft-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>내 구독에서 서버용 Microsoft Defender를 사용하도록 설정했습니다. 실행되지 않는 서버에 대해 비용을 지불하나요? 
아니요. 구독에서 [서버용 Microsoft Defender](defender-for-servers-introduction.md)를 사용하도록 설정하면 해당 상태에 있는 동안 할당 취소된 전원 상태에 있는 모든 컴퓨터에 대해 요금이 청구되지 않습니다. 머신은 다음 표에 표시된 대로 전원 상태에 따라 요금이 청구됩니다.

| 시스템 상태        | Description                                                                                                                                      | 인스턴스 사용량이 청구됨 |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| 시작 중     | VM이 시작되고 있습니다.                                                                                                                               | 청구되지 않음            |
| 실행 중      | VM의 정상적인 작동 상태                                                                                                                    | 청구됨                |
| 중지 중     | 전환 상태입니다. 작업이 완료되면 중지됨으로 표시됩니다.                                                                           | 청구됨                |
| 중지됨      | 게스트 OS 내에서 또는 PowerOff API를 사용하여 VM이 종료되었습니다. 하드웨어가 여전히 VM에 할당되고 호스트에서 유지됩니다. | 청구됨                |
| 할당 취소 중 | 전환 상태입니다. 작업이 완료되면 VM은 할당 취소됨.                                                                             | 청구되지 않음            |
| 할당 취소됨  | VM이 성공적으로 중지되고 호스트에서 제거됩니다.                                                                                  | 청구되지 않음            |

:::image type="content" source="media/enhanced-security-features-overview/deallocated-virtual-machines.png" alt-text="할당 취소된 머신을 보여주는 Azure Virtual Machines.":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Log Analytics 에이전트가 설치되지 않은 컴퓨터에 대해 요금이 청구되나요?
예. 구독에서 [서버용 Microsoft Defender](defender-for-servers-introduction.md)를 사용하도록 설정하면 Log Analytics 에이전트를 설치하지 않은 경우에도 해당 구독의 컴퓨터는 다양한 보호를 받습니다. 이는 Azure 가상 머신, Azure 가상 머신 확장 집합 인스턴스 및 Azure Arc 지원 서버에 적용됩니다.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Log Analytics 에이전트에서 여러 작업 영역에 보고하는 경우 요금이 두 번 청구되나요? 
예. 데이터를 둘 이상의 서로 다른 Log Analytics 작업 영역에 보내도록 Log Analytics 에이전트를 구성한 경우(멀티 호밍(multi-homing)) ‘보안’ 또는 ‘맬웨어 방지’ 솔루션이 설치된 모든 작업 영역에 대해 요금이 청구됩니다. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Log Analytics 에이전트에서 여러 작업 영역에 보고하는 경우 모든 작업 영역에서 500MB의 무료 데이터 수집을 사용할 수 있나요?
예. 데이터를 둘 이상의 서로 다른 Log Analytics 작업 영역에 보내도록(멀티호밍) Log Analytics 에이전트를 구성한 경우 500MB의 데이터를 무료로 수집할 수 있습니다. 노드, 보고된 작업 영역, 하루 단위로 계산되며, ‘보안’ 또는 ‘맬웨어 방지’ 솔루션이 설치된 모든 작업 영역에서 사용할 수 있습니다. 500MB 제한을 초과하여 수집되는 모든 데이터에 대해 요금이 청구됩니다.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>500MB의 무료 데이터 수집이 전체 작업 영역에 계산됩니까, 아니면 머신별로 계산됩니까?
작업 영역에 연결 된 모든 Windows 컴퓨터에 대해 하루에 500 메가바이트의 무료 데이터를 수집 합니다. 특히 Defender for Cloud에서 직접 수집한 보안 데이터 형식에 적합합니다. 

이 데이터는 모든 노드에서 일일 평균 속도입니다. 따라서 일부 머신이 100MB를 보내고 다른 머신이 800MB를 보내더라도 총계가 **[머신 수] x 500MB** 사용 가능 제한을 초과하지 않으면 추가 요금이 부과되지 않습니다.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>하루에 500MB 데이터를 허용하는 데이터 형식은 무엇입니까?
Defender for Cloud의 청구는 Log Analytics 청구와 밀접하게 연관되어 있습니다. [서버에 대 한 Microsoft Defender](defender-for-servers-introduction.md) 는 다음 [보안 데이터 형식](/azure/azure-monitor/reference/tables/tables-category#security)하위 집합에 대해 Windows 컴퓨터에 대 한 500 m b/노드/일 할당을 제공 합니다.
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- SysmonEvent
- ProtectionStatus
- 업데이트 관리 솔루션이 작업 영역에서 실행되고 있지 않거나 솔루션 대상 지정을 사용하는 경우 Update 및 UpdateSummary 데이터 형식

작업 영역이 레거시 노드당 가격 책정 계층에 있는 경우 Defender for Cloud 및 Log Analytics 할당량이 결합되어 청구 가능한 모든 수집된 데이터에 공동으로 적용됩니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Defender for Cloud의 가격 옵션에 대해 설명했습니다. 관련 자료는 다음을 참조하세요.

- [Azure 워크로드 비용을 최적화하는 방법](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [선택한 통화 및 해당 지역에 맞는 가격 세부 정보](https://azure.microsoft.com/pricing/details/security-center/)
- 특정 에이전트 집합으로 제한하여 비용을 관리하고 솔루션에 대해 수집되는 데이터 양을 제한할 수 있습니다. [솔루션 대상 지정](../azure-monitor/insights/solution-targeting.md)을 사용하여 범위를 솔루션에 적용하고 작업 영역에 있는 컴퓨터의 하위 집합을 대상으로 지정합니다. 솔루션 대상 지정을 사용하는 경우 Defender for Cloud에서 솔루션이 없는 작업 영역을 나열합니다.
