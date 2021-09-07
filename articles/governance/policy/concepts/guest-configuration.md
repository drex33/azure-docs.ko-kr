---
title: 가상 머신의 콘텐츠를 감사하는 방법 알아보기
description: Azure Policy가 게스트 구성 클라이언트를 사용하여 가상 머신 내에서 설정을 감사하는 방법에 대해 알아봅니다.
ms.date: 05/01/2021
ms.topic: conceptual
ms.openlocfilehash: 6ecfd3fd9f426676fe0b5c9a69af26b1245b7824
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528994"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure Policy 게스트 구성 이해

Azure Policy는 Azure 및 [Arc Connected Machines](../../../azure-arc/servers/overview.md)에서 실행되는 머신 모두에 대해 컴퓨터 내부의 설정을 감사할 수 있습니다. 게스트 구성 확장 및 클라이언트가 유효성 검사를 수행합니다. 클라이언트를 통한 확장은 다음과 같은 설정의 유효성을 검사합니다.

- 운영 체제의 구성
- 애플리케이션 구성 또는 현재 상태
- 환경 설정

현재 대부분의 Azure Policy 게스트 구성 정책 정의는 머신의 설정만 감사합니다. 구성은 적용하지 않습니다. 단, [아래에 참조된](#applying-configurations-using-guest-configuration) 기본 제공 정책은 예외입니다.

[이 문서의 동영상 연습 사용 가능](https://youtu.be/Y6ryD3gTHOs).

## <a name="enable-guest-configuration"></a>게스트 구성 사용

Azure 및 Arc Connected Machines의 머신을 포함하여 환경의 머신 상태를 감사하려면 다음 세부 정보를 검토하세요.

## <a name="resource-provider"></a>리소스 공급자

게스트 구성을 사용하려면 먼저 리소스 공급자를 등록해야 합니다.
게스트 구성 정책이 포털을 통해 할당되거나 구독이 Azure Security Center에 등록되어 있으면 리소스 공급자가 자동으로 등록됩니다. [포털](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) 또는 [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)를 통해 수동으로 등록할 수 있습니다.

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Azure 가상 머신의 요구 사항 배포

머신의 설정을 감사하려면 [가상 머신 확장](../../../virtual-machines/extensions/overview.md)이 사용으로 설정되고 머신에 시스템 관리 ID가 있어야 합니다. 이 확장은 적용 가능한 정책 할당 및 해당 구성 정의를 다운로드합니다. ID는 게스트 구성 서비스를 읽고 쓸 때 머신을 인증하는 데 사용합니다. Arc Connected Machine 에이전트에 포함되어 있기 때문에 Arc Connected Machines에는 확장이 필요하지 않습니다.

> [!IMPORTANT]
> Azure 가상 머신을 감사하려면 게스트 구성 확장과 관리 ID가 필요합니다. 확장을 대규모로 배포하려면 다음 정책 이니셔티브를 할당합니다.
>
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>확장에 설정된 제한

머신 내부에서 실행되는 애플리케이션에 영향을 주지 않도록 확장을 제한하기 위해 게스트 구성은 CPU의 5%를 초과할 수 없습니다. 기본 제공 및 사용자 지정 정의 모두에 대해 이러한 제한이 존재합니다. Arc Connected Machine 에이전트의 게스트 구성 서비스도 마찬가지입니다.

### <a name="validation-tools"></a>유효성 검사 도구

머신 내에서 게스트 구성 클라이언트는 로컬 도구를 사용하여 감사를 실행합니다.

다음 표에는 지원되는 각 운영 체제에서 사용되는 로컬 도구 목록이 나와 있습니다. 기본 제공 콘텐츠의 경우 게스트 구성에서 자동으로 해당 도구의 로드를 처리합니다.

|운영 체제|유효성 검사 도구|메모|
|-|-|-|
|Windows|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| Azure Policy에서만 사용되는 폴더에 테스트용으로 로드합니다. Windows PowerShell DSC와 충돌하지 않습니다. PowerShell Core는 시스템 경로에 추가되지 않습니다.|
|Linux|[Chef InSpec](https://www.chef.io/inspec/) | Chef InSpec 버전 2.2.61을 기본 위치에 설치하고 시스템 경로에 추가합니다. Ruby와 Python을 포함하여 InSpec 패키지의 종속성도 설치됩니다. |

### <a name="validation-frequency"></a>유효성 검사 빈도

게스트 구성 클라이언트는 5분마다 새 게스트 할당이나 변경된 게스트 할당을 확인합니다. 게스트 할당이 수신되면 해당 구성에 대해 15분 간격으로 설정을 다시 확인합니다. 감사가 완료되면 게스트 구성 리소스 공급자로 결과가 전송됩니다.
정책 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)가 발생하면 컴퓨터 상태가 게스트 구성 리소스 공급자에 기록됩니다. 이렇게 업데이트하면 Azure Policy에서 Azure Resource Manager 속성을 평가하게 됩니다. 요청 시 Azure Policy 평가에서는 게스트 구성 리소스 공급자에서 최신 값을 검색합니다. 그러나 해당 머신 내 구성의 새 감사는 트리거되지 않습니다. 상태는 Azure Resource Graph에 동시에 기록됩니다.

## <a name="supported-client-types"></a>지원되는 클라이언트 유형

게스트 구성 정책 정의는 새 버전을 포함합니다. 게스트 구성 클라이언트가 호환되지 않는 경우 Azure Marketplace에서 사용할 수 있는 이전 버전의 운영 체제가 제외됩니다. 다음 표에는 Azure 이미지에서 지원되는 운영 체제 목록이 나와 있습니다.
".x" 텍스트는 Linux 배포판의 새로운 부 버전을 나타내는 기호입니다.

|게시자|속성|버전|
|-|-|-|
|Canonical|Ubuntu Server|14.04 - 20.x|
|Credativ|Debian|8 - 10.x|
|Microsoft|Windows Server|2012 - 2019|
|Microsoft|Windows 클라이언트|윈도우 10|
|OpenLogic|CentOS|7.3 -8.x|
|Red Hat|Red Hat Enterprise Linux\*|7.4 - 8.x|
|SUSE|SLES|12 SP3-SP5, 15.x|

\* Red Hat CoreOS는 지원되지 않습니다.

사용자 지정 가상 머신 이미지는 위의 표에 나오는 운영 체제 중 하나의 이미지인 경우 게스트 구성 정책 정의에서 지원됩니다.

## <a name="network-requirements"></a>네트워크 요구 사항

Azure의 가상 머신은 로컬 네트워크 어댑터 또는 프라이빗 링크를 사용하여 게스트 구성 서비스와 통신할 수 있습니다.

Azure Arc 머신은 온-프레미스 네트워크 인프라를 사용하여 Azure 서비스에 연결하고 규정 준수 상태를 보고합니다.

### <a name="communicate-over-virtual-networks-in-azure"></a>Azure에서 가상 네트워크를 통해 통신

Azure에서 게스트 구성 리소스 공급자와 통신하려면 머신의 **443** 포트에서 아웃바운드로 Azure 데이터 센터에 액세스할 수 있어야 합니다. Azure의 네트워크에서 아웃바운드 트래픽을 허용하지 않는 경우 [네트워크 보안 그룹](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) 규칙을 사용하여 예외를 구성합니다. [서비스 태그](../../../virtual-network/service-tags-overview.md) "AzureArcInfrastructure" 및 "Storage"는 Azure 데이터 센터의 [IP 범위 목록](https://www.microsoft.com/download/details.aspx?id=56519)을 수동으로 유지 관리하는 대신 게스트 구성 및 스토리지 서비스를 참조하는 데 사용할 수 있습니다. 게스트 구성 콘텐츠 패키지는 Azure Storage에서 호스트되므로 두 태그가 모두 필요합니다.

### <a name="communicate-over-private-link-in-azure"></a>Azure에서 Private Link를 통해 통신

가상 머신은 게스트 구성 서비스와의 통신에 [프라이빗 링크](../../../private-link/private-link-overview.md)를 사용할 수 있습니다. 이 기능을 사용하도록 설정하려면 이름이 `EnablePrivateNetworkGC`이고 값이 `TRUE`인 태그를 적용합니다. 태그는 게스트 구성 정책 정의가 머신에 적용되기 전이나 후에 적용할 수 있습니다.

Azure 플랫폼 리소스를 사용하여 안전하고 인증된 채널을 설정하기 위해 Azure [가상 공용 IP 주소](../../../virtual-network/what-is-ip-address-168-63-129-16.md)를 사용하여 트래픽을 라우팅합니다.

### <a name="azure-arc-connected-machines"></a>Azure Arc Connected Machines

Azure Arc에서 연결하는 Azure 외부에 있는 노드는 게스트 구성 서비스에 연결되어야 합니다. [Azure Arc 설명서](../../../azure-arc/servers/overview.md)에서 제공되는 네트워크와 프록시 요구 사항에 관해 자세히 설명합니다.

프라이빗 데이터 센터에 있는 Arc 연결된 서버의 경우 다음 패턴을 사용하여 트래픽을 허용합니다.

- 포트: 아웃바운드 인터넷 액세스에는 TCP 443만 필요
- 글로벌 URL: `*.guestconfiguration.azure.com`

## <a name="managed-identity-requirements"></a>관리 ID 요구 사항

_가상 머신에서 게스트 구성 정책을 사용으로 설정하기 위해 필수 구성 요소 배포_ 이니셔티브의 정책 정의를 통해 시스템이 할당한 관리 ID를 사용할 수 있습니다(없는 경우). 이니셔티브에는 ID 만들기를 관리하는 두 가지 정책 정의가 있습니다. 정책 정의의 IF 조건을 사용하면 Azure에서 머신 리소스의 현재 상태를 기반으로 올바른 동작이 보장됩니다.

머신에 현재 관리 ID가 없으면 효과적인 정책은 [시스템이 할당한 관리 ID를 추가하여 ID가 없는 가상 머신에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)하는 것입니다.

머신에 현재 사용자가 할당한 시스템 ID가 있으면 효과적인 정책은 [시스템이 할당한 관리 ID를 추가하여 사용자가 할당한 ID가 있는 VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)하는 것입니다.

## <a name="guest-configuration-definition-requirements"></a>게스트 구성 정의 요구 사항

게스트 구성 정책 정의에서는 **AuditIfNotExists** 효과를 사용합니다. 정의가 할당되면 백 엔드 서비스가 `Microsoft.GuestConfiguration` Azure 리소스 공급자의 모든 요구 사항의 수명 주기를 자동으로 처리합니다.

**AuditIfNotExists** 정책 정의는 머신에서 모든 요구 사항이 충족될 때까지 준수 결과를 반환하지 않습니다. 요구 사항은 [Azure 가상 머신의 요구 사항 배포](#deploy-requirements-for-azure-virtual-machines) 섹션에 설명되어 있습니다.

> [!IMPORTANT]
> 게스트 구성의 이전 릴리스에서는 **DeployIfNotExists** 및 **AuditIfNotExists** 정의를 결합하기 위해 이니셔티브가 필요했습니다. **DeployIfNotExists** 정의가 더는 필요하지 않습니다. 정의와 이니셔티브는 `[Deprecated]`로 레이블이 지정되지만, 기존 할당은 계속 작동합니다. 자세한 내용은 블로그 게시물, [게스트 구성 감사 정책에 대해 릴리스된 중요 변경 내용](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)을 참조하세요.

### <a name="what-is-a-guest-assignment"></a>게스트 할당이란?

Azure Policy가 할당될 때 “게스트 구성” 범주에 있으면 게스트 할당을 설명하는 메타데이터가 포함됩니다. 게스트 할당은 머신과 Azure Policy 시나리오 사이의 링크로 생각할 수 있습니다. 예를 들어 다음 코드 조각은 Azure Windows Baseline 구성을 최소 버전 `1.0.0`으로 정책 범위에 있는 모든 머신에 연결합니다.
기본적으로 게스트 할당은 머신 감사만 수행합니다.

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

게스트 구성 서비스에서 머신당 게스트 할당을 자동으로 만듭니다. 리소스 종류는 `Microsoft.GuestConfiguration/guestConfigurationAssignments`입니다. Azure Policy에서는 게스트 할당 리소스의 **complianceStatus** 속성을 사용하여 준수 상태를 보고합니다. 자세한 내용은 [규정 준수 데이터 가져오기](../how-to/get-compliance-data.md)를 참조하세요.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>업계 기준에 따라 운영 체제 설정 감사

Azure Policy의 한 이니셔티브는 “기준”에 따라 운영 체제 설정을 감사합니다. _\[미리 보기\]: Windows 머신은 Azure 보안 기준의 요구 사항을 충족해야 함_ 정의에는 Active Directory 그룹 정책을 기준으로 하는 규칙 세트가 포함되어 있습니다.

대부분의 설정은 매개 변수로 사용할 수 있습니다. 매개 변수를 사용하여 감사할 항목을 사용자 지정할 수 있습니다.
정책을 요구 사항에 맞게 정렬하거나 업계 규정 표준 같은 타사 정보에 정책을 매핑합니다.

일부 매개 변수는 정수 값 범위를 지원합니다. 예를 들어, 최대 암호 사용 기간 설정은 유효 그룹 정책 설정을 감사할 수 있습니다. "1,70" 범위는 사용자에게 1일 이상, 최소 70일마다 암호를 변경하도록 요구합니다.

ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 정책을 할당하는 경우 매개 변수 파일을 사용하여 예외를 관리합니다. Git와 같은 버전 제어 시스템에 파일을 체크 인합니다. 파일 변경 내용에 대한 설명은 할당이 예상 값에 대한 예외가 되는 이유를 나타내는 증명 정보를 제공합니다.

#### <a name="applying-configurations-using-guest-configuration"></a>게스트 구성을 사용하여 구성 적용

_Windows 머신에서 표준 시간대 구성_ 정의만 표준 시간대를 구성하여 머신을 변경합니다. 머신 내부 설정을 구성하기 위한 사용자 지정 정책 정의는 지원되지 않습니다.

_구성_ 으로 시작하는 정의를 할당하는 경우 _필수 조건을 배포하여 Windows VM에서 게스트 구성 정책을 사용하도록 설정_ 정의도 할당해야 합니다. 선택하는 경우 이러한 정의를 하나의 이니셔티브에 결합할 수 있습니다.

> [!NOTE]
> 기본 제공 표준 시간대 정책은 머신 내부 설정 구성을 지원하는 유일한 정의이며, 머신 내부 설정을 구성하는 사용자 지정 정책 정의는 지원되지 않습니다.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Azure 외부의 머신에 정책 할당

게스트 구성에 사용할 수 있는 감사 정책 정의에는 **Microsoft.HybridCompute/machines** 리소스 종류가 포함됩니다. 정책 할당 범위 내에 있는 [서버용 Azure Arc](../../../azure-arc/servers/overview.md)에 온보딩된 모든 머신이 자동으로 포함됩니다.

## <a name="availability"></a>가용성

고가용성 솔루션을 설계하는 고객은 게스트 할당이 Azure의 컴퓨터 리소스 확장이기 때문에 [가상 머신](../../../virtual-machines/availability.md)에 대한 중복 계획 요구 사항을 고려해야 합니다. 게스트 할당 리소스가 [페어링된](../../../best-practices-availability-paired-regions.md) Azure 지역에 프로비저닝되면 쌍에서 하나 이상의 지역을 사용할 수 있는 한 게스트 할당 보고서를 사용할 수 있습니다. Azure 지역이 페어링되지 않고 사용할 수 없게 되면 지역이 복원될 때까지 게스트 할당에 대한 보고서에 액세스할 수 없습니다.

고가용성 애플리케이션을 위한 아키텍처를 고려할 때, 특히 가상 머신이 고가용성을 제공하기 위해 부하 분산 장치 솔루션 뒤의 [가용성 집합](../../../virtual-machines/availability.md#availability-sets)에 프로비저닝되는 경우 솔루션에서 모든 컴퓨터에 동일한 매개변수를 사용하여 동일한 정책 정의를 할당하는 것이 가장 좋습니다. 가능한 경우 모든 컴퓨터에 단일 정책을 할당하면 관리 오버헤드가 최소화됩니다.

[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)로 보호되는 컴퓨터의 경우 보조 사이트의 컴퓨터가 기본 사이트의 컴퓨터와 동일한 매개 변수 값을 사용하여 동일한 정의에 대한 Azure Policy 할당 범위 내에 있는지 확인합니다.

## <a name="data-residency"></a>데이터 상주

게스트 구성은 고객 데이터를 저장/처리합니다. 기본적으로 고객 데이터는 [쌍을 이루는 지역](../../../best-practices-availability-paired-regions.md)에 복제됩니다.
단일 상주 지역의 경우 모든 고객 데이터가 해당 지역에서 저장되고 처리됩니다.

## <a name="troubleshooting-guest-configuration"></a>게스트 구성 문제 해결

게스트 구성 문제 해결에 관한 자세한 내용은 [Azure Policy 문제 해결](../troubleshoot/general.md)을 참조하세요.

### <a name="multiple-assignments"></a>여러 할당

게스트 구성 정책 정의는 정책 할당이 다른 매개 변수를 사용해도 머신마다 한번씩 같은 게스트 할당만 지원합니다.

### <a name="client-log-files"></a>클라이언트 로그 파일

게스트 구성 확장은 다음 위치에 로그 파일을 씁니다.

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux

- Azure VM: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`
- Azure VM: `/var/lib/GuestConfig/arc_policy_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>원격으로 로그 수집

게스트 구성 또는 모듈 문제를 해결하는 첫 번째 단계는 `Test-GuestConfigurationPackage` cmdlet을 사용하여 [Windows에 대한 사용자 지정 게스트 구성 감사 정책을 만드는](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows) 방법에 대한 단계를 따르는 것입니다.
실패하는 경우 클라이언트 로그를 수집하여 문제를 진단하는 데 도움을 얻을 수 있습니다.

#### <a name="windows"></a>Windows

[Azure VM Run 명령](../../../virtual-machines/windows/run-command.md)을 사용하여 로그 파일에서 정보를 캡처합니다. 다음 예제 PowerShell 스크립트를 유용하게 사용할 수 있습니다.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

[Azure VM Run 명령](../../../virtual-machines/linux/run-command.md)을 사용하여 로그 파일에서 정보를 캡처합니다. 다음 예제 Bash 스크립트를 유용하게 사용할 수 있습니다.

```bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

### <a name="client-files"></a>클라이언트 파일

게스트 구성 클라이언트가 콘텐츠 패키지를 머신에 다운로드하고 콘텐츠를 추출합니다.
다운로드하여 저장한 콘텐츠를 확인하려면 아래에 지정된 폴더 위치를 확인합니다.

Windows: `c:\programdata\guestconfig\configuration`

Linux: `/var/lib/GuestConfig/Configuration`

## <a name="guest-configuration-samples"></a>게스트 구성 샘플

게스트 구성 기본 제공 정책 샘플은 다음 위치에서 사용할 수 있습니다.

- [기본 제공 정책 정의 - 게스트 구성](../samples/built-in-policies.md#guest-configuration)
- [기본 제공 이니셔티브 - 게스트 구성](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy 샘플 GitHub 리포지토리](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

### <a name="video-overview"></a>비디오 개요

Azure Policy 게스트 구성에 관한 다음 개요는 ITOps Talks 2021에 관한 내용입니다.

[Azure Policy 게스트 구성을 사용하여 하이브리드 서버 환경에서 기준 제어](https://techcommunity.microsoft.com/t5/itops-talk-blog/ops114-governing-baselines-in-hybrid-server-environments-using/ba-p/2109245)

## <a name="next-steps"></a>다음 단계

- [게스트 구성 규정 준수 보기](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)에서 각 설정에 대한 세부 정보를 보는 방법을 알아봅니다.
- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Azure Policy 정의 구조](./definition-structure.md)를 검토합니다.
- [정책 효과 이해](./effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](../how-to/programmatically-create.md)하는 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
