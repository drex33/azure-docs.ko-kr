---
title: Azure Policy의 게스트 구성 기능 이해
description: Azure Policy에서 게스트 구성 기능을 사용하여 가상 머신 내부의 설정을 감사하거나 구성하는 방법을 알아봅니다.
ms.date: 07/15/2021
ms.topic: conceptual
ms.openlocfilehash: 0e62909a14989567aee5f7c893f45cd7bdbbee61
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129999438"
---
# <a name="understand-the-guest-configuration-feature-of-azure-policy"></a>Azure Policy의 게스트 구성 기능 이해

Azure Policy 게스트 구성 기능은 Azure에서 실행되는 컴퓨터와 하이브리드 [Arc 지원 머신](../../../azure-arc/servers/overview.md)모두에 대해 운영 체제 설정을 코드로 감사하거나 구성하는 기본 기능을 제공합니다.
이 기능은 컴퓨터별로 직접 사용하거나 Azure Policy 오케스트레이션할 수 있습니다.

구성은 정책 정의와 다릅니다. 게스트 구성은 Azure Policy 사용하여 컴퓨터에 구성을 동적으로 할당합니다. 구성을 [컴퓨터에 수동으로](/guest-configuration-assignments.md#manually-creating-guest-configuration-assignments)할당하거나 [AutoManage와](../../../automanage/automanage-virtual-machines.md)같은 다른 Azure 서비스를 사용하여 할당할 수도 있습니다.

Azure의 구성 리소스는 [확장 리소스](../../../azure-resource-manager/management/extension-resource-types.md)로 설계되었습니다.
각 구성을 컴퓨터에 대한 추가 속성 집합으로 간주할 수 있습니다. 구성에는 다음과 같은 설정이 포함될 수 있습니다.

- 운영 체제 설정
- 애플리케이션 구성 또는 현재 상태
- 환경 설정

[이 문서의 동영상 연습 사용 가능](https://youtu.be/t9L8COY-BkM).

## <a name="enable-guest-configuration"></a>게스트 구성 사용

Azure 및 Arc 지원 서버의 컴퓨터를 포함하여 환경의 컴퓨터 상태를 관리하려면 다음 세부 정보를 검토하세요.

## <a name="resource-provider"></a>리소스 공급자

Azure Policy의 게스트 구성 기능을 사용하려면 먼저 `Microsoft.GuestConfiguration` 리소스 공급자를 등록해야 합니다. 게스트 구성 정책이 포털을 통해 할당되거나 구독이 Azure Security Center에 등록되어 있으면 리소스 공급자가 자동으로 등록됩니다. [포털](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) 또는 [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)를 통해 수동으로 등록할 수 있습니다.

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Azure 가상 머신의 요구 사항 배포

머신의 설정을 관리하려면 [가상 머신 확장](../../../virtual-machines/extensions/overview.md)이 사용으로 설정되고 컴퓨터에 시스템 관리 ID가 있어야 합니다. 확장 프로그램은 적용 가능한 게스트 구성 할당 및 해당 종속성을 다운로드합니다. ID는 게스트 구성 서비스를 읽고 쓸 때 머신을 인증하는 데 사용합니다. Arc Connected Machine 에이전트에 포함되어 있으므로 Arc 지원 서버에는 확장이 필요하지 않습니다.

> [!IMPORTANT]
> Azure 가상 머신을 관리하려면 게스트 구성 확장과 관리 ID가 필요합니다.

확장을 여러 컴퓨터에 대규모로 배포하려면 정책 이니셔티브 `Deploy prerequisites to enable guest configuration policies on virtual machines`를
관리하려는 시스템이 포함된 관리 그룹, 구독 또는 리소스 그룹에 할당합니다.

확장 및 관리 ID를 단일 컴퓨터에 배포하려는 경우 각각에 대한 지침을 따르세요.

- [Azure Policy 게스트 구성 확장 개요](../../../virtual-machines/extensions/guest-configuration.md)
- [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

구성을 적용하는 게스트 구성 패키지를 사용하려면 Azure VM 게스트 구성 확장 버전 **1.29.24** 이상이 필요합니다.

### <a name="limits-set-on-the-extension"></a>확장에 설정된 제한

컴퓨터 내부에서 실행되는 애플리케이션에 영향을 주지 않도록 확장을 제한하기 위해 게스트 구성 에이전트는 CPU의 5%를 초과할 수 없습니다. 기본 제공 및 사용자 지정 정의 모두에 대해 이러한 제한이 존재합니다. Arc Connected Machine 에이전트의 게스트 구성 서비스도 마찬가지입니다.

### <a name="validation-tools"></a>유효성 검사 도구

컴퓨터 내부에서 게스트 구성 에이전트는 로컬 도구를 사용하여 작업을 수행합니다.

다음 표에는 지원되는 각 운영 체제에서 사용되는 로컬 도구 목록이 나와 있습니다. 기본 제공 콘텐츠의 경우 게스트 구성에서 자동으로 해당 도구의 로드를 처리합니다.

|운영 체제|유효성 검사 도구|메모|
|-|-|-|
|Windows|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v3| Azure Policy에서만 사용되는 폴더에 테스트용으로 로드합니다. Windows PowerShell DSC와 충돌하지 않습니다. PowerShell Core는 시스템 경로에 추가되지 않습니다.|
|Linux|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v3| Azure Policy에서만 사용되는 폴더에 테스트용으로 로드합니다. PowerShell Core는 시스템 경로에 추가되지 않습니다.|
|Linux|[Chef InSpec](https://www.chef.io/inspec/) | Chef InSpec 버전 2.2.61을 기본 위치에 설치하고 시스템 경로에 추가합니다. Ruby와 Python을 포함하여 InSpec 패키지의 종속성도 설치됩니다. |

### <a name="validation-frequency"></a>유효성 검사 빈도

게스트 구성 에이전트는 5분마다 새 게스트 할당이나 변경된 게스트 할당을 확인합니다. 게스트 할당이 수신되면 해당 구성에 대해 15분 간격으로 설정을 다시 확인합니다. 여러 구성이 할당된 경우 각각은 순차적으로 평가됩니다. 장기 실행 구성은 이전 구성이 완료될 때까지 다음 구성이 실행되지 않기 때문에 모든 구성의 간격에 영향을 줍니다.

감사가 완료되면 결과가 게스트 구성 서비스로 전송됩니다.
정책 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)가 발생하면 머신 상태가 게스트 구성 리소스 공급자에 기록됩니다. 이렇게 업데이트하면 Azure Policy에서 Azure Resource Manager 속성을 평가하게 됩니다. 요청 시 Azure Policy 평가에서는 게스트 구성 리소스 공급자에서 최신 값을 검색합니다. 그러나 컴퓨터 내에서 새 활동을 트리거하지는 않습니다. 그런 다음 상태가 Azure Resource Graph에 기록됩니다.

## <a name="supported-client-types"></a>지원되는 클라이언트 유형

게스트 구성 정책 정의는 새 버전을 포함합니다. 게스트 구성 클라이언트가 호환되지 않는 경우 Azure Marketplace에서 사용할 수 있는 이전 버전의 운영 체제가 제외됩니다. 다음 표에는 Azure 이미지에서 지원되는 운영 체제 목록이 나와 있습니다.
".x" 텍스트는 Linux 배포판의 새로운 부 버전을 나타내는 기호입니다.

|게시자|속성|버전|
|-|-|-|
|Amazon|Linux|2|
|Canonical|Ubuntu Server|16.04 - 20.x|
|Credativ|Debian|9 - 10.x|
|Microsoft|Windows Server|2012 - 2019|
|Microsoft|Windows 클라이언트|윈도우 10|
|Oracle|Oracle-Linux|7.x-8.x|
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

### <a name="azure-arc-enabled-servers"></a>Azure Arc 지원 서버

Azure Arc에서 연결하는 Azure 외부에 있는 노드는 게스트 구성 서비스에 연결되어야 합니다. [Azure Arc 설명서](../../../azure-arc/servers/overview.md)에서 제공되는 네트워크와 프록시 요구 사항에 관해 자세히 설명합니다.

프라이빗 데이터 센터에 있는 Arc 지원 서버의 경우 다음 패턴을 사용하여 트래픽을 허용합니다.

- 포트: 아웃바운드 인터넷 액세스에는 TCP 443만 필요
- 글로벌 URL: `*.guestconfiguration.azure.com`

## <a name="assigning-policies-to-machines-outside-of-azure"></a>Azure 외부의 머신에 정책 할당

게스트 구성에 사용할 수 있는 감사 정책 정의에는 **Microsoft.HybridCompute/machines** 리소스 종류가 포함됩니다. 정책 할당 범위 내에 있는 [서버용 Azure Arc](../../../azure-arc/servers/overview.md)에 온보딩된 모든 머신이 자동으로 포함됩니다.

## <a name="managed-identity-requirements"></a>관리 ID 요구 사항

_가상 머신에서 게스트 구성 정책을 사용으로 설정하기 위해 필수 구성 요소 배포_ 이니셔티브의 정책 정의를 통해 시스템이 할당한 관리 ID를 사용할 수 있습니다(없는 경우). 이니셔티브에는 ID 만들기를 관리하는 두 가지 정책 정의가 있습니다. 정책 정의의 IF 조건을 사용하면 Azure에서 머신 리소스의 현재 상태를 기반으로 올바른 동작이 보장됩니다.

컴퓨터에 현재 관리 ID가 없으면 효과적인 정책은 [시스템이 할당한 관리 ID를 추가하여 ID가 없는 가상 머신에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)하는 것입니다.

컴퓨터에 현재 사용자가 할당한 시스템 ID가 있으면 효과적인 정책은 [시스템이 할당한 관리 ID를 추가하여 사용자가 할당한 ID가 있는 VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)하는 것입니다.

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

게스트 구성 정책 정의는 정책 할당이 다른 매개 변수를 사용할 때 컴퓨터마다 한번씩 같은 게스트 할당만 지원합니다.

### <a name="assignments-to-azure-management-groups"></a>Azure 관리 그룹에 대한 할당

'게스트 구성' 범주의 Azure 정책 정의는 효과가 'AuditIfNotExists'인 경우에만 관리 그룹에 할당할 수 있습니다. 'DeployIfNotExists' 효과가 있는 정책 정의는 관리 그룹에 대한 할당으로 지원되지 않습니다.

### <a name="client-log-files"></a>클라이언트 로그 파일

게스트 구성 확장은 다음 위치에 로그 파일을 씁니다.

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux

- Azure VM: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`
- Arc 지원 서버: `/var/lib/GuestConfig/arc_policy_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>원격으로 로그 수집

게스트 구성 또는 모듈 문제를 해결하는 첫 번째 단계는 [게스트 구성 패키지 아티팩트를 테스트하는 방법](../how-to/guest-configuration-create-test.md)의 단계에 따라 cmdlet을 사용하는 것입니다.
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

### <a name="agent-files"></a>에이전트 파일

게스트 구성 에이전트가 콘텐츠 패키지를 컴퓨터에 다운로드하고 콘텐츠를 추출합니다. 다운로드하여 저장한 콘텐츠를 확인하려면 아래에 지정된 폴더 위치를 확인합니다.

Windows: `c:\programdata\guestconfig\configuration`

Linux: `/var/lib/GuestConfig/Configuration`

## <a name="guest-configuration-samples"></a>게스트 구성 샘플

게스트 구성 기본 제공 정책 샘플은 다음 위치에서 사용할 수 있습니다.

- [기본 제공 정책 정의 - 게스트 구성](../samples/built-in-policies.md#guest-configuration)
- [기본 제공 이니셔티브 - 게스트 구성](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy 샘플 GitHub 리포지토리](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>다음 단계

- 사용자 지정 게스트 구성 패키지 [개발 환경](../how-to/guest-configuration-create-setup.md)을 설정합니다.
- 게스트 구성을 위한 [패키지 아티팩트를 만듭니다](../how-to/guest-configuration-create.md).
- 사용자 개발 환경에서 [패키지 아티팩트 테스트](../how-to/guest-configuration-create-test.md)합니다.
- `GuestConfiguration` 모듈을 사용하여 사용자 환경의 대규모 관리를 위한 [Azure Policy 정의를 생성](../how-to/guest-configuration-create-definition.md)합니다.
- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당](../assign-policy-portal.md)합니다.
- [게스트 구성 정책 할당에 대한 규정 준수 세부 정보](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)를 보는 방법을 알아봅니다.
