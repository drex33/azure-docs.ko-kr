---
title: Azure Security Center에 대한 릴리스 정보
description: Azure Security Center의 새로운 기능과 변경된 기능에 대한 설명
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 10/03/2021
ms.author: memildin
ms.openlocfilehash: c0ae5cc8d3dee5a09916194418345c1602a19e4b
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424787"
---
# <a name="whats-new-in-azure-security-center"></a>Azure Security Center의 새로운 기능

Security Center는 현재 개발 중이며 지속적으로 향상된 기능을 수용합니다. 이 페이지에서는 최신 개발 정보를 제공하기 위해 새로운 기능, 버그 수정 및 사용되지 않는 기능에 대한 정보를 제공합니다.

이 페이지는 자주 업데이트되므로 자주 다시 방문하세요. 

곧 Security Center에 적용되는 *계획된* 변경 내용에 대한 자세한 내용은 [Azure Security Center의 예정된 중요 변경 내용](upcoming-changes.md)을 참조하세요. 

> [!TIP]
> 6개월 이상된 항목을 찾으려는 경우 [Azure Security Center의 새로운 기능 아카이브](release-notes-archive.md)에서 찾을 수 있습니다.


## <a name="october-2021"></a>2021년 10월

10월의 업데이트는 다음과 같습니다.

