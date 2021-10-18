---
title: Azure Defender 개요 및 사용 가능한 플랜
description: Azure Defender의 플랜, 보호 및 경고에 대해 알아봅니다. 그런 다음, 고급 보안을 위해 구독에서 Azure Defender를 사용하도록 설정합니다.
author: memildin
ms.author: memildin
ms.date: 10/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 34d450535b25b8d3ba1bcc69b54a27d6aab73115
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129715394"
---
# <a name="introduction-to-azure-defender"></a>Azure Defender 소개

Azure Security Center의 기능은 다음과 같은 클라우드 보안의 두 가지 핵심 요소를 지원합니다.

- **CSPM(클라우드 보안 태세 관리)** - Security Center은 모든 Azure 사용자가 **무료** 로 사용할 수 있습니다. 무료 체험에는 보안 점수, Azure 머신에서 잘못된 보안 구성 검색, 자산 인벤토리 등과 같은 CSPM 기능이 포함됩니다. 이러한 CSPM 기능을 사용하여 하이브리드 클라우드 태세를 강화하고 기본 제공 정책의 준수 여부를 추적합니다.

- **CWP(클라우드 워크로드 보호)** - Security Center의 통합 CWPP(클라우드 워크로드 보호 플랫폼)인 **Azure Defender** 는 Azure 및 하이브리드 리소스 및 워크로드에 고급 인텔리전트 보호 기능을 제공합니다. Azure Defender를 사용하면 이 페이지에 설명된 대로 다양한 추가 보안 기능이 제공됩니다. 기본 제공 정책 외에도 Azure Defender 플랜을 사용하는 경우 사용자 지정 정책 및 이니셔티브를 추가할 수 있습니다. 규정 준수에 대한 진정한 사용자 지정 보기를 위해 NIST 및 Azure CIS와 같은 규제 표준과 [Azure Security Benchmark](/security/benchmark/azure/introduction)를 추가할 수 있습니다.

Security Center의 Azure Defender 대시보드는 사용자 환경에 대한 CWP 기능의 가시성 및 제어 기능을 제공합니다.

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Azure Defender 대시보드의 예입니다." lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Azure Defender가 보호할 수 있는 리소스 종류는 무엇입니까?

Azure Defender는 가상 머신, SQL 데이터베이스, 컨테이너, 웹 애플리케이션, 네트워크 등에 대한 보안 경고 및 고급 위협 방지 기능을 제공합니다.

Azure Security Center의 **가격 책정 및 설정** 영역에서 Azure Defender를 사용하도록 설정하면 다음 Defender 플랜이 동시에 사용되어 환경의 컴퓨팅, 데이터 및 서비스 계층에 대한 포괄적인 방어 기능을 제공합니다.

