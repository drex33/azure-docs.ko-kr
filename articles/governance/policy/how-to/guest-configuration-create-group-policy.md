---
title: 그룹 정책에서 게스트 구성 정책 만드는 방법
description: 그룹 정책을 정책 정의로 변환하는 방법을 알아봅니다.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 12bd1c905c254f16da170cde4a4426a2aa0cb263
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772297"
---
# <a name="how-to-create-a-guest-configuration-policy-from-group-policy"></a>그룹 정책에서 게스트 구성 정책 만드는 방법

시작하기 전에 [게스트 구성](../concepts/guest-configuration.md)에 대한 개요 페이지와 게스트 구성 정책 효과에 대한 세부 정보([게스트 구성에 대한 수정 옵션을 구성하는 방법](../concepts/guest-configuration-policy-effects.md))를 읽어보는 것이 좋습니다.

> [!IMPORTANT]
> 그룹 정책을 게스트 구성으로 변환하는 것은 **미리 보기 상태** 입니다. 모든 형식의 그룹 정책 설정에서 해당 DSC 리소스를 PowerShell 7에 사용할 수 있는 것은 아닙니다.
>
> 이 페이지의 모든 명령은 **Windows PowerShell 5.1** 에서 실행해야 합니다.
> 그러면 결과 출력 MOF 파일이 PowerShell 7.1.3 이상에서 `GuestConfiguration` 모듈을 사용하여 패키지되어야 합니다.
> 
> **AuditIfNotExists** 를 사용한 사용자 지정 게스트 구성 정책 정의는 일반적으로 사용 가능하지만 게스트 구성을 포함한 **DeployIfNotExists** 를 사용한 정의는 **미리 보기 상태** 입니다.
> 
> 게스트 구성 확장은 Azure 가상 머신에 필요합니다. 모든 머신에서 확장을 대규모로 배포하려면 정책 이니셔티브(`Deploy prerequisites to enable guest configuration policies on
> virtual machines`)를 할당합니다.
>
> 사용자 지정 콘텐츠 패키지에서 비밀 또는 기밀 정보를 사용하지 마세요.

