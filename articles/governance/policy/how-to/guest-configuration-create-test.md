---
title: 게스트 구성 패키지 아티팩트를 테스트하는 방법
description: 구성을 감사하거나 머신에 적용하는 패키지를 만들고 테스트합니다.
ms.date: 07/20/2021
ms.topic: how-to
ms.openlocfilehash: efa2fbd49509b323cbf0cf442cb0a29bbc51c8b7
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129418730"
---
# <a name="how-to-test-guest-configuration-package-artifacts"></a>게스트 구성 패키지 아티팩트를 테스트하는 방법

PowerShell 모듈 `GuestConfiguration`에는 Azure 외부에서 구성 패키지 테스트를 자동화하는 도구가 포함되어 있습니다. 이러한 도구를 사용하여 Azure 또는 Arc 연결 환경에서 테스트를 진행하기 전에 문제를 찾고 빠르게 반복합니다.

테스트를 시작하기 전에 [게스트 구성 제작 환경을 설정하는 방법](./guest-configuration-create-setup.md) 페이지의 단계를 수행한 후 [사용자 지정 게스트 구성 패키지 아티팩트를 만드는 방법](./guest-configuration-create.md)의 단계를 모두 수행하여 사용자 지정 게스트 구성 패키지를 만들고 게시합니다.

> [!IMPORTANT]
> 환경의 상태를 감사하는 사용자 지정 패키지는 일반적으로 사용할 수 있지만 구성을 적용하는 패키지는 **미리 보기 상태** 입니다. **다음과 같은 제한 사항이 적용됩니다.**
> 
> 구성을 적용하는 게스트 구성 패키지를 사용하려면 Azure VM 게스트 구성 확장 버전 **1.29.24** 이상 또는 Arc 에이전트 **1.10.0** 이상이 필요합니다.
> 
> Linux에서 구성 만들기와 적용을 테스트하기 위해 `GuestConfiguration` 모듈은 Ubuntu 18에서만 사용할 수 있지만, 모듈에서 생성된 패키지 및 정책은 Azure 또는 Arc에서 지원되는 모든 Linux 배포판/버전에서 사용할 수 있습니다.
>
> MacOS에서 패키지를 테스트할 수 없습니다.

워크스테이션 또는 CI/CD 환경(연속 통합 및 지속적인 배포)에서 패키지를 테스트할 수 있습니다.  `GuestConfiguration` 모듈은 Azure 또는 Arc 사용 머신 내에서 사용되는 것과 같은 에이전트를 사용자의 개발 환경을 위해 포함합니다. 에이전트에는 Windows용 PowerShell 7.1.3 및 Linux용 7.2.0-preview.7의 독립 실행형 인스턴스가 포함되어 있으므로, 패키지를 테스트하는 스크립트 환경은 게스트 구성을 사용하여 관리하는 머신과 일치합니다.

Azure 및 Arc 사용 머신의 에이전트 서비스는 Windows 및 Linux의 “루트”에서 “LocalSystem” 계정으로 실행됩니다. 최상의 결과를 위해 권한 있는 보안 컨텍스트에서 아래 명령을 실행합니다.

Windows에서 PowerShell을 “LocalSystem”으로 실행하려면 SysInternals 도구 [PSExec](/sysinternals/downloads/psexec)을 사용합니다.

