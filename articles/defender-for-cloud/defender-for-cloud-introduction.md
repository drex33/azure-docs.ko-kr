---
title: Microsoft Defender for Cloud - 소개
description: Microsoft Defender for Cloud를 사용하여 Azure, 하이브리드 및 다중 클라우드 리소스와 워크로드를 보호합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.custom: mvc
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: b809efec6a34179b97f7389cf09662a2600d61b4
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373589"
---
# <a name="what-is-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud란?

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud는 보안 상태 관리 및 위협 방지를 위한 도구입니다. 클라우드 리소스의 보안 상태를 강화하고, 통합된 Microsoft Defender 계획을 통해 Defender for Cloud에서 Azure, 하이브리드 및 기타 클라우드 플랫폼에서 실행되는 워크로드를 보호합니다.

Defender for Cloud는 리소스를 강화하고, 보안 상태를 추적하고, 사이버 공격으로부터 보호하고, 보안 관리를 간소화하는 데 필요한 도구를 제공합니다. Defender for Cloud는 기본적으로 통합되어 있으므로 쉽게 배포할 수 있으며, 기본적으로 리소스를 보호하는 간단한 자동 프로비전을 제공합니다.

Defender for Cloud는 클라우드 및 온-프레미스에서 리소스 및 워크로드의 보안을 관리할 때 세 가지 중요한 요구 사항을 충족합니다.

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-synopsis.png" alt-text="Microsoft Defender for Cloud의 핵심 기능 이해":::

|보안 요구 사항  | Defender for Cloud 솔루션|
|---------|---------|
|**지속적 평가** - 현재 보안 상태를 이해합니다.   | **보안 점수** - 현재 보안 상황을 한 눈에 파악할 수 있는 단일 점수입니다. 점수가 높을수록 식별된 위험 수준이 낮아집니다.       |
|**보안** - 연결된 모든 리소스와 서비스를 강화합니다. | **보안 권장 사항** - 상태를 개선하기 위해 사용자 지정되고 우선 순위가 지정된 강화 작업입니다. 권장 사항에서 제공하는 자세한 수정 단계에 따라 권장 사항을 구현합니다. Defender for Cloud는 많은 권장 사항에서 자동화된 구현에 대한 "수정" 단추를 제공합니다!|
|**방어** - 해당 리소스와 서비스에 대한 위협을 탐지하고 해결합니다. | **보안 경고** - 향상된 보안 기능이 사용하도록 설정되면 Defender for Cloud에서 리소스 및 워크로드에 대한 위협을 탐지합니다. 이러한 경고는 Azure Portal에 표시되며, Defender for Cloud에서 조직의 관련 담당자에게 이메일로 보낼 수도 있습니다. 또한 경고는 필요에 따라 SIEM, SOAR 또는 IT Service Management 솔루션으로 스트림할 수 있습니다. |

## <a name="posture-management-and-workload-protection"></a>상태 관리 및 워크로드 보호

Microsoft Defender for Cloud의 기능은 클라우드 보안의 두 가지 광범위한 핵심 요소인 클라우드 보안 상태 관리와 클라우드 워크로드 보호를 처리합니다.

### <a name="cloud-security-posture-management-cspm"></a>CSPM(클라우드 보안 태세 관리)

Defender for Cloud에서 제공하는 상태 관리 기능은 다음과 같습니다.

- **가시성** - 현재 보안 상황을 이해하는 데 도움이 됩니다.
- **강화 지침** - 보안을 효율적이고 효과적으로 향상시키는 데 도움이 됩니다.

이러한 목표를 달성할 수 있게 하는 Defender for Cloud의 핵심 기능은 **보안 점수** 입니다. Defender for Cloud는 리소스, 구독 및 조직의 보안 문제를 지속적으로 평가합니다. 그런 다음, 현재 보안 상황을 한눈에 파악할 수 있도록 모든 결과를 단일 점수에 집계합니다. 즉, 점수가 높을수록 식별된 위험 수준은 낮습니다.

