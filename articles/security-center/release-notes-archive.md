---
title: Azure Security Center의 새로운 기능 보관
description: 6개월 전 또는 그 이전의 Azure Security Center의 새로운 기능 및 변경된 기능에 대한 설명입니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 10/03/2021
ms.author: memildin
ms.openlocfilehash: 516fa13a24c9cb5376bc6e8d38eeb1fc077f1d57
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713949"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Azure Security Center의 새로운 기능 보관

기본 [Azure Security Center의 새로운 기능](release-notes.md) 릴리스 정보 페이지에는 지난 6개월 동안의 업데이트가 포함되어 있지만 이 페이지에는 더 오래된 항목도 포함되어 있습니다.

이 페이지에서는 다음에 대한 정보를 제공합니다.

- 새로운 기능
- 버그 수정
- 사용되지 않는 기능


## <a name="april-2021"></a>2021년 4월

4월의 업데이트는 다음과 같습니다.
- [새로 고친 리소스 상태 페이지(미리 보기)](#refreshed-resource-health-page-in-preview)
- [최근 풀한 컨테이너 레지스트리 이미지가 매주 다시 검색됩니다(GA(일반 공급) 용으로 릴리스됨)](#container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga)
- [Azure Defender for Kubernetes를 사용하여 하이브리드 및 다중 클라우드 Kubernetes 배포 보호(미리 보기)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)
- [이제 Azure Defender와 Microsoft Defender for Endpoint의 통합을 통해 GA(일반 공급)용으로 릴리스된 Windows Server 2019 및 Windows 10 Virtual Desktop(WVD)이 지원됩니다](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga)
- [DNS 및 Resource Manager용 Azure Defender를 사용하도록 설정하는 권장 사항(미리 보기)](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview)
- [Azure CIS 1.3.0, CMMC Level 3 및 뉴질랜드 ISM 제한됨 등 3가지 규정 준수 표준이 추가됨](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [게스트 구성과 관련된 4가지 새로운 권장 사항(미리 보기)](#four-new-recommendations-related-to-guest-configuration-in-preview)
- [CMK 권장 사항이 모범 사례 보안 제어로 이동했습니다.](#cmk-recommendations-moved-to-best-practices-security-control)
- [더 이상 사용되지 않는 11가지 Azure Defender 경고](#11-azure-defender-alerts-deprecated)
- ["시스템 업데이트 적용" 보안 제어에서 더 이상 사용되지 않는 두 가지 권장 사항](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [Azure Defender 대시보드에서 제거되는 머신 타일의 Azure Defender for SQL](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)
- [보안 제어 간에 권장 사항 21개 이동](#21-recommendations-moved-between-security-controls)

### <a name="refreshed-resource-health-page-in-preview"></a>새로 고친 리소스 상태 페이지(미리 보기)

Security Center의 리소스 상태가 단일 리소스의 전반적인 상태에 대한 스냅샷 보기를 제공하도록 확장되고 향상되고 개선되었습니다. 

리소스에 대한 자세한 정보와 해당 리소스에 적용되는 모든 권장 사항을 검토할 수 있습니다. 또한 [Azure Defender](azure-defender.md)를 사용하는 경우 해당 특정 리소스의 미해결 보안 경고도 볼 수 있습니다.

리소스에 대한 리소스 상태 페이지를 열려면 [자산 인벤토리 페이지](asset-inventory.md)에서 리소스를 선택합니다.

Security Center 포털 페이지의 이 미리 보기 페이지에는 다음이 표시됩니다.

1. **리소스 정보** - 리소스 그룹 및 연결된 구독, 지리적 위치 등입니다.
1. **적용된 보안 기능** - 리소스에 Azure Defender가 설정되어 있는지 여부입니다.
1. **미해결 권장 사항 및 경고 수** - 미해결 보안 권장 사항 및 Azure Defender 경고의 수입니다.
1. **실행 가능한 권장 사항 및 경고** - 리소스에 적용되는 권장 사항 및 경고를 나열하는 두 개의 탭입니다.

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="가상 머신의 상태 정보를 보여 주는 Azure Security Center의 리소스 상태 페이지":::

[자습서: 리소스 상태 조사](investigate-resource-health.md)에서 자세히 알아보세요.


### <a name="container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga"></a>최근 풀한 컨테이너 레지스트리 이미지가 매주 다시 검색됩니다(GA(일반 공급) 용으로 릴리스됨)

컨테이너 레지스트리용 Azure Defender에는 기본 제공 취약성 검사기가 포함되어 있습니다. 이 검사기는 사용자가 레지스트리에 밀어 넣는 이미지 및 지난 30일 이내에 끌어온 이미지를 즉시 검사합니다.

새 취약성은 매일 발견됩니다. 이 업데이트를 사용하면 지난 30일 동안 레지스트리에서 끌어온 컨테이너 이미지가 매주 다시 **검사됩니다**. 이렇게 하면 이미지에서 새로 발견된 취약점이 식별됩니다.

검사 요금은 이미지별로 청구되므로 이러한 다시 검사에는 추가 요금이 부과되지 않습니다.

[컨테이너 레지스트리용 Azure Defender를 사용하여 이미지에서 취약성 검사](defender-for-container-registries-usage.md)에서 이 검사기에 대해 자세히 알아보세요.


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview"></a>Azure Defender for Kubernetes를 사용하여 하이브리드 및 다중 클라우드 Kubernetes 배포 보호(미리 보기)

Azure Defender for Kubernetes는 배포되는 위치에 관계없이 클러스터를 보호하도록 위협 방지 기능을 확장하고 있습니다. 이는 [Azure Arc 지원 Kubernetes](../azure-arc/kubernetes/overview.md) 및 새로운 [확장 기능](../azure-arc/kubernetes/extensions.md)을 통합하여 사용하도록 설정되었습니다. 

비 Azure Kubernetes 클러스터에서 Azure Arc를 사용하도록 설정한 경우 Azure Security Center의 새로운 권장 사항은 몇 번의 클릭만으로 Azure Defender 확장을 배포하도록 제안합니다.

권장 사항(**Azure Arc 지원 Kubernetes 클러스터에는 Azure Defender의 확장이 설치되어 있어야 함**)과 확장을 사용하여 관리되는 Kubernetes 서비스가 아닌 다른 클라우드 공급자에 배포된 Kubernetes 클러스터를 보호합니다.

Azure Security Center, Azure Defender 및 Azure Arc 지원 Kubernetes 간의 이러한 통합은 다음을 제공합니다.

- 보호되지 않는 Azure Arc 지원 Kubernetes 클러스터에 대한 Azure Defender 확장의 간편한 프로비저닝(수동 및 대규모)
- Azure Arc 포털에서 Azure Defender 확장 및 해당 프로비저닝 상태 모니터링
- Security Center의 보안 권장 사항을 Azure Arc 포털의 새 보안 페이지에 보고
- Azure Defender에서 식별된 보안 위협을 Azure Arc 포털의 새 보안 페이지에 보고
- Azure Arc 지원 Kubernetes 클러스터를 Azure Security Center 플랫폼 및 환경에 통합

[온-프레미스 및 다중 클라우드 Kubernetes 클러스터에서 Azure Defender for Kubernetes 사용](defender-for-kubernetes-azure-arc.md)에서 자세히 알아보세요.

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Arc 지원 Kubernetes 클러스터용 Azure Defender 확장 배포에 대한 Azure Security Center의 권장 사항" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga"></a>이제 Azure Defender와 Microsoft Defender for Endpoint의 통합을 통해 GA(일반 공급)용으로 릴리스된 Windows Server 2019 및 Windows 10 Virtual Desktop(WVD)이 지원됩니다

Microsoft Defender for Endpoint는 클라우드에서 제공하는 전체적인 엔드포인트 보안 솔루션입니다. 위험 기반 취약성 관리 및 평가뿐 아니라 엔드포인트 검색 및 응답(EDR)도 제공합니다. Azure Security Center와 Defender for Endpoint를 함께 사용하는 경우의 이점에 대한 전체 목록은 [Security Center의 통합 EDR 솔루션 Microsoft Defender for Endpoint로 엔드포인트 보호](security-center-wdatp.md)를 참조하세요.

Windows 서버에서 서버용 Azure Defender를 사용하도록 설정하면 Defender for Endpoint용 라이선스가 플랜에 포함됩니다. 서버에 Azure Defender를 사용하도록 이미 설정했고 구독에 Windows 2019 서버가 있는 경우 Defender for Endpoint가 이 업데이트와 함께 자동으로 수신됩니다. 수동 조치가 필요하지 않습니다. 

이제 Windows Server 2019 및 [WVD(Windows Virtual Desktop)](../virtual-desktop/overview.md)를 포함하도록 지원 범위가 확장되었습니다.

> [!NOTE]
> Windows Server 2019 머신에서 Defender for Endpoint를 사용하도록 설정하는 경우 [Defender for Endpoint 통합 사용](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)에 설명된 필수 구성 요소를 충족하는지 확인합니다.


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview"></a>DNS 및 Resource Manager용 Azure Defender를 사용하도록 설정하는 권장 사항(미리 보기)

[Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md) 및 [Azure Defender for DNS](defender-for-dns-introduction.md)를 사용하도록 설정하는 프로세스를 간소화하기 위한 두 가지 새로운 권장 사항이 추가되었습니다.

- **Azure Defender for Resource Manager를 사용하도록 설정해야 함** - Defender for Resource Manager는 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 위협을 감지하고 의심스러운 활동에 대해 경고합니다.
- **Azure Defender for DNS를 사용하도록 설정해야 함** - Defender for DNS는 Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링하여 클라우드 리소스에 대한 추가 보호 계층을 제공합니다. Azure Defender는 DNS 계층에서 의심스러운 활동에 대해 경고합니다.

Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: https://aka.ms/pricing-security-center 에서 지역별 가격 정보에 대해 알아봅니다.

> [!TIP]
> 미리 보기 추천 사항은 리소스를 비정상으로 렌더링하지 않으며 보안 점수 계산에 포함되지 않습니다. 미리 보기 기간이 끝나면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정합니다. [Azure Security Center의 추천 사항 수정](security-center-remediate-recommendations.md)에서 이러한 추천 사항에 대응하는 방법에 대해 자세히 알아보세요.


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>Azure CIS 1.3.0, CMMC Level 3 및 뉴질랜드 ISM 제한됨 등 3가지 규정 준수 표준이 추가됨

Azure Security Center와 함께 사용할 수 있도록 3가지 표준을 추가했습니다. 규정 준수 대시보드를 사용하면 이제 다음을 통해 규정 준수를 추적할 수 있습니다.

- [CIS Microsoft Azure Foundations Benchmark 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC Level 3](../governance/policy/samples/cmmc-l3.md)
- [뉴질랜드 ISM 제한됨](../governance/policy/samples/new-zealand-ism.md)

[규정 준수 대시보드의 표준 집합 사용자 지정](update-regulatory-compliance-packages.md)의 설명대로 이러한 표준을 구독에 할당할 수 있습니다.

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="Azure Security Center의 규정 준수 대시보드와 함께 사용할 수 있도록 추가된 3가지 표준입니다." lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

다음에서 자세히 알아보세요.
- [규정 준수 대시보드의 표준 집합 사용자 지정](update-regulatory-compliance-packages.md)
- [자습서: 규정 준수 개선](security-center-compliance-dashboard.md)
- [FAQ - 규정 준수 대시보드](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-in-preview"></a>게스트 구성과 관련된 4가지 새로운 권장 사항(미리 보기)

Azure의 [게스트 구성 확장](../governance/policy/concepts/guest-configuration.md)은 가상 머신의 게스트 내 설정이 강화되었는지 확인할 수 있도록 Security Center에 보고합니다. Arc Connected Machine 에이전트에 포함되어 있으므로 Arc 사용 서버에는 확장이 필요하지 않습니다. 확장을 사용하려면 머신에서 시스템 관리 ID가 필요합니다.

이 확장을 최대한 활용하기 위해 네 가지 권장 사항이 Security Center에 새로 추가되었습니다.

- 확장 및 필요한 시스템 관리 ID를 설치하라는 두 가지 권장 사항:
    - **게스트 구성 확장을 머신에 설치해야 함**
    - **가상 머신의 게스트 구성 확장은 시스템이 할당한 관리 ID를 사용하여 배포해야 함**

- 확장이 설치되고 실행되면 컴퓨터에 대한 감사가 시작되고 운영 체제 구성 및 환경 설정과 같은 설정을 강화하라는 메시지가 표시됩니다. 이러한 두 가지 권장 사항은 설명한 대로 Windows 및 Linux 컴퓨터를 강화하라는 메시지를 표시합니다.
    - **Windows Defender Exploit Guard를 머신에서 사용하도록 설정해야 함**
    - **Linux 머신에 대한 인증에 SSH 키가 필요함**

[Azure Policy 게스트 구성 이해](../governance/policy/concepts/guest-configuration.md)에서 자세히 알아보세요.

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>CMK 권장 사항이 모범 사례 보안 제어로 이동했습니다.

모든 조직의 보안 프로그램에는 데이터 암호화 요구 사항이 포함되어 있습니다. 기본적으로 Azure 고객의 데이터는 미사용 시 서비스 관리형 키를 사용하여 암호화됩니다. 그러나 CMK(고객 관리형 키)는 일반적으로 규정 준수 표준을 충족하는 데 필요합니다. CMK를 사용하면 사용자가 만들고 소유한 [Azure Key Vault](../key-vault/general/overview.md) 키로 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함한 키 수명 주기에 대한 전체 제어권과 책임은 고객에게 있습니다.

Azure Security Center의 보안 제어는 관련 보안 권장 사항의 논리적 그룹이며, 취약한 공격 표면을 반영합니다. 각 컨트롤에는 모든 리소스의 컨트롤에 나열된 모든 권장 사항에 따라 수정할 경우 보안 점수에 추가될 수 있는 최대 포인트가 있습니다. **보안 모범 사례 구현** 보안 제어는 0 포인트의 가치가 있습니다. 따라서 이 컨트롤의 권장 사항은 보안 점수에 영향을 주지 않습니다.

아래에 나열된 권장 사항은 선택적 특성을 더 잘 반영하기 위해 **보안 모범 사례 구현** 보안 제어로 이동합니다. 이러한 권장 사항이 목표를 달성하는 데 가장 적합한 제어에 있도록 하기 위한 조치입니다.

- Azure Cosmos DB 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함
- Azure Machine Learning 작업 영역은 CMK(고객 관리형 키)를 사용하여 암호화해야 함
- Cognitive Services 계정은 CMK(고객 관리형 키)로 데이터 암호화를 사용하도록 설정해야 함
- 컨테이너 레지스트리는 CMK(고객 관리형 키)로 암호화해야 함
- SQL 관리형 인스턴스는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함
- SQL 서버는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함
- 스토리지 계정은 암호화에 CMK(고객 관리형 키)를 사용해야 함

[보안 컨트롤 및 해당 권장](secure-score-security-controls.md#security-controls-and-their-recommendations)에서 각 보안 제어에 어떤 권장 사항이 있는지 알아보세요.


### <a name="11-azure-defender-alerts-deprecated"></a>더 이상 사용되지 않는 11가지 Azure Defender 경고

더 이상 사용되지 않는 11가지 Azure Defender 경고는 다음과 같습니다.

- 새 경고는 이러한 두 가지 경고를 대체하고 더 나은 적용 범위를 제공합니다.

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | 미리 보기 - MicroBurst 도구 키트 "Get-AzureDomainInfo" 함수 실행이 감지됨 |
    | ARM_MicroBurstRunbook    | 미리 보기 - MicroBurst 도구 키트 "Get-AzurePasswords" 함수 실행이 감지됨  |
    |                          |                                                                          |

- 이러한 9가지 경고는 이미 사용되지 않는 Azure Active Directory IPC(ID 보호 커넥터)와 관련이 있습니다.

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | 일반적이지 않은 로그인 속성 |
    | AnonymousLogin      | 익명 IP 주소          |
    | InfectedDeviceLogin | 맬웨어 연결 IP 주소     |
    | ImpossibleTravel    | 비정상적 이동               |
    | MaliciousIP         | 악성 IP 주소          |
    | LeakedCredentials   | 유출된 자격 증명            |
    | PasswordSpray       | 암호 스프레이                |
    | LeakedCredentials   | Azure AD 위협 인텔리전스  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 
    > [!TIP]
    > 이러한 9가지 IPC 경고는 Security Center 경고가 아닙니다. Security Center에 보낸 AAD(Azure Active Directory) IPC(ID 보호 커넥터)의 일부입니다. 지난 2년 동안 이러한 경고를 확인한 고객은 2019년 또는 그 이전에 내보내기(커넥터에서 ASC로)를 구성한 조직뿐입니다. AAD IPC는 이러한 경고를 자체 경고 시스템에 계속 표시했으며 Azure Sentinel에서 계속 사용할 수 있었습니다. 유일한 변경 내용은 Security Center에 더 이상 표시되지 않는다는 것입니다.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>"시스템 업데이트 적용" 보안 제어에서 더 이상 사용되지 않는 두 가지 권장 사항 

더 이상 사용되지 않는 두 가지 권장 사항은 다음과 같으며, 이러한 변경으로 인해 보안 점수에 약간의 영향을 줄 수 있습니다.

- **시스템 업데이트를 적용하려면 머신을 다시 시작해야 함**
- **머신에 모니터링 에이전트를 설치해야 합니다**. 이 권장 사항은 온-프레미스 컴퓨터에만 관련되며, 해당 논리 중 일부는 다른 권장 사항인 **컴퓨터에서 Log Analytics 에이전트 상태 문제를 해결해야 함** 으로 이전됩니다.

연속 내보내기 및 워크플로 자동화 구성을 확인하여 이러한 권장 사항이 포함되어 있는지 확인하는 것이 좋습니다. 또한 대시보드 또는 이를 사용할 수 있는 기타 모니터링 도구를 적절하게 업데이트해야 합니다.

[보안 추천 사항 참조 페이지](recommendations-reference.md)에서 이러한 권장 사항에 대해 자세히 알아보세요.

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>Azure Defender 대시보드에서 제거되는 머신 타일의 Azure Defender for SQL

Azure Defender 대시보드의 검사 영역에는 사용자 환경과 관련된 Azure Defender 계획에 대한 타일이 포함되어 있습니다. 보호된 리소스 및 보호되지 않은 리소스의 수를 보고하는 문제로 인해 문제가 해결될 때까지 **머신에서 Azure Defender for SQL** 에 대한 리소스 검사 상태를 일시적으로 제거하기로 결정했습니다.


### <a name="21-recommendations-moved-between-security-controls"></a>보안 제어 간에 권장 사항 21개 이동 

다음 권장 사항은 다른 보안 제어로 이동했습니다. 보안 제어는 관련 보안 권장 사항의 논리적 그룹이며 취약한 공격 노출 영역을 반영합니다. 이러한 이동은 이러한 각 권장 사항에서 목표를 충족하는 가장 적절한 제어 상태에 있도록 보장합니다.

[보안 컨트롤 및 해당 권장](secure-score-security-controls.md#security-controls-and-their-recommendations)에서 각 보안 제어에 어떤 권장 사항이 있는지 알아보세요.

|권장 |변경 및 영향  |
|---------|---------|
|SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.<br>SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.<br>SQL 데이터베이스의 취약성을 수정해야 함 신규<br>VM의 SQL 데이터베이스 취약성을 수정해야 함     |취약성 수정(6점 가치)에서<br>보안 구성 수정(4점 가치)으로 이동합니다.<br>사용자 환경에 따라 이러한 권장 사항은 점수에 미치는 영향을 줄일 수 있습니다.|
|구독에 둘 이상의 소유자를 할당해야 합니다.<br>자동화 계정 변수를 암호화해야 합니다.<br>IoT 디바이스 - Auditd 프로세스가 이벤트 전송을 중지함<br>IoT 디바이스 - 운영 체제 기준 유효성 검사 실패<br>IoT 디바이스 - TLS 암호화 도구 모음 업그레이드 필요<br>IoT 디바이스 - 디바이스에서 포트 열기<br>IoT 디바이스 - 체인 중 하나에서 허용되는 방화벽 정책이 발견됨<br>IoT 디바이스 - 체인 중 하나에서 허용되는 방화벽 정책이 발견됨<br>IoT 디바이스 - 출력 체인에서 허용되는 방화벽 규칙이 발견됨<br>IoT Hub의 진단 로그를 사용하도록 설정해야 합니다.<br>IoT 디바이스 - 에이전트에서 미달 사용된 메시지를 보내고 있음<br>IoT 디바이스 - 기본 IP 필터 정책은 거부여야 함<br>IoT 디바이스 - IP 필터 규칙 큰 IP 범위<br>IoT 디바이스 - 에이전트 메시지 간격 및 크기를 조정해야 함<br>IoT 디바이스 - 동일한 인증 자격 증명<br>IoT 디바이스 - Audited 프로세스가 이벤트 전송을 중지함<br>IoT 디바이스 - OS(운영 체제) 기준 구성을 수정해야 함|**보안 모범 사례 구현** 으로 이동합니다.<br>권장 사항이 점수 가치가 없는 보안 모범 사례 구현 보안 제어로 이동하는 경우 해당 권장 사항은 더 이상 보안 점수에 영향을 주지 않습니다.|
|||


## <a name="march-2021"></a>2021년 3월

3월의 업데이트는 다음과 같습니다.

- [Security Center에 Azure 방화벽 관리 통합](#azure-firewall-management-integrated-into-security-center)
- [이제 SQL 취약성 평가에 "규칙 사용 안 함" 환경(미리 보기) 포함](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Security Center에 Azure Monitor 통합 문서를 통합하고 세 가지 템플릿 제공](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [이제 규정 준수 대시보드에 Azure 감사 보고서(미리 보기) 포함](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Azure Resource Graph에서 "ARG에서 살펴보기"를 사용하여 권장 사항 데이터 확인 가능](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [워크플로 자동화 배포에 대한 정책 업데이트](#updates-to-the-policies-for-deploying-workflow-automation)
- [두 가지 레거시 권장 사항은 더 이상 Azure 활동 로그에 직접 데이터를 쓰지 않습니다.](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [권장 사항 페이지 개선](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Security Center에 Azure 방화벽 관리 통합

Azure Security Center를 열 때 처음으로 표시되는 페이지는 개요 페이지입니다. 

이 대화형 대시보드는 하이브리드 클라우드 워크로드의 보안 상태에 대한 통합된 보기를 제공합니다. 또한 보안 경고, 적용 범위 정보 등을 보여 줍니다.

중앙 환경에서 보안 상태를 확인하는 데 도움이 될 수 있도록 Azure Firewall Manager를 이 대시보드에 통합했습니다. 이제 모든 네트워크에서 방화벽 검사 상태를 확인하고 Security Center에서 Azure 방화벽 정책을 중앙 집중식으로 관리할 수 있습니다.

[Azure Security Center의 개요 페이지](overview-page.md)에서 이 대시보드에 대해 자세히 알아보세요.

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Azure 방화벽에 대한 타일이 포함된 Security Center의 개요 대시보드":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>이제 SQL 취약성 평가에 "규칙 사용 안 함" 환경(미리 보기) 포함

Security Center에는 잠재적 데이터베이스 취약성을 검색, 추적 및 수정하는 데 도움이 되는 기본 제공 취약점 스캐너가 포함되어 있습니다. 평가 검사의 결과는 SQL 머신의 보안 상태에 대한 개요와 보안 결과에 대한 세부 정보를 제공합니다.

조직에서 결과를 수정하지 않고 무시해야 하는 요구 사항이 있으면 필요에 따라 이 결과를 사용하지 않도록 설정할 수 있습니다. 사용하지 않도록 설정된 결과는 보안 점수에 영향을 주거나 원치 않는 노이즈를 생성하지 않습니다.

[특정 결과 사용 안 함](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)에서 자세히 알아보세요.



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Security Center에 Azure Monitor 통합 문서를 통합하고 세 가지 템플릿 제공

Ignite Spring 2021에서 Security Center에 통합된 Azure Monitor 통합 문서 환경이 발표된 바 있습니다.

새로운 통합 환경을 활용하여 Security Center 갤러리에서 기본 제공 템플릿 사용을 시작할 수 있습니다. 통합 문서 템플릿을 사용하여 동적 및 시각적 보고서에 액세스하고 이를 작성하여 조직의 보안 상태를 추적할 수 있습니다. 또한 Security Center 데이터 또는 지원되는 기타 데이터 형식을 기반으로 새 통합 문서를 만들고 Security Center의 GitHub 커뮤니티에서 커뮤니티 통합 문서를 빠르게 배포할 수 있습니다.

다음과 같은 세 가지 템플릿 보고서가 제공됩니다.

- **시간 경과에 따른 보안 점수** - 구독의 점수와 리소스에 대한 권장 사항의 변경 내용 추적
- **시스템 업데이트** - 리소스, OS, 심각도 등에 따라 누락된 시스템 업데이트 보기
- **취약성 평가 결과** - Azure 리소스의 취약성 검사 결과 보기

이러한 보고서를 사용하는 방법 또는 [Security Center 데이터에 대한 풍부한 자체 대화형 보고서](custom-dashboards-azure-workbooks.md)를 작성하는 방법에 대해 알아보세요.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="시간 경과에 따른 보안 점수 보고서.":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>이제 규정 준수 대시보드에 Azure 감사 보고서(미리 보기) 포함

이제 규정 준수 대시보드의 도구 모음에서 Azure 및 Dynamics 인증 보고서를 다운로드할 수 있습니다. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="규정 준수 대시보드의 도구 모음":::

관련 보고서 유형(PCI, SOC, ISO 및 기타)에 대한 탭을 선택하고 필터를 사용하여 필요한 특정 보고서를 찾을 수 있습니다.

[규정 준수 대시보드에서 표준을 관리](update-regulatory-compliance-packages.md)하는 방법에 대해 자세히 알아보세요.

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="사용 가능한 Azure 감사 보고서 목록을 필터링합니다.":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Azure Resource Graph에서 "ARG에서 살펴보기"를 사용하여 권장 사항 데이터 확인 가능

이제 권장 사항 세부 정보 페이지에 "ARG에서 살펴보기" 도구 모음 단추가 포함됩니다. 이 단추를 사용하여 Azure Resource Graph 쿼리를 열고 권장 사항의 데이터를 살펴보고, 내보내고, 공유할 수 있습니다.

ARG(Azure Resource Graph)의 강력한 필터링, 그룹화 및 정렬 기능을 통해 클라우드 환경에서 리소스 정보에 즉시 액세스할 수 있습니다. Azure 구독 간에 프로그래밍 방식으로 또는 Azure Portal 내에서 정보를 쿼리하는 빠르고 효율적인 방법입니다.

[ARG(Azure Resource Graph)](../governance/resource-graph/index.yml)에 대해 자세히 알아보세요.

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Azure Resource Graph에서 권장 사항 데이터 살펴보기":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>워크플로 자동화 배포에 대한 정책 업데이트

조직의 모니터링 및 인시던트 대응 프로세스를 자동화하면 보안 인시던트를 조사하고 완화하는 데 걸리는 시간을 크게 향상시킬 수 있습니다.

자동화를 조직 전체에 배포할 수 있도록 워크플로 자동화 프로시저를 만들고 구성하는 다음과 같은 세 가지 Azure Policy 'DeployIfNotExist' 정책을 제공합니다.

|목표  |정책  |정책 ID  |
|---------|---------|---------|
|보안 경고에 대한 워크플로 자동화|[Azure Security Center 경고에 대한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|보안 추천 사항에 대한 워크플로 자동화|[Azure Security Center 권장 사항에 대한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|규정 준수 변경에 대한 워크플로 자동화|[Azure Security Center 규정 준수에 대한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

이러한 정책의 기능에 대한 두 가지 업데이트가 있습니다.

- 할당 후 적용하면 활성 상태로 유지됩니다.
- 이제 이미 배포된 후에도 이러한 정책을 사용자 지정하고 매개 변수를 업데이트할 수 있습니다. 예를 들어 사용자가 다른 평가 키를 추가하거나 기존 평가 키를 편집하려는 경우 그렇게 할 수 있습니다.

[워크플로 자동화 템플릿](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)을 시작합니다.

[Security Center 트리거에 대한 응답 자동화](workflow-automation.md) 방법에 대해 자세히 알아보세요.


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>두 가지 레거시 권장 사항은 더 이상 Azure 활동 로그에 직접 데이터를 쓰지 않습니다. 

Security Center는 거의 모든 보안 권장 사항에 대한 데이터를 Azure Advisor에 전달한 후 [Azure 활동 로그](../azure-monitor/essentials/activity-log.md)에 씁니다.

두 가지 권장 사항의 경우 데이터가 Azure 활동 로그에 동시에 직접 씁니다. 이 변경으로 Security Center는 이러한 레거시 보안 권장 사항에 대한 데이터를 활동 로그에 직접 쓰는 것을 중지합니다. 대신 다른 모든 권장 사항과 마찬가지로 Azure Advisor로 데이터를 내보냅니다.

두 가지 레거시 권장 사항은 다음과 같습니다.
- 머신에서 엔드포인트 보호 상태 문제를 해결해야 함
- 머신 보안 구성의 취약성을 수정해야 합니다.

활동 로그의 "TaskDiscovery 유형의 권장 사항" 범주에서 이 두 가지 권장 사항에 대한 정보에 액세스한 경우 이 정보는 더 이상 사용할 수 없습니다.


### <a name="recommendations-page-enhancements"></a>권장 사항 페이지 개선 

더 많은 정보를 한눈에 볼 수 있도록 향상된 버전의 권장 사항 목록이 출시되었습니다.

이제 권장 사항 페이지에 다음 정보가 표시됩니다.

1. 각 보안 컨트롤의 최고 점수 및 현재 점수
1. **수정** 및 **미리 보기** 와 같은 태그를 대체하는 아이콘
1. 각 권장 사항과 관련된 [정책 이니셔티브](security-policy-concept.md)를 보여주는 새 열 - "컨트롤 기준 그룹화"를 사용하지 않을 때 표시

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Azure Security Center 권장 사항 페이지의 향상된 기능 - 2021년 3월" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Azure Security Center 권장 사항 'flat' 목록의 향상된 기능 - 2021년 3월" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

[Azure Security Center의 보안 권장 사항](security-center-recommendations.md)에서 자세히 알아보세요.

## <a name="february-2021"></a>2021년 2월

2월의 업데이트는 다음과 같습니다.

- [GA(일반 공급)를 위해 릴리스된 Azure Portal의 새 보안 경고 페이지](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Kubernetes 워크로드 보호 권장 사항이 GA(일반 공급)로 릴리스됨](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [이제 Azure Defender와 Microsoft Defender for Endpoint의 통합을 통해 Windows Server 2019 및 Windows 10 Virtual Desktop(WVD) 지원(미리 보기)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [권장 사항 세부 정보 페이지에서 정책으로 직접 연결](#direct-link-to-policy-from-recommendation-details-page)
- [SQL 데이터 분류 권장 사항이 더 이상 보안 점수에 영향을 주지 않음](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [규정 준수 평가 변경 사항으로 워크플로 자동화가 트리거될 수 있음(미리 보기)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [자산 인벤토리 페이지 향상](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>GA(일반 공급)를 위해 릴리스된 Azure Portal의 새 보안 경고 페이지

Azure Security Center의 보안 경고 페이지가 다음을 제공하도록 다시 디자인되었습니다.

- **경고에 대한 향상된 심사 환경** - 경고 피로를 줄이고 가장 관련성이 높은 위협에 더 쉽게 집중하는 데 도움이 됩니다. 목록에는 사용자 지정 가능한 필터 및 그룹화 옵션이 포함되어 있습니다.
- **경고 목록의 추가 정보** - 예: MITRE ATT&ACK 전술.
- **샘플 알림을 만드는 단추** - Azure Defender 기능을 평가하고 알림을 테스트 구성(SIEM 통합, 이메일 알림 및 워크플로 자동화)을 테스트하기 위해 모든 Azure Defender 계획의 샘플 경고를 만들 수 있습니다.
- **Azure Sentinel의 인시던트 환경에 맞춤** - 이제 두 제품을 사용하는 고객이 두 제품 간에 더 직관적으로 전환할 수 있는 환경이 제공되며, 두 제품을 손쉽게 알아볼 수 있습니다.
- 대규모 경고 목록의 **성능 개선**.
- 경고 목록 **키보드 탐색**.
- **Azure Resource Graph의 경고** - 모든 리소스에 대한 Kusto 같은 API인 Azure Resource Graph에서 경고를 쿼리할 수 있습니다. 이는 자체 경고 대시보드를 빌드하는 경우에도 유용합니다. [Azure Resource Graph에 대한 자세한 정보](../governance/resource-graph/index.yml).
- **샘플 경고 기능 만들기** - 새 경고 환경에서 샘플 경고를 만들려면 [샘플 Azure Defender 경고 생성](security-center-alert-validation.md#generate-sample-azure-defender-alerts)을 참조하세요.

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center의 보안 경고 목록":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Kubernetes 워크로드 보호 권장 사항이 GA(일반 공급)로 릴리스됨

Kubernetes 워크로드 보호를 위한 권장 사항 세트가 GA(일반 공급)된다는 것을 발표하게 되어 기쁘게 생각합니다.

Kubernetes 워크로드를 기본적으로 안전하게 보호하기 위해 Security Center에서 Kubernetes 허용 제어가 있는 적용 옵션을 포함하여 Kubernetes 수준 보안 강화 권장 사항이 추가되었습니다.

Kubernetes에 대한 Azure Policy 추가 항목을 AKS(Azure Kubernetes Service) 클러스터에 설치하면 Kubernetes API 서버에 대한 모든 요청이 클러스터에 유지되기 전에 미리 정의된 모범 사례 세트(예: 13개 보안 권장 사항으로 표시됨)에 대해 모니터링됩니다. 그런 다음, 모범 사례를 적용하고 향후 워크로드에 대해 위임하도록 구성할 수 있습니다.

예를 들어 권한 있는 컨테이너를 만들지 않도록 위임할 수 있습니다. 그러면, 이러한 작업에 대한 이후의 모든 요청이 차단됩니다.

[Kubernetes 허용 제어를 사용하여 워크로드 보호 모범 사례](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)에서 자세히 알아보세요.

> [!NOTE]
> 권장 사항은 미리 보기에 있었지만 AKS 클러스터 리소스를 비정상으로 렌더링하지 않았으며 보안 점수 계산에 포함되지 않았습니다. 이제 이러한 권장 사항은 이 GA 알림과 함께 점수 계산에 포함됩니다. 아직 수정하지 않은 경우 보안 점수에 약간의 영향을 줄 수 있습니다. 가능한 경우 [Azure Security Center의 권장 사항 수정](security-center-remediate-recommendations.md)에서 설명한 대로 이러한 권장 사항을 수정합니다.


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>이제 Azure Defender와 Microsoft Defender for Endpoint의 통합을 통해 Windows Server 2019 및 Windows 10 Virtual Desktop(WVD) 지원(미리 보기)

Microsoft Defender for Endpoint는 클라우드에서 제공하는 전체적인 엔드포인트 보안 솔루션입니다. 위험 기반 취약성 관리 및 평가뿐 아니라 엔드포인트 검색 및 응답(EDR)도 제공합니다. Azure Security Center와 Defender for Endpoint를 함께 사용하는 경우의 이점에 대한 전체 목록은 [Security Center의 통합 EDR 솔루션 Microsoft Defender for Endpoint로 엔드포인트 보호](security-center-wdatp.md)를 참조하세요.

Windows 서버에서 서버용 Azure Defender를 사용하도록 설정하면 Defender for Endpoint용 라이선스가 플랜에 포함됩니다. 서버에 Azure Defender를 사용하도록 이미 설정했고 구독에 Windows 2019 서버가 있는 경우 Defender for Endpoint가 이 업데이트와 함께 자동으로 수신됩니다. 수동 조치가 필요하지 않습니다. 

이제 Windows Server 2019 및 [WVD(Windows Virtual Desktop)](../virtual-desktop/overview.md)를 포함하도록 지원 범위가 확장되었습니다.

> [!NOTE]
> Windows Server 2019 머신에서 Defender for Endpoint를 사용하도록 설정하는 경우 [Defender for Endpoint 통합 사용](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)에 설명된 필수 구성 요소를 충족하는지 확인합니다.

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>권장 사항 세부 정보 페이지에서 정책으로 직접 연결

권장 사항에 대한 세부 정보를 검토할 때 기본 정책을 볼 수 있으면 도움이 되는 경우가 많습니다. 정책으로 지원되는 모든 권장 사항에는 권장 사항 세부 정보 페이지의 새 링크가 있습니다.

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="권장 사항을 지원하는 특정 정책에 대한 Azure Policy 페이지로 연결됩니다.":::

이 링크를 사용하여 정책 정의를 확인하고 평가 논리를 검토합니다. 

[보안 권장 사항 참조 가이드](recommendations-reference.md)에서 권장 사항 목록을 검토하는 경우 정책 정의 페이지에 대한 링크도 표시됩니다.

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Azure Security Center 권장 사항 참조 페이지에서 직접 특정 정책에 대한 Azure Policy 페이지에 액세스합니다." lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>SQL 데이터 분류 권장 사항이 더 이상 보안 점수에 영향을 주지 않음
**SQL 데이터베이스에서 중요한 데이터를 분류해야 합니다.** 권장 사항은 더 이상 보안 점수에 영향을 주지 않습니다. 이는 **데이터 분류 적용** 보안 제어의 유일한 권장 사항이므로 이제 해당 제어의 보안 점수 값은 0입니다.

Security Center의 모든 보안 컨트롤에 대한 전체 목록과 각 보안 컨트롤의 점수 및 권장 사항 목록은 [보안 컨트롤 및 권장 사항](secure-score-security-controls.md#security-controls-and-their-recommendations)을 참조하세요.


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>규정 준수 평가 변경 사항으로 워크플로 자동화가 트리거될 수 있음(미리 보기)
워크플로 자동화를 위한 트리거 옵션에 세 번째 데이터 유형인 규정 준수 평가 변경 사항을 추가했습니다.

[Security Center 트리거에 대한 응답 자동화](workflow-automation.md)에서 워크플로 자동화 도구를 사용하는 방법에 대해 알아봅니다.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="규정 준수 평가에 대한 변경 사항을 사용하여 워크플로 자동화를 트리거합니다." lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>자산 인벤토리 페이지 향상
Security Center의 자산 인벤토리 페이지는 다음과 같은 방식으로 개선되었습니다.

- 이제 페이지 상단의 요약에 **등록되지 않은 구독** 이 포함되어 Security Center가 활성화되지 않은 구독 수를 보여줍니다.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="자산 인벤토리 페이지 상단의 요약에 있는 등록되지 않은 구독 수입니다.":::

- 필터는 다음을 포함하도록 확장 및 개선되었습니다.
    - **개수** - 각 필터는 각 범주의 조건을 충족하는 리소스 수를 표시합니다.

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Azure Security Center의 자산 인벤토리 페이지에 있는 필터의 개수입니다.":::

    - **예외 필터 포함**(선택 사항) - 예외가 있거나 없는 리소스로 결과 범위를 좁힙니다. 이 필터는 기본적으로 표시되지 않지만 **필터 추가** 단추에서 액세스할 수 있습니다.

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Azure Security Center의 자산 인벤토리 페이지에 '예외 포함' 필터 추가":::

[자산 인벤토리로 리소스를 검색 및 관리](asset-inventory.md)하는 방법에 대해 자세히 알아보세요.



## <a name="january-2021"></a>2021년 1월

1월의 업데이트는 다음과 같습니다.

- [Azure Security Benchmark는 이제 Azure Security Center의 기본 정책 이니셔티브입니다.](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [온-프레미스 및 다중 클라우드 머신의 취약성 평가가 GA(일반 공급)를 위해 릴리스되었습니다.](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [이제 관리 그룹에 대한 보안 점수를 미리 보기에서 볼 수 있습니다.](#secure-score-for-management-groups-is-now-available-in-preview)
- [보안 점수 API는 GA(일반 공급)를 위해 릴리스되었습니다.](#secure-score-api-is-released-for-general-availability-ga)
- [Azure Defender for App Service에 추가된 현수 DNS 보호](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [다중 클라우드 커넥터는 GA(일반 공급)용으로 릴리스되었습니다.](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [구독 및 관리 그룹에 대한 보안 점수에서 전체 권장 사항 제외](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [사용자는 이제 전역 관리자에게 테넌트 전체의 가시성을 요청할 수 있습니다.](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [Azure 보안 벤치마크의 적용 범위를 늘리기 위해 35개의 미리 보기 추천 사항이 추가됨](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [필터링된 권장 목록의 CSV 내보내기](#csv-export-of-filtered-list-of-recommendations)
- [Azure Policy 평가에서 "해당 없음" 리소스가 이제 "준수"로 보고됨](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [연속 내보내기를 통해 보안 점수 및 규정 준수 데이터의 주간 스냅샷 내보내기(미리 보기)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure Security Benchmark는 이제 Azure Security Center의 기본 정책 이니셔티브입니다.

Azure 보안 벤치마크는 일반적인 규정 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대해 Microsoft에서 작성한 Azure 관련 지침 세트입니다. 널리 인정받는 이 벤치마크는 클라우드 중심 보안에 초점을 맞춘 [CIS(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) 및 [NIST(National Institute of Standards and Technology)](https://www.nist.gov/)의 컨트롤을 기반으로 합니다.

최근 몇 개월 동안 Security Center의 기본 제공 보안 권장 사항 목록은 이 벤치마크의 적용 범위를 확장하기 위해 크게 증가했습니다.

이번 릴리스부터 벤치마크는 Security Center 권장 사항의 기반이 되었으며 기본 정책 이니셔티브로 완전히 통합되었습니다. 

모든 Azure 서비스에는 설명서에 보안 기준 페이지가 있습니다. 예를 들어, [이는 Security Center의 기준입니다](security-baseline.md). 이러한 기준은 Azure 보안 벤치마크를 기반으로 합니다.

Security Center의 규정 준수 대시보드를 사용하는 경우 전환 기간 동안 두 가지 벤치마크 인스턴스를 볼 수 있습니다.

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure 보안 벤치마크를 보여주는 Azure Security Center의 규정 준수 대시보드":::

기존 권장 사항은 영향을 받지 않으며 벤치마크가 증가하면 변경 사항이 Security Center에 자동으로 반영됩니다. 

자세히 알아보려면 다음 페이지를 참조하세요.

- [Azure 보안 벤치마크에 대한 자세한 정보](/security/benchmark/azure/introduction)
- [규정 준수 대시보드의 표준 집합 사용자 지정](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>온-프레미스 및 다중 클라우드 머신의 취약성 평가가 GA(일반 공급)를 위해 릴리스되었습니다.

10월에는 서버용 Azure Defender 있는 Azure Arc 지원 [서버의](defender-for-servers-introduction.md)통합 취약성 평가 스캐너(Qualys 기반)를 검사하기 위한 미리 보기를 발표했습니다.

이제 GA(일반 공급)를 위해 릴리스되었습니다.

Azure가 아닌 머신에서 Azure Arc를 사용하도록 설정한 경우 Security Center는 이러한 머신에 통합 취약성 스캐너를 수동 및 대규모로 배포할 수 있습니다.

이번 업데이트를 통해 모든 Azure 및 비-Azure 자산의 취약성 관리 프로그램을 통합하는 **서버용 Azure Defender** 의 강력한 성능을 발휘할 수 있습니다.

주요 기능:

- Azure Arc 머신의 VA(취약성 평가) 스캐너 프로비저닝 상태 모니터링
- 보호되지 않는 Windows 및 Linux Azure Arc 머신에 통합 VA 에이전트 프로비저닝(수동 및 대규모로)
- 배포된 에이전트에서 탐지된 취약성 수신 및 분석(수동 및 대규모로)
- Azure VM 및 Azure Arc 머신에 대한 통합 환경

[하이브리드 머신에 통합 Qualys 취약성 스캐너를 배포하는 방법에 대해 자세히 알아봅니다.](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

[Azure Arc 지원 서버에 대해 자세히 알아보세요.](../azure-arc/servers/index.yml)


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>이제 관리 그룹에 대한 보안 점수를 미리 보기에서 볼 수 있습니다.

이제 보안 점수 페이지에는 구독 수준 외에도 관리 그룹에 대해 집계된 보안 점수가 표시됩니다. 이제 조직의 관리 그룹 목록과 각 관리 그룹의 점수를 볼 수 있습니다.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="관리 그룹의 보안 점수 보기.":::

[Azure Security Center의 보안 점수 및 보안 제어](secure-score-security-controls.md)에 대해 자세히 알아보세요.

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>보안 점수 API는 GA(일반 공급)를 위해 릴리스되었습니다.

이제 [보안 점수 API](/rest/api/securitycenter/securescores/)를 통해 점수에 액세스할 수 있습니다. API 메서드는 데이터를 쿼리할 수 있는 유연성을 제공하고, 시간 경과에 따른 보안 점수에 대한 사용자 고유의 보고 메커니즘을 빌드합니다. 예를 들면 다음과 같습니다.

- **보안 점수** API를 사용하여 특정 구독에 대한 점수를 가져옵니다.
- **보안 점수 제어** API를 사용하여 보안 제어 및 구독의 현재 점수를 나열합니다.

[GitHub 커뮤니티의 보안 점수 영역](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)에서 보안 점수 API를 사용하여 가능한 외부 도구에 대해 알아보세요.

[Azure Security Center의 보안 점수 및 보안 제어](secure-score-security-controls.md)에 대해 자세히 알아보세요.


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Azure Defender for App Service에 추가된 현수 DNS 보호

하위 도메인 인수는 조직에 일반적이고 심각도가 높은 위협입니다. 프로비저닝이 해제된 웹 사이트를 가리키는 DNS 레코드가 있는 경우 하위 도메인 인수가 발생할 수 있습니다. 이러한 DNS 레코드는 "현수 DNS" 항목으로도 알려져 있습니다. CNAME 레코드는 특히 이 위협에 취약합니다. 

하위 도메인 인수를 통해 위협 행위자는 조직의 도메인에 대한 트래픽을 악의적인 활동을 수행하는 사이트로 리디렉션할 수 있습니다.

이제 앱 서비스 웹 사이트가 해제될 때 Azure Defender for App Service가 현수 DNS 항목을 검색합니다. 이때는 DNS 항목이 존재하지 않는 리소스를 가리키고 웹 사이트가 하위 도메인 인수에 취약한 시점입니다. 이러한 보호는 도메인이 Azure DNS 또는 외부 도메인 등록 기관으로 관리되는지 여부에 관계없이 사용할 수 있고 Windows의 App Service와 Linux의 App Service 모두에 적용합니다.

자세한 정보:

- [App Service 경고 참조 테이블](alerts-reference.md#alerts-azureappserv) - 현수 DNS 항목이 검색될 때 트리거되는 두 개의 새로운 Azure Defender 경고가 포함됩니다.
- [현수 DNS 항목 방지 및 하위 도메인 인수 방지](../security/fundamentals/subdomain-takeover.md) - 하위 도메인 인수 위협 및 현수 DNS 측면에 대해 알아보세요.
- [Azure Defender for App Service 소개](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>다중 클라우드 커넥터는 GA(일반 공급)용으로 릴리스되었습니다.

클라우드 워크로드가 일반적으로 여러 클라우드 플랫폼에 걸쳐 있는 경우 클라우드 보안 서비스도 동일한 작업을 수행해야 합니다.

Azure Security Center는 Azure, AWS(Amazon Web Services) 및 GCP(Google Cloud Platform)의 워크로드를 보호합니다.

AWS 또는 GCP 계정을 연결하면 AWS Security Hub 및 GCP 보안 명령 센터와 같은 기본 보안 도구를 Azure Security Center에 통합합니다.

이 기능은 Security Center가 모든 주요 클라우드 환경에서 가시성과 보호를 제공함을 의미합니다. 이러한 통합의 이점 중 일부는 다음과 같습니다.

- 자동 에이전트 프로비저닝 - Security Center가 Azure Arc를 사용하여 AWS 인스턴스에 Log Analytics 에이전트 배포
- 정책 관리
- 취약점 관리
- 포함된 EDR(엔드포인트 검색 및 응답)
- 잘못된 보안 구성 검색
- 모든 클라우드 공급자의 보안 권장 사항을 보여주는 단일 보기
- Security Center의 보안 점수 계산에 모든 리소스 통합
- AWS 및 GCP 리소스의 규정 준수 평가

Security Center의 메뉴에서 **다중 클라우드 커넥터** 를 선택하면 새 커넥터를 만드는 옵션이 표시됩니다.

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Security Center의 다중 클라우드 커넥터 페이지에 표시되는 [AWS 계정 추가] 단추":::

다음에서 자세히 알아보세요.
- [Azure Security Center에 AWS 계정 연결](quickstart-onboard-aws.md)
- [Azure Security Center에 GCP 계정 연결](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>구독 및 관리 그룹에 대한 보안 점수에서 전체 권장 사항 제외

전체 권장 사항을 포함하도록 면제 기능을 확장하고 있습니다. Security Center에서 구독, 관리 그룹 또는 리소스에 대해 제공하는 보안 권장 사항을 세부 조정할 수 있는 추가 옵션을 제공합니다.

경우에 따라 Security Center가 검색하지 못한 타사 도구로 문제가 해결되었음을 알고 있는 경우 리소스가 비정상으로 표시됩니다. 또는 해당 영역이 아닌 것으로 생각되는 범위에 권장 사항이 표시됩니다. 권장 사항은 특정 구독에 적합하지 않을 수도 있습니다. 또는 조직에서 특정 리소스 또는 권장 사항과 관련된 위험을 수용하기로 결정했을 수 있습니다.

이 미리 보기 기능으로 이제 다음과 같은 권장 사항에 대한 예외를 만들 수 있습니다.

- **리소스를 제외** 하여 향후 비정상적인 리소스에 나열되지 않고 보안 점수에 영향을 주지 않도록 합니다. 리소스가 해당되지 않는 것으로 나열되고 선택한 특정 근거와 함께 이유가 "예외"로 표시됩니다.

- 권장 사항이 보안 점수에 영향을 주지 않고 향후 구독 또는 관리 그룹에 표시되지 않도록 하려면 **구독 또는 관리 그룹을 제외** 합니다. 이는 기존 리소스와 나중에 만드는 리소스와 관련이 있습니다. 권장 사항은 선택한 범위에 대해 선택한 특정 근거로 표시됩니다.

[보안 점수에서 리소스 및 권장 사항 제외](exempt-resource.md)에서 자세히 알아보세요.



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>사용자는 이제 전역 관리자에게 테넌트 전체의 가시성을 요청할 수 있습니다.

사용자에게 Security Center 데이터를 볼 수 있는 권한이 없는 경우 이제 조직의 전역 관리자에게 권한을 요청하는 링크가 표시됩니다. 요청에는 원하는 역할과 필요한 이유에 대한 근거가 포함됩니다.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="사용자에게 테넌트 전체의 사용 권한을 요청할 수 있음을 알리는 배너입니다.":::

[사용자 권한이 부족할 때 테넌트 전체 권한 요청](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)에서 자세히 알아보세요.


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure 보안 벤치마크의 적용 범위를 늘리기 위해 35개의 미리 보기 추천 사항이 추가됨

[Azure Security Benchmark](/security/benchmark/azure/introduction)는 Azure Security Center의 기본 정책 이니셔티브입니다. 

이 벤치마크의 적용 범위를 늘리기 위해 다음 35개의 미리 보기 권장 사항이 Security Center에 추가되었습니다.

> [!TIP]
> 미리 보기 추천 사항은 리소스를 비정상으로 렌더링하지 않으며 보안 점수 계산에 포함되지 않습니다. 미리 보기 기간이 끝나면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정합니다. [Azure Security Center의 추천 사항 수정](security-center-remediate-recommendations.md)에서 이러한 추천 사항에 대응하는 방법에 대해 자세히 알아보세요.

| 보안 컨트롤                     | 새로운 권장 사항                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 저장 데이터 암호화 사용            | - Azure Cosmos DB 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함<br>- Azure Machine Learning 작업 영역은 CMK(고객 관리형 키)를 사용하여 암호화해야 함<br>- MySQL 서버에 대해 BYOK(Bring Your Own Key) 데이터 보호를 사용하도록 설정해야 함<br>- PostgreSQL 서버에 대해 BYOK(Bring Your Own Key) 데이터 보호를 사용하도록 설정해야 함<br>- Cognitive Services 계정은 CMK(고객 관리형 키)로 데이터 암호화를 사용하도록 설정해야 함<br>- 컨테이너 레지스트리는 CMK(고객 관리형 키)로 암호화해야 함<br>- SQL Managed Instance는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함<br>- SQL Server는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함<br>- 스토리지 계정은 암호화에 CMK(고객 관리형 키)를 사용해야 함                                                                                                                                                              |
| 보안 모범 사례 구현    | - 구독에 보안 문제에 대한 연락처 이메일 주소가 있어야 함<br> - 구독에 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 함<br> - 심각도가 높은 경고에 대해 이메일 알림을 사용하도록 설정해야 함<br> - 심각도가 높은 경고에 대해 구독 소유자에게 이메일 알림을 사용하도록 설정해야 함<br> - 키 자격 증명 모음에 제거 방지를 사용하도록 설정해야 함<br> - 키 자격 증명 모음에 일시 삭제를 사용하도록 설정해야 함 |
| 액세스 및 사용 권한 관리        | - 함수 앱은 '클라이언트 인증서(들어오는 클라이언트 인증서)'를 사용하도록 설정해야 함 |
| DDoS 공격으로부터 애플리케이션 보호 | - Application Gateway에 WAF(웹 애플리케이션 방화벽)를 사용하도록 설정해야 함<br> - Azure Front Door Service 서비스에 대해 WAF(Web Application Firewall)를 사용하도록 설정해야 함 |
| 무단 네트워크 액세스 제한 | - Key Vault에서 방화벽을 사용하도록 설정해야 함<br> - Key Vault의 프라이빗 엔드포인트를 구성해야 함<br> - App Configuration은 프라이빗 링크를 사용해야 함<br> - Azure Cache for Redis는 가상 네트워크 내에 있어야 함<br> - Azure Event Grid 도메인은 프라이빗 링크를 사용해야 함<br> - Azure Event Grid 토픽은 프라이빗 링크를 사용해야 함<br> - Azure Machine Learning 작업 영역은 프라이빗 링크를 사용해야 함<br> - Azure SignalR Service는 프라이빗 링크를 사용해야 함<br> - Azure Spring Cloud는 네트워크 주입을 사용해야 함<br> - 컨테이너 레지스트리는 무제한 네트워크 액세스를 허용하지 않아야 함<br> - 컨테이너 레지스트리는 프라이빗 링크를 사용해야 함<br> - MariaDB 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 함<br> - MySQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 함<br> - PostgreSQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 함<br> - 스토리지 계정은 프라이빗 링크 연결을 사용해야 함<br> - 스토리지 계정은 가상 네트워크 규칙을 사용하여 네트워크 액세스를 제한해야 함<br> - VM Image Builder 템플릿은 프라이빗 링크를 사용해야 함|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

관련 링크:

- [Azure 보안 벤치마크에 대한 자세한 정보](/security/benchmark/azure/introduction)
- [Azure Database for MariaDB에 대한 자세한 정보](../mariadb/overview.md)
- [Azure Database for MySQL에 대한 자세한 정보](../mysql/overview.md)
- [Azure Database for PostgreSQL에 대한 자세한 정보](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>필터링된 권장 목록의 CSV 내보내기 

2020년 11월에 권장 사항 페이지에 필터를 추가했습니다([이제 권장 목록에 필터가 포함됨](release-notes-archive.md#recommendations-list-now-includes-filters)). 12월에 이러한 필터를 확장했습니다([권장 사항 페이지에는 환경, 심각도 및 사용 가능한 응답에 대한 새 필터가 있음](release-notes-archive.md#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

이 공지 사항에서는 **CSV로 다운로드** 단추의 동작을 변경하여 CSV 내보내기에 현재 필터링된 목록에 표시된 권장 사항만 포함되도록 합니다. 

예를 들어 아래 이미지에서 목록이 두 가지 권장 사항으로 필터링된 것을 볼 수 있습니다. 생성된 CSV 파일에는 이러한 두 가지 권장 사항이 적용되는 모든 리소스에 대한 상태 정보가 포함됩니다.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="필터링된 권장 사항을 CSV 파일로 내보내기.":::

[Azure Security Center의 보안 권장 사항](security-center-recommendations.md)에서 자세히 알아보세요.


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Azure Policy 평가에서 "해당 없음" 리소스가 이제 "준수"로 보고됨

이전에는 권장 사항에 대해 평가되고 **해당 없음** 으로 확인된 리소스는 Azure Policy에 "비규격"으로 표시되었습니다. 어떤 사용자 작업도 상태를 "규정 준수"로 변경할 수 없었습니다. 이 변경으로 명확성을 높이기 위해 "규정 준수"로 보고됩니다.

규정 준수 리소스의 수가 증가하는 Azure Policy에만 영향을 줍니다. Azure Security Center의 보안 점수에는 영향을 주지 않습니다.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>연속 내보내기를 통해 보안 점수 및 규정 준수 데이터의 주간 스냅샷 내보내기(미리 보기)

보안 점수 및 규정 준수 데이터의 주간 스냅샷을 내보내기 위한 [연속 내보내기](continuous-export.md) 도구에 새로운 미리 보기 기능을 추가했습니다.

연속 내보내기를 정의할 때 내보내기 빈도를 설정합니다.

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="연속 내보내기의 빈도 선택.":::

- **스트리밍** – 리소스의 상태가 업데이트되면 평가를 보냅니다(업데이트되지 않으면 데이터를 보내지 않음).
- **스냅샷** – 모든 규정 준수 평가의 현재 상태에 대한 스냅샷이 매주 전송됩니다(이는 보안 점수 및 규정 준수 데이터의 주간 스냅샷에 대한 미리 보기 기능임).

[Security Center 데이터 연속 내보내기](continuous-export.md)에서 이 기능의 전체 기능에 대해 자세히 알아보세요.

## <a name="december-2020"></a>2020년 12월

12월의 업데이트는 다음과 같습니다.

- [머신의 SQL 서버용 Azure Defender를 일반적으로 사용할 수 있습니다.](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Synapse Analytics 전용 SQL 풀에 대한 Azure Defender for SQL 지원이 일반적으로 제공됩니다.](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [전역 관리자는 이제 자신에게 테넌트 수준 권한을 부여할 수 있습니다.](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [새로운 두 가지 Azure Defender 계획: Azure Defender for DNS 및 Azure Defender for Resource Manager(미리 보기)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Azure Portal의 새 보안 경고 페이지(미리 보기)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Azure SQL Database & SQL Managed Instance의 재활성화된 Security Center 환경](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [자산 인벤토리 도구 및 필터 업데이트됨](#asset-inventory-tools-and-filters-updated)
- [SSL 인증서를 요청하는 웹앱에 대한 권장 사항은 더 이상 보안 점수에 포함되지 않음](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [권장 사항 페이지에는 환경, 심각도 및 사용 가능한 응답에 대한 새 필터가 있음](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [연속 내보내기는 새 데이터 형식 및 향상된 deployifnotexist 정책을 가져옴](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>머신의 SQL 서버용 Azure Defender를 일반적으로 사용할 수 있습니다.

Azure Security Center는 SQL Server에 대한 두 가지 Azure Defender 계획을 제공합니다.

- **Azure SQL 데이터베이스 서버용 Azure Defender** - Azure 네이티브 SQL Server 방어 
- **컴퓨터의 SQL 서버용 Azure Defender** - 동일한 보호를 하이브리드, 다중 클라우드 및 온-프레미스 환경의 SQL 서버로 확장

이 공지를 통해 **Azure Defender for SQL** 은 이제 데이터베이스와 해당 데이터가 어디에 있든 보호합니다.

Azure Defender for SQL에는 취약성 평가 기능이 포함되어 있습니다. 취약성 평가 도구에는 다음과 같은 고급 기능이 포함되어 있습니다.

- **기본 구성**(신규!)을 통해 취약성 검색 결과를 실제 보안 문제를 나타낼 수 있는 결과로 지능적으로 구체화할 수 있습니다. 기본 보안 상태를 설정한 후 취약성 평가 도구는 해당 기준 상태의 편차만 보고합니다. 기준과 일치하는 결과는 후속 검색을 통과한 것으로 간주됩니다. 이를 통해 사용자와 분석가는 중요한 부분에 집중할 수 있습니다.
- 검색된 결과 및 리소스와 관련된 이유를 *이해* 하는 데 도움이 되는 **자세한 벤치마크 정보**.
- 식별된 위험을 완화하는 데 도움이 되는 **수정 스크립트**.

[Azure Defender for SQL](defender-for-sql-introduction.md)에 대해 자세히 알아봅니다.


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Synapse Analytics 전용 SQL 풀에 대한 Azure Defender for SQL 지원이 일반적으로 제공됩니다.

Azure Synapse Analytics(이전의 SQL DW)는 엔터프라이즈 데이터 웨어하우징과 빅 데이터 분석을 결합한 분석 서비스입니다. 전용 SQL 풀은 Azure Synapse의 엔터프라이즈 데이터 웨어하우징 기능입니다. [Azure Synapse Analytics(이전의 SQL DW)란?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에서 자세히 알아봅니다.

Azure Defender for SQL은 다음을 통해 전용 SQL 풀을 보호합니다.

- 위협 및 공격을 감지하는 **지능형 위협 방지** 
- 보안 오류를 식별하고 수정하기 위한 **취약성 평가 기능**

Azure Synapse Analytics SQL 풀에 대한 Azure Defender for SQL의 지원은 Azure Security Center의 Azure SQL 데이터베이스 번들에 자동으로 추가됩니다. Azure Portal의 Synapse 작업 영역 페이지에 새로운 "Azure Defender for SQL" 탭을 찾을 수 있습니다.

[Azure Defender for SQL](defender-for-sql-introduction.md)에 대해 자세히 알아봅니다.


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>전역 관리자는 이제 자신에게 테넌트 수준 권한을 부여할 수 있습니다.

**전역 관리자** 의 Azure Active Directory 역할을 가진 사용자는 테넌트 전체의 책임이 있지만 Azure Security Center에서 해당 조직 전체 정보를 볼 수 있는 Azure 권한은 없습니다. 

자신에게 테넌트 수준 사용 권한을 할당하려면 [자신에게 테넌트 전체 사용 권한 부여](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself)의 지침을 따르세요.


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>새로운 두 가지 Azure Defender 계획: Azure Defender for DNS 및 Azure Defender for Resource Manager(미리 보기)

Azure 환경을 위한 새로운 두 가지 클라우드 네이티브 너비 위협 방지 기능이 추가되었습니다.

이러한 새로운 보호 기능은 위협 행위자의 공격에 대한 복원력을 크게 향상시키고 Azure Defender로 보호되는 Azure 리소스의 수를 크게 늘립니다.

- **Azure Defender for Resource Manager** - 조직에서 수행되는 모든 리소스 관리 작업을 자동으로 모니터링합니다. 자세한 내용은 다음을 참조하세요.
    - [Azure Defender for Resource Manager 소개](defender-for-resource-manager-introduction.md)
    - [Azure Defender for Resource Manager 경고에 대한 대응](defender-for-resource-manager-usage.md)
    - [Azure Defender for Resource Manager에서 제공하는 경고 목록](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender for DNS** - Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링합니다. 자세한 내용은 다음을 참조하세요.
    - [Azure Defender for DNS 소개](defender-for-dns-introduction.md)
    - [Azure Defender for DNS 경고에 대한 대응](defender-for-dns-usage.md)
    - [Azure Defender for DNS에서 제공하는 경고 목록](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Azure Portal의 새 보안 경고 페이지(미리 보기)

Azure Security Center의 보안 경고 페이지가 다음을 제공하도록 다시 디자인되었습니다.

- **경고에 대한 향상된 심사 환경** - 경고 피로를 줄이고 가장 관련성이 높은 위협에 더 쉽게 집중하는 데 도움이 됩니다. 목록에는 사용자 지정 가능한 필터 및 그룹화 옵션이 포함되어 있습니다.
- **경고 목록의 추가 정보** - 예: MITRE ATT&ACK 전술
- **샘플 경고를 만드는 단추** - Azure Defender 기능을 평가하고 경고 구성(SIEM 통합, 이메일 알림 및 워크플로 자동화)을 테스트하기 위해 모든 Azure Defender 계획의 샘플 경고를 만들 수 있습니다.
- **Azure Sentinel의 인시던트 환경에 맞춤** - 이제 두 제품을 사용하는 고객이 두 제품 간에 더 직관적으로 전환할 수 있는 환경이 제공되며, 두 제품을 손쉽게 알아볼 수 있습니다.
- 대규모 경고 목록의 **성능 개선**
- 경고 목록 **키보드 탐색**
- **Azure Resource Graph의 경고** - 모든 리소스에 대한 Kusto 같은 API인 Azure Resource Graph에서 경고를 쿼리할 수 있습니다. 이는 자체 경고 대시보드를 빌드하는 경우에도 유용합니다. [Azure Resource Graph에 대한 자세한 정보](../governance/resource-graph/index.yml).

새 환경에 액세스하려면 보안 경고 페이지 맨 위에 있는 배너에서 '지금 사용해 보기' 링크를 사용합니다.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="새 미리 보기 경고 환경에 대한 링크가 포함된 배너":::

새 경고 환경에서 샘플 경고를 만들려면 [샘플 Azure Defender 경고 생성](security-center-alert-validation.md#generate-sample-azure-defender-alerts)을 참조하세요.


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Azure SQL Database & SQL Managed Instance의 재활성화된 Security Center 환경 

SQL 내의 Security Center 환경은 다음과 같은 Security Center 및 Azure Defender for SQL 기능에 대한 액세스를 제공합니다.

- **보안 권장 사항** – Security Center는 연결된 모든 Azure 리소스의 보안 상태를 정기적으로 분석하여 잠재적인 보안 구성 오류를 식별합니다. 그런 다음, 이러한 취약성을 해결하고 조직의 보안 태세를 개선하는 방법에 대한 권장 사항을 제공합니다.
- **보안 경고** - SQL 삽입, 무차별 암호 대입 공격 및 권한 남용 같은 위협에 대해 Azure SQL 활동을 지속적으로 모니터링하는 검색 서비스입니다. 이 서비스는 Security Center에서 세부적이고 작업 지향적인 보안 경고를 트리거하고 Microsoft의 Azure 네이티브 SIEM 솔루션인 Azure Sentinel을 사용하여 지속적으로 조사하기 위한 옵션을 제공합니다.
- **검색 결과** – Azure SQL 구성을 지속적으로 모니터링하고 취약성을 해결하는 데 도움이 되는 취약성 평가 서비스입니다. 평가 검사는 자세한 보안 결과와 함께 Azure SQL 보안 상태의 개요를 제공합니다.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="SQL에 대한 Azure Security Center의 보안 기능은 Azure SQL 내에서 사용할 수 있습니다.":::


### <a name="asset-inventory-tools-and-filters-updated"></a>자산 인벤토리 도구 및 필터 업데이트됨

Azure Security Center의 인벤토리 페이지가 다음과 같이 변경되었습니다.

- 도구 모음에 **가이드 및 피드백** 추가. 관련 정보 및 도구에 대한 링크가 포함된 창이 열립니다. 
- 리소스에 사용할 수 있는 기본 필터에 **구독 필터** 추가.
- 현재 필터 옵션을 Azure Resource Graph 쿼리(이전에는 "리소스 그래프 탐색기에서 보기"라고 함)로 열기 위한 **쿼리 열기** 링크.
- 각 필터에 대한 **운영자 옵션**. 이제 '=' 이외의 추가 논리 연산자에서 선택할 수 있습니다. 예를 들어, 'encrypt(암호화)' 문자열이 있는 제목을 가진 활성 권장 사항이 있는 모든 리소스를 찾을 수 있습니다. 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="자산 인벤토리 필터의 운영자 옵션에 대한 컨트롤":::

[자산 인벤토리를 사용하여 리소스 검색 및 관리](asset-inventory.md)에서 인벤토리에 대해 자세히 알아보세요.


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>SSL 인증서를 요청하는 웹앱에 대한 권장 사항은 더 이상 보안 점수에 포함되지 않음

"웹앱에서 들어오는 모든 요청에 대해 SSL 인증서를 요청해야 합니다" 권장 사항이 보안 컨트롤 **액세스 및 권한 관리**(최대 4포인트 상당)에서 **보안 모범 사례 구현**(포인트 가치 없음)으로 이동되었습니다. 

웹앱에서 인증서를 요청하도록 보장하면 더 안전하게 보호할 수 있습니다. 그러나 공용 웹앱의 경우에는 관련이 없습니다. HTTP를 통해 사이트에 액세스하고 HTTPS를 통해서는 액세스하지 않는 경우 클라이언트 인증서가 제공되지 않습니다. 따라서 애플리케이션에 클라이언트 인증서가 필요한 경우 HTTP를 통한 애플리케이션 요청을 허용해서는 안 됩니다. [Azure App Service에 대한 TLS 상호 인증 구성](../app-service/app-service-web-configure-tls-mutual-auth.md)에서 자세히 알아보세요.

이러한 변경으로 권장 사항은 이제 점수에 영향을 주지 않는 권장 모범 사례가 됩니다. 

[보안 컨트롤 및 해당 권장](secure-score-security-controls.md#security-controls-and-their-recommendations)에서 각 보안 제어에 어떤 권장 사항이 있는지 알아보세요.


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>권장 사항 페이지에는 환경, 심각도 및 사용 가능한 응답에 대한 새 필터가 있음

Azure Security Center는 연결된 모든 리소스를 모니터링하고 보안 권장 사항을 생성합니다. 이러한 권장 사항을 사용하여 하이브리드 클라우드 상태를 강화하고 조직, 산업 및 국가와 관련된 정책 및 표준 준수 여부를 추적합니다.

Security Center가 범위와 기능을 계속 확장함에 따라 보안 권장 사항 목록이 매월 증가하고 있습니다. 예를 들어, [Azure 보안 벤치마크의 적용 범위를 늘리기 위해 29개의 미리 보기 추천 사항이 추가됨](release-notes-archive.md#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)을 참조하세요.

목록이 증가함에 따라 가장 관심 있는 권장 사항을 찾기 위해 해당 권장 사항을 필터링해야 합니다. 11월에는 권장 사항 페이지에 필터를 추가했습니다([이제 추천 목록에 필터가 포함됨](release-notes-archive.md#recommendations-list-now-includes-filters) 참조).

이 달에 추가된 필터는 다음에 따라 추천 목록을 세분화하는 옵션을 제공합니다.

- **환경** - AWS, GCP 또는 Azure 리소스(또는 모든 조합)에 대한 권장 사항 보기
- **심각도** - Security Center에 의해 설정된 심각도 분류에 따른 권장 사항 보기
- **대응 조치** - 수정, 거부 및 적용 등 사용 가능한 Security Center 응답 옵션에 따른 권장 사항 보기

    > [!TIP]
    > 대응 조치 필터는 **빠른 수정 사용 가능(예/아니요)** 필터를 대체합니다. 
    > 
    > 각각의 대응 옵션에 대해 자세히 알아보세요.
    > - [수정 단추](security-center-remediate-recommendations.md#fix-button)
    > - [적용/거부 권장 사항을 사용하여 구성 오류 방지](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="보안 컨트롤별로 그룹화된 권장 사항" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>연속 내보내기는 새 데이터 형식 및 향상된 deployifnotexist 정책을 가져옴

Azure Security Center의 연속 내보내기 도구를 사용하여 환경의 다른 모니터링 도구와 함께 사용할 Security Center의 권장 사항 및 경고를 내보낼 수 있습니다.

연속 내보내기를 사용하면 내보낼 대상과 위치를 자유롭게 사용자 지정할 수 있습니다. 자세한 내용은 [Security Center 데이터 연속 내보내기](continuous-export.md)를 참조하세요.

이러한 도구는 다음과 같은 방법으로 향상되고 확장되었습니다.

- **연속 내보내기의 deployifnotexist 정책이 향상되었습니다**. 현재 정책은

    - **구성을 사용할 수 있는지 여부를 확인합니다.** 그렇지 않은 경우 정책은 비준수로 표시되고 준수 리소스를 생성합니다. [연속 내보내기 설정](continuous-export.md#set-up-a-continuous-export)의 "Azure Policy를 사용하여 대규모 배포 탭"에서 제공된 Azure Policy 템플릿에 대해 자세히 알아보세요.

    - **보안 결과 내보내기를 지원합니다.** Azure Policy 템플릿을 사용하는 경우 검색 결과를 포함하도록 연속 내보내기를 구성할 수 있습니다. 이는 취약성 평가 스캐너의 검색 결과 또는 '상위' 권장 사항 "시스템 업데이트가 컴퓨터에 설치되어 있어야 합니다."에 대한 특정 시스템 업데이트와 같은 '하위' 권장 사항이 있는 권장 사항을 내보낼 때 관련됩니다.
    
    - **보안 점수 데이터 내보내기를 지원합니다.**

- **규정 준수 평가 데이터(미리 보기 형식)가 추가되었습니다.** 이제 사용자 지정 이니셔티브를 포함하여 규정 준수 평가에 대한 업데이트를 Log Analytics 작업 영역 또는 Event Hub로 지속적으로 내보낼 수 있습니다. 국가/소버린 클라우드에서는 이 기능을 사용할 수 없습니다.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="연속 내보내기 데이터와 함께 규정 준수 평가 정보를 포함하기 위한 옵션":::

## <a name="november-2020"></a>2020년 11월

11월의 업데이트는 다음과 같습니다.

- [Azure 보안 벤치마크의 적용 범위를 늘리기 위해 29개의 미리 보기 추천 사항이 추가됨](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Security Center의 규정 준수 대시보드에 NIST SP 800 171 R2가 추가됨](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [이제 추천 목록에 필터가 포함됨](#recommendations-list-now-includes-filters)
- [자동 프로비저닝 환경 향상 및 확장](#auto-provisioning-experience-improved-and-expanded)
- [이제 연속 내보내기(미리 보기)에서 보안 점수를 사용할 수 있습니다.](#secure-score-is-now-available-in-continuous-export-preview)
- ["시스템 업데이트가 컴퓨터에 설치되어 있어야 합니다." 권장 사항이 이제 하위 권장 사항을 포함합니다.](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [이제 기본 정책 할당의 상태를 보여주는 Azure Portal의 정책 관리 페이지](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure 보안 벤치마크의 적용 범위를 늘리기 위해 29개의 미리 보기 권장 사항 추가됨

Azure 보안 벤치마크는 일반적인 규정 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대해 Microsoft에서 작성한 Azure 관련 지침 세트입니다. [Azure 보안 벤치마크에 대해 자세히 알아보세요](/security/benchmark/azure/introduction).

이 벤치마크의 적용 범위를 넓히기 위해 다음 29개의 새로운 추천 사항이 Security Center에 추가되었습니다.

미리 보기 추천 사항은 리소스를 비정상으로 렌더링하지 않으며 보안 점수 계산에 포함되지 않습니다. 미리 보기 기간이 끝나면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정합니다. [Azure Security Center의 추천 사항 수정](security-center-remediate-recommendations.md)에서 이러한 추천 사항에 대응하는 방법에 대해 자세히 알아보세요.

| 보안 컨트롤                     | 새로운 권장 사항                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 전송 중인 데이터 암호화              | - PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.<br>- MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.<br>- TLS를 최신 API 앱 버전으로 업데이트해야 합니다.<br>- TLS를 최신 함수 앱 버전으로 업데이트해야 합니다.<br>- TLS를 최신 웹앱 버전으로 업데이트해야 합니다.<br>- API 앱에서 FTPS를 요구해야 합니다.<br>- 함수 앱에서 FTPS를 요구해야 합니다.<br>- 웹앱에서 FTPS를 요구해야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 액세스 및 사용 권한 관리        | - 웹앱에서 들어오는 모든 요청에 대해 SSL 인증서를 요청해야 합니다.<br>- API 앱에서 관리 ID를 사용해야 합니다.<br>- 함수 앱에서 관리 ID를 사용해야 합니다.<br>- 웹앱에서 관리 ID를 사용해야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 무단 네트워크 액세스 제한 | - 프라이빗 엔드포인트를 PostgreSQL 서버에서 사용할 수 있어야 합니다.<br>- 프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.<br>- 프라이빗 엔드포인트를 MySQL 서버에서 사용할 수 있어야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 감사 및 로깅 사용          | - App Services에서 진단 로그를 사용하도록 설정해야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| 보안 모범 사례 구현    | - Azure Backup을 가상 머신에 사용하도록 설정해야 합니다.<br>- Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.<br>- Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.<br>- Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.<br>- PHP를 최신 API 앱 버전으로 업데이트해야 합니다.<br>- PHP를 최신 웹앱 버전으로 업데이트해야 합니다.<br>- Java를 최신 API 앱 버전으로 업데이트해야 합니다.<br>- Java를 최신 함수 앱 버전으로 업데이트해야 합니다.<br>- Java를 최신 웹앱 버전으로 업데이트해야 합니다.<br>- Python을 최신 API 앱 버전으로 업데이트해야 합니다.<br>- Python을 최신 함수 앱 버전으로 업데이트해야 합니다.<br>- Python을 최신 웹앱 버전으로 업데이트해야 합니다.<br>- SQL 서버에 대한 감사 보존 기간은 90일 이상으로 설정해야 합니다. |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

관련 링크:

- [Azure 보안 벤치마크에 대한 자세한 정보](/security/benchmark/azure/introduction)
- [Azure API 앱에 대한 자세한 정보](../app-service/app-service-web-tutorial-rest-api.md)
- [Azure 함수 앱에 대한 자세한 정보](../azure-functions/functions-overview.md)
- [Azure 웹앱에 대한 자세한 정보](../app-service/overview.md)
- [Azure Database for MariaDB에 대한 자세한 정보](../mariadb/overview.md)
- [Azure Database for MySQL에 대한 자세한 정보](../mysql/overview.md)
- [Azure Database for PostgreSQL에 대한 자세한 정보](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>Security Center의 규정 준수 대시보드에 NIST SP 800 171 R2가 추가됨

NIST SP 800-171 R2 표준은 이제 Azure Security Center의 규정 준수 대시보드에서 사용할 수 있는 기본 이니셔티브로 제공됩니다. 제어에 대한 매핑은 [NIST SP 800-171 R2 규정 준수 기본 제공 이니셔티브의 세부 정보](../governance/policy/samples/nist-sp-800-171-r2.md)에 설명되어 있습니다. 

표준을 구독에 적용하고 규정 준수 상태를 지속적으로 모니터링하려면 [규정 준수 대시보드의 표준 집합 사용자 지정](update-regulatory-compliance-packages.md)의 지침을 사용합니다.

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Security Center 규정 준수 대시보드의 NIST SP 800 171 R2 표준":::

이 규정 준수 표준에 대한 자세한 내용은 [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)를 참조하세요.


### <a name="recommendations-list-now-includes-filters"></a>이제 추천 목록에 필터가 포함됨

이제 일련의 조건에 따라 보안 추천 목록을 필터링할 수 있습니다. 다음 예에서는 다음과 같은 추천을 보여주기 위해 추천 목록이 필터링되었습니다.

- **일반 공급**(즉, 미리 보기 아님)
- **스토리지 계정** 에 대한 추천
- **빠른 수정** 수정을 지원하는 추천

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="추천 목록에 대한 필터":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>자동 프로비저닝 환경 향상 및 확장

자동 프로비저닝 기능은 신규 및 기존 Azure VM에 필요한 확장을 설치하여 관리 오버헤드를 줄이는 데 도움이 되므로 Security Center의 보호 기능을 활용할 수 있습니다. 

Azure Security Center가 성장함에 따라 더 많은 확장이 개발되었으며 보안 센터에서 더 많은 리소스 유형을 모니터링할 수 있습니다. 자동 프로비저닝 도구는 이제 Azure Policy의 기능을 활용하여 다른 확장 및 리소스 종류를 지원하도록 확장되었습니다.

이제 다음 항목의 자동 프로비저닝을 구성할 수 있습니다.

- Log Analytics 에이전트
- (신규) Kubernetes에 대한 Azure Policy 추가 기능
- (신규) Microsoft Dependency Agent

[Azure Security Center에서 에이전트 및 확장 자동 프로비저닝](security-center-enable-data-collection.md)에서 자세히 알아보세요.


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>이제 연속 내보내기(미리 보기)에서 보안 점수를 사용할 수 있습니다.

보안 점수를 지속적으로 내보내면 점수 변경 사항을 Azure Event Hubs 또는 Log Analytics 작업 영역에 실시간으로 스트리밍할 수 있습니다. 이 기능을 사용하여 다음을 수행할 수 있습니다.

- 동적 보고서로 시간 경과에 따른 보안 점수 추적
- 보안 점수 데이터를 Azure Sentinel(또는 기타 SIEM)로 내보내기
- 이 데이터를 조직에서 보안 점수를 모니터링하는 데 이미 사용하고 있을 수 있는 프로세스와 통합

[Security Center 데이터를 연속적으로 내보내는](continuous-export.md) 방법에 대해 자세히 알아보세요.


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>"시스템 업데이트가 컴퓨터에 설치되어 있어야 합니다." 권장 사항이 이제 하위 권장 사항을 포함합니다.

**시스템 업데이트가 컴퓨터에 설치되어 있어야 합니다.** 권장 사항이 향상되었습니다. 새 버전에는 누락된 각 업데이트에 대한 하위 권장 사항이 포함되어 있으며 다음과 같은 기능이 향상되었습니다.

- Azure Portal의 Azure Security Center 페이지에서 새롭게 디자인된 환경. **시스템 업데이트는 머신에 설치되어야 합니다** 에 대한 권장 사항 세부 정보 페이지에는 아래와 같은 결과 목록이 포함되어 있습니다. 단일 찾기를 선택하면 재구성 정보 및 영향을 받는 리소스의 목록에 대한 링크가 포함된 세부 정보 창이 열립니다.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="업데이트된 권장 사항에 대한 포털 환경에서 하위 권장 사항 중 하나 열기":::

- ARG(Azure Resource Graph)의 권장 사항에 대한 보강 데이터. ARG는 효율적인 리소스 탐색을 제공하도록 디자인된 Azure 서비스입니다. ARG를 사용하여 사용자 환경을 효과적으로 관리할 수 있도록 지정된 구독 세트에서 대규모로 쿼리할 수 있습니다. 

    Azure Security Center의 경우 ARG 및 [KQL(Kusto Query Language)](/azure/data-explorer/kusto/query/)을 사용하여 다양한 보안 태세 데이터를 쿼리할 수 있습니다.

    이전에는 ARG에서 이 권장 사항을 쿼리한 경우 권장 사항을 머신에서 수정해야 한다는 정보만 제공되었습니다. 향상된 버전의 다음 쿼리는 누락된 각 시스템 업데이트를 머신별로 그룹화하여 반환합니다.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>이제 기본 정책 할당의 상태를 보여주는 Azure Portal의 정책 관리 페이지

이제 Azure Portal의 Security Center **보안 정책** 페이지에서 구독에 기본 Security Center 정책이 할당되었는지 여부를 확인할 수 있습니다.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="기본 정책 할당을 보여 주는 Azure Security Center의 정책 관리 페이지":::



## <a name="october-2020"></a>2020년 10월

10월의 업데이트는 다음과 같습니다.
- [온-프레미스 및 다중 클라우드 머신의 취약성 평가(미리 보기)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall 권장 사항 추가(미리 보기)](#azure-firewall-recommendation-added-preview)
- [[Kubernetes 서비스에서 권한 있는 IP 범위를 정의해야 함] 권장 사항의 빠른 픽스가 업데이트됨](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [이제 규정 준수 대시보드에는 표준을 제거하는 옵션이 포함됨](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [ARG(Azure Resource Graph)에서 Microsoft.Security/securityStatuses 테이블이 제거됨](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>온-프레미스 및 다중 클라우드 머신의 취약성 평가(미리 보기)

[서버의 Azure Defender](defender-for-servers-introduction.md)' Qualys에서 지원하는 통합 취약성 평가 스캐너)는 이제 Azure Arc 지원 서버를 검사합니다.

Azure가 아닌 머신에서 Azure Arc를 사용하도록 설정한 경우 Security Center는 이러한 머신에 통합 취약성 스캐너를 수동 및 대규모로 배포할 수 있습니다.

이번 업데이트를 통해 모든 Azure 및 비-Azure 자산의 취약성 관리 프로그램을 통합하는 **서버용 Azure Defender** 의 강력한 성능을 발휘할 수 있습니다.

주요 기능:

- Azure Arc 머신의 VA(취약성 평가) 스캐너 프로비저닝 상태 모니터링
- 보호되지 않는 Windows 및 Linux Azure Arc 머신에 통합 VA 에이전트 프로비저닝(수동 및 대규모로)
- 배포된 에이전트에서 탐지된 취약성 수신 및 분석(수동 및 대규모로)
- Azure VM 및 Azure Arc 머신에 대한 통합 환경

[하이브리드 머신에 통합 Qualys 취약성 스캐너를 배포하는 방법에 대해 자세히 알아봅니다.](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

[Azure Arc 지원 서버에 대해 자세히 알아봅니다.](../azure-arc/servers/index.yml)


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall 권장 사항 추가(미리 보기)

Azure Firewall을 통해 모든 가상 네트워크를 보호하기 위해 새로운 권장 사항이 추가되었습니다.

**가상 네트워크는 Azure Firewall로 보호해야 합니다** 권장 사항은 Azure Firewall을 사용하여 가상 네트워크에 대한 액세스를 제한하고 잠재적 위협을 차단할 것을 조언합니다.

[Azure Firewall](https://azure.microsoft.com/services/azure-firewall/)에 대해 자세히 알아보세요.


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>[Kubernetes 서비스에서 권한 있는 IP 범위를 정의해야 함] 권장 사항의 빠른 픽스가 업데이트됨

**Kubernetes 서비스에서 권한 있는 IP 범위를 정의해야 함** 권장 사항은 이제 빠른 픽스 옵션을 제공합니다.

이 권장 사항 및 기타 모든 Security Center 권장 사항에 대한 자세한 내용은 [보안 권장 사항 - 참조 가이드](recommendations-reference.md)를 참조하세요.

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="빠른 픽스 옵션을 제공하는 [Kubernetes 서비스에서 권한 있는 IP 범위를 정의해야 함] 권장 사항":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>이제 규정 준수 대시보드에는 표준을 제거하는 옵션이 포함됨

Security Center의 규정 준수 대시보드는 특정 규정 준수 제어 및 요구 사항을 충족하는 방법에 따라 규정 준수 상태에 대한 인사이트를 제공합니다.

대시보드에는 규정 표준의 기본 세트가 포함되어 있습니다. 제공된 표준이 조직과 관련이 없는 경우 이제 구독에 대한 UI에서 해당 표준을 제거할 수 있는 간단한 프로세스입니다. 표준은 관리 그룹 범위가 아닌 *구독* 수준에서만 제거할 수 있습니다.

[대시보드에서 표준 제거](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)에서 자세히 알아봅니다.


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>ARG(Azure Resource Graph)에서 Microsoft.Security/securityStatuses 테이블이 제거됨

Azure Resource Graph는 작업 환경을 효과적으로 관리할 수 있도록 특정 구독 세트에서 대규모로 쿼리를 수행할 수 있는 효율적인 리소스 탐색 기능을 제공하도록 디자인된 Azure 서비스입니다. 

Azure Security Center의 경우 ARG 및 [KQL(Kusto Query Language)](/azure/data-explorer/kusto/query/)을 사용하여 다양한 보안 태세 데이터를 쿼리할 수 있습니다. 예를 들어:

- 자산 인벤토리 활용(ARG)
- [MFA(다단계 인증)를 사용하지 않고 계정을 식별](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)하는 방법에 대한 샘플 ARG 쿼리가 문서로 준비되어 있습니다.

ARG 내에는 쿼리에 사용할 수 있는 데이터 테이블이 들어 있습니다.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer 및 사용 가능한 테이블":::

> [!TIP]
> ARG 설명서에는 [Azure Resource Graph 테이블 및 리소스 종류 참조](../governance/resource-graph/reference/supported-tables-resources.md)에서 사용 가능한 모든 테이블이 나열되어 있습니다.

이번 업데이트에서 **Microsoft.Security/securityStatuses** 테이블이 제거되었습니다. securityStatuses API는 계속 사용할 수 있습니다.

데이터 교체는 Microsoft.Security/Assessments에서 사용할 수 있습니다.

Microsoft.Security/securityStatuses는 평가의 집계를 보여주고 Microsoft.Security/Assessments는 각 평가에 대한 단일 레코드를 포함한다는 것이 가장 큰 차이점입니다.

예를 들어 Microsoft.Security/securityStatuses는 다음과 같은 두 개의 policyAssessments 배열이 포함된 결과를 반환합니다.

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
반면 Microsoft.Security/Assessments는 다음과 같이 각 정책 평가에 대한 레코드를 보유합니다.

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**이제 assessments 테이블을 사용하도록 securityStatuses를 사용하여 기존 ARG 쿼리를 변환하는 예제:**

SecurityStatuses를 참조하는 쿼리:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Assessments 테이블의 대체 쿼리:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

다음 링크에서 자세한 내용을 알아보세요.
- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language(KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>2020년 9월

9월의 업데이트는 다음과 같습니다.
- [Security Center가 새로운 모습으로 바뀌었습니다!](#security-center-gets-a-new-look)
- [Azure Defender가 릴리스됨](#azure-defender-released)
- [Key Vault용 Azure Defender가 일반 공급됨](#azure-defender-for-key-vault-is-generally-available)
- [Files 및 ADLS Gen2에 대한 Storage용 Azure Defender 보호가 일반 공급됨](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [이제 자산 인벤토리 도구가 일반 공급됨](#asset-inventory-tools-are-now-generally-available)
- [컨테이너 레지스트리 및 가상 머신 검사에 대한 특정 취약성 결과 사용 안 함](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [권장 사항에서 리소스 제외](#exempt-a-resource-from-a-recommendation)
- [Security Center의 AWS 및 GCP 커넥터에서 다중 클라우드 환경을 제공함](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Kubernetes 워크로드 보호 추천 사항 번들](#kubernetes-workload-protection-recommendation-bundle)
- [이제 취약성 평가 결과를 연속 내보내기에서 사용할 수 있음](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [새 리소스를 만들 때 추천 사항을 적용하여 보안 구성 오류 방지](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [네트워크 보안 그룹 추천 사항이 향상됨](#network-security-group-recommendations-improved)
- ["Kubernetes 서비스에 Pod 보안 정책을 정의해야 합니다."라는 미리 보기 AKS 추천 사항이 더 이상 사용되지 않음](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Azure Security Center의 이메일 알림이 향상됨](#email-notifications-from-azure-security-center-improved)
- [보안 점수에 미리 보기 추천 사항이 포함되지 않음](#secure-score-doesnt-include-preview-recommendations)
- [이제 추천 사항에 심각도 표시기 및 새로 고침 간격이 포함됨](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center가 새로운 모습으로 바뀌었습니다!

Security Center의 포털 페이지에 대해 새로 고친 UI를 릴리스했습니다. 새 페이지에는 보안 점수, 자산 인벤토리 및 Azure Defender에 대한 새 개요 페이지와 대시보드가 포함됩니다.

다시 설계된 개요 페이지에는 이제 보안 점수, 자산 인벤토리 및 Azure Defender 대시보드에 액세스하기 위한 타일이 있습니다. 또한 규정 준수 대시보드에 연결되는 타일도 있습니다.

[개요 페이지](overview-page.md)에 대해 자세히 알아보세요.


### <a name="azure-defender-released"></a>Azure Defender가 릴리스됨

**Azure Defender** 는 Security Center 내부에 통합되어 Azure 및 하이브리드 워크로드에 대한 고급 인텔리전트 보호를 제공하는 CWPP(클라우드 워크로드 보호 플랫폼)입니다. Security Center의 표준 가격 책정 계층 옵션을 대체합니다. 

Azure Security Center의 **가격 책정 및 설정** 영역에서 Azure Defender를 사용하도록 설정하면 다음 Defender 계획이 동시에 사용하도록 설정되어 환경의 컴퓨팅, 데이터 및 서비스 계층에 대한 포괄적인 방어 기능을 제공합니다.

- [서버용 Azure Defender](defender-for-servers-introduction.md)
- [App Service용 Azure Defender](defender-for-app-service-introduction.md)
- [스토리지용 Azure Defender](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [Key Vault용 Azure Defender](defender-for-key-vault-introduction.md)
- [Kubernetes용 Azure Defender](defender-for-kubernetes-introduction.md)
- [컨테이너 레지스트리용 Azure Defender](defender-for-container-registries-introduction.md)

이러한 계획 각각은 Security Center 설명서에서 별도로 설명하고 있습니다.

Azure Defender는 전용 대시보드를 사용하여 가상 머신, SQL 데이터베이스, 컨테이너, 웹 애플리케이션, 네트워크 등에 대한 보안 경고 및 지능형 위협 방지 기능을 제공합니다.

[Azure Defender](azure-defender.md)에 대해 자세히 알아보세요.

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Key Vault용 Azure Defender가 일반 공급됨

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 

**Key Vault용 Azure Defender** 는 Azure Key Vault용 Azure 네이티브 Advanced Threat Protection 기능을 통해 추가 보안 인텔리전스 계층을 제공합니다. Key Vault용 Azure Defender는 확장을 통해 결과적으로 Key Vault 계정에 종속된 많은 리소스를 보호합니다.

선택적 계획은 이제 GA입니다. 이 기능은 미리 보기에서 "Azure Key Vault용 Advanced Threat Protection"으로 제공되었습니다.

또한 Azure Portal의 Key Vault 페이지에는 이제 **Security Center** 추천 사항 및 경고에 대한 전용 **보안** 페이지가 포함됩니다.

[Key Vault용 Azure Defender](defender-for-key-vault-introduction.md)에서 자세히 알아보세요.


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Files 및 ADLS Gen2에 대한 Storage용 Azure Defender 보호가 일반 공급됨 

**Storage용 Azure Defender** 는 Azure Storage 계정에서 잠재적으로 유해한 활동을 탐지합니다. Blob 컨테이너, 파일 공유 또는 데이터 레이크로 저장되는지에 관계없이 데이터를 보호할 수 있습니다.

이제 [Azure Files](../storage/files/storage-files-introduction.md) 및 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)에 대한 지원이 일반 공급됩니다.

2020년 10월 1일부터 이러한 서비스에서 리소스를 보호하는 데 드는 요금이 청구됩니다.

[Storage용 Azure Defender](defender-for-storage-introduction.md)에서 자세히 알아보세요.


### <a name="asset-inventory-tools-are-now-generally-available"></a>이제 자산 인벤토리 도구가 일반 공급됨

Azure Security Center의 자산 인벤토리 페이지는 Security Center에 연결한 리소스의 보안 상태를 확인할 수 있는 단일 페이지를 제공합니다.

Security Center는 Azure 리소스의 보안 상태를 정기적으로 분석하여 잠재적인 보안 취약성을 식별합니다. 그런 다음, 이러한 취약성을 수정하는 방법에 대한 추천 사항을 제공합니다.

리소스에 수정되지 않은 추천 사항이 있으면 인벤토리에 표시됩니다.

[자산 인벤토리로 리소스 탐색 및 관리](asset-inventory.md)에서 자세히 알아보세요.



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>컨테이너 레지스트리 및 가상 머신 검사에 대한 특정 취약성 결과 사용 안 함

Azure Defender에는 Azure Container Registry 및 가상 머신의 이미지를 검사하는 취약성 스캐너가 포함되어 있습니다.

조직에서 결과를 수정하지 않고 무시해야 하는 요구 사항이 있으면 필요에 따라 이 결과를 사용하지 않도록 설정할 수 있습니다. 사용하지 않도록 설정된 결과는 보안 점수에 영향을 주거나 원치 않는 노이즈를 생성하지 않습니다.

결과가 사용 안 함 규칙에 정의한 조건과 일치하면 검색 결과 목록에 표시되지 않습니다.

이 옵션은 다음에 대한 추천 사항 세부 정보 페이지에서 사용할 수 있습니다.

- **Azure Container Registry 이미지의 취약성을 수정해야 함**
- **가상 머신의 취약성을 수정해야 함**

[컨테이너 이미지에 대한 특정 결과 사용 안 함](defender-for-container-registries-usage.md#disable-specific-findings-preview) 및 [가상 머신에 대한 특정 결과 사용 안 함](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)에서 자세히 알아보세요.


### <a name="exempt-a-resource-from-a-recommendation"></a>권장 사항에서 리소스 제외

경우에 따라 리소스가 특정 추천 사항과 관련하여 비정상 상태가 아니라고 생각하더라도 비정상 상태로 표시됩니다. 이로 인해 보안 점수가 낮아집니다. Security Center에서 추적하지 않는 프로세스를 통해 수정되었을 수 있습니다. 또는 조직에서 특정 리소스에 대한 위험을 감수하기로 결정했을 수도 있습니다. 

이러한 경우 예외 규칙을 만들고 나중에 해당 리소스가 비정상 리소스에 나열되지 않도록 할 수 있습니다. 이러한 규칙에는 아래에서 설명한 대로 문서화된 근거가 포함될 수 있습니다.

[추천 사항 및 보안 점수에서 리소스 제외](exempt-resource.md)에서 자세히 알아보세요.


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Security Center의 AWS 및 GCP 커넥터에서 다중 클라우드 환경을 제공함

클라우드 워크로드가 일반적으로 여러 클라우드 플랫폼에 걸쳐 있는 경우 클라우드 보안 서비스도 동일한 작업을 수행해야 합니다.

Azure Security Center는 이제 Azure, AWS(Amazon Web Services) 및 GCP(Google Cloud Platform)에서 워크로드를 보호합니다.

AWS 및 GCP 계정을 Security Center에 온보딩하면 AWS Security Hub, GCP Security Command 및 Azure Security Center를 통합합니다. 

[Azure Security Center에 AWS 계정 연결](quickstart-onboard-aws.md) 및 [Azure Security Center에 GCP 계정 연결](quickstart-onboard-gcp.md)에서 자세히 알아보세요.


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Kubernetes 워크로드 보호 추천 사항 번들

Kubernetes 워크로드에서 기본적으로 보안을 유지할 수 있도록 하기 위해 Security Center에서 Kubernetes 허용 제어를 사용하는 적용 옵션을 포함하여 Kubernetes 수준 보안 강화 추천 사항을 추가합니다.

Kubernetes용 Azure Policy 추가 기능을 AKS 클러스터에 설치한 경우 Kubernetes API 서버에 대한 모든 요청은 클러스터에 유지되기 전에 미리 정의된 모범 사례 세트에 대해 모니터링됩니다. 그런 다음, 모범 사례를 적용하고 향후 워크로드에 대해 위임하도록 구성할 수 있습니다.

예를 들어 권한 있는 컨테이너를 만들지 않도록 위임할 수 있습니다. 그러면, 이러한 작업에 대한 이후의 모든 요청이 차단됩니다.

[Kubernetes 허용 제어를 사용하여 워크로드 보호 모범 사례](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)에서 자세히 알아보세요.


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>이제 취약성 평가 결과를 연속 내보내기에서 사용할 수 있음

연속 내보내기를 사용하여 경고 및 권장 사항을 Azure Event Hubs, Log Analytics 작업 영역 또는 Azure Monitor에 스트리밍합니다. 여기서는 이 데이터를 SIEM(예: Azure Sentinel, Power BI, Azure Data Explorer 등)과 통합할 수 있습니다.

Security Center의 통합 취약성 평가 도구는 "가상 머신의 취약성을 수정해야 함"과 같은 '부모' 추천 사항 내에서 리소스에 대한 결과를 실행 가능한 추천 사항으로 반환합니다. 

이제 추천 사항을 선택하고 **보안 결과 포함** 옵션을 사용하도록 설정하면 연속 내보내기를 통해 보안 결과를 내보낼 수 있습니다.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="연속 내보내기 구성의 보안 결과 포함 설정/해제" :::

관련 페이지:

- [Azure virtual machines에 대 한 Security Center의 통합 Qualys 취약점 평가 솔루션](deploy-vulnerability-assessment-vm.md)
- [Azure Container Registry 이미지에 대한 Security Center의 통합 취약성 평가 솔루션](defender-for-container-registries-usage.md)
- [연속 내보내기](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>새 리소스를 만들 때 추천 사항을 적용하여 보안 구성 오류 방지

보안 구성 오류는 보안 인시던트의 주요 원인입니다. 이제 Security Center에는 특정 추천 사항과 관련하여 새 리소스의 구성 오류를 *방지* 하는 데 도움이 되는 기능이 있습니다. 

이 기능을 통해 워크로드를 안전하게 유지하고 보안 점수를 안정화할 수 있습니다.

특정 추천 사항에 따라 보안 구성을 적용하는 방법은 다음 두 가지 모드로 제공됩니다.

- Azure Policy의 **거부** 효과를 사용하여 비정상 리소스가 만들어지는 것을 중지할 수 있습니다.

- **적용** 옵션을 사용하여 Azure Policy의 **DeployIfNotExist** 효과를 활용하고, 비준수 리소스를 만들 때 자동으로 수정할 수 있습니다.
 
이는 선택한 보안 추천 사항에 사용할 수 있으며 리소스 세부 정보 페이지의 위쪽에서 찾을 수 있습니다.

[적용/거부 추천 사항을 사용하여 구성 오류 방지](prevent-misconfigurations.md)에서 자세히 알아보세요.

###  <a name="network-security-group-recommendations-improved"></a>네트워크 보안 그룹 추천 사항이 향상됨

일부 가양성 인스턴스를 줄이기 위해 네트워크 보안 그룹과 관련된 다음과 같은 보안 추천 사항이 향상되었습니다.

- VM에 연결된 NSG에서 모든 네트워크 포트를 제한해야 함
- 가상 머신에서 관리 포트를 닫아야 합니다.
- 인터넷 연결 가상 머신은 네트워크 보안 그룹과 함께 보호되어야 합니다.
- 서브넷을 네트워크 보안 그룹과 연결해야 합니다.


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>"Kubernetes 서비스에 Pod 보안 정책을 정의해야 합니다."라는 미리 보기 AKS 추천 사항이 더 이상 사용되지 않음

[Azure Kubernetes Service](../aks/use-pod-security-policies.md) 설명서에서 설명한 대로 "Kubernetes Services에서 Pod 보안 정책을 정의해야 합니다."라는 미리 보기 추천 사항이 더 이상 사용되지 않습니다.

Pod 보안 정책(미리 보기) 기능은 더 이상 사용하지 않도록 설정되며, AKS에 대한 Azure Policy에 따라 2020년 10월 15일 이후에는 더 이상 사용할 수 없습니다.

Pod 보안 정책(미리 보기)이 더 이상 사용되지 않는 경우 향후 클러스터 업그레이드를 수행하고 Azure 지원을 유지하려면 더 이상 사용되지 않는 기능을 사용하여 기존 클러스터에서 이 기능을 사용하지 않도록 설정해야 합니다.


### <a name="email-notifications-from-azure-security-center-improved"></a>Azure Security Center의 이메일 알림이 향상됨

보안 경고와 관련하여 향상된 이메일 영역은 다음과 같습니다. 

- 모든 심각도 수준에 대한 경고와 관련된 이메일 알림을 보내는 기능이 추가되었습니다.
- 구독에서 다른 Azure 역할의 사용자에게 알리는 기능이 추가되었습니다.
- 심각도가 높은 경고(진짜 위반일 가능성이 높음)는 기본적으로 구독 소유자에게 사전에 알려줍니다.
- 이메일 알림 구성 페이지에서 전화 번호 필드가 제거되었습니다.

[보안 경고에 대한 이메일 알림 설정](security-center-provide-security-contact-details.md)에서 자세히 알아보세요.


### <a name="secure-score-doesnt-include-preview-recommendations"></a>보안 점수에 미리 보기 추천 사항이 포함되지 않음 

Security Center는 리소스, 구독 및 조직의 보안 이슈를 지속적으로 평가합니다. 그런 다음, 현재 보안 상황을 한눈에 파악할 수 있도록 모든 결과를 단일 점수에 집계합니다. 즉, 점수가 높을수록 식별된 위험 수준은 낮습니다.

새 위협이 발견되면 Security Center에서 새 추천 사항을 통해 새로운 보안 제안이 제공됩니다. 보안 점수가 예기치 않게 변경되지 않도록 방지하고 새 추천 사항이 점수에 영향을 주기 전에 이러한 새 추천 사항을 검색할 수 있는 유예 기간을 제공하기 위해 **미리 보기** 플래그로 지정된 추천 사항은 더 이상 보안 점수 계산에 포함되지 않습니다. 미리 보기 기간이 종료되면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정해야 합니다.

또한 **미리 보기** 추천 사항은 리소스를 "비정상"으로 렌더링하지 않습니다.

미리 보기 추천 사항의 예는 다음과 같습니다.

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="미리 보기 플래그가 있는 권장 사항":::

[보안 점수](secure-score-security-controls.md)에 대해 자세히 알아보세요.


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>이제 추천 사항에 심각도 표시기 및 새로 고침 간격이 포함됨

추천 사항에 대한 세부 정보 페이지에는 이제 새로 고침 간격 표시기(관련될 때마다) 및 추천 사항의 심각도에 대한 명확한 표시가 포함됩니다.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="새고 고침 및 심각도를 보여 주는 추천 사항 페이지":::


## <a name="august-2020"></a>2020년 8월

8월의 업데이트는 다음과 같습니다.

- [자산 인벤토리 - 자산의 보안 상태에 대한 강력하고 새로운 보기](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Azure Active Directory 보안 기본값에 대한 지원(다단계 인증용)이 추가됨](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [서비스 주체 추천 사항이 추가됨](#service-principals-recommendation-added)
- [VM에 대한 취약성 평가 - 추천 사항과 정책이 통합됨](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [새 AKS 보안 정책이 ASC_default 이니셔티브에 추가됨 - 프라이빗 미리 보기 고객만 사용](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>자산 인벤토리 - 자산의 보안 상태에 대한 강력하고 새로운 보기

Security Center의 자산 인벤토리(현재 미리 보기 상태)는 Security Center에 연결한 리소스의 보안 상태를 확인할 수 있는 방법을 제공합니다.

Security Center는 Azure 리소스의 보안 상태를 정기적으로 분석하여 잠재적인 보안 취약성을 식별합니다. 그런 다음, 이러한 취약성을 수정하는 방법에 대한 추천 사항을 제공합니다. 리소스에 수정되지 않은 추천 사항이 있으면 인벤토리에 표시됩니다.

보기 및 해당 필터를 사용하여 보안 상태 데이터를 검색하고 결과에 따라 추가 작업을 수행할 수 있습니다.

[자산 인벤토리](asset-inventory.md)에 대해 자세히 알아보세요.


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Azure Active Directory 보안 기본값에 대한 지원(다단계 인증용)이 추가됨

Security Center에는 Microsoft의 무료 ID 보안 보호인 [보안 기본값](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)에 대한 완전한 지원이 추가되었습니다.

보안 기본값은 일반적인 ID 관련 공격으로부터 조직을 보호하기 위해 미리 구성된 ID 보안 설정을 제공합니다. 보안 기본값은 이미 전체적으로 500만 개 이상의 테넌트를 보호하고 있으며, 5만 개의 테넌트도 Security Center를 통해 보호됩니다.

Security Center는 이제 보안 기본값을 사용하도록 설정되지 않은 Azure 구독을 식별할 때마다 보안 추천 사항을 제공합니다. 지금까지 Security Center는 AD(Azure Active Directory) Premium 라이선스의 일부인 조건부 액세스를 사용하는 다단계 인증을 사용하도록 추천했습니다. Azure AD 체험 라이선스를 사용하는 고객의 경우 이제 보안 기본값을 사용하도록 설정하는 것이 좋습니다. 

Microsoft의 목표는 더 많은 고객이 MFA를 사용하여 클라우드 환경을 보호하도록 권장하고, [보안 점수](secure-score-security-controls.md)에 가장 큰 영향을 미치는 가장 높은 위험 중 하나를 완화하는 것입니다.

[보안 기본값](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)에 대해 자세히 알아보세요.


### <a name="service-principals-recommendation-added"></a>서비스 주체 추천 사항이 추가됨

관리 인증서를 사용하여 구독을 관리하는 Security Center 고객에게 서비스 주체로 전환하도록 추천하는 새 추천 사항이 추가되었습니다.

**관리 인증서 대신 서비스 주체를 사용하여 구독을 보호해야 합니다.** 라는 추천 사항은 서비스 주체 또는 Azure Resource Manager를 사용하여 구독을 더 안전하게 관리하도록 추천합니다. 

[Azure Active Directory의 애플리케이션 및 서비스 주체 개체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)에 대해 자세히 알아보세요.


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>VM에 대한 취약성 평가 - 추천 사항과 정책이 통합됨

Security Center는 VM을 검사하여 해당 VM에서 취약성 평가 솔루션을 실행하고 있는지 여부를 검색합니다. 취약성 평가 솔루션을 찾을 수 없는 경우 Security Center는 배포를 간소화하기 위한 추천 사항을 제공합니다.

취약성이 발견되면 Security Center에서 필요에 따라 조사하고 수정할 수 있도록 결과를 요약한 추천 사항을 제공합니다.

사용하는 스캐너 유형에 관계없이 모든 사용자에게 일관된 환경을 보장하기 위해 4가지 추천 사항이 다음 2가지 추천 사항으로 통합되었습니다.

|통합 추천 사항|변경 내용 설명|
|----|:----|
|**취약성 평가 솔루션을 가상 머신에서 사용하도록 설정해야 함**|다음 두 가지 추천 사항을 대체합니다.<br> ***** 가상 머신에서 기본 제공 취약성 평가 솔루션 사용(Qualys에서 제공(현재 사용되지 않음)(표준 계층에 포함됨))<br> ***** 가상 머신에 취약성 평가 솔루션을 설치해야 함(현재 사용되지 않음)(표준 및 체험 계층)|
|**가상 머신의 취약성을 수정해야 함**|다음 두 가지 추천 사항을 대체합니다.<br>***** 가상 머신에서 발견한 취약성 수정(Qualys 제공)(현재 사용되지 않음)<br>***** 취약성 평가 솔루션으로 취약성을 수정해야 함(현재 사용되지 않음)|
|||

이제 동일한 추천 사항을 사용하여 Qualys 또는 Rapid7과 같은 파트너로부터 Security Center의 취약성 평가 확장 또는 개인적으로 사용이 허가된 솔루션("BYOL")을 배포합니다.

또한 취약성이 발견되어 Security Center에 보고되면 이를 식별한 취약성 평가 솔루션에 관계없이 단일 추천 사항에서 결과를 알려줍니다.

#### <a name="updating-dependencies"></a>종속성 업데이트

이전 추천 사항 또는 정책 키/이름을 참조하는 스크립트, 쿼리 또는 자동화가 있는 경우 아래 표를 사용하여 참조를 업데이트합니다.

##### <a name="before-august-2020"></a>2020년 8월 이전

| 권장|Scope|
|----|:----|
|**가상 머신에서 기본 제공 취약성 평가 솔루션 사용(Qualys에서 제공)**<br>키: 550e890b-e652-4d22-8274-60b3bdb24c63|기본 제공|
|**가상 머신에서 발견한 취약성 수정(Qualys 제공)**<br>키: 1195afff-c881-495e-9bc5-1486211ae03f|기본 제공|
|**가상 머신에 취약성 평가 솔루션을 설치해야 함**<br>키: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**취약성 평가 솔루션으로 취약성을 수정해야 합니다.**<br>키: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
|||


|정책|Scope|
|----|:----|
|**가상 머신에서 취약성 평가를 사용하도록 설정해야 함**<br>정책 ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|기본 제공|
|**취약성 평가 솔루션으로 취약성을 수정해야 함**<br>정책 ID: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
|||


##### <a name="from-august-2020"></a>2020년 8월부터

|권장|Scope|
|----|:----|
|**취약성 평가 솔루션을 가상 머신에서 사용하도록 설정해야 함**<br>키: ffff0522-1e88-47fc-8382-2a80ba848f5d|기본 제공 + BYOL|
|**가상 머신의 취약성을 수정해야 함**<br>키: 1195afff-c881-495e-9bc5-1486211ae03f|기본 제공 + BYOL|
|||

|정책|Scope|
|----|:----|
|[**가상 머신에서 취약성 평가를 사용하도록 설정해야 함**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>정책 ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |기본 제공 + BYOL|
|||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>새 AKS 보안 정책이 ASC_default 이니셔티브에 추가됨 - 프라이빗 미리 보기 고객만 사용

Kubernetes 워크로드에서 기본적으로 보안을 유지할 수 있도록 하기 위해 Security Center에서 Kubernetes 허용 제어를 사용하는 적용 옵션을 포함하여 Kubernetes 수준 정책 보안 강화 추천 사항을 추가합니다.

이 프로젝트의 초기 단계에는 프라이빗 미리 보기 및 ASC_default 이니셔티브에 추가된 새 정책(기본적으로 사용 안 함)이 포함됩니다.

이러한 정책은 무시해도 되지만 환경에는 영향을 주지 않습니다. 이러한 정책을 사용하도록 설정하려면 https://aka.ms/SecurityPrP 에서 미리 보기에 가입하고 다음 옵션 중에서 선택합니다.

1. **단일 미리 보기** – 이 프라이빗 미리 보기에만 조인합니다. "ASC 연속 검사"를 조인하려는 미리 보기로 명시적으로 언급합니다.
1. **진행 중인 프로그램** - 현재 및 향후 프라이빗 미리 보기에 추가됩니다. 프로필 및 개인 정보 계약을 완료해야 합니다.


## <a name="july-2020"></a>2020년 7월

7월의 업데이트는 다음과 같습니다.
- [이제 가상 머신에 대한 취약성 평가를 비 마켓플레이스 이미지에 사용할 수 있음](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Azure Storage에 대한 위협 방지 기능이 Azure Files 및 Azure Data Lake Storage Gen2(미리 보기)로 확장됨](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [위협 방지 기능을 사용하도록 설정하기 위한 새로운 8가지 추천 사항](#eight-new-recommendations-to-enable-threat-protection-features)
- [컨테이너 보안이 향상됨 - 더 빠른 레지스트리 검사 및 새로 고친 설명서](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [적응형 애플리케이션 제어가 새 추천 사항 및 경로 규칙의 와일드카드 지원으로 업데이트됨](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [SQL Advanced Data Security에 대한 6가지 정책이 더 이상 사용되지 않음](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>이제 가상 머신에 대한 취약성 평가를 비 마켓플레이스 이미지에 사용할 수 있음

이전에는 취약성 평가 솔루션을 배포할 때 Security Center에서 배포하기 전에 유효성 검사를 수행했습니다. 이 검사는 대상 가상 머신의 마켓플레이스 SKU를 확인하는 것이었습니다. 

이 업데이트에서는 검사가 제거되었으며, 이제 취약성 평가 도구를 '사용자 지정' Windows 및 Linux 컴퓨터에 배포할 수 있습니다. 사용자 지정 이미지는 마켓플레이스 기본값에서 수정한 이미지입니다.

이제 통합 취약성 평가 확장(Qualys에서 제공)을 더 많은 컴퓨터에 배포할 수 있지만, 지원은 [표준 계층 VM에 통합 취약성 스캐너 배포](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)에 나열된 OS를 사용하는 경우에만 지원을 사용할 수 있습니다.

[가상 머신용 통합 취약성 스캐너(Azure Defender 필요)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)에 대해 자세히 알아보세요.

개인적으로 사용이 허가된 Qualys 또는 Rapid7의 취약성 평가 솔루션을 사용하는 방법은 [파트너 취약성 검사 솔루션 배포](deploy-vulnerability-assessment-vm.md)에서 자세히 알아보세요.


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Azure Storage에 대한 위협 방지 기능이 Azure Files 및 Azure Data Lake Storage Gen2(미리 보기)로 확장됨

Azure Storage에 대한 위협 방지는 Azure Storage 계정에서 잠재적으로 유해한 활동을 탐지합니다. Security Center는 스토리지 계정에 액세스하거나 이를 악용하려는 시도를 탐지하면 경고를 표시합니다. 

Blob 컨테이너, 파일 공유 또는 데이터 레이크로 저장되는지에 관계없이 데이터를 보호할 수 있습니다.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>위협 방지 기능을 사용하도록 설정하기 위한 새로운 8가지 추천 사항

가상 머신, App Service 요금제, Azure SQL Database 서버, 컴퓨터의 SQL 서버, Azure Storage 계정, Azure Kubernetes Service 클러스터, Azure Container Registry 레지스트리 및 Azure Key Vault 자격 증명 모음과 같은 리소스 종류에 대해 Azure Security Center의 위협 방지 기능을 사용하도록 설정하는 간단히 방법을 제공하기 위해 8가지 추천 사항이 새로 추가되었습니다.

새 추천 사항은 다음과 같습니다.

- **Azure SQL Database 서버에서 Advanced Data Security를 사용하도록 설정해야 함**
- **머신의 SQL 서버에서 Advanced Data Security를 사용하도록 설정해야 함**
- **Azure App Service 계획에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Azure Container Registry 레지스트리에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Azure Key Vault 자격 증명 모음에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Azure Kubernetes Service 클러스터에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Azure Storage 계정에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Virtual Machines에서 Advanced Threat Protection을 사용하도록 설정해야 함**

이러한 새 추천 사항은 **Azure Defender 사용** 보안 제어에 속합니다.

추천 사항에는 빠른 수정 기능도 포함됩니다. 

> [!IMPORTANT]
> 이러한 추천 사항 중 하나를 수정하면 관련 리소스를 보호하는 데 드는 요금이 청구됩니다. 현재 구독에 관련 리소스가 있는 경우 이러한 청구가 즉시 시작됩니다. 또는 나중에 추가하는 경우가 있습니다.
> 
> 예를 들어 구독에 Azure Kubernetes Service 클러스터가 없고 위협 방지를 사용하도록 설정하면 요금이 발생하지 않습니다. 나중에 클러스터를 동일한 구독에 추가하면 해당 클러스터가 자동으로 보호되고 해당 시간에 요금이 청구됩니다.

이러한 리소스 각각에 대해 [보안 추천 사항 참조 페이지](recommendations-reference.md)에서 자세히 알아보세요.

[Azure Security Center의 위협 방지](azure-defender.md)에 대해 자세히 알아보세요.




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>컨테이너 보안이 향상됨 - 더 빠른 레지스트리 검사 및 새로 고친 설명서

컨테이너 보안 도메인에 대한 지속적인 투자의 일환으로, Azure Container Registry에 저장된 컨테이너 이미지에 대한 Security Center의 동적 검사에서 상당히 향상된 성능을 공유할 수 있습니다. 이제 검사는 일반적으로 약 2분 안에 완료됩니다. 경우에 따라 최대 15분이 걸릴 수 있습니다.

Azure Security Center의 컨테이너 보안 기능에 대한 명확성과 지침을 향상시키기 위해 컨테이너 보안 문서 페이지도 새로 고쳤습니다. 

Security Center의 컨테이너 보안에 대해 다음 문서에서 자세히 알아보세요.

- [Security Center의 컨테이너 보안 기능에 대한 개요](container-security.md)
- [Azure Container Registry와의 통합에 대한 세부 정보](defender-for-container-registries-introduction.md)
- [Azure Kubernetes Service와의 통합에 대한 세부 정보](defender-for-kubernetes-introduction.md)
- [레지스트리를 검사하고 Docker 호스트를 강화하는 방법](container-security.md)
- [Azure Kubernetes Service 클러스터에 대한 위협 방지 기능의 보안 경고](alerts-reference.md#alerts-k8scluster)
- [Azure Kubernetes Service 호스트에 대한 위협 방지 기능의 보안 경고](alerts-reference.md#alerts-containerhost)
- [컨테이너에 대한 보안 추천 사항](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>적응형 애플리케이션 제어가 새 추천 사항 및 경로 규칙의 와일드카드 지원으로 업데이트됨

적응형 애플리케이션 제어 기능에는 다음과 같은 두 가지 중요한 업데이트가 제공되었습니다.

* 새 추천 사항은 이전에 허용되지 않은 잠재적으로 합법적인 동작을 식별합니다. **적응형 애플리케이션 제어 정책의 허용 목록 규칙을 업데이트해야 합니다.** 라는 새 추천 사항은 적응형 애플리케이션 제어 위반 경고의 가양성의 수를 줄이기 위해 새 규칙을 기존 정책에 추가하도록 요청합니다.

* 경로 규칙에서 이제 와일드카드를 지원합니다. 이 업데이트에서는 와일드카드를 사용하여 허용되는 경로 규칙을 구성할 수 있습니다. 지원되는 두 가지 시나리오는 다음과 같습니다.

    * 와일드카드를 경로 끝에 사용하여 이 폴더 및 하위 폴더에 있는 모든 실행 파일 허용

    * 와일드카드를 경로 중간에 사용하여 변경된 폴더 이름(예: 알려진 실행 파일이 있는 개인 사용자 폴더, 자동으로 생성된 폴더 이름 등)으로 알려진 실행 파일 이름을 사용할 수 있도록 설정.


[적응형 애플리케이션 제어에 대해 자세히 알아봅니다](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>SQL Advanced Data Security에 대한 6가지 정책이 더 이상 사용되지 않음

SQL 컴퓨터의 고급 데이터 보안과 관련된 6가지 정책은 더 이상 사용되지 않습니다.

- SQL Managed Instance Advanced Data Security 설정에서 Advanced Threat Protection 유형을 '모두'로 설정해야 합니다.
- SQL Server Advanced Data Security 설정에서 Advanced Threat Protection 유형을 '모두'로 설정해야 합니다.
- SQL 관리형 인스턴스에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.
- SQL Server에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.
- SQL 관리형 인스턴스 Advanced Data Security 설정에는 관리자 및 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.
- SQL Server Advanced Data Security 설정에는 관리자 및 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.

[기본 제공 정책](./policy-reference.md)에 대해 자세히 알아보세요.



## <a name="june-2020"></a>2020년 6월

6월의 업데이트는 다음과 같습니다.

- [보안 점수 API(미리 보기)](#secure-score-api-preview)
- [SQL 컴퓨터(Azure, 기타 클라우드 및 온-프레미스)에 대한 Advanced Data Security(미리 보기)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Log Analytics 에이전트를 Azure Arc 컴퓨터에 배포하기 위한 두 가지 새 추천 사항(미리 보기)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [연속 내보내기 및 워크플로 자동화 구성을 규모에 맞게 만들기 위한 새 정책](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [NSG를 사용하여 인터넷에 연결되지 않은 가상 머신을 보호하기 위한 새 추천 사항](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [위협 방지 및 고급 데이터 보안을 사용하도록 설정하기 위한 새 정책](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>보안 점수 API(미리 보기)

이제 [보안 점수 API](/rest/api/securitycenter/securescores/)(현재 미리 보기 상태)를 통해 점수에 액세스할 수 있습니다. API 메서드는 데이터를 쿼리할 수 있는 유연성을 제공하고, 시간 경과에 따른 보안 점수에 대한 사용자 고유의 보고 메커니즘을 빌드합니다. 예를 들어 **보안 점수** API를 사용하여 특정 구독에 대한 점수를 가져올 수 있습니다. 또한 **보안 점수 제어** API를 사용하여 보안 제어 및 구독의 현재 점수를 나열할 수 있습니다.

보안 점수 API를 사용하여 가능한 외부 도구의 예는 [GitHub 커뮤니티의 보안 점수 영역](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)을 참조하세요.

[Azure Security Center의 보안 점수 및 보안 제어](secure-score-security-controls.md)에 대해 자세히 알아보세요.



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>SQL 컴퓨터(Azure, 기타 클라우드 및 온-프레미스)에 대한 Advanced Data Security(미리 보기)

SQL 컴퓨터에 대한 Azure Security Center의 고급 데이터 보안 기능은 이제 Azure, 다른 클라우드 환경 및 심지어 온-프레미스 컴퓨터에서 호스팅되는 SQL Server를 보호합니다. 이렇게 하면 Azure 네이티브 SQL Server에 대한 보호 기능이 확장되어 하이브리드 환경을 완벽하게 지원합니다.

고급 데이터 보안은 SQL 컴퓨터가 어디에 있든 취약성 평가 및 지능형 위협 방지 기능을 제공합니다.

설정에는 다음 두 가지 단계가 포함됩니다.

1. Log Analytics 에이전트를 SQL Server의 호스트 컴퓨터에 배포하여 Azure 계정에 대한 연결을 제공합니다.

1. Security Center의 가격 책정 및 설정 페이지에서 선택적 번들을 사용하도록 설정합니다.

[SQL 컴퓨터에 대한 고급 데이터 보안](defender-for-sql-usage.md)에 대해 자세히 알아보세요.



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Log Analytics 에이전트를 Azure Arc 컴퓨터에 배포하기 위한 두 가지 새 추천 사항(미리 보기)

[Log Analytics 에이전트](../azure-monitor/agents/log-analytics-agent.md)를 Azure Arc 컴퓨터에 배포하고 Azure Security Center에서 보호하도록 하는 데 도움이 되는 두 가지 추천 사항이 새로 추가되었습니다.

- **Windows 기반 Azure Arc 컴퓨터에 Log Analytics 에이전트를 설치해야 함(미리 보기)**
- **Linux 기반 Azure Arc 컴퓨터에 Log Analytics 에이전트를 설치해야 함(미리 보기)**

이러한 새 추천 사항은 기존(관련) 추천 사항(**컴퓨터에 모니터링 에이전트를 설치해야 합니다.** )과 동일한 네 가지 보안 제어(보안 구성 수정, 적응형 애플리케이션 제어 적용, 시스템 업데이트 적용 및 엔드포인트 보호 사용)에 나타납니다.

또한 추천 사항에는 배포 프로세스 속도를 높이는 데 도움이 되는 빠른 수정 기능도 포함됩니다. 

이러한 두 가지 새 추천 사항에 대해 [컴퓨팅 및 앱 추천 사항](recommendations-reference.md#recs-compute) 표에서 자세히 알아보세요.

Azure Security Center에서 에이전트를 사용하는 방법에 대해 [Log Analytics 에이전트는 무엇인가요?](./faq-data-collection-agents.yml#what-is-the-log-analytics-agent-)에서 자세히 알아보세요.

[Azure Arc 컴퓨터용 확장](../azure-arc/servers/manage-vm-extensions.md)에 대해 자세히 알아보세요.


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>연속 내보내기 및 워크플로 자동화 구성을 규모에 맞게 만들기 위한 새 정책

조직의 모니터링 및 인시던트 대응 프로세스를 자동화하면 보안 인시던트를 조사하고 완화하는 데 걸리는 시간을 크게 향상시킬 수 있습니다.

자동화 구성을 조직 전체에 배포하려면 다음과 같은 기본 제공 'DeployIfdNotExist' Azure 정책을 사용하여 [연속 내보내기](continuous-export.md) 및 [워크플로 자동화](workflow-automation.md) 프로시저를 만들고 구성합니다.

정책 정의는 Azure Policy에서 찾을 수 있습니다.


|목표  |정책  |정책 ID  |
|---------|---------|---------|
|이벤트 허브로 연속 내보내기|[Azure Security Center 경고 및 권장 사항에 대한 Event Hub로 내보내기 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Log Analytics 작업 영역으로 연속 내보내기|[Azure Security Center 경고 및 권장 사항에 대한 Log Analytics 작업 영역으로 내보내기 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|보안 경고에 대한 워크플로 자동화|[Azure Security Center 경고에 대한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|보안 추천 사항에 대한 워크플로 자동화|[Azure Security Center 권장 사항에 대한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

[워크플로 자동화 템플릿](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)을 시작합니다.

[제공된 정책을 사용하여 대규모 워크플로 자동화 구성](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) 및 [연속 내보내기 설정](continuous-export.md#set-up-a-continuous-export)에서 두 가지 내보내기 정책 사용에 대해 자세히 알아봅니다.


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>NSG를 사용하여 인터넷에 연결되지 않은 가상 머신을 보호하기 위한 새 추천 사항

"보안 모범 사례 구현" 보안 제어에는 이제 다음과 같은 새 추천 사항이 포함됩니다.

- **네트워크 보안 그룹을 사용하여 비인터넷 연결 가상 머신을 보호해야 함**

**네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함** 이라는 기존 추천 사항은 VM을 대상으로 하는 인터넷 연결 VM과 인터넷에 연결되지 않은 VM을 구분하지 않았습니다. 두 경우 모두에서 VM이 네트워크 보안 그룹에 할당되지 않은 경우 높은 심각도 추천 사항이 생성되었습니다. 이 새 추천 사항은 인터넷에 연결되지 않은 머신을 분리하여 가양성을 줄이고 불필요한 높은 심각도 경고를 방지합니다.

[네트워크 추천 사항](recommendations-reference.md#recs-networking) 표에서 자세히 알아보세요.




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>위협 방지 및 고급 데이터 보안을 사용하도록 설정하기 위한 새 정책

아래의 새 정책 정의는 ASC 기본 이니셔티브에 추가되었으며, 관련 리소스 종류에 대한 위협 방지 또는 고급 데이터 보안을 지원하도록 설계되었습니다.

정책 정의는 Azure Policy에서 찾을 수 있습니다.


| 정책                                                                                                                                                                                                                                                                | 정책 ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Azure SQL Database 서버에서 Advanced Data Security를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [머신의 SQL 서버에서 Advanced Data Security를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Azure Storage 계정에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Azure Key Vault 자격 증명 모음에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Azure App Service 계획에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Azure Container Registry 레지스트리에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Azure Kubernetes Service 클러스터에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Virtual Machines에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

[Azure Security Center의 위협 방지](azure-defender.md)에 대해 자세히 알아보세요.


## <a name="may-2020"></a>2020년 5월

5월의 업데이트는 다음과 같습니다.
- [중복된 경고 제거 규칙(미리 보기)](#alert-suppression-rules-preview)
- [공급되는 가상 머신 취약성 평가 일반 공급](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [JIT(just-in-Time) VM(가상 머신) 액세스의 변경 사항](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [사용자 지정 권장 사항이 별도의 보안 컨트롤로 이동됨](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [설정/해제가 컨트롤의 권장 사항 보기에 또는 단순 목록으로 추가됨](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [확장된 보안 컨트롤 "보안 모범 사례 구현"](#expanded-security-control-implement-security-best-practices)
- [이제 일반 공급되는 사용자 지정 메타데이터가 포함된 사용자 지정 정책](#custom-policies-with-custom-metadata-are-now-generally-available)
- [파일리스 공격 탐지로 마이그레이션하는 크래시 덤프 분석 기능](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>중복된 경고 제거 규칙(미리 보기)

이 새로운 기능(현재 미리 보기 상태)은 경고 피로를 줄이는 데 도움이 됩니다. 규칙을 사용하여 조직 내 정상적인 작업에 무해하거나 관련이 있는 것으로 알려진 경고를 자동으로 숨깁니다. 이렇게 하면 가장 관련성이 높은 위협에 집중할 수 있습니다. 

활성화된 중복된 경고 제거 규칙과 일치하는 경고는 계속 생성되지만 해당 상태는 해제됨으로 설정됩니다. Azure Portal에서 상태를 보거나 Security Center 보안 경고에 액세스할 수 있습니다.

중복된 경고 제거 규칙은 경고를 자동으로 해제할 조건을 정의합니다. 일반적으로 다음과 같은 경우에는 중복된 경고 제거 규칙을 사용합니다.

- 거짓 긍정으로 식별한 경고를 표시 안 함

- 너무 자주 트리거되는 경고를 표시 안 함

[Azure Security Center의 위협 방지에서 경고 표시 안 함](alerts-suppression-rules.md)에 대해 자세히 알아보세요.


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>공급되는 가상 머신 취약성 평가 일반 공급

가상 머신에 대한 통합 취약성 평가가 이제 Security Center의 표준 계층에 추가 비용 없이 포함됩니다. 이 확장은 Qualys에서 제공하지만 검색 결과를 다시 Security Center에 보고합니다. Qualys 라이선스 또는 Qualys 계정이 필요하지 않습니다. 모든 항목이 Security Center 내에서 원활하게 처리됩니다.

새 솔루션은 가상 머신을 지속적으로 스캔하여 취약성을 찾고 Security Center에 검색 결과를 제공할 수 있습니다. 

솔루션을 배포하려면 새 보안 권장 사항을 사용합니다.

"가상 머신에서 기본 제공 취약성 평가 솔루션 사용(Qualys에서 제공)"

[가상 머신에 대한 Security Center의 통합 취약성 평가](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)에 대해 자세히 알아보세요.



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>JIT(just-in-Time) VM(가상 머신) 액세스의 변경 사항

Security Center에는 VM의 관리 포트를 보호하는 선택적 기능이 포함되어 있습니다. 이는 가장 일반적인 형태의 무차별 암호 대입 공격에 대한 방어를 제공합니다.

이 업데이트는 이 기능을 다음과 같이 변경합니다.

- VM에서 JIT를 사용하도록 설정하라는 권장 사항의 이름이 변경되었습니다. 이전의 "가상 머신에서 Just-In-Time 네트워크 액세스 제어를 적용해야 함"은 이제 다음과 같습니다. "가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함".

- 추천 사항은 열린 관리 포트가 있는 경우에만 트리거됩니다.

[JIT 액세스 기능](security-center-just-in-time.md)에 대해 자세히 알아보세요.


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>사용자 지정 권장 사항이 별도의 보안 컨트롤로 이동됨

향상된 보안 점수와 함께 도입된 보안 제어 중 하나는 "보안 모범 사례 구현"이었습니다. 구독에 대해 생성된 사용자 지정 권장 사항은 자동으로 해당 컨트롤에 배치되었습니다. 

사용자 지정 권장 사항을 더 쉽게 찾을 수 있도록 이를 전용 보안 컨트롤인 "사용자 지정 권장 사항"으로 이동했습니다. 이 컨트롤은 보안 점수에 영향을 미치지 않습니다.

[Azure Security Center에서 향상된 보안 점수(미리 보기)](secure-score-security-controls.md)의 보안 컨트롤에 대해 자세히 알아보세요.


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>설정/해제가 컨트롤의 권장 사항 보기에 또는 단순 목록으로 추가됨

보안 컨트롤은 관련된 보안 권장 사항의 논리적 그룹입니다. 취약한 공격 노출 영역을 반영합니다. 컨트롤은 이러한 권장 사항을 구현하는 데 도움이 되는 지침을 포함하는 보안 권장 사항 집합입니다.

조직이 각 개별 공격 노출 영역을 얼마나 잘 보호하고 있는지 즉시 확인하려면 각 보안 컨트롤의 점수를 검토합니다.

기본적으로 권장 사항은 보안 컨트롤에 표시됩니다. 이 업데이트에서는 목록으로 표시할 수도 있습니다. 영향을 받는 리소스의 상태를 기준으로 정렬된 간단한 목록으로 표시하려면 새 ' 컨트롤 그룹화' 토글을 사용합니다. 토글은 포털의 목록 상단에 있습니다.

보안 컨트롤 및 이 토글은 새로운 보안 점수 환경의 일부입니다. 포털 내에서 사용자 의견을 보내 주시기 바랍니다.

[Azure Security Center에서 향상된 보안 점수(미리 보기)](secure-score-security-controls.md)의 보안 컨트롤에 대해 자세히 알아보세요.

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="추천 사항에 대한 제어를 기준으로 그룹화 설정/해제":::

### <a name="expanded-security-control-implement-security-best-practices"></a>확장된 보안 컨트롤 "보안 모범 사례 구현" 

향상된 보안 점수와 함께 도입된 보안 제어 중 하나는 "보안 모범 사례 구현"입니다. 이 컨트롤에 권장 사항이 있으면 보안 점수에 영향을 주지 않습니다. 

이 업데이트를 사용하면 세 가지 권장 사항이 원래 배치된 컨트롤에서 이 모범 사례 제어로 이동했습니다. 이러한 세 가지 권장 사항에 대한 위험이 처음에 생각된 것보다 낮은 것으로 판단했기 때문에 이 단계를 수행했습니다.

또한 이 컨트롤에는 두 가지 새로운 권장 사항이 도입되어 추가되었습니다.

이동한 세 가지 권장 사항은 다음과 같습니다.

- **구독에 대한 읽기 권한이 있는 계정에서 MFA를 사용하도록 설정해야 합니다**(원래 "MFA 사용" 컨트롤).
- **읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다**(원래 "액세스 및 권한 관리" 컨트롤).
- **구독에 대해 최대 3명의 소유자를 지정해야 합니다**(원래 "액세스 및 권한 관리" 컨트롤).

컨트롤에 추가된 두 가지 새로운 권장 사항은 다음과 같습니다.

- **Windows Virtual Machines에 게스트 구성 확장을 설치해야 함(미리 보기)** - [Azure Policy 게스트 구성](../governance/policy/concepts/guest-configuration.md)을 사용하면 가상 머신 내에서 서버 및 애플리케이션 설정에 대한 가시성을 제공합니다(Windows만 해당).

- **Windows Defender Exploit Guard를 머신에서 사용하도록 설정해야 함(미리 보기)** - Windows Defender Exploit Guard는 Azure Policy 게스트 구성 에이전트를 활용합니다. Exploit Guard에는 다양한 공격 벡터에 대해 디바이스를 잠그고 맬웨어 공격에서 일반적으로 사용되는 동작을 차단하면서 기업에서 보안 위험과 생산성 요구 사항 사이의 균형을 맞출 수 있도록 설계된 4가지 구성 요소가 있습니다(Windows에만 해당).

[Exploit Guard 정책 만들기 및 배포](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)에서 Windows Defender Exploit Guard에 대해 자세히 알아보세요.

[향상된 보안 점수(미리 보기)](secure-score-security-controls.md)의 보안 제어에 대해 자세히 알아보세요.



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>이제 일반 공급되는 사용자 지정 메타데이터가 포함된 사용자 지정 정책

사용자 지정 정책은 이제 Security Center 권장 사항 환경, 보안 점수 및 규정 준수 표준 대시보드의 일부입니다. 이 기능은 이제 일반 공급되며, Security Center에서 조직의 보안 평가 적용 범위를 확장할 수 있습니다. 

Azure Policy에서 사용자 지정 이니셔티브를 만들고, 여기에 정책을 추가하고 Azure Security Center에 온보딩하고, 권장 사항으로 시각화합니다.

이제 사용자 지정 권장 구성 메타데이터를 편집하는 옵션도 추가했습니다. 메타데이터 옵션에는 심각도, 수정 단계, 위협 정보 등이 포함됩니다.  

[세부 정보를 사용하여 사용자 지정 권장 사항 향상](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information)에 대해 자세히 알아보세요.



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>파일리스 공격 탐지로 마이그레이션하는 크래시 덤프 분석 기능 

Microsoft는 CDA(Windows 크래시 덤프 분석) 탐지 기능을 [파일리스 공격 탐지](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers)에 통합하고 있습니다. 파일리스 공격 탐지 분석은 Windows 컴퓨터에 대해 다음과 같은 보안 경고의 향상된 버전을 제공합니다. 코드 주입 검색됨, 모조 Windows 모듈 탐지됨, 셸 코드 검색됨 및 의심스러운 코드 세그먼트 탐지됨.

이러한 전환의 이점 중 일부는 다음과 같습니다.

- **사전 예방적이고 시기 적절한 맬웨어 탐지** - 작동 중단이 발생할 때까지 기다린 다음, 분석을 실행하여 악성 아티팩트를 찾는 데 관련된 CDA 접근 방법입니다. 파일리스 공격 탐지를 사용하면 실행되는 동안 메모리 내 위협이 사전 예방적으로 식별됩니다. 

- **보강 경고** - 파일리스 공격 탐지의 보안 경고에는 CDA에서 사용할 수 없는 강화(예: 활성 네트워크 연결 정보)가 포함됩니다. 

- **경고 집계** - CDA는 단일 크래시 덤프 내에서 여러 공격 패턴을 탐지한 경우 여러 보안 경고를 트리거했습니다. 파일리스 공격 탐지는 동일한 프로세스에서 식별된 모든 공격 패턴을 단일 경고로 결합하여 여러 경고를 상호 연결할 필요가 없도록 합니다.

- **Log Analytics 작업 영역에 대한 요구 사항 감소** - 잠재적으로 중요한 데이터가 포함된 크래시 덤프는 Log Analytics 작업 영역에 더 이상 업로드되지 않습니다.






## <a name="april-2020"></a>2020년 4월

4월의 업데이트는 다음과 같습니다.
- [이제 일반 공급되는 동적 규정 준수 패키지](#dynamic-compliance-packages-are-now-generally-available)
- [이제 Azure Security Center 무료 계층에 포함되는 ID 권장 사항](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>이제 일반 공급되는 동적 규정 준수 패키지

Azure Security Center 규제 준수 대시보드에는 이제 추가 산업 및 규제 표준을 추적하는 **동적 규정 준수 패키지**(현재 일반 공급)가 포함되어 있습니다.

Security Center 보안 정책 페이지에서 구독 또는 관리 그룹에 동적 규정 준수 패키지를 추가할 수 있습니다. 표준 또는 벤치 마크를 온보딩하면 규정 준수 대시보드에 평가로 매핑된 모든 관련 준수 데이터가 포함된 표준이 표시됩니다. 온보딩된 표준에 대한 요약 보고서는 다운로드할 수 있습니다.

이제 다음과 같은 표준을 추가할 수 있습니다.

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **영국 공식 및 영국 NHS**
- **캐나다 연방 PBMM**
- **Azure CIS 1.1.0(신규)** (Azure CIS 1.1.0을 보다 완벽하게 표현)

또한 최근 일반 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대해 Microsoft에서 작성한 Azure 관련 지침인 [Azure 보안 벤치마크](/security/benchmark/azure/introduction)를 추가했습니다. 추가 표준은 사용할 수 있게 되면 대시보드에서 지원될 예정입니다.  
 
[규정 준수 대시보드의 표준 집합 사용자 지정](update-regulatory-compliance-packages.md)에 대해 자세히 알아보세요.


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>이제 Azure Security Center 무료 계층에 포함되는 ID 권장 사항

Azure Security Center 무료 계층에 대한 ID 및 액세스에 대한 보안 권장 사항이 이제 일반 공급됩니다. 이는 CSPM(클라우드 보안 상태 관리) 기능을 무료로 제공하는 노력의 일부입니다. 지금까지 이러한 권장 사항은 표준 가격 책정 계층에서만 사용할 수 있었습니다.

ID 및 액세스 권장 사항의 예는 다음과 같습니다.

- "구독에 대한 소유자 권한이 있는 계정에 대해 다단계 인증을 사용하도록 설정해야 합니다."
- "구독에 대해 최대 3명의 소유자를 지정해야 합니다."
- "더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다."

무료 가격 책정 계층에 대한 구독이 있는 경우 해당 보안 점수는 ID 및 액세스 보안에 대해 평가하지 않았기 때문에 이 변경의 영향을 받습니다.

[ID 및 액세스 권장 사항](recommendations-reference.md#recs-identityandaccess)에 대해 자세히 알아보세요.

[구독에 대한 MFA(다단계 인증) 적용 관리](security-center-identity-access.md)에 대해 자세히 알아보세요.



## <a name="march-2020"></a>2020년 3월

3월의 업데이트는 다음과 같습니다.

- [이제 일반 공급되는 워크플로 자동화](#workflow-automation-is-now-generally-available)
- [Windows Admin Center와 Azure Security Center 통합](#integration-of-azure-security-center-with-windows-admin-center)
- [Azure Kubernetes Service에 대한 보호](#protection-for-azure-kubernetes-service)
- [Just-in-Time 환경 개선](#improved-just-in-time-experience)
- [사용되지 않는 웹 애플리케이션에 대한 두 가지 보안 권장 사항](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>이제 일반 공급되는 워크플로 자동화

Azure Security Center의 워크플로 자동화 기능이 이제 일반 공급됩니다. 이를 사용하여 보안 경고 및 권장 사항에 대한 Logic Apps를 자동으로 트리거합니다. 또한 빠른 수정 옵션을 사용할 수 있는 경고 및 모든 권장 사항에 대해 수동 트리거를 사용할 수 있습니다.

모든 보안 프로그램에는 인시던트 응답을 위한 여러 워크플로가 포함되어 있습니다. 이러한 프로세스에는 관련 이해 관계자에게 알리고, 변경 관리 프로세스를 시작하고, 특정 수정 단계를 적용하는 것이 포함될 수 있습니다. 보안 전문가는 가능한 한 해당 절차의 여러 단계를 자동화할 것을 권장합니다. 자동화는 오버헤드를 줄이고 프로세스 단계가 미리 정의된 요구 사항에 따라 빠르고 일관되게 수행되도록 하여 보안을 향상시킬 수 있습니다.

워크플로를 실행하기 위한 자동 및 수동 Security Center 기능에 대한 자세한 내용은 [워크플로 자동화](workflow-automation.md)를 참조하세요.

[Logic Apps 만들기](../logic-apps/logic-apps-overview.md)에 대해 자세히 알아보세요.


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Windows Admin Center와 Azure Security Center 통합

이제 Windows Admin Center에서 Azure Security Center로 온-프레미스 Windows 서버를 바로 이동할 수 있습니다. 그러면 Security Center는 온-프레미스 서버, 가상 머신 및 추가 PaaS 워크로드를 포함하여 모든 Windows Admin Center 리소스에 대한 보안 정보를 볼 수 있는 단일 창이 됩니다.

Windows Admin Center에서 Azure Security Center로 서버를 이동한 후 다음을 수행할 수 있습니다.

- Windows Admin Center의 Security Center 확장에서 보안 경고 및 권장 사항을 확인합니다.
- 보안 상태를 확인하고 Azure Portal 또는 API를 통해 Security Center에서 Windows Admin Center 관리 서버에 대한 추가 세부 정보를 검색합니다.

[Windows Admin Center와 Azure Security Center를 통합하는 방법](windows-admin-center-integration.md)에 대해 자세히 알아보세요.


### <a name="protection-for-azure-kubernetes-service"></a>Azure Kubernetes Service에 대한 보호

Azure Security Center는 AKS(Azure Kubernetes Service)를 보호하기 위해 컨테이너 보안 기능을 확장하고 있습니다.

널리 사용되는 오픈 소스 플랫폼 Kubernetes는 이제 컨테이너 오케스트레이션에 대한 업계 표준으로 널리 채택되고 있습니다. 이러한 광범위한 구현에도 불구하고, Kubernetes 환경을 보호하는 방법에 대한 이해가 부족합니다. 컨테이너화된 애플리케이션의 공격 노출 영역을 방어하려면 인프라가 안전하게 구성되고 잠재적 위협에 대해 지속적으로 모니터링되도록 하는 전문 지식이 필요합니다.

Security Center 방어에는 다음이 포함됩니다.

- **검색 및 가시성** - Security Center에 등록된 구독 내에서 관리되는 AKS 인스턴스를 지속적으로 검색합니다.
- **보안 권장 사항** - AKS에 대한 보안 모범 사례를 준수하는 데 도움이 되는 실행 가능한 권장 사항입니다. 이러한 권장 사항은 조직의 보안 상태의 일부로 표시되도록 보안 점수에 포함됩니다. AKS 관련 권장 사항의 예는 "역할 기반 액세스 제어를 사용하여 Kubernetes 서비스 클러스터에 대한 액세스를 제한해야 합니다"를 참조하세요.
- **위협 방지** - AKS 배포에 대한 지속적인 분석을 통해 Security Center는 호스트 및 AKS 클러스터 수준에서 탐지된 위협 및 악의적인 활동을 경고합니다.

[Security Center와 Azure Kubernetes Services 통합](defender-for-kubernetes-introduction.md)에 대해 자세히 알아보세요.

[Security Center의 컨테이너 보안 기능](container-security.md)에 대해 자세히 알아보세요.


### <a name="improved-just-in-time-experience"></a>Just-in-Time 환경 개선

관리 포트를 보호하는 Azure Security Center의 Just-in-Time 도구의 기능, 작업 및 UI는 다음과 같이 향상되었습니다. 

- **양쪽 맞춤 필드** - Azure Portal의 Just-in-Time 페이지를 통해 VM(가상 머신)에 대한 액세스를 요청할 때 새 선택적 필드를 사용하여 요청에 대한 근거를 입력할 수 있습니다. 이 필드에 입력한 정보는 활동 로그에서 추적할 수 있습니다. 
- **중복된 JIT(Just-in-Time) 규칙 자동 정리** - JIT 정책을 업데이트할 때마다 정리 도구가 자동으로 실행되어 전체 규칙 집합의 유효성을 검사합니다. 이 도구는 정책의 규칙과 NSG의 규칙 간 불일치를 검색합니다. 정리 도구에서 불일치를 발견하면 원인을 확인하고 안전한 경우 더 이상 필요하지 않은 기본 제공 규칙을 제거합니다. 클리너는 만든 규칙을 삭제하지 않습니다. 

[JIT 액세스 기능](security-center-just-in-time.md)에 대해 자세히 알아보세요.


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>사용되지 않는 웹 애플리케이션에 대한 두 가지 보안 권장 사항

사용되지 않는 웹 애플리케이션에 대한 두 가지 보안 권장 사항은 다음과 같습니다. 

- IaaS NSG의 웹 애플리케이션에 대한 규칙을 강화해야 합니다.
    (관련 정책: IaaS의 웹 애플리케이션에 대한 NSG 규칙을 강화해야 합니다.)

- App Services에 대한 액세스를 제한해야 합니다.
    (관련 정책: App Services에 대한 액세스를 제한해야 합니다[미리 보기].)

이러한 권장 사항은 Security Center 권장 사항 목록에 더 이상 표시되지 않습니다. 관련 정책은 "Security Center 기본값"이라는 이니셔티브에 더 이상 포함되지 않습니다.

[보안 권장 사항](recommendations-reference.md)에 대해 자세히 알아보세요.




## <a name="february-2020"></a>2020년 2월

### <a name="fileless-attack-detection-for-linux-preview"></a>Linux에 대한 파일리스 공격 탐지(미리 보기)

공격자가 더 은밀한 방법을 사용하여 탐지를 방지하려고 함에 따라 Azure Security Center는 Windows 뿐만 아니라 Linux에 대한 파일리스 공격 탐지 기능을 확장하고 있습니다. 파일리스 공격은 소프트웨어 취약성을 이용하고, 악성 페이로드를 무해한 시스템 프로세스에 주입하며 메모리에 숨습니다. 이러한 기술은 다음과 같습니다.

- 디스크에서 맬웨어 추적 최소화 또는 제거
- 디스크 기반 맬웨어 검색 솔루션으로 검색 가능성을 크게 줄임

이 위협에 대처하기 위해 Azure Security Center는 2018년 10월에 파일리스 공격 탐지 기능을 릴리스했으며, Linux에 대해서도 파일리스 공격 탐지 기능을 확장했습니다. 



## <a name="january-2020"></a>2020년 1월

### <a name="enhanced-secure-score-preview"></a>보안 점수 향상(미리 보기)

이제 Azure Security Center의 향상된 버전의 보안 점수 기능이 미리 보기에서 제공됩니다. 이 버전에서 여러 권장 사항은 취약한 공격 노출 영역을 더 잘 반영하는 보안 컨트롤로 그룹화되어 있습니다(예: 관리 포트에 대한 액세스 제한).

미리 보기 단계에서 보안 점수 변경을 숙지하고 환경을 보다 안전하게 보호하는 데 도움이 되는 다른 수정을 결정합니다.

[향상된 보안 점수(미리 보기)](secure-score-security-controls.md)에 대해 자세히 알아보세요.



## <a name="november-2019"></a>2019년 11월

11월의 업데이트는 다음과 같습니다.
 - [북아메리카 지역의 Azure Key Vault에 대한 위협 방지(미리 보기)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Azure Storage에 대한 위협 방지에 맬웨어 평판 차단 포함](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Logic Apps로 워크플로 자동화(미리 보기)](#workflow-automation-with-logic-apps-preview)
 - [대량 리소스에 대한 빠른 수정 일반 공급](#quick-fix-for-bulk-resources-generally-available)
 - [취약성에 대한 컨테이너 이미지 검사(미리 보기)](#scan-container-images-for-vulnerabilities-preview)
 - [추가 규정 준수 표준(미리 보기)](#additional-regulatory-compliance-standards-preview)
 - [Azure Kubernetes Service에 대한 위협 방지(미리 보기)](#threat-protection-for-azure-kubernetes-service-preview)
 - [가상 머신 취약성 평가(미리 보기)](#virtual-machine-vulnerability-assessment-preview)
 - [Azure Virtual Machines의 SQL Server에 대한 고급 데이터 보안(미리 보기)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [사용자 지정 정책 지원(미리 보기)](#support-for-custom-policies-preview)
 - [커뮤니티 및 파트너를 위한 플랫폼으로 Azure Security Center 적용 범위 확장](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [권장 사항 및 경고 내보내기와의 고급 통합(미리 보기)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Windows 관리 센터에서 Security Center에 온-프레미스 서버 온보딩(미리 보기)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>북아메리카 지역의 Azure Key Vault에 대한 위협 방지(미리 보기)

Azure Key Vault는 클라우드에서 키, 비밀, 암호화 키 및 정책을 중앙에서 관리하는 기능을 제공하여 데이터를 보호하고 클라우드 애플리케이션의 성능을 향상시키는 데 필수적인 서비스입니다. Azure Key Vault는 중요한 데이터와 중요 비즈니스용 데이터를 저장하므로 키 자격 증명 모음 및 여기에 저장된 데이터에 대한 보안을 최대화해야 합니다.

Azure Key Vault에 대한 위협 방지를 지원하는 Azure Security Center는 키 자격 증명 모음에 액세스하거나 악용하려는 비정상적인 잠재적 유해 시도를 감지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 새로운 보호 계층을 통해 고객은 보안 전문가가 되거나 보안 모니터링 시스템을 관리하지 않고도 키 자격 증명 모음에 대한 위협을 해결할 수 있습니다. 이 기능은 북아메리카 지역에서 공개 미리 보기로 제공됩니다.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Azure Storage에 대한 위협 방지에 맬웨어 평판 차단 포함

Azure Storage에 대한 위협 방지는 해시 평판 분석 및 활성 Tor 종료 노드(프록시 익명화)로부터의 의심스러운 액세스를 사용하여 Azure Storage에 맬웨어 업로드를 탐지하기 위해 Microsoft Threat Intelligence에서 제공하는 새로운 탐지 기능을 제공합니다. 이제 Azure Security Center를 사용하여 스토리지 계정에서 검색된 맬웨어를 볼 수 있습니다.


### <a name="workflow-automation-with-logic-apps-preview"></a>Logic Apps로 워크플로 자동화(미리 보기)

중앙에서 관리되는 보안 및 IT/운영을 보유한 조직은 내부 워크플로 프로세스를 구현하여 환경에서 불일치가 발견될 때 조직 내에서 필요한 작업을 수행합니다. 대부분의 경우 이러한 워크플로는 반복 가능한 프로세스이며 자동화는 조직 내의 프로세스를 대폭 간소화할 수 있습니다.

고객이 Azure Logic Apps를 활용하는 자동화 구성을 만들고 권장 사항 또는 경고와 같은 특정 ASC 결과에 따라 자동으로 트리거하는 정책을 만들 수 있도록 하는 Security Center의 새로운 기능을 소개하겠습니다. Azure Logic App은 다양한 Logic App 커넥터 커뮤니티에서 지원하는 모든 사용자 지정 작업을 수행하거나, 이메일 보내기 또는 ServiceNow&trade; 티켓 열기와 같은 Security Center에서 제공하는 템플릿 중 하나를 사용하도록 구성할 수 있습니다.

워크플로를 실행하기 위한 자동 및 수동 Security Center 기능에 대한 자세한 내용은 [워크플로 자동화](workflow-automation.md)를 참조하세요.

Logic Apps를 만드는 방법에 대한 자세한 내용은 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 참조하세요.


### <a name="quick-fix-for-bulk-resources-generally-available"></a>대량 리소스에 대한 빠른 수정 일반 공급

Secure Score의 일부로 사용자에게 제공되는 많은 작업으로 인해 대규모 문제를 효과적으로 해결하는 기능이 어려워질 수 있습니다.

보안 구성 오류 수정을 간소화하고 대량의 리소스에 대한 권장 사항을 빠르게 수정하고 보안 점수를 향상할 수 있도록 하려면 빠른 수정 재구성을 사용합니다.

이 작업을 통해 수정을 적용하려는 리소스를 선택하고 사용자 대신 설정을 구성하는 수정 작업을 시작할 수 있습니다.

빠른 수정은 현재 고객에게 Security Center 권장 사항 페이지의 일부로 일반 공급됩니다.

[보안 권장 사항에 대한 참조 가이드](recommendations-reference.md)에서 빠른 수정이 사용하도록 설정된 권장 사항을 확인하세요.


### <a name="scan-container-images-for-vulnerabilities-preview"></a>취약성에 대한 컨테이너 이미지 검사(미리 보기)

이제 Azure Security Center는 Azure Container Registry의 컨테이너 이미지의 취약성을 검사할 수 있습니다.

이미지 검사는 컨테이너 이미지 파일을 구문 분석한 다음 알려진 취약성이 있는지 확인하는 방식으로 작동합니다(Qualys 제공).

검사 자체는 Azure Container Registry에 새 컨테이너 이미지를 푸시할 때 자동으로 트리거됩니다. 발견 된 취약점은 Security Center 권장 사항으로 노출 되 고, 허용 되는 공격 노출 영역을 줄이기 위해 패치 하는 방법에 대 한 정보와 함께 보안 점수에 포함 됩니다.


### <a name="additional-regulatory-compliance-standards-preview"></a>추가 규정 준수 표준(미리 보기)

규정 준수 대시보드는 Security Center 평가를 기반으로 규정 준수 태세에 대한 인사이트를 제공합니다. 이 대시보드는 환경이 특정 규제 표준 및 업계 벤치마크에서 지정한 제어 및 요구 사항을 준수하는 방법을 보여 주고 이러한 요구 사항을 해결하는 방법에 대한 규범적인 권장 사항을 제공합니다.

규정 준수 대시보드는 다음 네 가지 기본 제공 표준을 지원했습니다.  Azure CIS 1.1.0, PCI-DSS, ISO 27001 및 SOC-TSP. 이제 다음과 같은 지원되는 추가 표준의 공개 미리 보기 릴리스를 발표합니다. NIST SP 800-53 4, SWIFT CSP CSCF v2020, 캐나다 연방 PBMM 및 영국 공식과 영국 NHS. 또한 표준에서 더 많은 제어를 제공하고 확장성을 개선하는 Azure CIS 1.1.0의 업데이트된 버전을 릴리스합니다.

[규정 준수 대시보드의 표준 집합을 사용자 지정하는 방법에 대해 자세히 알아봅니다](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Azure Kubernetes Service에 대한 위협 방지(미리 보기)

Kubernetes는 클라우드에서 소프트웨어를 배포하고 관리하기 위한 새로운 표준으로 빠르게 자리 잡고 있습니다. Kubernetes에 대한 풍부한 경험을 가지고 있는 사람은 거의 없으며 일반적으로 일반 엔지니어링 및 관리에만 중점을 두고 보안 측면을 간과하는 사람들이 많습니다. Kubernetes 환경을 안전하게 구성하여, 컨테이너에 초점을 맞춘 공격 표면 도어가 공격자에게 노출되지 않도록 해야 합니다. Security Center는 Azure에서 가장 빠르게 성장하는 서비스 중 하나인 AKS(Azure Kubernetes Service)로 컨테이너 공간의 지원을 확장하고 있습니다.

이 공개 미리 보기 릴리스의 새로운 기능은 다음과 같습니다.

- **검색 및 표시 유형** - Security Center의 등록된 구독 내에서 관리되는 AKS 인스턴스를 지속적으로 검색합니다.
- **보안 점수 권장 사항** - 고객이 AKS에 대한 보안 모범 사례를 준수하고 보안 점수를 높이는 데 도움이 되는 실행 가능한 항목입니다. 권장 사항에는 "역할 기반 액세스 제어를 사용하여 Kubernetes Service 클러스터에 대한 액세스를 제한해야 합니다."와 같은 항목이 포함됩니다.
- **위협 탐지** - "권한 있는 컨테이너 탐지"와 같은 호스트 및 클러스터 기반 분석입니다.


### <a name="virtual-machine-vulnerability-assessment-preview"></a>가상 머신 취약성 평가(미리 보기)

가상 머신에 설치된 애플리케이션에는 가상 머신의 위험으로 이어질 수 있는 취약성이 있을 수 있습니다. Security Center 표준 계층에는 추가 비용 없이 가상 머신에 대한 기본 제공 취약성 평가가 포함되어 있습니다. 공개 미리 보기에서 Qualys가 제공하는 취약성 평가를 통해 가상 머신에 설치된 모든 애플리케이션을 지속적으로 검사하여 취약한 애플리케이션을 찾고 Security Center 포털의 환경에 대한 결과를 제공할 수 있습니다. Security Center는 모든 배포 작업을 처리하므로 사용자의 추가 작업이 필요하지 않습니다. 향후 고객의 고유한 비즈니스 요구 사항을 지원하기 위한 취약성 평가 옵션을 제공할 계획입니다.

[Azure Virtual Machines에 대한 취약성 평가에 대해 자세히 알아보세요](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure Virtual Machines의 SQL Server에 대한 고급 데이터 보안(미리 보기)

IaaS VM에서 실행되는 SQL DB에 대한 위협 방지 및 취약성 평가에 대한 Azure Security Center 지원은 현재 미리 보기로 제공됩니다.

[취약성 평가](../azure-sql/database/sql-vulnerability-assessment.md)는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 서비스를 간편하게 구성합니다. 보안 점수에 대 한 가시성을 제공 하 고 보안 문제를 해결 하 고 데이터베이스 fortifications을 개선 하는 단계를 포함 합니다.

[Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md)은 비정상적이며 잠재적으로 유해할 수 있는 SQL Server 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 검색합니다. 데이터베이스에서 의심스러운 활동을 지속적으로 모니터링하고 비정상적인 데이터베이스 액세스 패턴에 대해 작업 지향 보안 경고를 제공합니다. 이러한 경고는 의심스러운 활동 세부 정보와 위협을 조사하고 완화하기 위한 권장 조치를 제공합니다.


### <a name="support-for-custom-policies-preview"></a>사용자 지정 정책 지원(미리 보기)

Azure Security Center에서 이제 사용자 지정 정책을 지원합니다(미리 보기).

고객은 Azure Policy에서 만든 정책을 기반으로 자체 보안 평가를 사용하여 Security Center의 현재 보안 평가 범위를 확장했습니다. 사용자 지정 정책에 대한 지원을 통해 이제 이 기능을 사용할 수 있습니다.

이러한 새 정책은 Security Center 권장 사항 환경, Secure Score 및 규정 준수 표준 대시보드에 포함됩니다. 사용자 지정 정책 지원을 통해 이제 Azure Policy에서 사용자 지정 이니셔티브를 만든 다음, Security Center에서 정책으로 추가하고 권장 사항으로 시각화할 수 있습니다.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>커뮤니티 및 파트너를 위한 플랫폼으로 Azure Security Center 적용 범위 확장

Security Center를 사용하면 Microsoft뿐만 아니라 Check Point, Tenable 및 CyberArk와 같은 파트너의 기존 솔루션에서 더 많은 통합이 제공되는 권장 사항을 받을 수 있습니다.  Security Center의 간단한 온보딩 흐름은 기존 솔루션을 Security Center에 연결하여 보안 태세 권장 사항을 한 곳에서 확인하고, 통합 보고서를 실행하며, 기본 제공 및 파트너 권장 사항에 대한 모든 Security Center 기능을 활용할 수 있습니다. Security Center 권장 사항을 파트너 제품으로 내보낼 수 있습니다.

[Microsoft 지능형 보안 연합에 대해 자세히 알아봅니다](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>권장 사항 및 경고 내보내기와의 고급 통합(미리 보기)

Security Center 맨 위에서 엔터프라이즈 수준 시나리오를 사용하도록 설정하기 위해 이제 Azure Portal 또는 API를 제외하고 추가 위치에서 Security Center 경고 및 권장 사항을 사용할 수 있습니다. 이를 이벤트 허브 및 Log Analytics 작업 영역으로 직접 내보낼 수 있습니다. 다음은 이러한 새 기능을 중심으로 만들 수 있는 몇 가지 워크플로입니다.

- Log Analytics 작업 영역으로 내보내기를 사용하면 Power BI를 사용하여 사용자 지정 대시보드를 만들 수 있습니다.
- Event Hub로 내보내기를 사용하면 Security Center 경고 및 권장 사항을 타사 SIEM, 타사 솔루션 또는 Azure Data Explorer로 내보낼 수 있습니다.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Windows 관리 센터에서 Security Center에 온-프레미스 서버 온보딩(미리 보기)

Windows Admin Center는 Azure에 배포되지 않은 Windows 서버용 관리 포털로, 백업 및 시스템 업데이트와 같은 몇 가지 Azure 관리 기능을 제공합니다. Windows 관리 센터 환경의 ASC에서 직접 보호하기 위해 이러한 비 Azure 서버를 온보딩하는 기능을 최근에 추가했습니다.

이 새로운 환경을 통해 사용자는 Azure Security Center에 WAC 서버를 온보딩하여 Windows 관리 센터 환경에서 직접 보안 경고 및 권장 사항을 볼 수 있습니다.


## <a name="september-2019"></a>2019년 9월

9월의 업데이트는 다음과 같습니다.

 - [적응형 애플리케이션 제어 향상으로 규칙 관리](#managing-rules-with-adaptive-application-controls-improvements)
 - [Azure Policy를 사용하여 컨테이너 보안 권장 사항 제어](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>적응형 애플리케이션 제어 향상으로 규칙 관리

적응형 애플리케이션 제어를 사용하여 가상 머신에 대한 규칙을 관리하는 환경이 개선되었습니다. Azure Security Center의 적응형 애플리케이션 제어는 가상 머신에서 실행할 수 있는 애플리케이션을 제어하는 데 도움이 됩니다. 규칙 관리의 일반적인 향상 외에도 새 혜택을 통해 새 규칙을 추가할 때 보호할 파일 형식을 제어할 수 있습니다.

[적응형 애플리케이션 제어에 대해 자세히 알아봅니다](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Azure Policy를 사용하여 컨테이너 보안 권장 사항 제어

컨테이너 보안의 취약성을 해결하는 Azure Security Center 권장 사항은 이제 Azure Policy를 통해 사용하거나 사용하지 않도록 설정할 수 있습니다.

사용하도록 설정된 보안 정책을 보려면 Security Center에서 보안 정책 페이지를 엽니다.


## <a name="august-2019"></a>2019년 8월

8월의 업데이트는 다음과 같습니다.

 - [Azure Firewall에 대한 JIT(just-in-time) VM 액세스](#just-in-time-jit-vm-access-for-azure-firewall)
 - [보안 태세를 높이기 위한 단일 클릭 수정(미리 보기)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [테넌트 간 관리](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Azure Firewall에 대한 JIT(just-in-time) VM 액세스 

이제 Azure Firewall에 대한 JIT(just-in-time) VM 액세스가 일반 공급됩니다. 이를 사용하여 NSG 보호 환경 외에도 Azure Firewall 보호 환경을 보호합니다.

JIT VM 액세스는 NSG 및 Azure Firewall 규칙을 사용하여 필요한 경우에만 VM에 제어된 액세스를 제공하여 네트워크 대규모 공격에 대한 노출을 줄입니다.

VM에 JIT를 사용하도록 설정하는 경우 보호될 포트, 포트가 열려 있는 상태로 유지되는 기간 및 이러한 포트에 액세스하도록 승인된 IP 주소를 결정하는 정책을 만듭니다. 이 정책은 사용자가 액세스를 요청할 때 수행할 수 있는 작업을 제어하는 데 도움이 됩니다.

요청은 Azure 활동 로그에 기록되므로 액세스를 쉽게 모니터링하고 감사할 수 있습니다. JIT(just-in-time) 페이지를 사용하면 JIT가 사용하도록 설정된 기존 VM과 JIT가 권장되는 VM을 빠르게 식별할 수 있습니다.

[Azure Firewall에 대해 자세히 알아보세요](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>보안 태세를 높이기 위한 단일 클릭 수정(미리 보기)

보안 점수는 워크로드 보안에 대한 사용자의 대비 상태를 평가할 수 있는 도구입니다. 이는 사용자의 보안 권장 사항을 검토하고 순위를 지정하므로 사용자는 먼저 수행할 권장 사항을 파악할 수 있습니다. 이를 통해 가장 심각한 보안 취약성을 찾아내 조사의 순위를 지정할 수 있습니다.

보안 구성 오류 수정을 간소화하고 보안 점수를 빠르게 개선할 수 있도록 한 번의 클릭으로 대량의 리소스에 대한 권장 사항을 수정할 수 있는 새로운 기능이 추가되었습니다.

이 작업을 통해 수정을 적용하려는 리소스를 선택하고 사용자 대신 설정을 구성하는 수정 작업을 시작할 수 있습니다.

[보안 권장 사항에 대한 참조 가이드](recommendations-reference.md)에서 빠른 수정이 사용하도록 설정된 권장 사항을 확인하세요.


### <a name="cross-tenant-management"></a>테넌트 간 관리

Security Center는 이제 Azure Lighthouse의 일부로 교차 테넌트 관리 시나리오를 지원합니다. 이를 통해 Security Center에서 여러 테넌트의 보안 태세를 파악하고 관리할 수 있습니다. 

[테넌트 간 관리 환경에 대해 자세히 알아보세요](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>2019년 7월

### <a name="updates-to-network-recommendations"></a>네트워크 권장 사항 업데이트

ASC(Azure Security Center)에서 새로운 네트워킹 권장 사항을 출시하고 기존 권장 사항을 개선했습니다. 이제 Security Center를 사용하면 리소스에 대해 훨씬 더 강력한 네트워킹 보호를 보장합니다. 

[네트워크 권장 사항에 대해 자세히 알아봅니다](recommendations-reference.md#recs-networking).


## <a name="june-2019"></a>2019년 6월

### <a name="adaptive-network-hardening---generally-available"></a>적응형 네트워크 강화 - 일반 공급

공용 클라우드에서 실행되는 워크로드에 대한 가장 큰 공격 노출 영역 중 하나는 공용 인터넷과의 연결입니다. 고객은 Azure 워크로드를 필요한 원본 범위에만 사용할 수 있도록 하기 위해 어떤 NSG(네트워크 보안 그룹) 규칙을 사용해야 하는지 파악하기 어렵습니다. 이 기능을 통해 Security Center는 Azure 워크로드의 네트워크 트래픽 및 연결 패턴을 학습하고 인터넷 연결 가상 머신에 대한 NSG 규칙 권장 사항을 제공합니다. 이를 통해 고객은 네트워크 액세스 정책을 더 잘 구성하고 공격에 대한 노출을 제한할 수 있습니다. 

[적응형 네트워크 강화에 대해 자세히 알아봅니다.](security-center-adaptive-network-hardening.md)
