---
title: Microsoft Defender for Cloud의 릴리스 정보
description: Microsoft Defender for Cloud의 새로운 기능과 변경 된 기능에 대 한 설명
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 11/04/2021
ms.author: memildin
ms.openlocfilehash: 0e93c254a28aa62ce84f60daa02f91b247893a5b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577159"
---
# <a name="whats-new-in-microsoft-defender-for-cloud"></a>클라우드 용 Microsoft Defender의 새로운 기능

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

클라우드 용 Defender는 활성 개발 중 이며 지속적으로 향상 된 기능을 받습니다. 이 페이지에서는 최신 개발 정보를 제공하기 위해 새로운 기능, 버그 수정 및 사용되지 않는 기능에 대한 정보를 제공합니다.

이 페이지는 자주 업데이트되므로 자주 다시 방문하세요. 

클라우드 용으로 출시 *될 예정 된 변경 내용* 에 대 한 자세한 내용은 [Microsoft defender for cloud의 예정 된 주요 변경 내용](upcoming-changes.md)을 참조 하세요. 

> [!TIP]
> 6 개월 이상 지난 항목을 찾고 있다면 [Microsoft Defender For Cloud의 새로운 기능에 대 한 보관 파일](release-notes-archive.md)에서 찾을 수 있습니다.


## <a name="november-2021"></a>2021년 11월

Ignite 릴리스에는 다음이 포함 됩니다.

