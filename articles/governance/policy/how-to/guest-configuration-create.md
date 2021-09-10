---
title: 사용자 지정 게스트 구성 패키지 아티팩트 만드는 방법
description: 게스트 구성 패키지 파일을 만드는 방법을 알아봅니다.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: 12767e40ef99cf218666b6dc540a5ae1c2e2bffa
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772540"
---
# <a name="how-to-create-custom-guest-configuration-package-artifacts"></a>사용자 지정 게스트 구성 패키지 아티팩트 만드는 방법

시작하기 전에 [게스트 구성](../concepts/guest-configuration.md)에 대한 개요 페이지를 읽어보는 것이 좋습니다.

Windows와 Linux 모두를 감사/구성할 때 게스트 구성은 DSC([Desired State Configuration](/powershell/scripting/dsc/overview/overview))를 사용합니다. DSC 구성은 컴퓨터가 충족해야 하는 조건을 정의합니다.

> [!IMPORTANT]
> 환경의 상태를 감사하는 사용자 지정 패키지는 일반적으로 사용할 수 있지만 구성을 적용하는 패키지는 **미리 보기 상태** 입니다. **다음과 같은 제한 사항이 적용됩니다.**
> 
> 구성을 적용하는 게스트 구성 패키지를 사용하려면 Azure VM 게스트 구성 확장 버전 **1.29.24** 이상 또는 Arc 에이전트 **1.10.0** 이상이 필요합니다.
> 
> Linux에서 구성 만들기와 적용을 테스트하기 위해 `GuestConfiguration` 모듈은 Ubuntu 18에서만 사용할 수 있지만, 모듈에서 생성된 패키지 및 정책은 Azure 또는 Arc에서 지원되는 모든 Linux 배포판/버전에서 사용할 수 있습니다.
>
> MacOS에서 패키지를 테스트할 수 없습니다.
> 
> 사용자 지정 콘텐츠 패키지에서 비밀 또는 기밀 정보를 사용하지 마세요.

다음 단계를 수행하여 Azure 또는 비 Azure 머신 상태를 관리하는 고유한 구성을 만듭니다.

## <a name="install-powershell-7-and-required-powershell-modules"></a>PowerShell 7 및 필수 PowerShell 모듈 설치

먼저 OS, `GuestConfiguration` 모듈 및 필요한 경우 `PSDesiredStateConfiguration` 모듈에 필요한 버전의 PowerShell을 설치하기 위해 [게스트 구성 제작 환경을 설정하는 방법](./guest-configuration-create-setup.md) 페이지의 모든 단계를 수행했는지 확인합니다.

## <a name="author-a-configuration"></a>구성 작성

구성 패키지를 만들기 전에 DSC 구성을 작성하고 컴파일합니다.
필요한 경우 Windows 및 Linux에 예제 구성을 사용할 수 있습니다.

> [!IMPORTANT]
> 구성을 Windows용으로 컴파일할 때는 `PSDesiredStateConfiguration` 버전 `2.0.5`(안정적인 릴리스)를 사용합니다. 구성을 Linux용으로 컴파일할 때는 시험판 버전 `3.0.0`을 설치합니다.