- [서버용 Azure Defender](defender-for-servers-introduction.md)
- [App Service용 Azure Defender](defender-for-app-service-introduction.md)
- [스토리지용 Azure Defender](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [Kubernetes용 Azure Defender](defender-for-kubernetes-introduction.md)
- [컨테이너 레지스트리용 Azure Defender](defender-for-container-registries-introduction.md)
- [Key Vault용 Azure Defender](defender-for-key-vault-introduction.md)
- [Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md)
- [Azure Defender for DNS](defender-for-dns-introduction.md)
- [오픈 소스 관계형 데이터베이스용 Azure Defender](defender-for-databases-introduction.md)

각 플랜은 Security Center 설명서에 별도로 설명되어 있습니다.

> [!TIP]
> Azure Defender for IoT(미리 보기)는 별도의 제품입니다. 모든 세부 정보는 [Azure Defender for IoT(미리 보기) 소개](../defender-for-iot/overview.md)에서 찾을 수 있습니다. 

## <a name="hybrid-cloud-protection"></a>하이브리드 클라우드 보호

Azure 환경을 방어할 수 있을 뿐 아니라, 하이브리드 클라우드 환경에 다음과 같은 Azure Defender 기능을 추가할 수 있습니다.

- 비 Azure 서버 보호
- 다른 클라우드의 가상 머신 보호(예: AWS 및 GCP)

환경에 따라 사용자 지정된 위협 인텔리전스 및 우선 순위가 지정된 경고가 제공되므로 가장 중요한 문제에 집중할 수 있습니다.

다른 클라우드 또는 온-프레미스에 있는 가상 머신 및 SQL 데이터베이스로 보호 범위를 확장하려면 [Azure Arc](https://azure.microsoft.com/services/azure-arc/)를 배포하고 Azure Defender를 사용합니다. 서버용 Azure Arc는 무료 서비스이지만, Azure Defender처럼 Azure Arc 지원 서버에서 사용되는 서비스는 해당 서비스의 가격 책정에 따라 요금이 청구됩니다. [Azure Arc를 사용하여 비 Azure 머신 추가](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc)에서 자세히 알아보세요.

> [!TIP]
> AWS용 기본 커넥터는 Azure Arc 배포를 투명하게 처리합니다. [Azure Security Center에 AWS 계정 연결](quickstart-onboard-aws.md)에서 자세히 알아보세요.



## <a name="azure-defender-security-alerts"></a>Azure Defender 보안 경고 

Azure Defender는 환경의 모든 영역에서 위협을 탐지하면 보안 경고를 생성합니다. 이러한 경고는 영향을 받는 리소스의 세부 정보, 제안된 수정 단계 및 경우에 따라 응답으로 논리 앱을 트리거하는 옵션을 설명합니다.

Security Center가 경고를 직접 생성하든 아니면 통합된 보안 제품으로부터 경고를 수신하든, 경고를 내보낼 수 있습니다. 경고를 Azure Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM, SOAR 또는 IT 서비스 관리 솔루션에 대한 경고 스트리밍](export-to-siem.md)의 지침을 따릅니다.

> [!NOTE]
> 서로 다른 원본의 경고가 표시되는 데 각각 다른 시간이 걸릴 수 있습니다. 예를 들어 네트워크 트래픽을 분석해야 하는 경고를 표시하는 경우 가상 머신에서 실행되는 의심스러운 프로세스와 관련된 경고보다 더 오래 걸릴 수 있습니다.


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender 고급 보호 기능

Azure Defender는 고급 분석을 사용하여 리소스와 관련된 맞춤형 권장 사항을 제공합니다. 

보호 기능에는 Just-In-Time 액세스 및 컴퓨터에서 실행되어야 하는 앱과 실행되면 안 되는 앱의 허용 목록을 작성하는 적응형 애플리케이션 제어를 사용하여 VM의 관리 포트를 보호하는 기능이 포함됩니다. 

Azure Defender 대시보드의 고급 보호 타일을 사용하여 이러한 보호 기능을 모니터링하고 구성할 수 있습니다. 

## <a name="vulnerability-assessment-and-management"></a>취약성 평가 및 관리

Azure Defender는 추가 비용 없이 가상 머신과 컨테이너 레지스트리의 취약성을 평가합니다. 일부 스캐너는 Qualys 제품이지만 Qualys 라이선스 또는 Qualys 계정이 필요하지 않습니다. 모든 것이 Security Center 내에서 원활하게 처리됩니다. 

[엔드포인트용 Microsoft Defender와의 통합](security-center-wdatp.md)을 사용하도록 설정한 경우 **Microsoft 위협 및 취약성 관리** 의 취약성 발견에 액세스할 수 있습니다. [엔드포인트용 Microsoft Defender의 위협 및 취약성 관리를 통한 취약성 조사](deploy-vulnerability-assessment-tvm.md)에서 자세히 알아보세요.

Security Center 내에서 이러한 취약성 스캐너의 결과를 검토하고 대응할 수 있습니다. 이와 같이 모든 클라우드 보안 작업을 Security Center에서 거의 대부분 처리할 수 있습니다.

> [!IMPORTANT]
> Security Center와 엔드포인트용 Microsoft Defender의 통합은 기본적으로 사용하도록 설정되어 있습니다. 따라서 Azure Defender를 사용하도록 설정하는 경우 서버용 Azure Defender에서 취약성, 설치된 소프트웨어 및 엔드포인트에 대한 경고와 관련된 엔드포인트용 Microsoft Defender 데이터에 액세스하도록 동의합니다.

다음 페이지에서 자세히 알아보세요.

- [Azure 및 하이브리드 머신을 위한 Azure Defender의 통합 Qualys 스캐너](deploy-vulnerability-assessment-vm.md)
- [Azure 컨테이너 레지스트리의 이미지 취약성 식별](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender의 이점에 대해 알아보았습니다. 

> [!div class="nextstepaction"]
> [Azure Defender 사용](enable-azure-defender.md)