PowerShell을 Linux에서 “루트”로 실행하려면 [Su 명령](https://manpages.ubuntu.com/manpages/man1/su.1.html)을 사용합니다.

## <a name="validate-the-configuration-package-meets-requirements"></a>구성 패키지가 요구 사항을 충족하는지 확인

우선 `Get-GuestConfigurationPackageComplianceStatus `를 사용하여 구성 패키지가 기본 요구 사항을 충족하는지 테스트합니다. 명령은 다음 패키지 요구 사항을 확인합니다.

- MOF는 올바른 위치에 있으며 유효합니다.
- 필요한 모듈/종속성은 중복 없이 올바른 버전으로 존재합니다.
- 패키지가 서명되었는지 확인(선택 사항)
- `Test` 및 `Get`이 규정 준수 상태 정보를 반환하는지 테스트합니다.

`Get-GuestConfigurationPackageComplianceStatus ` cmdlet의 매개 변수는 다음과 같습니다.

- **경로:** 게스트 구성 패키지의 파일 경로 또는 URI입니다.
- **Parameter**: hashtable 형식으로 제공되는 정책 매개 변수입니다.

이 명령을 처음 실행하는 경우 게스트 구성 에이전트는 Windows의 `c:\programdata\GuestConfig\bin` 및 Linux의 `/var/lib/GuestConfig/bin` 경로에 있는 테스트 머신에 설치됩니다. 이 경로는 사용자 계정에서 액세스할 수 없으므로 명령에 권한 상승이 필요합니다.

다음 명령을 실행하여 패키지를 테스트합니다.

Windows의 경우 권한 상승된 PowerShell 7 세션에서 실행합니다.

```powershell
# Get the current compliance results for the local machine
Get-GuestConfigurationPackageComplianceStatus -Path ./MyConfig.zip
```

Linux의 경우 sudo를 통해 PowerShell을 실행하여 실행합니다.

```bash
# Get the current compliance results for the local machine
sudo pwsh -command 'Get-GuestConfigurationPackageComplianceStatus -Path ./MyConfig.zip'
```

명령은 리소스별로 규정 준수 상태 및 세부 정보를 포함하는 개체를 출력합니다.

```powershell
  complianceStatus  resources
  ----------------  ---------
  True              @{BuiltInAccount=localSystem; ConfigurationName=MyConfig; Credential=; Dependencies=System.Obje…
```

#### <a name="test-the-configuration-package-can-apply-a-configuration"></a>구성 패키지가 구성을 적용하는지 테스트

마지막으로 구성 패키지 모드가 `AuditandSet`인 경우 `Start-GuestConfigurationPackageRemediation` 명령을 사용하여 `Set` 메서드를 통해 로컬 머신에 설정을 적용할 수 있는지 테스트할 수 있습니다.

> [!IMPORTANT]
> 이 명령은 실행되는 로컬 환경에서 변경 내용을 만들려고 시도합니다.

`Start-GuestConfigurationPackageRemediation` cmdlet의 매개 변수는 다음과 같습니다.

- **경로**: 게스트 구성 패키지의 전체 경로입니다.

Windows의 경우 권한 상승된 PowerShell 7 세션에서 실행합니다.

```powershell
# Test applying the configuration to local machine
Start-GuestConfigurationPackageRemediation -Path ./MyConfig.zip
```

Linux의 경우 sudo를 통해 PowerShell을 실행하여 실행합니다.

```bash
# Test applying the configuration to local machine
sudo pwsh -command 'Start-GuestConfigurationPackageRemediation -Path ./MyConfig.zip'
```

오류가 발생하지 않으면 명령이 출력을 반환하지 않습니다. `Set` 중에 발생하는 이벤트에 대한 세부 정보 문제를 해결하려면 `-verbose` 매개 변수를 사용합니다.

`Start-GuestConfigurationPackageRemediation` 명령을 실행한 후에는 다시 `Get-GuestConfigurationComplianceStatus` 명령을 실행하여 이제 머신이 올바른 상태에 있음을 확인합니다.

## <a name="next-steps"></a>다음 단계

- 머신에서 액세스할 수 있도록 [패키지 아티팩트를 게시](./guest-configuration-create-publish.md)합니다.
- `GuestConfiguration` 모듈을 사용하여 사용자 환경의 대규모 관리를 위한 [Azure Policy 정의를 생성](./guest-configuration-create-definition.md)합니다.
- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당](../assign-policy-portal.md)합니다.
- [게스트 구성 정책 할당에 대한 규정 준수 세부 정보](./determine-non-compliance.md#compliance-details-for-guest-configuration)를 보는 방법을 알아봅니다.