예시가 DSC Windows용 [시작 설명서](/powershell/scripting/dsc/getting-started/wingettingstarted#define-a-configuration-and-generate-the-configuration-document)에 나와 있습니다.

Linux의 경우 [PowerShell 클래스](/powershell/scripting/dsc/resources/authoringResourceClass)를 사용하여 사용자 지정 DSC 리소스 모듈을 만들어야 합니다.
PowerShell 문서 페이지 [PowerShell 클래스를 통해 사용자 지정 DSC 리소스 작성](/powershell/scripting/dsc/resources/authoringResourceClass)에서 사용자 지정 리소스 및 구성의 전체 예제를 사용할 수 있으며 게스트 구성으로 테스트되었습니다.

## <a name="create-a-configuration-package-artifact"></a>구성 패키지 아티팩트 만들기

MOF가 컴파일되면 지원 파일을 함께 패키지해야 합니다.
완료된 패키지는 게스트 구성에서 Azure Policy 정의를 만드는 데 사용됩니다.

`New-GuestConfigurationPackage` cmdlet은 패키지를 만듭니다. 구성에 필요한 모듈은 모듈의 명령이 패키지에 추가할 수 있도록 개발 환경을 위한 `$Env:PSModulePath`에서 사용할 수 있어야 합니다.

Windows 콘텐츠를 만들 때 `New-GuestConfigurationPackage` cmdlet의 매개 변수:

- **이름**: 게스트 구성 패키지 이름입니다.
- **구성**: 컴파일된 DSC 구성 문서의 전체 경로입니다.
- **경로**: 출력 폴더 경로입니다. 이 매개 변수는 선택 사항입니다. 지정하지 않으면 패키지가 현재 디렉터리에 만들어집니다.
- **유형**:(Audit, AuditandSet) 구성이 감사만 해야 하는지 또는 구성이 적용되고 머신의 상태를 변경해야 하는지를 결정합니다. 기본값은 “Audit”입니다.

이 단계에서는 권한 상승이 필요하지 않습니다. Force cmdlet은 해당 명령을 두 번 이상 실행하는 경우 기존 패키지를 덮어쓰는 데 사용됩니다.

다음 명령은 다음과 같은 패키지 아티팩트를 만듭니다.

```powershell
# Create a package that will only audit compliance
New-GuestConfigurationPackage `
  -Name 'MyConfig' `
  -Configuration './Config/MyConfig.mof' `
  -Type Audit `
  -Force
```

```powershell
# Create a package that will audit and apply the configuration (Set)
New-GuestConfigurationPackage `
  -Name 'MyConfig' `
  -Configuration './Config/MyConfig.mof' `
  -Type AuditAndSet `
  -Force
```

개체는 만든 패키지의 이름 및 경로와 함께 반환됩니다.

```
Name      Path                                                    
----      ----                                                    
MyConfig  /Users/.../MyConfig/MyConfig.zip
```

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>게스트 구성 아티팩트의 예상 콘텐츠

완료된 패키지는 게스트 구성에서 Azure Policy 정의를 만드는 데 사용됩니다. 패키지는 다음으로 구성됩니다.

- MOF로 컴파일된 DSC 구성
- 모듈 폴더
  - GuestConfiguration 모듈
  - DscNativeResources 모듈
  - MOF에 필요한 DSC 리소스 모듈
- 패키지 `type` 및 `version`을 저장하는 메타 구성 파일

PowerShell cmdlet은 패키지 .zip 파일을 만듭니다. 루트 수준 폴더 또는 버전 폴더는 필요하지 않습니다. 패키지 형식은 .Zip 파일이어야 하며, 압축을 풀었을 때 총 크기가 100MB를 초과할 수 없습니다.

## <a name="extending-guest-configuration-with-third-party-tools"></a>타사 도구를 사용하여 게스트 구성 확장

게스트 구성에 대한 아티팩트 패키지를 확장하여 타사 도구를 포함할 수 있습니다. 게스트 구성을 확장하려면 두 가지 구성 요소를 개발해야 합니다.

- 타사 도구 관리와 관련된 모든 작업을 처리하는 Desired State Configuration 리소스
  - 설치
  - 호출
  - 출력 변환
- 도구에서 기본적으로 사용하는 올바른 형식의 콘텐츠

커뮤니티 솔루션이 아직 없는 경우 DSC 리소스에 사용자 지정 개발이 필요합니다. 커뮤니티 솔루션은 PowerShell 갤러리에서 [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22) 태그를 검색하여 찾을 수 있습니다.

> [!NOTE]
> 게스트 구성 확장성은 “사용자 라이선스 필요” 시나리오입니다. 사용하기 전에 타사 도구의 사용 약관을 충족하는지 확인합니다.

DSC 리소스를 개발 환경에 설치한 후에는 `New-GuestConfigurationPackage`에 대한 **FilesToInclude** 매개 변수를 사용하여 콘텐츠 아티팩트의 타사 플랫폼에 대한 콘텐츠를 포함합니다.

## <a name="next-steps"></a>다음 단계

- 사용자 개발 환경에서 [패키지 아티팩트 테스트](./guest-configuration-create-test.md)합니다.
- 머신에서 액세스할 수 있도록 [패키지 아티팩트를 게시](./guest-configuration-create-publish.md)합니다.
- `GuestConfiguration` 모듈을 사용하여 사용자 환경의 대규모 관리를 위한 [Azure Policy 정의를 생성](./guest-configuration-create-definition.md)합니다.
- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당](../assign-policy-portal.md)합니다.
- [게스트 구성 정책 할당에 대한 규정 준수 세부 정보](./determine-non-compliance.md#compliance-details-for-guest-configuration)를 보는 방법을 알아봅니다.