Defender for Cloud를 처음 열면 가시성 및 강화 목표를 다음과 같이 충족합니다.

1. [Azure Security Benchmark](/security/benchmark/azure/overview)의 지침과 비교하여 연결된 리소스의 평가에 따라 구독에 대한 **보안 점수를 생성** 합니다. 점수를 사용하여 보안 상태를 이해하고, 규정 준수 대시보드를 사용하여 기본 제공 벤치마크에 대한 규정 준수를 검토합니다. 향상된 보안 기능을 사용하도록 설정한 경우 규정 준수를 평가하는 데 사용되는 표준을 사용자 지정하고, 다른 규정(예: NIST 및 Azure CIS) 또는 조직별 보안 요구 사항을 추가할 수 있습니다.

1. 식별된 보안 구성 오류 및 약점에 따라 **강화 권장 사항을 제공** 합니다. 이러한 보안 권장 사항을 사용하여 조직의 Azure, 하이브리드 및 다중 클라우드 리소스의 보안 상태를 강화합니다.

[보안 점수](secure-score-security-controls.md)에 대해 자세히 알아보세요.

### <a name="cloud-workload-protection-cwp"></a>CWP(클라우드 워크로드 보호)

Defender for Cloud는 [Microsoft 위협 인텔리전스](https://go.microsoft.com/fwlink/?linkid=2128684)에서 제공하는 보안 경고를 제공합니다. 또한 워크로드에 대한 다양한 고급 인텔리전트 보호 기능이 포함되어 있습니다. 워크로드 보호는 구독의 리소스 종류와 관련된 Microsoft Defender 계획을 통해 제공됩니다. 예를 들어 **Microsoft Defender for Storage** 를 사용하도록 설정하여 Azure Storage 계정과 관련된 의심스러운 활동에 대한 경고를 받을 수 있습니다.

## <a name="azure-hybrid-and-multi-cloud-protections"></a>Azure, 하이브리드 및 다중 클라우드 보호

Defender for Cloud는 Azure 네이티브 서비스이므로 배포 없이도 많은 Azure 서비스를 모니터링하고 보호합니다.

필요한 경우 Defender for Cloud는 Log Analytics 에이전트를 자동으로 배포하여 보안 관련 데이터를 수집할 수 있습니다. Azure 컴퓨터의 경우 배포가 직접 처리됩니다. 하이브리드 및 다중 클라우드 환경의 경우 Microsoft Defender 요금제는 [Azure Arc](https://azure.microsoft.com/services/azure-arc/)의 도움을 통해 비 Azure 머신으로 확장됩니다. CSPM 기능은 에이전트가 필요 없이 다중 클라우드 머신으로 확장됩니다([다른 클라우드에서 실행되는 리소스 방어](#defend-resources-running-on-other-clouds) 참조).

### <a name="azure-native-protections"></a>Azure 네이티브 보호

Defender for Cloud를 사용하면 다음에서 위협을 탐지할 수 있습니다.

- **Azure PaaS 서비스** - Azure App Service, Azure SQL, Azure Storage 계정 및 기타 데이터 서비스를 포함한 Azure 서비스를 대상으로 하는 위협을 탐지합니다. 또한 Microsoft Defender for Cloud Apps(이전의 Microsoft Cloud App Security)와의 네이티브 통합을 사용하여 Azure 활동 로그에서 변칙 검색을 수행할 수 있습니다.

- **Azure 데이터 서비스** - Defender for Cloud에는 Azure SQL에서 데이터를 자동으로 분류하는 데 도움이 되는 기능이 포함되어 있습니다. Azure SQL 및 Storage 서비스 전체의 잠재적 취약성 평가 및 취약성을 완화하는 방법에 대한 권장 사항을 확인할 수도 있습니다.

- **네트워크** - Defender for Cloud를 사용하면 무차별 암호 대입 공격(brute force attack)에 대한 노출을 제한할 수 있습니다. Just-In-Time VM 액세스를 통해 가상 머신 포트에 대한 액세스를 줄이면 불필요한 액세스를 방지하여 네트워크를 강화할 수 있습니다. 권한 있는 사용자, 허용된 원본 IP 주소 범위 또는 IP 주소에 대해서만, 제한된 시간 동안 선택한 포트에 보안 액세스 정책을 설정할 수 있습니다.

### <a name="defend-your-hybrid-resources"></a>하이브리드 리소스 방어

Azure 환경을 방어하는 것 외에도 Defender for Cloud 기능을 하이브리드 클라우드 환경에 추가하여 비 Azure 서버를 보호할 수 있습니다. 가장 중요한 것에 집중할 수 있도록 특정 환경에 따라 사용자 지정된 위협 인텔리전스와 우선 순위가 지정된 경고를 받게 됩니다.

보호를 온-프레미스 컴퓨터로 확장하려면 [Azure Arc](https://azure.microsoft.com/services/azure-arc/)를 배포하고 Defender for Cloud의 향상된 보안 기능을 사용하도록 설정합니다. [Azure Arc를 사용하여 비 Azure 머신 추가](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc)에서 자세히 알아보세요.

### <a name="defend-resources-running-on-other-clouds"></a>다른 클라우드에서 실행되는 리소스 방어

Defender for Cloud는 다른 클라우드(예: AWS 및 GCP)의 리소스를 보호할 수 있습니다.

예를 들어 [AWS(Amazon Web Services) 계정](quickstart-onboard-aws.md)을 Azure 구독에 연결한 경우 다음과 같은 보호를 사용하도록 설정할 수 있습니다.

- **Defender for Cloud의 CSPM 기능** 은 AWS 리소스로 확장됩니다. 이 에이전트 없는 플랜은 AWS 관련 보안 권장 사항에 따라 AWS 리소스를 평가하며, 평가 결과는 보안 점수에 포함됩니다. 또한 리소스가 AWS(AWS CIS, AWS PCI DSS 및 AWS Foundational 보안 모범 사례)와 관련된 기본 제공 표준을 준수하는지 여부도 평가됩니다. Defender for Cloud의 [자산 인벤토리 페이지](asset-inventory.md)는 Azure 리소스와 함께 AWS 리소스를 관리하는 데 도움이 되는 다중 클라우드 지원 기능입니다.
- **Kubernetes용 Microsoft Defender** 는 컨테이너 위협 탐지 및 고급 방어를 **Amazon EKS Linux 클러스터** 로 확장합니다.
- **서버용 Microsoft Defender** 는 위협 탐지 및 고급 방어를 Windows 및 Linux EC2 인스턴스로 확장합니다. 이 플랜에는 엔드포인트용 Microsoft Defender의 통합 라이선스, 보안 기준 및 OS 수준 평가, 취약성 평가 검사, AAC(적응형 애플리케이션 제어), FIM(파일 무결성 모니터링) 등이 포함됩니다.

[AWS](quickstart-onboard-aws.md) 및 [GCP](quickstart-onboard-gcp.md) 계정을 Microsoft Defender for Cloud에 연결하는 방법에 대해 자세히 알아봅니다.

## <a name="vulnerability-assessment-and-management"></a>취약성 평가 및 관리

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-assess.png" alt-text="Microsoft Defender for Cloud의 평가 기능에 집중":::

Defender for Cloud에는 향상된 보안 기능의 일부로 가상 머신, 컨테이너 레지스트리 및 SQL 서버에 대한 취약성 평가 솔루션이 포함되어 있습니다. 일부 스캐너는 Qualys에서 구동됩니다. 그러나 Qualys 라이선스 또는 Qualys 계정이 필요하지 않습니다. 모든 항목이 Defender for Cloud 내에서 원활하게 처리됩니다.

서버용 Microsoft Defender에는 엔드포인트용 Microsoft Defender와의 자동 네이티브 통합이 포함되어 있습니다. [Defender for Cloud의 통합 EDR 솔루션인 엔드포인트용 Microsoft Defender를 사용하여 엔드포인트 보호](integration-defender-for-endpoint.md)에서 자세히 알아보세요. 이 통합이 사용하도록 설정되면 **Microsoft 위협 및 취약성 관리** 의 취약성 결과에 액세스할 수 있습니다. [엔드포인트용 Microsoft Defender의 위협 및 취약성 관리를 통한 취약성 조사](deploy-vulnerability-assessment-tvm.md)에서 자세히 알아보세요.

Defender for Cloud 내에서 이러한 취약성 스캐너의 결과를 검토하고 모든 결과에 대응합니다. 이 광범위한 접근 방식을 통해 Defender for Cloud는 모든 클라우드 보안 작업을 수행하기 위한 단일 창이 될 수 있습니다.

다음 페이지에서 자세히 알아보세요.

- [Azure 및 하이브리드 머신을 위한 Defender for Cloud의 통합 Qualys 스캐너](deploy-vulnerability-assessment-vm.md)
- [Azure 컨테이너 레지스트리의 이미지 취약성 식별](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)

## <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>권장 제어를 구성하여 보안 최적화 및 개선

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-secure.png" alt-text="Microsoft Defender for Cloud의 '보안' 기능에 집중":::

워크로드가 안전한지 알고 확인하는 것이 보안의 기본 사항이며, 이는 맞춤화된 보안 정책을 구현하는 것에서 시작됩니다. Defender for Cloud의 정책은 Azure Policy 제어를 기반으로 하여 빌드되므로 **세계 최고 수준의 정책 솔루션** 의 전체 범위와 유연성을 얻을 수 있습니다. Defender for Cloud에서는 관리 그룹, 구독 전체 및 테넌트 전체에 대해 실행되는 정책을 설정할 수 있습니다.

Defender for Cloud는 워크로드 전체에 배포되는 새 리소스를 지속적으로 검색하고 보안 모범 사례에 따라 구성되었는지 여부를 평가합니다. 그렇지 않은 경우 플래그가 지정되고 수정해야 하는 항목에 대해 우선 순위가 지정된 권장 사항 목록이 표시됩니다. 권장 사항은 각 리소스에서 공격 표면을 줄이는 데 도움이 됩니다.

권장 사항 목록은 Azure Security Benchmark에서 사용하도록 설정되고 지원됩니다. Microsoft에서 작성한 이 Azure 관련 벤치마크는 일반적인 규정 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대한 일단의 지침을 제공합니다. [Azure Security Benchmark 소개](/security/benchmark/azure/introduction)에서 자세히 알아보세요.

Defender for Cloud를 이 방식으로 사용하면 보안 정책을 설정할 뿐만 아니라 *리소스 전체에 보안 구성 표준을 적용* 할 수도 있습니다.

:::image type="content" source="./media/defender-for-cloud-introduction/recommendation-example.png" alt-text="Defender for Cloud 권장 사항 예":::

각 권장 사항이 전체 보안 상태에 중요한 정도를 이해하는 데 도움이 되도록 Defender for Cloud는 권장 사항을 보안 제어로 그룹화하고 **보안 점수** 값을 각 제어에 추가합니다. 이는 **보안 작업의 우선 순위를 지정** 하는 데 중요합니다.

:::image type="content" source="./media/defender-for-cloud-introduction/sc-secure-score.png" alt-text="Defender for Cloud 보안 점수":::

## <a name="defend-against-threats"></a>위협 방어

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-defend.png" alt-text="Microsoft Defender for Cloud의 '방어' 기능에 집중":::

Defender for Cloud는 다음을 제공합니다.

- **보안 경고** - Defender for Cloud는 환경의 모든 영역에서 위협을 탐지하면 보안 경고를 생성합니다. 이러한 경고는 영향을 받는 리소스의 세부 정보, 제안된 수정 단계 및 경우에 따라 응답으로 논리 앱을 트리거하는 옵션을 설명합니다. Defender for Cloud에서 경고를 생성하는지, 아니면 Defender for Cloud에서 경고를 통합 보안 제품으로부터 받는지 여부에 관계없이 내보낼 수 있습니다. 경고를 Microsoft Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM, SOAR 또는 IT Service Management 솔루션으로 경고 스트리밍](export-to-siem.md)의 지침을 따릅니다. Defender for Cloud의 위협 방지에는 사이버 킬 체인 분석에 따라 환경에서 경고의 상관 관계를 자동으로 지정하는 융합 킬 체인 분석이 포함되어 공격 캠페인의 전체 스토리, 시작 위치 및 리소스에 대한 영향의 종류를 더 잘 이해할 수 있습니다. [Defender for Cloud에서 지원하는 킬 체인 의도는 MITRE ATT&CK 행렬 버전 7을 기반으로 합니다](alerts-reference.md#intentions).

- 가상 머신, SQL 데이터베이스, 컨테이너, 웹 애플리케이션, 네트워크 등에 대한 위한 **고급 위협 방지 기능** - 이 방지 기능에는 [Just-In-Time 액세스](just-in-time-access-overview.md)를 사용하여 VM의 관리 포트를 보호하고 [적응형 애플리케이션 제어](adaptive-application-controls.md)를 사용하여 컴퓨터에서 실행해야 하는 앱과 실행하지 않아야 하는 앱에 대한 허용 목록을 만들 수 있는 기능이 포함됩니다.

Microsoft Defender for Cloud의 **Defender 계획** 페이지에서 제공하는 환경의 컴퓨팅, 데이터 및 서비스 계층에 대한 포괄적인 방어 계획은 다음과 같습니다.

- [서버용 Microsoft Defender](defender-for-servers-introduction.md)
- [Microsoft Defender for App Service](defender-for-app-service-introduction.md)
- [스토리지용 Microsoft Defender](defender-for-storage-introduction.md)
- [Microsoft Defender for SQL](defender-for-sql-introduction.md)
- [Microsoft Defender for Kubernetes](defender-for-kubernetes-introduction.md)
- [컨테이너 레지스트리용 Microsoft Defender](defender-for-container-registries-introduction.md)
- [Microsoft Defender for Key Vault](defender-for-key-vault-introduction.md)
- [Microsoft Defender for Resource Manager](defender-for-resource-manager-introduction.md)
- [Microsoft Defender for DNS](defender-for-dns-introduction.md)
- [오픈 소스 관계형 데이터베이스용 Microsoft Defender](defender-for-databases-introduction.md)

[워크로드 보호 대시보드](workload-protections-dashboard.md)의 고급 보호 타일을 사용하여 이러한 보호를 각각 모니터링하고 구성합니다.

> [!TIP]
> Microsoft Defender for IoT는 별도의 제품입니다. 모든 세부 정보는 [Microsoft Defender for IoT 소개](../defender-for-iot/overview.md)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Defender for Cloud를 시작하려면 Microsoft Azure에 대한 구독이 필요합니다. 구독이 없는 경우 [평가판에 가입](https://azure.microsoft.com/free/)하세요.

- Defender for Cloud의 무료 요금제는 Azure Portal에서 Defender for Cloud 페이지를 처음 방문하거나 REST API를 통해 프로그래밍 방식으로 사용하도록 설정된 경우 현재의 모든 Azure 구독에서 사용하도록 설정됩니다. 고급 보안 관리 및 위협 탐지 기능을 활용하려면 향상된 보안 기능을 사용하도록 설정해야 합니다. 이러한 기능은 처음 30일 동안 무료입니다. [가격 책정에 대해 자세히 알아보세요](https://azure.microsoft.com/pricing/details/security-center/).

- 이제 향상된 보안 기능을 사용하도록 설정할 준비가 되었으면 [빠른 시작: 향상된 보안 기능 사용](enable-enhanced-security.md)에서 단계를 안내합니다.

> [!div class="nextstepaction"]
> [Microsoft Defender 계획 사용](enable-enhanced-security.md)