- [일부 경고 형식의 접두사가 "ARM_"에서 "VM_" (으)로 변경 되었습니다.](#changed-prefix-of-some-alert-types-from-arm_-to-vm_)

### <a name="changed-prefix-of-some-alert-types-from-arm_-to-vm_"></a>일부 경고 형식의 접두사가 "ARM_"에서 "VM_" (으)로 변경 되었습니다. 

7 월 2021에는 [Azure Defender의 논리적 재구성을 리소스 관리자 경고에 대해](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts) 발표 했습니다. 

일부 Azure Defender 플랜에 대한 논리적 재구성의 일환으로 [Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md)에서 [서버용 Azure Defender](defender-for-servers-introduction.md)로 21개의 경고를 이동했습니다.

이 업데이트를 사용 하 여 다음 표에 나와 있는 것 처럼이 재할당에 일치 하도록 이러한 경고의 접두사를 변경 하 고 "ARM_"을 "VM_"로 바꿉니다.

| 원래 이름                                  | 이 변경 내용에서                              |
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

## <a name="september-2021"></a>2021년 9월

9 월에는 다음 업데이트가 릴리스 되었습니다.

### <a name="two-new-recommendations-to-audit-os-configurations-for-azure-security-baseline-compliance"></a>Azure 보안 기준 준수에 대 한 OS 구성을 감사 하기 위한 두 가지 새로운 권장 사항

다음 두 가지 권장 사항은 [Windows 보안 기준](../governance/policy/samples/guest-configuration-baseline-windows.md) 및 [Linux 보안 기준](../governance/policy/samples/guest-configuration-baseline-linux.md)으로 컴퓨터의 호환성을 평가 하기 위해 릴리스 되었습니다.

- Windows 컴퓨터의 경우 [Windows 컴퓨터에서 보안 구성의 취약성을 재구성 해야 합니다 (게스트 구성에서 구동 됨)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6) .
- Linux 컴퓨터의 경우 [linux 컴퓨터에서 보안 구성의 취약성을 재구성 해야 합니다 (게스트 구성에서 구동)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda) .

이러한 권장 사항은 Azure Policy의 게스트 구성 기능을 사용 하 여 컴퓨터의 OS 구성을 [Azure 보안 벤치 마크](/security/benchmark/azure/overview)에 정의 된 기준선과 비교 합니다.

[게스트 구성을 사용 하 여 컴퓨터의 OS 구성 강화](apply-security-baseline.md)에서 이러한 권장 사항을 사용 하는 방법에 대해 자세히 알아보세요.

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

- [Azure Defender 및 엔드포인트용 Microsoft Defender(Windows)의 기존 사용자](security-center-wdatp.md?tabs=linux#existing-users-of-azure-defender-and-microsoft-defender-for-endpoint-for-windows)
- [엔드포인트용 Microsoft Defender(Windows)와의 통합을 한 번도 설정한 적이 없는 신규 사용자](security-center-wdatp.md?tabs=linux#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)

[Security Center의 통합 EDR 솔루션으로 엔드포인트를 보호: 엔드포인트용 Microsoft Defender](security-center-wdatp.md)를 자세히 알아봅니다.

### <a name="two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview"></a>엔드포인트 보호 솔루션 관리를 위한 두 가지 새로운 권장 사항(미리 보기)

컴퓨터에 엔드포인트 보호 솔루션을 배포하고 유지 관리하기 위한 두 가지 새로운 **미리 보기** 권장 사항을 추가했습니다. 두 권장 사항에는 Azure 가상 머신 및 Azure Arc를 사용하도록 설정된 서버에 연결된 머신에 대한 지원이 포함됩니다.

|권장 |Description |심각도 |
|---|---|---|
|[머신에 Endpoint Protection을 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/4fb67663-9ab9-475d-b026-8c544cced439) |위협 및 취약성으로부터 머신을 보호하려면 지원되는 엔드포인트 보호 솔루션을 설치합니다.  <br> <a href="/azure/security-center/security-center-endpoint-protection">머신의 Endpoint Protection을 평가하는 방법에 대한 자세한 정보</a><br />(관련 정책: [Azure Security Center에서 누락된 엔드포인트 보호 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |높음 |
|[머신에서 엔드포인트 보호 상태 문제를 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/37a3689a-818e-4a0e-82ac-b1392b9bb000) |가상 머신의 엔드포인트 보호 상태 문제를 해결하여 최신 위협 및 취약성으로부터 보호합니다. Azure Security Center 지원 엔드포인트 보호 솔루션은 [여기](./security-center-services.md?tabs=features-windows)에 설명되어 있습니다. 엔드포인트 보호 평가는 <a href='/azure/security-center/security-center-endpoint-protection'>여기</a>에 설명되어 있습니다.<br />(관련 정책: [Azure Security Center에서 누락된 엔드포인트 보호 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |중간 |
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

자세한 내용은 [준수 상태 보고서 및 인증서 생성](security-center-compliance-dashboard.md#generate-compliance-status-reports-and-certificates)을 참조하세요.

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

이 업데이트를 사용하면 지원되는 모든 컴퓨터에 이 확장을 자동으로 프로비전하도록 Security Center를 설정할 수 있습니다. 

:::image type="content" source="media/release-notes/auto-provisioning-guest-configuration.png" alt-text="게스트 구성 확장의 자동 배포를 사용하도록 설정합니다.":::

[에이전트 및 확장에 대한 자동 프로비저닝 구성](security-center-enable-data-collection.md)에서 자동 프로비저닝의 작동 방식에 대해 자세히 알아봅니다.

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

Azure Sentinel에는 구독 및 테넌트 수준에서 Azure Security Center에 대한 기본 제공 커넥터가 포함되어 있습니다. [Azure Sentinel로 경고 스트리밍](export-to-siem.md#stream-alerts-to-azure-sentinel)에서 자세히 알아보세요.

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

2021년 5월에 평가 API를 **FirstEvaluationDate** 및 **StatusChangeDate** 라는 두 개의 새 필드로 업데이트했습니다. [평가 API가 두 개의 새 필드로 확장됨](#assessments-api-expanded-with-two-new-fields)에서 자세히 알아보세요.

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

Azure Defender for Kubernetes는 최근에 확장되어 온-프레미스 및 다중 클라우드 환경에서 호스트되는 Kubernetes 클러스터를 보호합니다. [Azure Defender for Kubernetes를 사용하여 하이브리드 및 다중 클라우드 Kubernetes 배포 보호(미리 보기)](release-notes-archive.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)에서 자세히 알아봅니다.

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


## <a name="may-2021"></a>2021년 5월

5월의 업데이트는 다음과 같습니다.

- [DNS 및 Resource Manager용 Azure Defender가 GA(일반 공급)용으로 출시](#azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga)
- [오픈 소스 관계형 데이터베이스용 Azure Defender가 GA(일반 공급)용으로 출시](#azure-defender-for-open-source-relational-databases-released-for-general-availability-ga)
- [Resource Manager용 Azure Defender의 새 경고](#new-alerts-for-azure-defender-for-resource-manager)
- [GitHub 워크플로 및 Azure Defender를 사용한 컨테이너 이미지의 CI/CD 취약성 검색(미리 보기)](#cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview)
- [일부 권장 사항에 사용할 수 있는 추가 Resource Graph 쿼리](#more-resource-graph-queries-available-for-some-recommendations)
- [SQL 데이터 분류 권장 사항 심각도가 변경됨](#sql-data-classification-recommendation-severity-changed)
- [신뢰할 수 있는 시작 기능을 사용하도록 설정하는 새로운 권장 사항(미리 보기)](#new-recommendations-to-enable-trusted-launch-capabilities-in-preview)
- [Kubernetes 클러스터 강화에 대한 새로운 권장 사항(미리 보기)](#new-recommendations-for-hardening-kubernetes-clusters-in-preview)
- [평가 API가 두 개의 새 필드로 확장됨](#assessments-api-expanded-with-two-new-fields)
- [자산 인벤토리가 클라우드 환경 필터를 가져옴](#asset-inventory-gets-a-cloud-environment-filter)


### <a name="azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga"></a>DNS 및 Resource Manager용 Azure Defender가 GA(일반 공급)용으로 출시

이러한 두 클라우드 네이티브 범위 위협 보호 플랜은 이제 GA입니다.

이러한 새로운 보호 기능은 위협 행위자의 공격에 대한 복원력을 크게 향상시키고 Azure Defender로 보호되는 Azure 리소스의 수를 크게 늘립니다.

- **Azure Defender for Resource Manager** - 조직에서 수행되는 모든 리소스 관리 작업을 자동으로 모니터링합니다. 자세한 내용은 다음을 참조하세요.
    - [Azure Defender for Resource Manager 소개](defender-for-resource-manager-introduction.md)
    - [Azure Defender for Resource Manager 경고에 대한 대응](defender-for-resource-manager-usage.md)
    - [Azure Defender for Resource Manager에서 제공하는 경고 목록](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender for DNS** - Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링합니다. 자세한 내용은 다음을 참조하세요.
    - [Azure Defender for DNS 소개](defender-for-dns-introduction.md)
    - [Azure Defender for DNS 경고에 대한 대응](defender-for-dns-usage.md)
    - [Azure Defender for DNS에서 제공하는 경고 목록](alerts-reference.md#alerts-dns)

이러한 계획을 사용하도록 설정하는 프로세스를 간소화하려면 다음 권장 사항을 사용합니다.

- **Azure Defender for Resource Manager를 사용하도록 설정해야 함**
- **Azure Defender for DNS를 사용하도록 설정해야 함**

> [!NOTE]
> Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: https://aka.ms/pricing-security-center 에서 지역별 가격 정보에 대해 알아봅니다.


### <a name="azure-defender-for-open-source-relational-databases-released-for-general-availability-ga"></a>오픈 소스 관계형 데이터베이스용 Azure Defender가 GA(일반 공급)용으로 출시

Azure Security Center에서 오픈 소스 관계형 데이터베이스를 포괄하는 새 번들로 SQL 보호에 대한 제안을 확장합니다.

- **Azure SQL 데이터베이스 서버용 Azure Defender** - Azure 네이티브 SQL Server 방어
- **컴퓨터의 SQL 서버용 Azure Defender** - 동일한 보호를 하이브리드, 다중 클라우드 및 온-프레미스 환경의 SQL 서버로 확장
- **오픈 소스 관계형 데이터베이스용 Azure Defender** - Azure Databases for MySQL, PostgreSQL 및 MariaDB 단일 서버를 방어합니다.

오픈 소스 관계형 데이터베이스용 Azure Defender는 서버의 보안 위협을 지속적으로 모니터링하고 Azure Database for MySQL, PostgreSQL 및 MariaDB에 대한 잠재적 위협을 나타내는 비정상적인 데이터베이스 활동을 검색합니다. 몇 가지 예는 다음과 같습니다.

- **무차별 암호 대입 공격 탐지** - 오픈 소스 관계형 데이터베이스용 Azure Defender는 성공적으로 시도된 무차별 암호 대입 공격에 대한 자세한 정보를 제공합니다. 이를 통해 사용자 환경에 대한 공격의 특성과 상태를 보다 완전하게 이해함으로써 추가적으로 조사하고 대응할 수 있습니다.
- **동작 경고 검색** - 오픈 소스 관계형 데이터베이스용 Azure Defender는 데이터베이스에 대한 달라진 액세스 패턴 등, 서버의 의심스럽고 예기치 않은 동작을 경고합니다.
- **위협 인텔리전스 기반 검색** - Azure Defender는 Microsoft의 위협 인텔리전스 및 방대한 기술 자료를 적용하여 위협 경고를 표면화함으로써 조치를 취할 수 있도록 합니다.

[오픈 소스 관계형 데이터베이스용 Azure Defender 소개](defender-for-databases-introduction.md)에서 자세히 알아보세요.

### <a name="new-alerts-for-azure-defender-for-resource-manager"></a>Resource Manager용 Azure Defender의 새 경고

Resource Manager용 Azure Defender에서 제공하는 위협 방지 기능을 확장하기 위해 다음과 같은 경고를 추가했습니다.

| 경고(경고 유형)                                                                                                                                                | Description                                                                                                                                                                                                                                                                                                                                                                                                                              | MITRE 전술 | 심각도 |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------:|----------|
|**Azure 환경에 대해 비정상적인 방식으로 RBAC 역할에 대해 사용 권한이 부여됨(미리 보기)**<br>(ARM_AnomalousRBACRoleAssignment)|Resource Manager용 Azure Defender가 할당 시간, 할당자 위치, 할당자, 인증 방법, 할당된 엔터티, 사용되는 클라이언트 소프트웨어, 할당 익스텐트 등의 변칙으로 인해 테넌트의 동일한 담당자에 대해 동일한 할당자가 수행한 다른 할당과 비교할 때 일반적이지 않은 RBAC 역할 할당을 감지했습니다. 조직의 합법적인 사용자가 이 작업을 수행했을 수 있습니다. 또는 조직의 계정이 위반되었으며 위협 행위자가 소유한 추가 사용자 계정에 사용 권한을 부여하려고 시도하고 있음을 나타낼 수 있습니다.|수평 이동, 방어 우회|중간|
|**구독에 대해 권한 있는 사용자 지정 역할이 의심스러운 방식으로 생성됨(미리 보기)**<br>(ARM_PrivilegedRoleDefinitionCreation)|Resource Manager용 Azure Defender가 구독에서 의심스러운 방식으로 생성된 권한 있는 사용자 지정 역할 정의를 감지했습니다. 조직의 합법적인 사용자가 이 작업을 수행했을 수 있습니다. 또는 조직의 계정이 위반되었으며 위협 행위자가 나중에 우회하는 데 사용할 권한 있는 역할을 만들려고 하는 것임을 나타낼 수 있습니다.|수평 이동, 방어 우회|낮음|
|**의심스러운 IP 주소의 Azure Resource Manager 작업**<br>(ARM_OperationFromSuspiciousIP)|Resource Manager용 Azure Defender가 위협 인텔리전스 피드에서 의심스러운 것으로 표시된 IP 주소의 작업을 감지했습니다.|실행|중간|
|**의심스러운 프록시 IP 주소의 Azure Resource Manager 작업(미리 보기)**<br>(ARM_OperationFromSuspiciousProxyIP)|Resource Manager용 Azure Defender가 TOR과 같은 프록시 서비스와 연결된 IP 주소의 리소스 관리 작업을 감지했습니다. 이 동작은 합법적일 수 있지만, 위협 행위자가 원본 IP를 숨기려고 하는 악의적인 활동에서 나타나는 경우가 많습니다.|방어 회피|중간|
||||

자세한 내용은 다음을 참조하세요.
- [Azure Defender for Resource Manager 소개](defender-for-resource-manager-introduction.md)
- [Azure Defender for Resource Manager 경고에 대한 대응](defender-for-resource-manager-usage.md)
- [Azure Defender for Resource Manager에서 제공하는 경고 목록](alerts-reference.md#alerts-resourcemanager)


### <a name="cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview"></a>GitHub 워크플로 및 Azure Defender를 사용한 컨테이너 이미지의 CI/CD 취약성 검색(미리 보기)

이제 DevSecOps 팀은 컨테이너 레지스트리용 Azure Defender를 사용하여 GitHub Action 워크플로를 관찰할 수 있습니다.

Trivy를 활용하는 컨테이너 이미지에 대한 새로운 취약성 검색 기능은 개발자가 컨테이너 레지스트리에 이미지를 푸시하기 *전에* 컨테이너 이미지의 일반적인 취약성을 검색할 수 있도록 합니다.

컨테이너 검색 보고서는 Azure Security Center에 요약되어 보안 팀이 취약한 컨테이너 이미지의 원본과 해당 이미지가 시작된 워크플로 및 리포지토리를 보다 잘 이해하고 더 나은 인사이트를 얻을 수 있도록 합니다.

[CI/CD 워크플로에서 취약한 컨테이너 이미지 식별](defender-for-container-registries-cicd.md)에서 자세히 알아보세요.

### <a name="more-resource-graph-queries-available-for-some-recommendations"></a>일부 권장 사항에 사용할 수 있는 추가 Resource Graph 쿼리

모든 Security Center 권장 사항에는 **쿼리 열기** 에서 Azure Resource Graph를 사용하여 영향을 받은 리소스의 상태에 대한 정보를 볼 수 있는 옵션이 있습니다. 이 강력한 기능에 대한 자세한 내용은 [ARG(Azure Resource Graph) Explorer에서 권장 사항 데이터 검토](security-center-recommendations.md#review-recommendation-data-in-azure-resource-graph-explorer-arg)를 참조하세요.

Security Center에는 VM, SQL Server 및 해당 호스트, 컨테이너 레지스트리에서 보안 취약성을 검사하는 기본 제공 취약성 검사기가 포함되어 있습니다. 결과는 각 리소스 종류에 대한 모든 개별 결과를 단일 보기로 제공하는 권장 사항으로 반환됩니다. 권장 사항은 다음과 같습니다.

- Azure Container Registry 이미지의 취약성을 수정해야 함(Qualys 제공)
- 가상 머신의 취약성을 수정해야 함
- SQL 데이터베이스가 발견한 취약성을 해결해야 함
- 컴퓨터의 SQL Server는 발견한 취약성을 해결해야 함

이 변경으로 **쿼리 열기** 단추를 사용하여 보안 결과를 보여 주는 쿼리도 열 수 있습니다.

:::image type="content" source="media/release-notes/open-query-menu-security-findings.png" alt-text="이제 쿼리 열기 단추는 취약성 검사기 관련 권장 사항에 대한 보안 결과를 보여주는 심층 쿼리에 대한 옵션을 제공합니다.":::

**쿼리 열기** 단추는 관련된 다른 권장 사항에 대한 추가 옵션도 제공합니다.

Security Center 취약성 검사기에 대한 자세한 정보:

- [Azure 및 하이브리드 머신을 위한 Azure Defender 통합 취약성 평가 검사기](deploy-vulnerability-assessment-vm.md)
- [SQL Server용 Azure Defender 통합 취약성 평가 검사기](defender-for-sql-on-machines-vulnerability-assessment.md)
- [컨테이너 레지스트리용 Azure Defender 통합 취약성 평가 검사기](defender-for-container-registries-usage.md)

### <a name="sql-data-classification-recommendation-severity-changed"></a>SQL 데이터 분류 권장 사항 심각도가 변경됨

권장 사항 **SQL 데이터베이스에서 중요한 데이터를 분류해야 함** 의 심각도가 **높음** 에서 **낮음** 으로 변경되었습니다.

이는 [SQL 데이터베이스의 민감한 데이터 분류 권장 사항 향상](upcoming-changes.md#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)에서 발표된 이 권장 사항에 대한 지속적인 변경 내용의 일부입니다.

### <a name="new-recommendations-to-enable-trusted-launch-capabilities-in-preview"></a>신뢰할 수 있는 시작 기능을 사용하도록 설정하는 새로운 권장 사항(미리 보기)

Azure는 [2세대](../virtual-machines/generation-2.md) VM의 보안을 향상시키기 위한 원활한 방법으로 신뢰할 수 있는 시작을 제공합니다. 신뢰할 수 있는 시작은 지속적인 고급 공격 기술로부터 보호합니다. 신뢰할 수 있는 시작은 독립적으로 사용하도록 설정할 수 있는 몇 가지 조정된 인프라 기술로 구성되어 있습니다. 각 기술은 정교한 위협에 대한 또 다른 방어 계층을 제공합니다. [Azure 가상 머신에 대한 신뢰할 수 있는 시작](../virtual-machines/trusted-launch.md)에서 자세히 알아봅니다.

> [!IMPORTANT]
> 신뢰할 수 있는 시작을 사용하려면 새 가상 머신을 만들어야 합니다. 처음 신뢰할 수 있는 시작을 사용하지 않고 만든 기존 가상 머신에서는 이를 사용할 수 없습니다.
> 
> 신뢰할 수 있는 시작은 현재 공개 미리 보기로 제공됩니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

Security Center 권장 사항인 **지원되는 가상 머신에서 vTPM을 사용하도록 설정해야 함** 은 Azure VM에서 vTPM을 사용하고 있는지 확인합니다. 이 가상화된 버전의 하드웨어 신뢰할 수 있는 플랫폼 모듈은 VM의 전체 부팅 체인(UEFI, OS, 시스템 및 드라이버)을 측정하여 증명을 사용하도록 설정합니다.

vTPM을 사용하도록 설정하면 **게스트 증명 확장** 이 원격으로 보안 부팅의 유효성을 검사할 수 있습니다. 다음 권장 사항은 이 확장이 배포되었는지 확인합니다.

- **지원되는 Windows 가상 머신에서 보안 부팅을 사용하도록 설정해야 함**
- **지원되는 Windows 가상 머신에 게스트 증명 확장을 설치해야 함**
- **지원되는 Windows 가상 머신 확장 집합에 게스트 증명 확장을 설치해야 함**
- **지원되는 Linux 가상 머신에 게스트 증명 확장을 설치해야 함**
- **지원되는 Windows 가상 머신 확장 집합에 게스트 증명 확장을 설치해야 함**

[Azure 가상 머신에 대한 신뢰할 수 있는 시작](../virtual-machines/trusted-launch.md)에서 자세히 알아봅니다. 

### <a name="new-recommendations-for-hardening-kubernetes-clusters-in-preview"></a>Kubernetes 클러스터 강화에 대한 새로운 권장 사항(미리 보기)

다음 권장 사항을 통해 Kubernetes 클러스터를 보다 강화할 수 있습니다.

- **Kubernetes 클러스터에서 기본 네임스페이스를 사용하지 않아야 함** - ConfigMap, Pod, Secret, Service 및 ServiceAccount 리소스 종류에 대한 무단 액세스를 방지하려면 Kubernetes 클러스터에서 기본 네임스페이스를 사용하지 않도록 합니다.
- **Kubernetes 클러스터에서 자동 탑재 API 자격 증명을 사용하지 않도록 설정해야 함** - 잠재적으로 손상된 Pod 리소스가 Kubernetes 클러스터에 대해 API 명령을 실행하지 못하도록 하려면 API 자격 증명 자동 탑재를 사용하지 않도록 설정합니다.
- **Kubernetes 클러스터는 CAPSYSADMIN 보안 기능을 부여하지 않아야 함**

Security Center가 [Security Center의 컨테이너 보안](container-security.md)에서 컨테이너화된 환경을 보호하는 방법을 알아봅니다.

### <a name="assessments-api-expanded-with-two-new-fields"></a>평가 API가 두 개의 새 필드로 확장됨

다음 두 필드를 [평가 REST API](/rest/api/securitycenter/assessments)에 추가했습니다.

- **FirstEvaluationDate** - 권장 사항이 만들어지고 처음 평가된 시간입니다. ISO 8601 형식의 UTC 시간으로 반환됩니다.
- **StatusChangeDate** - 권장 사항의 상태가 마지막으로 변경된 시간입니다. ISO 8601 형식의 UTC 시간으로 반환됩니다.

이러한 필드의 초기 기본값(모든 권장 사항의 경우)은 `2021-03-14T00:00:00+0000000Z`입니다.

이 정보에 액세스하려면 아래 표의 메서드를 사용할 수 있습니다.

| 도구                 | 세부 정보                                                                                                                                                                |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| REST API 호출        | GET https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/providers/Microsoft.Security/assessments?api-version=2019-01-01-preview& $expand=statusEvaluationDates |
| Azure Resource Graph | `securityresources`<br>`where type == "microsoft.security/assessments"`                                                                                                |
| 연속 내보내기    | 두 개의 전용 필드는 Log Analytics 작업 영역 데이터에 사용할 수 있습니다.                                                                                            |
| [CSV 내보내기](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations) | 두 필드가 CSV 파일에 포함됩니다.                                                        |
|                      |                                                                                                                                                                        |


[평가 REST API](/rest/api/securitycenter/assessments)에 대해 자세히 알아봅니다.


### <a name="asset-inventory-gets-a-cloud-environment-filter"></a>자산 인벤토리가 클라우드 환경 필터를 가져옴

Security Center의 자산 인벤토리 페이지에는 표시되는 리소스 목록을 신속하게 구체화할 수 있는 다양한 필터가 제공됩니다. [자산 인벤토리로 리소스 탐색 및 관리](asset-inventory.md)에서 자세히 알아보세요.

새 필터는 Security Center의 다중 클라우드 기능에 연결한 클라우드 계정에 따라 목록을 구체화하는 옵션을 제공합니다.

:::image type="content" source="media/asset-inventory/filter-environment.png" alt-text="인벤토리의 환경 필터":::

다중 클라우드 기능에 대해 자세히 알아보세요.

- [Azure Security Center에 AWS 계정 연결](quickstart-onboard-aws.md)
- [Azure Security Center에 GCP 계정 연결](quickstart-onboard-gcp.md)