오픈 소스 커뮤니티는 내보낸 [그룹 정책](/support/windows-server/group-policy/group-policy-overview) 템플릿을 PowerShell DSC 형식으로 변환하기 위해 [BaselineManagement](https://github.com/microsoft/BaselineManagement) 모듈을 게시했습니다. 내보낸 그룹 정책 개체에서 `GuestConfiguration` 모듈과 함께 Windows를 위한 게스트 구성 패키지를 만들 수 있습니다. 그런 다음, 게스트 구성 패키지를 사용하여 도메인에 가입되지 않은 경우에도 로컬 정책으로 서버를 감사하거나 구성할 수 있습니다.

이 가이드에서는 GPO(그룹 정책 개체)에서 Azure Policy 게스트 구성 패키지를 만드는 프로세스를 안내합니다.

## <a name="download-required-powershell-modules"></a>필수 PowerShell 모듈 다운로드

PowerShell에서 필요한 모듈을 모든 설치하려면 다음을 수행합니다.

```powershell
Install-Module guestconfiguration
Install-Module baselinemanagement
```

Active Directory 환경에서 GPO(그룹 정책 개체)를 백업하려면 RSAT(원격 서버 관리 도구 키트)에서 사용할 수 있는 PowerShell 명령이 필요합니다.

Windows 10에서 그룹 정책 관리 콘솔용 RSAT를 사용하도록 설정하려면 다음을 수행합니다.

```powerShell
Add-WindowsCapability -Online -Name 'Rsat.GroupPolicy.Management.Tools~~~~0.0.1.0'
Add-WindowsCapability -Online -Name 'Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0'
```

## <a name="export-and-convert-group-policy-to-guest-configuration"></a>그룹 정책 내보내기 및 게스트 구성으로 변환

그룹 정책 파일을 내보내고 게스트 구성에 사용할 DSC로 변환하는 옵션은 세 가지입니다.

- 단일 그룹 정책 개체 내보내기
- OU의 병합된 그룹 정책 개체 내보내기
- 머신 내에서 병합된 그룹 정책 개체 내보내기

### <a name="single-group-policy-object"></a>단일 그룹 정책 개체

`Group Policy` 모듈의 명령을 사용하여 내보낼 그룹 정책 개체의 GUID를 확인합니다. 대규모 환경에서는 출력을 `where-object`로 파이핑하고 이름으로 필터링하는 것이 좋습니다.

**도메인 가입** Windows 머신의  **Windows PowerShell 5.1** 환경에서 다음을 각각 실행하세요.

```powershell
# List all Group Policy Objects
Get-GPO -all
```

파일에 그룹 정책을 백업합니다. 이 명령은 “이름” 매개 변수도 허용하지만 정책의 GUID를 사용하면 오류가 덜 발생합니다.

```powershell
Backup-GPO -Guid 'f0cf623e-ae29-4768-9bb4-406cce1f3cff' -Path C:\gpobackup\
```

```

The output of the command returns the details of the files.

ConfigurationScript                   Configuration                   Name
-------------------                   -------------                   ----
C:\convertfromgpo\myCustomPolicy1.ps1 C:\convertfromgpo\localhost.mof myCustomPolicy1
```

내보낸 PowerShell 스크립트를 검토하여 모든 설정이 채워졌으며 오류 메시지가 기록되지 않았는지 확인합니다. [사용자 지정 게스트 구성 패키지 아티팩트 만드는 방법](./guest-configuration-create.md) 페이지의 지침에 따라 MOF 파일을 사용하여 새 구성 패키지를 만듭니다.
게스트 구성 패키지를 만들고 테스트하는 단계는 PowerShell 7 환경에서 실행해야 합니다.

### <a name="merged-group-policy-objects-for-an-ou"></a>OU의 병합된 그룹 정책 개체

지정된 조직 구성 단위에서 병합된 그룹 정책 개체 조합(정책의 결과 집합과 유사)을 내보냅니다. 병합 작업은 WMI 필터가 아닌 계정 링크 상태, 적용, 액세스를 고려합니다.

```powershell
Merge-GPOsFromOU -Path C:\mergedfromou\ -OUDistinguishedName 'OU=mySubOU,OU=myOU,DC=mydomain,DC=local' -OutputConfigurationScript
```

명령의 출력은 파일의 세부 정보를 반환합니다.

```powershell
Configuration                                Name    ConfigurationScript
-------------                                ----    -------------------
C:\mergedfromou\mySubOU\output\localhost.mof mySubOU C:\mergedfromou\mySubOU\output\mySubOU.ps1
```

### <a name="merged-group-policy-objects-from-within-a-machine"></a>머신 내에서 병합된 그룹 정책 개체

Windows PowerShell에서 `Merge-GPOs` 명령을 실행하여 특정 머신에 적용된 정책을 병합할 수도 있습니다. WMI 필터는 머신 내에서 병합하는 경우에만 평가됩니다.

```powershell
Merge-GPOs -OutputConfigurationScript -Path c:\mergedgpo
```

명령의 출력은 파일의 세부 정보를 반환합니다.

```powershell
Configuration              Name                  ConfigurationScript                    PolicyDetails
-------------              ----                  -------------------                    -------------
C:\mergedgpo\localhost.mof MergedGroupPolicy_ws1 C:\mergedgpo\MergedGroupPolicy_ws1.ps1 {@{Name=myEnforcedPolicy; Ap...
```

## <a name="optional-download-sample-group-policy-files-for-testing"></a>선택 사항: 테스트용 샘플 그룹 정책 파일 다운로드

Active Directory 환경에서 그룹 정책 파일을 내보낼 준비가 되지 않은 경우 Windows 보안 및 규정 준수 도구 키트에서 Windows Server 보안 기준을 다운로드할 수 있습니다.

디렉터리를 만들고 Windows 보안 규정 준수 도구 키트에서 Windows Server 2019 보안 기준을 다운로드합니다.

```azurepowershell-interactive
# Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
```

다운로드한 Server 2019 기준을 차단 해제하고 확장합니다.

```azurepowershell-interactive
Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
```

**MapGuidsToGpoNames.ps1** 을 사용하여 Server 2019 기준 콘텐츠의 유효성을 검사합니다.

```azurepowershell-interactive
# Show content details of downloaded GPOs
C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
```

## <a name="next-steps"></a>다음 단계

- 게스트 구성을 위한 [패키지 아티팩트를 만듭니다](./guest-configuration-create.md).
- 사용자 개발 환경에서 [패키지 아티팩트 테스트](./guest-configuration-create-test.md)합니다.
- 머신에서 액세스할 수 있도록 [패키지 아티팩트를 게시](./guest-configuration-create-publish.md)합니다.
- `GuestConfiguration` 모듈을 사용하여 사용자 환경의 대규모 관리를 위한 [Azure Policy 정의를 생성](./guest-configuration-create-definition.md)합니다.
- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당](../assign-policy-portal.md)합니다.
- [게스트 구성 정책 할당에 대한 규정 준수 세부 정보](./determine-non-compliance.md#compliance-details-for-guest-configuration)를 보는 방법을 알아봅니다.