- [Azure Security Center 및 Azure Defender가 클라우드 용 Microsoft Defender가 됩니다.](#azure-security-center-and-azure-defender-become-microsoft-defender-for-cloud)
- [Amazon EKS 및 AWS EC2에 대 한 AWS 및 위협 방지를 위한 기본 CSPM](#native-cspm-for-aws-and-threat-protection-for-amazon-eks-and-aws-ec2)
- [Azure 부서의 범위에서 제공 하는 데이터 민감도에 따라 보안 작업 우선 순위 지정 (미리 보기)](#prioritize-security-actions-by-data-sensitivity-powered-by-azure-purview-in-preview)
- [Azure Security 벤치 마크 v3을 사용 하 여 확장 된 보안 제어 평가](#expanded-security-control-assessments-with-azure-security-benchmark-v3)
- [Microsoft 센티널 커넥터의 선택적 양방향 경고 동기화는 GA (일반 공급) 용으로 릴리스 되었습니다.](#microsoft-sentinel-connectors-optional-bi-directional-alert-synchronization-released-for-general-availability-ga)
- [센티널에 AKS (Azure Kubernetes Service) 로그를 푸시하는 새로운 권장 사항](#new-recommendation-to-push-azure-kubernetes-service-aks-logs-to-sentinel)

11 월의 다른 변경 내용은 다음과 같습니다.

- [취약성 평가 솔루션으로 추가 된 Microsoft 위협 및 취약성 관리-GA (일반 공급) 용으로 릴리스 되었습니다.](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga)
- [현재 서버에 대해 Microsoft Defender에서 지원 되는 Linux 용 microsoft Defender-GA (일반 공급) 용으로 릴리스 되었습니다.](#microsoft-defender-for-endpoint-for-linux-now-supported-by-microsoft-defender-for-servers---released-for-general-availability-ga)
- [권장 사항 및 보안 결과에 대 한 스냅숏 내보내기 (미리 보기)](#snapshot-export-for-recommendations-and-security-findings-in-preview)
- [GA (일반 공급)를 위해 릴리스된 취약점 평가 솔루션의 자동 프로 비전](#auto-provisioning-of-vulnerability-assessment-solutions-released-for-general-availability-ga)
- [GA (일반 공급)를 위해 릴리스된 asset inventory의 소프트웨어 인벤토리 필터](#software-inventory-filters-in-asset-inventory-released-for-general-availability-ga)

### <a name="azure-security-center-and-azure-defender-become-microsoft-defender-for-cloud"></a>Azure Security Center 및 Azure Defender가 클라우드 용 Microsoft Defender가 됩니다.

[클라우드 보고서의 2021 상태](https://info.flexera.com/CM-REPORT-State-of-the-Cloud#download)에 따라 이제 조직의 92%에는 다중 클라우드 전략이 있습니다. Microsoft의 목표는 이러한 환경에서 보안을 중앙 집중화 하 고 보안 팀이 보다 효과적으로 작업할 수 있도록 하는 것입니다.

**Microsoft Defender For cloud** (이전의 Azure Security Center 및 Azure Defender)는 클라우드 구성에서 약점을 검색 하 고, 사용자 환경의 전반적인 보안 상태를 강화 하 고, 다중 클라우드 및 하이브리드 환경에서 워크 로드를 보호 하는 클라우드 구성 (cspm) 및 클라우드 워크 로드 보호 (cspm) 솔루션입니다.

Ignite 2019에서 비전을 공유 하 여 Microsoft Defender 브랜드에서 디지털 공간을 보호 하 고 XDR 기술을 통합 하는 가장 완전 한 접근 방식을 만들어 보았습니다. 새 이름으로 **Microsoft defender For cloud에** Azure Security Center 및 Azure Defender를 통합 하는 것은 보안 제품의 통합 기능과 클라우드 플랫폼을 지 원하는 기능을 반영 합니다.


### <a name="native-cspm-for-aws-and-threat-protection-for-amazon-eks-and-aws-ec2"></a>Amazon EKS 및 AWS EC2에 대 한 AWS 및 위협 방지를 위한 기본 CSPM

새 **환경 설정** 페이지는 관리 그룹, 구독 및 AWS 계정에 대 한 가시성과 제어를 제공 합니다. 이 페이지는 AWS 계정을 대규모로 등록 하도록 설계 되었습니다. AWS **관리 계정** 에 연결 하면 기존 및 향후 계정을 자동으로 등록 합니다. 

:::image type="content" source="media/release-notes/add-aws-account.png" alt-text="새 환경 설정 페이지를 사용 하 여 AWS 계정을 연결 합니다.":::

AWS 계정을 추가 하면 Defender for Cloud는 다음 계획 중 하나 또는 모두를 사용 하 여 AWS 리소스를 보호 합니다.

- **클라우드의 CSPM 기능에 대 한 Defender는** AWS 리소스로 확장 됩니다. 이 에이전트 없는 계획은 AWS 특정 보안 권장 사항에 따라 AWS 리소스를 평가 하며 이러한 리소스는 보안 점수에 포함 됩니다. 또한 리소스는 AWS (AWS CIS, AWS PCI DSS 및 AWS 기본 보안 모범 사례)와 관련 된 기본 제공 표준과 호환 되는 것으로 평가 됩니다. 클라우드 용 Defender의 [자산 인벤토리 페이지](asset-inventory.md) 는 Azure 리소스와 함께 AWS 리소스를 관리 하는 데 도움이 되는 다중 클라우드 지원 기능입니다.
- **Kubernetes 용 Microsoft Defender** 는 컨테이너 위협 검색 및 고급 방어를 **Amazon EKS Linux 클러스터** 로 확장 합니다.
- **서버에 대 한 Microsoft Defender는** Windows 및 Linux EC2 인스턴스에 대 한 위협 검색 및 고급 방어 기능을 제공 합니다. 이 요금제에는 끝점에 대 한 Microsoft Defender의 통합 라이선스, 보안 기준 및 OS 수준 평가, 취약성 평가 검사, AAC (적응 응용 프로그램 컨트롤), FIM (파일 무결성 모니터링) 등이 포함 됩니다.

[AWS 계정을 Microsoft Defender For Cloud에 연결 하는](quickstart-onboard-aws.md)방법에 대해 자세히 알아보세요.


### <a name="prioritize-security-actions-by-data-sensitivity-powered-by-azure-purview-in-preview"></a>Azure 부서의 범위에서 제공 하는 데이터 민감도에 따라 보안 작업 우선 순위 지정 (미리 보기)
데이터 리소스는 위협 행위자의 인기 있는 대상으로 유지 됩니다. 따라서 보안 팀은 클라우드 환경에서 중요 한 데이터 리소스를 식별 하 고 우선 순위를 지정 하 고 보안을 유지 하는 것이 중요 합니다.

이러한 문제를 해결 하기 위해 클라우드 용 Microsoft Defender는 이제 [Azure 부서의 범위](../purview/overview.md)의 민감도 정보를 통합 합니다. Azure 부서의 범위는 다중 클라우드 및 온-프레미스 워크 로드 내에서 데이터의 민감도에 대 한 풍부한 정보를 제공 하는 통합 데이터 거 버 넌 스 서비스입니다.

Azure 부서의 범위와 통합 되 면 보안 팀에 대 한 리소스 및 보안 작업의 우선 순위를 지정 하는 완전히 새로운 방법을 사용할 수 있도록 Defender for Cloud의 보안 가시성을 인프라 수준에서 데이터로 확장할 수 있습니다.

[데이터 민감도에 따라 보안 작업 우선 순위](information-protection.md)지정에서 자세히 알아보세요.


### <a name="expanded-security-control-assessments-with-azure-security-benchmark-v3"></a>Azure Security 벤치 마크 v3을 사용 하 여 확장 된 보안 제어 평가
클라우드의 Microsoft Defender 보안 권장 사항은 Azure 보안 벤치 마크에서 사용 하도록 설정 되 고 지원 됩니다. 

[Azure Security 벤치 마크](../security/benchmarks/introduction.md) 는 일반적인 규정 준수 프레임 워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대 한 Microsoft에서 작성 한 azure 관련 지침 집합입니다. 널리 인정받는 이 벤치마크는 클라우드 중심 보안에 초점을 맞춘 [CIS(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) 및 [NIST(National Institute of Standards and Technology)](https://www.nist.gov/)의 컨트롤을 기반으로 합니다.

Ignite 2021부터 Azure Security 벤치 마크 **v3** 은 [클라우드 규정 준수 대시보드의 Defender](update-regulatory-compliance-packages.md) 에서 사용할 수 있으며, Microsoft defender for cloud로 보호 되는 모든 Azure 구독에 대 한 새로운 기본 이니셔티브로 사용할 수 있습니다. 

V3의 향상 된 기능은 다음과 같습니다. 

- 업계 프레임 워크에 대 한 추가 매핑 [PCI-DSS v 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf) 및 [ci 컨트롤 v8](https://www.cisecurity.org/controls/v8/).
- 소개를 통해 컨트롤에 대 한 보다 세분화 되 고 실행 가능한 지침을 제공 합니다.
    - **보안 원칙** -권장 사항에 대 한 기초를 구축 하는 전반적인 보안 목표에 대 한 통찰력을 제공 합니다.
    - **Azure 지침** -이러한 목표를 충족 하는 기술 "방법"

새 컨트롤에는 위협 모델링 및 소프트웨어 공급망 보안과 같은 문제에 대 한 DevOps 보안 뿐만 아니라 Azure의 모범 사례에 대 한 키 및 인증서 관리가 포함 됩니다.

[Azure 보안 벤치 마크 소개](/security/benchmark/azure/introduction)에서 자세히 알아보세요.


### <a name="microsoft-sentinel-connectors-optional-bi-directional-alert-synchronization-released-for-general-availability-ga"></a>Microsoft 센티널 커넥터의 선택적 양방향 경고 동기화는 GA (일반 공급) 용으로 릴리스 되었습니다.

7 월에 [Microsoft 센티널](../sentinel/index.yml) (microsoft의 클라우드-기본 siem 및 대화 충성도 솔루션)의 기본 제공 커넥터용 미리 보기 기능, **양방향 경고 동기화** 를 [발표 했습니다](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview) . 이 기능은 이제 GA (일반 공급)에 대해 출시 되었습니다.

Microsoft 센티널에 클라우드 용 Microsoft Defender를 연결 하면 보안 경고의 상태가 두 서비스 간에 동기화 됩니다. 예를 들어 Defender for Cloud에서 경고가 종결 되 면 해당 경고도 Microsoft 센티널 에서도 닫힘으로 표시 됩니다. Defender for Cloud에서 경고의 상태를 변경 해도 동기화 된 Microsoft 센티널 경고를 포함 하는 Microsoft 센티널 **인시던트의** 상태에는 영향을 주지 않습니다. 동기화 된 경고 자체의 상태에는 영향을 주지 않습니다.

**양방향 경고 동기화** 를 사용 하는 경우 클라우드 경고에 대 한 해당 defender의 복사본을 포함 하는 Microsoft 센티널 인시던트를 사용 하 여 클라우드 경고에 대 한 원래 defender의 상태를 자동으로 동기화 합니다. 예를 들어, 클라우드 용 Defender 경고를 포함 하는 Microsoft 센티널 인시던트가 종결 되는 경우 클라우드 용 Defender는 해당 하는 원래 경고를 자동으로 닫습니다.

azure에 대 한 경고를 azure [Azure Security Center에서 커넥트](../sentinel/connect-azure-security-center.md) 하 고 [azure 센티널에 경고를 스트리밍하](export-to-siem.md#stream-alerts-to-microsoft-sentinel)는 방법에 대해 자세히 알아보세요.


### <a name="new-recommendation-to-push-azure-kubernetes-service-aks-logs-to-sentinel"></a>센티널에 AKS (Azure Kubernetes Service) 로그를 푸시하는 새로운 권장 사항

클라우드 및 Microsoft 센티널에 대 한 Defender의 결합 된 값을 추가로 개선 하 여 이제 Microsoft 센티널로 로그 데이터를 보내지 않는 Azure Kubernetes 서비스 인스턴스를 강조 표시 합니다.

SecOps 팀은 권장 사항 세부 정보 페이지에서 직접 관련 Microsoft 센티널 작업 영역을 선택 하 고 즉시 원시 로그 스트리밍을 사용 하도록 설정할 수 있습니다. 이 두 제품을 원활 하 게 연결 하면 보안 팀에서 전체 환경에 맞게 워크 로드에 대 한 전체 로깅 범위를 쉽게 유지할 수 있습니다.

새 권장 사항 "Kubernetes services의 진단 로그를 사용 하도록 설정 해야 합니다."에는 보다 신속한 업데이트를 위한 ' 수정 ' 옵션이 포함 되어 있습니다.

또한 동일한 센티널 스트리밍 기능을 사용 하 여 "SQL 서버에서 감사를 사용 하도록 설정 해야 합니다." 권장 사항을 개선 했습니다. 


### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga"></a>취약성 평가 솔루션으로 추가 된 Microsoft 위협 및 취약성 관리-GA (일반 공급) 용으로 릴리스 되었습니다.

10 월에는 컴퓨터에 대 한 새로운 취약점 평가 공급자를 지원 하기 위해 microsoft [defender for server](defender-for-servers-introduction.md) 와 microsoft defender for Endpoint의 통합에 대 한 확장을 [발표 했습니다](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview) . [microsoft 위협 및 취약성 관리](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt). 이 기능은 이제 GA (일반 공급)에 대해 출시 되었습니다.

**위협 및 취약성 관리** 를 사용 하 여 끝점을 사용할 수 있도록 [Microsoft Defender와의 통합](integration-defender-for-endpoint.md) , 추가 에이전트 또는 정기적 검사가 필요 없이 거의 실시간으로 취약성 및 잘못 된 구성을 검색할 수 있습니다. 위협 및 취약성 관리 조직의 위협 환경 및 검색에 따라 취약점의 우선 순위를 결정 합니다.

"[가상 머신에서 취약점 평가 솔루션을 사용 하도록 설정 해야](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d)합니다" 라는 보안 권장 사항을 사용 하 여 [지원 되는 컴퓨터](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true)에 대 한 위협 및 취약성 관리에서 검색 된 취약점을 발생 시킬 수 있습니다. 

권장 사항을 수동으로 해결할 필요 없이 기존 및 새 컴퓨터에서 취약점 평가 솔루션을 자동으로 [사용 하도록 설정할 수 있습니다 (미리 보기)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview).

[엔드포인트용 Microsoft Defender의 위협 및 취약성 관리를 통한 취약성 조사](deploy-vulnerability-assessment-tvm.md)에서 자세히 알아보세요.

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-microsoft-defender-for-servers---released-for-general-availability-ga"></a>현재 서버에 대해 Microsoft Defender에서 지원 되는 Linux 용 microsoft Defender-GA (일반 공급) 용으로 릴리스 되었습니다.

8 월에 [linux 용 Defender For Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux) 센서를 지원 되는 linux 컴퓨터에 배포 하기 위한 미리 보기 지원을 [발표 했습니다](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview) . 이 기능은 이제 GA (일반 공급)에 대해 출시 되었습니다.

[서버용 Microsoft defender](defender-for-servers-introduction.md) 에는 [끝점 용 microsoft defender](https://www.microsoft.com/microsoft-365/security/endpoint-defender)에 대 한 통합 라이선스가 포함 되어 있습니다. 또한 포괄적인 EDR(엔드포인트 검색 및 응답) 기능을 제공합니다.

엔드포인트용 Defender는 위협을 감지하면 경고를 트리거합니다. 이 경고는 클라우드에 대 한 Defender에 표시 됩니다. Defender for Cloud에서 끝점 콘솔용 Defender로 피벗 하 고 자세한 조사를 수행 하 여 공격의 범위를 확인할 수도 있습니다.

[Security Center의 통합 EDR 솔루션으로 엔드포인트를 보호: 엔드포인트용 Microsoft Defender](integration-defender-for-endpoint.md)를 자세히 알아봅니다.


### <a name="snapshot-export-for-recommendations-and-security-findings-in-preview"></a>권장 사항 및 보안 결과에 대 한 스냅숏 내보내기 (미리 보기)

클라우드에 대 한 Defender는 자세한 보안 경고 및 권장 사항을 생성 합니다. 관련 내용은 포털에서 또는 프로그래밍 도구를 통해 볼 수 있습니다. 사용자 환경에서 다른 모니터링 도구를 사용해 추적하기 위해서는 이 정보의 일부 또는 전부 내보내야 할 수도 있습니다.

Defender for Cloud의 **연속 내보내기** 기능을 사용 하 여 내보낼 *대상과* 이동할 *위치* 를 완벽 하 게 사용자 지정할 수 있습니다. [클라우드 데이터에 대해 Microsoft Defender를 계속 내보내는](continuous-export.md)방법에 대해 자세히 알아보세요.

기능이 *연속* 으로 호출 되더라도 주간 스냅숏을 내보내는 옵션도 있습니다. 지금 까지는 이러한 주간 스냅숏이 보안 점수 및 규정 준수 데이터로 제한 되었습니다. 권장 사항 및 보안 결과를 내보내는 기능이 추가되었습니다.

### <a name="auto-provisioning-of-vulnerability-assessment-solutions-released-for-general-availability-ga"></a>GA(일반 공급)를 위해 릴리스된 취약성 평가 솔루션의 자동 프로비저닝

10월에 Defender for Cloud의 자동 프로비저닝 페이지에 취약성 평가 솔루션이 [추가되었다고 발표했습니다.](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview) 이는 Azure 가상 머신 및 서버용 Azure Defender 의해 보호되는 구독의 Azure Arc 머신과 관련이 [있습니다.](defender-for-servers-introduction.md) 이 기능은 이제 GA(일반 공급)를 위해 릴리스되었습니다.

[엔드포인트용 Microsoft Defender와의 통합이](integration-defender-for-endpoint.md) 활성화된 경우 Defender for Cloud는 다음과 같은 취약성 평가 솔루션을 선택할 수 있습니다.

- (**NEW**) 엔드포인트용 Microsoft Defender의 Microsoft 위협 및 취약성 관리 모듈(릴리스 [참고](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga)참조)
- 통합 Qualys 에이전트

선택한 솔루션은 지원되는 컴퓨터에서 자동으로 사용하도록 설정됩니다.

[머신에 대한 취약성 평가 자동 구성에서](auto-deploy-vulnerability-assessment.md)자세히 알아보세요.

### <a name="software-inventory-filters-in-asset-inventory-released-for-general-availability-ga"></a>GA(일반 공급)를 위해 릴리스된 자산 인벤토리의 소프트웨어 인벤토리 필터

10월에 [자산 인벤토리](asset-inventory.md) 페이지에 대한 새 필터를 [발표하여](#software-inventory-filters-added-to-asset-inventory-in-preview) 특정 소프트웨어를 실행하는 머신을 선택하고 관심 있는 버전을 지정했습니다. 이 기능은 이제 GA(일반 공급)를 위해 릴리스되었습니다.

**Azure Resource Graph Explorer에서** 소프트웨어 인벤토리 데이터를 쿼리할 수 있습니다.

이러한 기능을 사용하려면 [엔드포인트용 Microsoft Defender와의 통합을](integration-defender-for-endpoint.md)사용하도록 설정해야 합니다. 

Azure Resource Graph 대한 샘플 Kusto 쿼리를 비롯한 자세한 내용은 [소프트웨어 인벤토리 액세스를](asset-inventory.md#access-a-software-inventory)참조하세요.

### <a name="new-aks-security-policy-added-to-default-initiative--for-use-by-private-preview-customers-only"></a>기본 이니셔티브에 추가된 새 AKS 보안 정책 - 프라이빗 미리 보기 고객만 사용할 수 있습니다.

Kubernetes 워크로드가 기본적으로 안전한지 확인하기 위해 Defender for Cloud에는 Kubernetes 허용 제어를 사용하는 적용 옵션을 포함하여 Kubernetes 수준 정책 및 강화 권장 사항이 포함되어 있습니다.

이 프로젝트의 일부로 Kubernetes 클러스터에서 배포를 게이팅하기 위한 정책 및 권장 사항(기본적으로 사용하지 않도록 설정)을 추가했습니다. 정책은 기본 이니셔티브에 있지만 관련 프라이빗 미리 보기에 등록하는 조직과만 관련이 있습니다.

정책 및 권장 사항("Kubernetes 클러스터는 취약한 이미지의 배포를 게이트해야 합니다")을 안전하게 무시할 수 있으며 환경에 영향을 미치지 않습니다. 

프라이빗 미리 보기에 참여하려면 프라이빗 미리 보기 링의 멤버가 되어야 합니다. 아직 멤버가 아닌 경우 여기에서 요청을 [제출합니다.](https://aka.ms/atscale) 미리 보기가 시작되면 멤버에게 알림이 제공됩니다.

## <a name="october-2021"></a>2021년 10월

10월의 업데이트는 다음과 같습니다.

- [Microsoft 위협 및 취약성 관리가 취약성 평가 솔루션으로 추가(미리 보기)](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview)
- [이제 취약성 평가 솔루션을 자동으로 사용하도록 설정할 수 있습니다(미리 보기 상태).](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview)
- [자산 인벤토리에 추가된 소프트웨어 인벤토리 필터(미리 보기)](#software-inventory-filters-added-to-asset-inventory-in-preview)
- [일부 경고 유형의 접두사 "ARM_"에서 "VM_"로 변경](#changed-prefix-of-some-alert-types-from-arm_-to-vm_)
- [Kubernetes 클러스터에 대한 보안 권장 사항의 논리 변경 내용](#changes-to-the-logic-of-a-security-recommendation-for-kubernetes-clusters)
- [이제 권장 사항 세부 정보 페이지에 관련 권장 사항이 표시됩니다.](#recommendations-details-pages-now-show-related-recommendations)
- [Azure Defender for Kubernetes 대한 새 경고(미리 보기)](#new-alerts-for-azure-defender-for-kubernetes-in-preview)


### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview"></a>Microsoft 위협 및 취약성 관리가 취약성 평가 솔루션으로 추가(미리 보기)

[서버에 대한 Azure Defender 엔드포인트용](defender-for-servers-introduction.md) Microsoft Defender 간의 통합을 확장하여 머신에 대한 새로운 취약성 평가 공급자를 지원했습니다. [Microsoft 위협 및 취약성 관리.](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt) 

**위협 및 취약성 관리** 사용하여 [엔드포인트용 Microsoft Defender와의 통합이](integration-defender-for-endpoint.md) 활성화된 상태에서 추가 에이전트 또는 정기 검색 없이도 거의 실시간으로 취약성 및 잘못된 구성을 검색할 수 있습니다. 위협 및 취약성 관리 조직의 위협 환경 및 탐지에 따라 취약성의 우선 순위를 지정합니다.

보안 권장 사항 "[가상 머신에서 취약성 평가 솔루션을 사용하도록 설정해야 합니다."를](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d)사용하여 [지원되는](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true)머신에 대해 위협 및 취약성 관리 감지한 취약성을 파악합니다. 

권장 사항을 수동으로 수정할 필요 없이 기존 및 새 머신에서 취약성을 자동으로 표시하려면 [이제 취약성 평가 솔루션을 자동으로 사용하도록 설정할 수 있음(미리 보기 상태)을](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview)참조하세요.

[엔드포인트용 Microsoft Defender의 위협 및 취약성 관리를 통한 취약성 조사](deploy-vulnerability-assessment-tvm.md)에서 자세히 알아보세요.

### <a name="vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview"></a>이제 취약성 평가 솔루션을 자동으로 사용하도록 설정할 수 있습니다(미리 보기 상태).

이제 Security Center 자동 프로비저닝 페이지에는 서버용 Azure Defender 보호되는 구독에서 Azure 가상 머신 및 Azure Arc 머신에 취약성 평가 솔루션을 자동으로 사용하도록 설정하는 옵션이 포함되어 [있습니다.](defender-for-servers-introduction.md)

[엔드포인트용 Microsoft Defender와의 통합이](integration-defender-for-endpoint.md) 활성화된 경우 Defender for Cloud는 다음과 같은 취약성 평가 솔루션을 선택할 수 있습니다.

- (**NEW**) 엔드포인트용 Microsoft Defender의 Microsoft 위협 및 취약성 관리 모듈(릴리스 [참고](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview)참조)
- 통합 Qualys 에이전트

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/auto-provision-vulnerability-assessment-agent.png" alt-text="Azure Security Center Microsoft 위협 및 취약성 관리 자동 프로비저닝을 구성합니다.":::

선택한 솔루션은 지원되는 컴퓨터에서 자동으로 사용하도록 설정됩니다.

[머신에 대한 취약성 평가 자동 구성에서](auto-deploy-vulnerability-assessment.md)자세히 알아보세요.

### <a name="software-inventory-filters-added-to-asset-inventory-in-preview"></a>자산 인벤토리에 추가된 소프트웨어 인벤토리 필터(미리 보기)

이제 [자산 인벤토리](asset-inventory.md) 페이지에 특정 소프트웨어를 실행하는 머신을 선택하는 필터가 포함되어 있으며, 관심 있는 버전도 지정합니다. 

또한 **Azure Resource Graph Explorer에서** 소프트웨어 인벤토리 데이터를 쿼리할 수 있습니다.

이러한 새로운 기능을 사용하려면 [엔드포인트용 Microsoft Defender와의 통합을](integration-defender-for-endpoint.md)사용하도록 설정해야 합니다. 

Azure Resource Graph 대한 샘플 Kusto 쿼리를 비롯한 자세한 내용은 [소프트웨어 인벤토리 액세스를](asset-inventory.md#access-a-software-inventory)참조하세요.

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/software-inventory.png" alt-text="위협 및 취약성 솔루션을 사용하도록 설정한 경우 Security Center 자산 인벤토리는 설치된 소프트웨어로 리소스를 선택하는 필터를 제공합니다.":::

### <a name="changed-prefix-of-some-alert-types-from-arm_-to-vm_"></a>일부 경고 유형의 접두사 "ARM_"에서 "VM_"로 변경 

2021년 7월에 Resource Manager [경고에 대한 Azure Defender 논리적 재구성을 발표했습니다.](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts) 

일부 Azure Defender 계획의 논리적 재구성의 일환으로 Resource Manager [대한 Azure Defender](defender-for-resource-manager-introduction.md) 21개의 경고를 [서버의 Azure Defender](defender-for-servers-introduction.md)이동했습니다.

이 업데이트를 통해 이러한 경고의 접두사도 이 재할당과 일치하도록 변경하고 다음 표와 같이 "ARM_"을 "VM_"로 바꿨습니다.

| 원래 이름                                  | 이 변경에서                              |
|------------------------------------------------|-----------------------------------------------|
| ARM_AmBroadFilesExclusion                      | VM_AmBroadFilesExclusion                      |
| ARM_AmDisablementAndCodeExecution              | VM_AmDisablementAndCodeExecution              |
| ARM_AmDisablement                              | VM_AmDisablement                              |
| ARM_AmFileExclusionAndCodeExecution            | VM_AmFileExclusionAndCodeExecution            |
| ARM_AmTempFileExclusionAndCodeExecution        | VM_AmTempFileExclusionAndCodeExecution        |
| ARM_AmTempFileExclusion                        | VM_AmTempFileExclusion                        |
| ARM_AmRealtimeProtectionDisabled               | VM_AmRealtimeProtectionDisabled               |
| ARM_AmTempRealtimeProtectionDisablement        | VM_AmTempRealtimeProtectionDisablement        |
| ARM_AmRealtimeProtectionDisablementAndCodeExec | VM_AmRealtimeProtectionDisablementAndCodeExec |
| ARM_AmMalwareCampaignRelatedExclusion          | VM_AmMalwareCampaignRelatedExclusion          |
| ARM_AmTemporarilyDisablement                   | VM_AmTemporarilyDisablement                   |
| ARM_UnusualAmFileExclusion                     | VM_UnusualAmFileExclusion                     |
| ARM_CustomScriptExtensionSuspiciousCmd         | VM_CustomScriptExtensionSuspiciousCmd         |
| ARM_CustomScriptExtensionSuspiciousEntryPoint  | VM_CustomScriptExtensionSuspiciousEntryPoint  |
| ARM_CustomScriptExtensionSuspiciousPayload     | VM_CustomScriptExtensionSuspiciousPayload     |
| ARM_CustomScriptExtensionSuspiciousFailure     | VM_CustomScriptExtensionSuspiciousFailure     |
| ARM_CustomScriptExtensionUnusualDeletion       | VM_CustomScriptExtensionUnusualDeletion       |
| ARM_CustomScriptExtensionUnusualExecution      | VM_CustomScriptExtensionUnusualExecution      |
| ARM_VMAccessUnusualConfigReset                 | VM_VMAccessUnusualConfigReset                 |
| ARM_VMAccessUnusualPasswordReset               | VM_VMAccessUnusualPasswordReset               |
| ARM_VMAccessUnusualSSHReset                    | VM_VMAccessUnusualSSHReset                    |
|||

[Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md) 및 [서버용 Azure Defender](defender-for-servers-introduction.md) 계획에 대해 자세히 알아봅니다.

### <a name="changes-to-the-logic-of-a-security-recommendation-for-kubernetes-clusters"></a>Kubernetes 클러스터에 대한 보안 권장 사항의 논리 변경 내용

"Kubernetes 클러스터는 기본 네임스페이스를 사용하지 않아야 합니다."라는 권장 사항은 리소스 종류 범위에 대한 기본 네임스페이스의 사용을 방지합니다. 이 권장 사항에 포함된 두 가지 리소스 유형인 ConfigMap 및 Secret이 제거되었습니다. 

Kubernetes 클러스터에 대한 Azure Policy 이해에서 이 권장 사항 및 [Kubernetes 클러스터 강화에 대해](../governance/policy/concepts/policy-for-kubernetes.md)자세히 알아보세요.

### <a name="recommendations-details-pages-now-show-related-recommendations"></a>이제 권장 사항 세부 정보 페이지에 관련 권장 사항이 표시됩니다.

다양한 권장 사항 간의 관계를 명확히 하기 위해 많은 **권장 사항의** 세부 정보 페이지에 관련 권장 사항 영역을 추가했습니다. 

이러한 페이지에 표시된 세 가지 관계 유형은 다음과 같습니다.

- **필수 조건** - 선택한 권장 사항 전에 완료해야 하는 권장 사항
- **대안** - 선택한 권장 사항의 목표를 달성하는 다른 방법을 제공하는 다른 권장 사항
- **종속성** - 선택한 권장 사항이 필수 조건인 권장 사항

각 관련 권장 사항에 대해 비정상 리소스 수가 "영향을 받는 리소스" 열에 표시됩니다.

> [!TIP]
> 관련 권장 사항이 회색으로 표시됩니다. 해당 종속성이 아직 완료되지 않았으므로 사용할 수 없습니다.

관련 권장 사항의 예:

1. Security Center 머신에서 지원되는 취약성 평가 솔루션을 확인합니다.<br>
    **취약성 평가 솔루션을 가상 머신에서 사용하도록 설정해야 함**

1. 취약성이 발견되면 검색된 취약성에 대한 알림이 표시됩니다.<br>
    **가상 머신의 취약성을 수정해야 함**

물론 Security Center 지원되는 취약성 평가 솔루션을 찾지 않으면 검색된 취약성에 대해 알릴 수 없습니다.

따라서

 - 권장 사항 #1은 권장 사항의 필수 조건 #2입니다.
 - 권장 사항 #2는 권장 사항 #1에 따라 달라집니다.

:::image type="content" source="media/release-notes/related-recommendations-solution-not-found.png" alt-text="취약성 평가 솔루션을 배포하기 위한 권장 사항의 스크린샷.":::

:::image type="content" source="media/release-notes/related-recommendations-vulnerabilities-found.png" alt-text="검색된 취약성을 해결하기 위한 권장 사항의 스크린샷":::



### <a name="new-alerts-for-azure-defender-for-kubernetes-in-preview"></a>Azure Defender for Kubernetes 대한 새 경고(미리 보기)

Azure Defender for Kubernetes 제공하는 위협 방지를 확장하기 위해 두 개의 미리 보기 경고가 추가되었습니다.

이러한 경고는 새 기계 학습 모델 및 Kubernetes 고급 분석을 기반으로 생성되며, 클러스터의 이전 활동과 Azure Defender 모니터링되는 모든 클러스터에서 여러 배포 및 역할 할당 특성을 측정합니다.

| 경고(경고 유형)                                                                 | Description                                                                                                                                                                                                                                                                                                                                                      | MITRE 전술 | 심각도 |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| **비정상적인 Pod 배포(미리 보기)**<br>(K8S_AnomalousPodDeployment)             | Kubernetes 감사 로그 분석에서 이전 Pod 배포 활동을 기반으로 비정상적인 Pod 배포를 검색했습니다. 이 작업은 배포 작업에서 볼 수 있는 다양한 기능이 서로 어떻게 관계가 있는지를 고려할 때 변칙으로 간주됩니다. 이 분석에서 모니터링되는 기능에는 사용되는 컨테이너 이미지 레지스트리, 배포를 수행하는 계정, 요일, 이 계정이 Pod 배포를 수행하는 빈도, 작업에 사용되는 사용자 에이전트, Pod 배포가 자주 발생하는 네임스페이스 또는 기타 기능이 포함됩니다. 비정상적인 활동으로 이 경고를 제기하기 위한 주요 원인은 경고 확장 속성 아래에 자세히 설명되어 있습니다. | 실행 | 중간 |
| **Kubernetes 클러스터에 할당된 과도한 역할 권한(미리 보기)**<br>(K8S_ServiceAcountPermissionAnomaly) | Kubernetes 감사 로그를 분석한 결과는 클러스터에 대한 과도한 권한 역할 할당을 검색했습니다. 역할 할당 검사에서 나열된 권한은 특정 서비스 계정에 일반적이지 않습니다. 이 검색에서는 Azure에서 모니터링하는 클러스터에서 동일한 서비스 계정에 대한 이전 역할 할당, 권한당 볼륨 및 특정 권한의 영향을 고려합니다. 이 경고에 사용되는 변칙 검색 모델은 Azure Defender 모니터링하는 모든 클러스터에서 이 사용 권한을 사용하는 방법을 고려합니다. | 권한 상승 | 낮음 |
|||

Kubernetes 경고의 전체 목록은 [Kubernetes 클러스터에 대한 경고](alerts-reference.md#alerts-k8scluster)를 참조하세요.

## <a name="september-2021"></a>2021년 9월

9월에는 다음 업데이트가 릴리스되었습니다.

### <a name="two-new-recommendations-to-audit-os-configurations-for-azure-security-baseline-compliance-in-preview"></a>Azure 보안 기준 준수에 대한 OS 구성을 감사하기 위한 두 가지 새로운 권장 사항(미리 보기)

Windows 보안 기준 및 [Linux](../governance/policy/samples/guest-configuration-baseline-linux.md) [보안 기준을](../governance/policy/samples/guest-configuration-baseline-windows.md) 사용하여 머신의 규정 준수를 평가하기 위해 다음 두 가지 권장 사항이 릴리스되었습니다.

- Windows 컴퓨터의 경우 Windows 컴퓨터의 보안 구성 취약성을 [수정해야 합니다(게스트 구성에서 구동).](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6)
- Linux 머신의 경우 [Linux 머신의 보안 구성 취약성을 수정해야 합니다(게스트 구성에서 구동).](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda)

이러한 권장 사항은 Azure Policy 게스트 구성 기능을 사용하여 컴퓨터의 OS 구성을 [Azure 보안 벤치마크](/security/benchmark/azure/overview)에 정의된 기준과 비교합니다.

게스트 구성을 사용하여 [컴퓨터의 OS 구성 강화에서](apply-security-baseline.md)이러한 권장 사항을 사용하는 방법에 대해 자세히 알아보세요.

## <a name="august-2021"></a>2021년 8월

8월의 업데이트는 다음과 같습니다.

- [이제 Linux 엔드포인트용 Microsoft Defender는 서버용 Azure Defender가 지원됩니다(미리 보기)](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview)
- [엔드포인트 보호 솔루션 관리를 위한 두 가지 새로운 권장 사항(미리 보기)](#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)
- [일반적인 문제를 해결하기 위한 기본 제공 문제 해결 및 지침](#built-in-troubleshooting-and-guidance-for-solving-common-issues)
- [GA(일반 공급)를 위해 릴리스된 규정 준수 대시보드의 Azure 감사 보고서](#regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga)
- ['머신에서 Log Analytics 에이전트 상태 문제를 해결해야 함' 권장 사항이 사용 중단됨](#deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines)
- [컨테이너 레지스트리에 대한 Azure Defender는 이제 Azure Private Link 보호되는 레지스트리의 취약성을 검색합니다](#azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link)
- [이제 Security Center Azure Policy 게스트 구성 확장을 자동으로 프로비전할 수 있습니다(미리 보기)](#security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview)
- ["적용"을 지원하는 Azure Defender 계획을 사용하도록 설정하는 권장 사항](#recommendations-to-enable-azure-defender-plans-now-support-enforce)
- [권장 사항 데이터의 CSV 내보내기는 이제 20MB로 제한됩니다](#csv-exports-of-recommendation-data-now-limited-to-20-mb)
- [이제 권장 사항 페이지에 여러 보기가 포함됩니다](#recommendations-page-now-includes-multiple-views)

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview"></a>이제 Linux 엔드포인트용 Microsoft Defender는 서버용 Azure Defender가 지원됩니다(미리 보기)

[서버용 Azure Defender](defender-for-servers-introduction.md)에는 [엔드포인트용 Microsoft Defender](https://www.microsoft.com/microsoft-365/security/endpoint-defender)와의 통합 라이선스가 포함되어 있습니다. 또한 포괄적인 EDR(엔드포인트 검색 및 응답) 기능을 제공합니다.

엔드포인트용 Defender는 위협을 감지하면 경고를 트리거합니다. 이 경고는 Security Center에 표시됩니다. Security Center에서 엔드포인트용 Defender 콘솔로 피벗하고 자세히 조사하여 공격 범위를 확인할 수도 있습니다.

미리 보기 기간에는 Windows 머신에 이미 배포했는지 여부에 따라 다음 두 가지 방법 중 하나로 Linux 머신에 [Linux 엔드포인트용 Defender](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux) 센서를 배포합니다.

- [Defender for Cloud의 향상된 보안 기능이 사용하도록 설정되고 엔드포인트용 Microsoft Defender가 있는 기존 사용자 Windows](integration-defender-for-endpoint.md#existing-users-with-defender-for-clouds-enhanced-security-features-enabled-and-microsoft-defender-for-endpoint-for-windows)
- [엔드포인트용 Microsoft Defender(Windows)와의 통합을 한 번도 설정한 적이 없는 신규 사용자](integration-defender-for-endpoint.md?tabs=linux#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)

[Security Center의 통합 EDR 솔루션으로 엔드포인트를 보호: 엔드포인트용 Microsoft Defender](integration-defender-for-endpoint.md)를 자세히 알아봅니다.

### <a name="two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview"></a>엔드포인트 보호 솔루션 관리를 위한 두 가지 새로운 권장 사항(미리 보기)

컴퓨터에 엔드포인트 보호 솔루션을 배포하고 유지 관리하기 위한 두 가지 새로운 **미리 보기** 권장 사항을 추가했습니다. 두 권장 사항에는 Azure 가상 머신 및 Azure Arc 지원 서버에 연결된 머신에 대한 지원이 포함됩니다.

|권장 |Description |심각도 |
|---|---|---|
|[머신에 Endpoint Protection을 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/4fb67663-9ab9-475d-b026-8c544cced439) |위협 및 취약성으로부터 머신을 보호하려면 지원되는 엔드포인트 보호 솔루션을 설치합니다.  <br> <a href="/azure/security-center/endpoint-protection-recommendations-technical">머신의 Endpoint Protection을 평가하는 방법에 대한 자세한 정보</a><br />(관련 정책: [Azure Security Center에서 누락된 엔드포인트 보호 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |높음 |
|[머신에서 엔드포인트 보호 상태 문제를 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/37a3689a-818e-4a0e-82ac-b1392b9bb000) |가상 머신의 엔드포인트 보호 상태 문제를 해결하여 최신 위협 및 취약성으로부터 보호합니다. Azure Security Center 지원 엔드포인트 보호 솔루션은 [여기](./supported-machines-endpoint-solutions-clouds.md?tabs=features-windows)에 설명되어 있습니다. 엔드포인트 보호 평가는 <a href='/azure/security-center/endpoint-protection-recommendations-technical'>여기</a>에 설명되어 있습니다.<br />(관련 정책: [Azure Security Center에서 누락된 엔드포인트 보호 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |중간 |
|||

> [!NOTE]
> 권장 사항은 새로 고침 간격을 8시간으로 표시하지만, 이 시간이 훨씬 더 오래 걸릴 수 있는 몇 가지 시나리오가 있습니다. 예를 들어, 온-프레미스 머신이 삭제되면 Security Center 삭제를 식별하는 데 24시간이 걸립니다. 그런 다음, 평가에서 정보를 반환하는 데 최대 8시간이 걸립니다. 따라서, 이러한 특정 상황에서는 영향을 받는 리소스 목록에서 컴퓨터를 제거하는 데 32시간이 걸릴 수 있습니다.
>
> :::image type="content" source="media/release-notes/freshness-interval.png" alt-text="두 가지 새로운 Security Center 권장 사항에 대한 새로 고침 간격 표시기":::

### <a name="built-in-troubleshooting-and-guidance-for-solving-common-issues"></a>일반적인 문제를 해결하기 위한 기본 제공 문제 해결 및 지침

Azure Portal에 있는 Security Center 페이지의 전용 영역인 A는 Security Center 및 Azure Defender와 관련된 일반적인 문제를 해결하기 위해 지속적으로 증가하는 자가 도움말 자료 세트를 제공합니다.

문제가 발생하거나 지원 팀의 조언을 구하는 경우 **문제 진단 및 해결** 을 통해 솔루션을 찾을 수 있습니다.

:::image type="content" source="media/release-notes/solve-problems.png" alt-text="Security Center의 '문제 진단 및 해결' 페이지":::
 

### <a name="regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga"></a>GA(일반 공급)를 위해 릴리스된 규정 준수 대시보드의 Azure 감사 보고서

규정 준수 대시보드의 도구 모음은 구독에 적용되는 표준에 대한 Azure 및 Dynamics 인증 보고서를 제공합니다. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="감사 보고서를 생성하기 위한 단추를 보여주는 규정 준수 대시보드의 도구 모음.":::

관련 보고서 유형(PCI, SOC, ISO 및 기타)에 대한 탭을 선택하고 필터를 사용하여 필요한 특정 보고서를 찾을 수 있습니다.

자세한 내용은 [준수 상태 보고서 및 인증서 생성](regulatory-compliance-dashboard.md#generate-compliance-status-reports-and-certificates)을 참조하세요.

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard-ga.png" alt-text="사용 가능한 Azure 감사 보고서의 탭 목록. ISO 보고서, SOC 보고서, PCI 등에 대한 탭입니다.":::

### <a name="deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>'머신에서 Log Analytics 에이전트 상태 문제를 해결해야 함' 권장 사항이 사용 중단됨

**머신에서 Log Analytics 에이전트 상태 문제를 해결해야 함** 권장 사항이 Security Center의 CSPM(클라우드 보안 상태 관리) 포커스와 일치하지 않는 방식으로 보안 점수에 영향을 줍니다. 일반적으로 CSPM은 잘못된 보안 구성을 식별하는 것과 관련이 있습니다. 에이전트 상태 문제는 이 문제 범주에 맞지 않습니다.

또한 이 권장 사항은 Security Center와 관련된 다른 에이전트와 비교할 때 이례적입니다. 이는 상태 문제와 관련된 권장 사항이 있는 유일한 에이전트입니다.

권장 사항이 더 이상 사용되지 않습니다.

이 사용 중단으로 인해 Log Analytics 에이전트 설치 권장 사항도 약간 변경합니다(**Log Analytics 에이전트를 ...에 설치해야 함**).

이 변경 내용은 보안 점수에 영향을 줄 수도 있습니다. 대부분의 구독에서는 변경으로 인해 점수가 증가할 것으로 예상하지만, 설치 권장 사항을 업데이트하면 경우에 따라 점수가 저하될 수 있습니다.

> [!TIP]
> [자산 인벤토리](asset-inventory.md) 페이지는 머신이 모니터링되는지, 모니터링되지 않거나 부분적으로 모니터링되는지(상태 문제가 있는 에이전트를 나타내는 상태)에 대한 정보도 표시하기 때문에 이 변경의 영향을 받습니다.


### <a name="azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link"></a>컨테이너 레지스트리에 대한 Azure Defender는 이제 Azure Private Link 보호되는 레지스트리의 취약성을 검색합니다
컨테이너 레지스트리용 Azure Defender에는 Azure Container Registry의 이미지를 검사하는 취약성 스캐너가 포함되어 있습니다. [컨테이너 레지스트리에 Azure Defender 사용하여 이미지에서 취약성 검색에서 레지스트리를 검사하고 결과를 수정하는 방법](defender-for-container-registries-usage.md)을 알아봅니다.

Azure Container Registry에서 호스팅되는 레지스트리에 대한 액세스를 제한하려면 레지스트리 엔드포인트에 가상 네트워크 개인 IP 주소를 할당하고 Azure Private Link 를 사용하여 [Azure 컨테이너 레지스트리에 비공개로 연결](../container-registry/container-registry-private-link.md)에서 설명한 대로 Azure Private Link를 사용합니다.

추가 환경 및 사용 사례를 지원하기 위한 지속적인 노력의 일환으로 Azure Defender는 이제 [Azure Private Link](../private-link/private-link-overview.md)로 보호되는 컨테이너 레지스트리도 검색합니다.


### <a name="security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview"></a>이제 Security Center Azure Policy 게스트 구성 확장을 자동으로 프로비전할 수 있습니다(미리 보기)
Azure Policy는 Azure 및 Arc Connected Machines에서 실행되는 머신 모두에 대해 컴퓨터 내부의 설정을 감사할 수 있습니다. 게스트 구성 확장 및 클라이언트가 유효성 검사를 수행합니다. [Azure Policy 게스트 구성 이해](../governance/policy/concepts/guest-configuration.md)에서 자세히 알아보세요.

이 업데이트를 사용하면 지원되는 모든 컴퓨터에 이 확장을 자동으로 프로비전하도록 Security Center 설정할 수 있습니다. 

:::image type="content" source="media/release-notes/auto-provisioning-guest-configuration.png" alt-text="게스트 구성 확장의 자동 배포를 사용하도록 설정합니다.":::

[에이전트 및 확장에 대한 자동 프로비저닝 구성](enable-data-collection.md)에서 자동 프로비저닝의 작동 방식에 대해 자세히 알아봅니다.

### <a name="recommendations-to-enable-azure-defender-plans-now-support-enforce"></a>"적용"을 지원하는 Azure Defender 계획을 사용하도록 설정하는 권장 사항
Security Center 새로 만든 리소스가 안전한 방식으로 프로비전되도록 하는 데 도움이 되는 두 가지 기능인 **강제 적용** 및 **거부** 가 포함되어 있습니다. 권장 사항에서 이러한 옵션을 제공하는 경우, 누군가가 리소스를 생성하려고 할 때마다 보안 요구 사항이 충족되는지 확인할 수 있습니다.

- **거부** 는 비정상 리소스 생성을 중지합니다
- **강제 적용** 은 리소스를 생성할 때 자동으로 수정을 적용합니다

이 업데이트를 사용하면 권장 사항에서 적용 옵션을 사용하여 Azure Defender 계획을 사용하도록 설정할 수 있습니다(예: **App Service용 Azure Defender를 사용하도록 설정해야 하고**, **Key Vault용 Azure Defender 사용하도록 설정해야 하고**, **Storage Azure Defender를 사용하도록 설정해야 합니다**).

[강제 적용/거부 추천 사항을 사용하여 구성 오류 방지](prevent-misconfigurations.md)에서 자세히 알아보세요.

### <a name="csv-exports-of-recommendation-data-now-limited-to-20-mb"></a>권장 사항 데이터의 CSV 내보내기는 이제 20MB로 제한됩니다

Security Center 권장 사항 데이터를 내보낼 때 20MB로 제한됩니다.

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="권장 사항 데이터를 내보내는 Security Center의 'CSV 보고서 다운로드' 단추입니다.":::

더 많은 양의 데이터를 내보내야 하는 경우 선택하기 전에 사용 가능한 필터를 사용하거나 구독의 하위 집합을 선택하고 데이터를 일괄 처리로 다운로드합니다.

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="Azure Portal에서 구독 필터링.":::

[보안 권장 사항의 CSV 내보내기를 수행하는 방법](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations)에 대해 자세히 알아봅니다.



### <a name="recommendations-page-now-includes-multiple-views"></a>이제 권장 사항 페이지에 여러 보기가 포함됩니다

이제 권장 사항 페이지에는 리소스와 관련된 권장 사항을 볼 수 있는 대체 방법을 제공하는 두 개의 탭이 있습니다.

- **보안 점수 권장 사항** - 이 탭을 사용하여 보안 제어별로 그룹화되는 권장 사항 목록을 볼 수 있습니다. [보안 컨트롤 및 해당 권장 사항](secure-score-security-controls.md#security-controls-and-their-recommendations)에서 이러한 컨트롤에 대해 자세히 알아봅니다.
- **모든 권장 사항** - 이 탭을 사용하여 권장 사항 목록을 플랫 목록으로 볼 수 있습니다. 이 탭은 권장 사항을 생성한 이니셔티브(규정 준수 표준 포함)를 이해하는 데에도 적합합니다. [보안 정책, 이니셔티브 및 권장 사항이란?](security-policy-concept.md)에서 이니셔티브 및 권장 사항과의 관계에 대해 자세히 알아보세요.

:::image type="content" source="media/release-notes/recommendations-tabs.png" alt-text="Azure Security Center 권장 사항 목록의 보기를 변경하는 탭.":::

## <a name="july-2021"></a>2021년 7월

7월의 업데이트는 다음과 같습니다.

- [이제 Azure Sentinel 커넥터에 선택적 양방향 경고 동기화가 포함됩니다(미리 보기)](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview)
- [Resource Manager 경고에 대한 Azure Defender의 논리적 재구성](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                           
- [ADE(Azure Disk Encryption)를 사용하도록 권장 사항 향상](#enhancements-to-recommendation-to-enable-azure-disk-encryption-ade)                                     
- [GA(일반 공급)를 위해 릴리스된 보안 점수 및 규정 준수 데이터의 연속 내보내기](#continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga)
- [규정 준수 평가 변경 사항으로 워크플로 자동화가 트리거될 수 있습니다(GA)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga)
- [이제 작업 영역 스키마 및 논리 앱에서 평가 API 필드 'FirstEvaluationDate' 및 'StatusChangeDate'를 사용할 수 있습니다](#assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps)
- [Azure Monitor 통합 문서 갤러리에 추가된 '시간에 따른 규정 준수' 통합 문서 템플릿](#compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery)

### <a name="azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview"></a>이제 Azure Sentinel 커넥터에 선택적 양방향 경고 동기화가 포함됩니다(미리 보기)

Security Center는 기본적으로 Azure의 클라우드 네이티브 SIEM 및 SOAR 솔루션인 [Azure Sentinel](../sentinel/index.yml)과 통합됩니다. 

Azure Sentinel에는 구독 및 테넌트 수준에서 Azure Security Center에 대한 기본 제공 커넥터가 포함되어 있습니다. [Azure Sentinel로 경고 스트리밍](export-to-siem.md#stream-alerts-to-microsoft-sentinel)에서 자세히 알아보세요.

Azure Defender를 Azure Sentinel에 연결하면 Azure Sentinel로 수집된 Azure Defender 경고의 상태가 두 서비스 간에 동기화됩니다. 예를 들어 Azure Defender에서 경고가 닫히면 해당 경고는 Azure Sentinel에서도 닫힘으로 표시됩니다. Azure Defender에서 경고 상태를 변경하면 동기화된 Azure Sentinel 경고를 포함하는 Azure Sentinel **인시던트** 의 상태에 영향을 미치지 "않고"* 동기화된 경고 자체에만 영향을 미칩니다.

미리 보기 기능인 **양방향 경고 동기화** 를 사용하면 원래 Azure Defender 경고의 상태가 해당 Azure Defender 경고의 복사본이 포함된 Azure Sentinel 인시던트와 자동으로 동기화됩니다. 따라서, 예를 들어 Azure Defender 경고가 포함된 Azure Sentinel 인시던트가 닫히면 Azure Defender가 해당 원본 경고를 자동으로 닫습니다.

자세한 내용은 [Azure Security Center에서 Azure Defender 경고 연결](../sentinel/connect-azure-security-center.md)을 참조하세요.

### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>Resource Manager 경고에 대한 Azure Defender의 논리적 재구성

아래에 나열된 경고는 [Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md) 플랜의 일부로 제공됩니다.

일부 Azure Defender 플랜에 대한 논리적 재구성의 일환으로 **Azure Defender for Resource Manager** 에서 **서버용 Azure Defender** 로 일부 경고를 이동하고 있습니다.

경고는 다음과 같은 두 가지 주요 원칙에 따라 구성됩니다.

- 여러 Azure 리소스 유형에서 제어 평면 보호를 제공하는 경고는 Azure Defender for Resource Manager에 포함될 예정입니다
- 특정 워크로드를 보호하는 경고는 해당 워크로드와 관련된 해당 Azure Defender 플랜으로 이동됩니다

이러한 경고는 Azure Defender for Resource Manager에 속하며, 이러한 변경의 결과로 서버용 Azure Defender로 이동됩니다.

- ARM_AmBroadFilesExclusion
- ARM_AmDisablementAndCodeExecution
- ARM_AmDisablement
- ARM_AmFileExclusionAndCodeExecution
- ARM_AmTempFileExclusionAndCodeExecution
- ARM_AmTempFileExclusion
- ARM_AmRealtimeProtectionDisabled
- ARM_AmTempRealtimeProtectionDisablement
- ARM_AmRealtimeProtectionDisablementAndCodeExec
- ARM_AmMalwareCampaignRelatedExclusion
- ARM_AmTemporarilyDisablement
- ARM_UnusualAmFileExclusion
- ARM_CustomScriptExtensionSuspiciousCmd
- ARM_CustomScriptExtensionSuspiciousEntryPoint
- ARM_CustomScriptExtensionSuspiciousPayload
- ARM_CustomScriptExtensionSuspiciousFailure
- ARM_CustomScriptExtensionUnusualDeletion
- ARM_CustomScriptExtensionUnusualExecution
- ARM_VMAccessUnusualConfigReset
- ARM_VMAccessUnusualPasswordReset
- ARM_VMAccessUnusualSSHReset

[Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md) 및 [서버용 Azure Defender](defender-for-servers-introduction.md) 계획에 대해 자세히 알아봅니다.


### <a name="enhancements-to-recommendation-to-enable-azure-disk-encryption-ade"></a>ADE(Azure Disk Encryption)를 사용하도록 권장 사항 향상

사용자 피드백에 따라 **가상 머신에 디스크 암호화를 적용해야 한다** 는 권장 사항의 이름을 변경했습니다.

새 권장 사항은 동일한 평가 ID를 사용하며 **가상 머신은 컴퓨팅과 Storage 리소스 간의 임시 디스크, 캐시 및 데이터 흐름을 암호화해야 합니다**.

이 권장 사항의 용도를 더 잘 설명하기 위해 설명도 업데이트되었습니다.

| 권장                                                                                               | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 심각도 |
|--------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------:|
| **가상 머신은 Compute 및 Storage 리소스 간에 임시 디스크, 캐시 및 데이터 흐름을 암호화해야 합니다.** | 기본적으로 가상 머신의 OS 및 데이터 디스크는 플랫폼 관리형 키를 사용하여 암호화되어 있습니다. 임시 디스크 및 데이터 캐시는 암호화되지 않으며 컴퓨팅 리소스와 스토리지 리소스 간에 흐르는 경우 데이터가 암호화되지 않습니다. Azure의 다양한 디스크 암호화 기술을 비교하는 내용은 https://aka.ms/diskencryptioncomparison 을 참조하세요.<br>Azure Disk Encryption을 사용하여 이 모든 데이터를 암호화합니다. (1) 호스트에서 암호화 기능을 사용하거나 (2) Managed Disks 서버 쪽 암호화가 보안 요구 사항을 충족하는 경우, 이 권장 사항을 무시합니다. Azure Disk Storage의 서버 쪽 암호화에서 자세히 알아봅니다. | 높음     |
|                                                                                                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |          |


### <a name="continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga"></a>GA(일반 공급)를 위해 릴리스된 보안 점수 및 규정 준수 데이터의 연속 내보내기

[연속 내보내기](continuous-export.md)는 사용자 환경의 다른 모니터링 도구를 사용하여 추적하기 위한 보안 경고 및 권장 사항을 내보내는 메커니즘을 제공합니다.

연속 내보내기 설정 시, 내보내기되는 항목과 해당 내보내기 위치를 구성합니다. [연속 내보내기 개요](continuous-export.md)에서 자세히 알아보세요.

시간이 지남에 따라 이 기능이 향상되고 확장되었습니다.

- 2020년 11월에 **보안 점수** 에 변경 내용을 스트리밍하는 **미리 보기** 옵션을 추가했습니다.<br/>자세한 내용은 [이제 연속 내보내기에서 보안 점수를 사용할 수 있습니다(미리 보기)](release-notes-archive.md#secure-score-is-now-available-in-continuous-export-preview)를 참조하세요.

- 2020년 12월에 **규정 준수 평가 데이터** 에 변경 내용을 스트리밍하는 **미리 보기** 옵션을 추가했습니다.<br/>자세한 내용은 [연속 내보내기에서 새 데이터 형식을 가져옵니다(미리 보기)](release-notes-archive.md#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)를 참조하세요.

이 업데이트에서는 이러한 두 가지 옵션이 GA(일반 공급)를 위해 릴리스됩니다. 


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga"></a>규정 준수 평가 변경 사항으로 워크플로 자동화가 트리거될 수 있습니다(GA)

2021년 2월에, 워크플로 자동화를 위한 트리거 옵션에 세 번째 데이터 유형인 규정 준수 평가 변경 사항 **미리보기** 를 추가했습니다. [규정 준수 평가 변경 사항으로 워크플로 자동화가 트리거될 수 있음(미리 보기)](release-notes-archive.md#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)에서 자세히 알아보세요.

이 업데이트를 사용하면 GA(일반 공급)를 위해 이 트리거 옵션이 릴리스됩니다.

[Security Center 트리거에 대한 응답 자동화](workflow-automation.md)에서 워크플로 자동화 도구를 사용하는 방법에 대해 알아봅니다.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="규정 준수 평가에 대한 변경 사항을 사용하여 워크플로 자동화를 트리거합니다." lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

### <a name="assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps"></a>이제 작업 영역 스키마 및 논리 앱에서 평가 API 필드 'FirstEvaluationDate' 및 'StatusChangeDate'를 사용할 수 있습니다

2021년 5월에 평가 API를 **FirstEvaluationDate** 및 **StatusChangeDate** 라는 두 개의 새 필드로 업데이트했습니다. [평가 API가 두 개의 새 필드로 확장됨](release-notes-archive.md#assessments-api-expanded-with-two-new-fields)에서 자세히 알아보세요.

이러한 필드는 REST API, Azure Resource Graph, 연속 내보내기 및 CSV 내보내기에서 액세스할 수 있었습니다.

이 변경으로 Log Analytics 작업 영역 스키마 및 논리 앱에서 정보를 사용할 수 있습니다.


### <a name="compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery"></a>Azure Monitor 통합 문서 갤러리에 추가된 '시간에 따른 규정 준수' 통합 문서 템플릿

3월에는 Security Center 통합 Azure Monitor 통합 문서 환경을 발표했습니다([Security Center에 통합된 Azure Monitor 통합 문서 및 제공된 템플릿 3개](release-notes-archive.md#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided) 참조).

초기 릴리스에는 조직의 보안 태세에 대한 동적 및 시각적 보고서를 작성하는 세 가지 템플릿이 포함되어 있습니다.

이제 구독이 적용되는 규정 또는 업계 표준을 준수하는지 추적하기 위한 통합 문서가 추가되었습니다. 

이러한 보고서를 사용하는 방법 또는 [Security Center 데이터에 대한 풍부한 자체 대화형 보고서](custom-dashboards-azure-workbooks.md)를 작성하는 방법에 대해 알아보세요.

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="시간 통합 문서에 따른 Azure Security Center 규정 준수":::


## <a name="june-2021"></a>2021년 6월

6월의 업데이트는 다음과 같습니다.

- [Key Vault용 Azure Defender의 새 경고](#new-alert-for-azure-defender-for-key-vault)
- [CMK(고객 관리형 키)를 사용한 암호화에 대한 권장 사항이 기본적으로 사용하지 않도록 설정됨](#recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default)
- [Kubernetes 경고의 접두사가 "AKS_"에서 "K8S_"로 변경됨](#prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_)
- ["시스템 업데이트 적용" 보안 제어의 두 가지 권장 사항이 더 이상 사용되지 않음](#deprecated-two-recommendations-from-apply-system-updates-security-control)


### <a name="new-alert-for-azure-defender-for-key-vault"></a>Key Vault용 Azure Defender의 새 경고

Key Vault용 Azure Defender에서 제공하는 위협 방지 기능을 확장하기 위해 다음과 같은 경고를 추가했습니다.

| 경고(경고 유형)                                                                 | Description                                                                                                                                                                                                                                                                                                                                                      | MITRE 전술 | 심각도 |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| 의심스러운 IP 주소에서 Key Vault에 액세스<br>(KV_SuspiciousIPAccess)  | Microsoft 위협 인텔리전스에서 의심스러운 IP 주소로 식별된 IP를 통해 Key Vault에 액세스했습니다. 이것은 인프라가 손상되었음을 나타낼 수 있습니다. 추가 조사가 권장됩니다. [Microsoft 위협 인텔리전스 기능](https://go.microsoft.com/fwlink/?linkid=2128684)에 대해 자세히 알아보세요. | 자격 증명 액세스                            | 중간   |
|||

자세한 내용은 다음을 참조하세요.
- [Azure Defender for Key Vault 소개](defender-for-resource-manager-introduction.md)
- [Key Vault용 Azure Defender 경고에 응답](defender-for-key-vault-usage.md)
- [Key Vault용 Azure Defender에서 제공하는 경고 목록](alerts-reference.md#alerts-azurekv)


### <a name="recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default"></a>CMK(고객 관리형 키)를 사용한 암호화에 대한 권장 사항이 기본적으로 사용하지 않도록 설정됨

Security Center는 고객 관리형 키를 사용하여 미사용 데이터를 암호화하기 위한 다음과 같은 여러 권장 사항을 포함합니다.

- 컨테이너 레지스트리는 CMK(고객 관리형 키)로 암호화해야 함
- Azure Cosmos DB 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함
- Azure Machine Learning 작업 영역은 CMK(고객 관리형 키)를 사용하여 암호화해야 함

Azure의 데이터는 플랫폼 관리형 키를 사용하여 자동으로 암호화되므로 고객 관리형 키는 조직에서 적용하도록 선택한 특정 정책을 준수하는 데 필요한 경우에만 사용해야 합니다.

이와 같이 변경되면서 CMK를 사용하기 위한 권장 사항이 이제 **기본적으로 사용하지 않도록 설정** 됩니다. 조직과 관련된 경우 해당 보안 정책에 대한 *Effect* 매개 변수를 **AuditIfNotExists** 또는 **Enforce** 로 변경하여 사용하도록 설정할 수 있습니다. [보안 정책 사용](tutorial-security-policy.md#enable-a-security-policy)에서 자세히 알아보세요.

이 변경 내용은 다음 예제와 같이 새 접두사 **[필요한 경우 사용]** 를 사용하여 권장 사항 이름에 반영됩니다.

- [필요한 경우 사용] 스토리지 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함
- [필요한 경우 사용] Container Registry를 CMK(고객 관리형 키)로 암호화해야 함
- [필요한 경우 사용] Azure Cosmos DB 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함

:::image type="content" source="media/upcoming-changes/customer-managed-keys-disabled.png" alt-text="Security Center의 CMK 권장 사항은 기본적으로 사용하지 않도록 설정됩니다." lightbox="media/upcoming-changes/customer-managed-keys-disabled.png":::


### <a name="prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_"></a>Kubernetes 경고의 접두사가 "AKS_"에서 "K8S_"로 변경됨

Azure Defender for Kubernetes는 최근에 확장 되어 온-프레미스 및 다중 클라우드 환경에서 호스트 되는 Kubernetes 클러스터를 보호 합니다. [Azure Defender for Kubernetes를 사용하여 하이브리드 및 다중 클라우드 Kubernetes 배포 보호(미리 보기)](release-notes-archive.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)에서 자세히 알아봅니다.

Azure Defender for Kubernetes에서 제공하는 보안 경고가 더 이상 Azure Kubernetes Service의 클러스터로 제한되지 않는 사실을 반영하기 위해 경고 유형의 접두사를 "AKS_"에서 "K8S_"로 변경했습니다. 필요한 경우 이름과 설명도 업데이트되었습니다. 예를 들면 다음과 같습니다.

|경고(경고 유형)|Description|
|----|----|
|Kubernetes 침투 테스트 도구가 감지됨<br>(**AKS** _PenTestToolsKubeHunter)|Kubernetes 감사 로그를 분석한 결과, **AKS** 클러스터에서 Kubernetes 침투 테스트 도구의 사용이 감지되었습니다. 이 동작은 합법적일 수 있지만, 공격자가 이러한 공용 도구를 악의적인 목적으로 사용할 수 있습니다.
|||

이 내용이 다음으로 변경되었습니다.

|경고(경고 유형)|Description|
|----|----|
|Kubernetes 침투 테스트 도구가 감지됨<br>(**K8S** _PenTestToolsKubeHunter)|Kubernetes 감사 로그를 분석한 결과, **Kubernetes** 클러스터에서 Kubernetes 침투 테스트 도구의 사용이 감지되었습니다. 이 동작은 합법적일 수 있지만, 공격자가 이러한 공용 도구를 악의적인 목적으로 사용할 수 있습니다.|
|||

"AKS_"로 시작하는 경고를 참조하는 모든 제거 규칙은 자동으로 변환되었습니다. SIEM 내보내기 또는 경고 유형별 Kubernetes 경고를 참조하는 사용자 지정 자동화 스크립트를 설정한 경우 새 경고 유형으로 업데이트해야 합니다.

Kubernetes 경고의 전체 목록은 [Kubernetes 클러스터에 대한 경고](alerts-reference.md#alerts-k8scluster)를 참조하세요.

### <a name="deprecated-two-recommendations-from-apply-system-updates-security-control"></a>"시스템 업데이트 적용" 보안 제어의 두 가지 권장 사항이 더 이상 사용되지 않음

다음 두 가지 권장 사항은 더 이상 사용되지 않습니다.

- **클라우드 서비스 역할에 대해 OS 버전을 업데이트해야 함** - 기본적으로 Azure는 Windows Server 2016과 같이 게스트 OS를 서비스 구성(.cscfg)에서 지정한 OS 제품군 내에서 지원되는 최신 이미지로 주기적으로 업데이트합니다.
- **Kubernetes 서비스를 취약하지 않은 Kubernetes 버전으로 업그레이드해야 함** - 이 권장 사항의 평가는 원하는 만큼 광범위하지 않습니다. 권장 사항을 보안 요구 사항에 부합하는 향상된 버전으로 바꿀 예정입니다.
