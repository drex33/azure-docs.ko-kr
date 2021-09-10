---
title: 게스트 구성 제작 모듈을 설치하는 방법
description: 게스트 구성 정책 정의 및 할당을 만들고 테스트하기 위한 PowerShell 모듈을 설치하는 방법에 대해 알아봅니다.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: c32c405cffb71527e61b68f7fb532487d695743f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773167"
---
# <a name="how-to-setup-a-guest-configuration-authoring-environment"></a>게스트 구성 제작 환경 설정 방법

PowerShell 모듈 `GuestConfiguration`은 다음과 같은 사용자 지정 콘텐츠를 만드는 프로세스를 자동화합니다.

- 게스트 구성 콘텐츠 아티팩트 만들기(.zip)
- 패키지가 요구 사항을 충족하는지 확인
- 테스트를 위해 로컬로 게스트 구성 에이전트 설치
- 패키지 유효성 검사를 사용하여 머신의 설정 감사 가능
- 패키지 유효성 검사를 사용하여 머신에서 설정 구성 가능
- Azure Storage에 패키지 게시
- 정책 정의 만들기
- 정책 게시

게스트 구성을 통한 구성 적용에 대한 지원은 `3.4.2` 버전에서 도입되었습니다.

> [!IMPORTANT]
> 환경의 상태를 감사하는 사용자 지정 패키지는 일반적으로 사용할 수 있지만 구성을 적용하는 패키지는 **미리 보기 상태** 입니다. **다음과 같은 제한 사항이 적용됩니다.**
> 
> Linux에서 구성 만들기와 적용을 테스트하기 위해 `GuestConfiguration` 모듈은 Ubuntu 18 에서만 사용할 수 있지만, 모듈에서 생성된 패키지 및 정책 정의는 Azure 또는 Arc에서 지원되는 모든 Linux 배포판/버전에서 사용할 수 있습니다.
>
> MacOS에서 패키지를 테스트할 수 없습니다.

### <a name="base-requirements"></a>기본 요구 사항

모듈을 설치할 수 있는 운영 체제는 다음과 같습니다.

- Ubuntu 18
- Windows

모듈은 PowerShell 7을 실행하는 머신에 설치할 수 있습니다. 아래 나열된 PowerShell 버전을 설치합니다.

| OS | PowerShell 버전 |
|-|-|
|Windows|[PowerShell 7.1.3](https://github.com/PowerShell/PowerShell/releases/tag/v7.1.3)|
|Ubuntu 18|[PowerShell 7.2.0-preview.6](https://github.com/PowerShell/PowerShell/releases/tag/v7.2.0-preview.6)|

`GuestConfiguration` 모듈에는 다음과 같은 소프트웨어가 필요합니다.

- Azure PowerShell 5.9.0 이상. 필요한 Az 모듈은 `GuestConfiguration` 모듈과 함께 자동으로 설치됩니다. 또는 [해당 지침](/powershell/azure/install-az-ps)에 따라 수행해도 됩니다.
  - Az 모듈인 ‘Az.Accounts’ 및 ‘Az.Resources’, ‘Az.Storage’만 필요합니다.
- `PSDesiredStateConfiguration` 모듈.

### <a name="install-the-module-from-the-powershell-gallery"></a>먼저 PowerShell 갤러리에서 이 모듈을 설치합니다.

Windows 또는 Linux에서 `GuestConfiguration` 모듈을 설치하려면 PowerShell 7에서 다음 명령을 실행합니다.

```powershell
# Install the guest configuration DSC resource module from PowerShell Gallery
Install-Module -Name GuestConfiguration
```

모듈을 가져왔는지 확인합니다.

```powershell
# Get a list of commands for the imported GuestConfiguration module
Get-Command -Module 'GuestConfiguration'
```

## <a name="update-and-import-the-psdesiredstateconfiguration-module-on-linux"></a>Linux에서 PSDesiredStateConfiguration 모듈 업데이트 및 가져오기

PowerShell 7.2 미리 보기 6부터 DSC는 PowerShell과는 별개로 PowerShell 갤러리의 모듈로써 릴리스됩니다. Linux의 PowerShell 환경에 DSC 버전 3을 설치하려면 아래 명령을 실행합니다.

```powershell
# Install the DSC module before compiling using the Configuration keyword
Install-Module PSDesiredStateConfiguration -AllowPreRelease -Force
```

## <a name="next-steps"></a>다음 단계

- 게스트 구성을 위한 [패키지 아티팩트를 만듭니다](./guest-configuration-create.md).
- 사용자 개발 환경에서 [패키지 아티팩트 테스트](./guest-configuration-create-test.md)합니다.
- `GuestConfiguration` 모듈을 사용하여 사용자 환경의 대규모 관리를 위한 [Azure Policy 정의를 생성](./guest-configuration-create-definition.md)합니다.
- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당](../assign-policy-portal.md)합니다.
- [게스트 구성 정책 할당에 대한 규정 준수 세부 정보](./determine-non-compliance.md#compliance-details-for-guest-configuration)를 보는 방법을 알아봅니다.
