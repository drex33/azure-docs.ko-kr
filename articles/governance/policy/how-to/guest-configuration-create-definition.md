---
title: 사용자 지정 게스트 구성 정책 정의에서 매개 변수를 사용하는 방법
description: 게스트 구성 정책 만들기에 대해 알아봅니다.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: 1dd1620d0ef41bf28a276cfe2412ca4bdc09d183
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644907"
---
# <a name="how-to-create-custom-guest-configuration-policy-definitions"></a>사용자 지정 게스트 구성 정책 정의에서 매개 변수를 사용하는 방법

시작하기 전에 [게스트 구성](../concepts/guest-configuration.md)에 대한 개요 페이지와 게스트 구성 정책 효과에 대한 세부 정보([게스트 구성에 대한 수정 옵션을 구성하는 방법](../concepts/guest-configuration-policy-effects.md))를 읽어보는 것이 좋습니다.

> [!IMPORTANT]
> 게스트 구성 확장은 Azure 가상 머신에 필요합니다. 모든 머신에서 확장을 대규모로 배포하려면 정책 이니셔티브(`Deploy prerequisites to enable guest configuration policies on
> virtual machines`)를 할당합니다.
> 
> 구성을 적용하는 게스트 구성 패키지를 사용하려면 Azure VM 게스트 구성 확장 버전 **1.29.24** 이상 또는 Arc 에이전트 **1.10.0** 이상이 필요합니다.
>
> **AuditIfNotExists** 를 사용한 사용자 지정 게스트 구성 정책 정의는 일반적으로 사용 가능하지만 게스트 구성을 포함한 **DeployIfNotExists** 를 사용한 정의는 **미리 보기 상태** 입니다.

다음 단계를 사용하여 규정 준수를 감사하거나 Azure 또는 Arc 사용 머신의 상태를 관리하는 고유한 정책을 만듭니다.

## <a name="install-powershell-7-and-required-powershell-modules"></a>PowerShell 7 및 필수 PowerShell 모듈 설치

먼저 OS, `GuestConfiguration` 모듈에 필요한 버전의 PowerShell을 설치하기 위해 [게스트 구성 제작 환경을 설정하는 방법](./guest-configuration-create-setup.md) 페이지의 모든 단계를 수행했는지 확인합니다.

## <a name="create-and-publish-a-guest-configuration-package-artifact"></a>게스트 구성 패키지 아티팩트 만들기 및 게시

아직 수행하지 않은 경우 [사용자 지정 게스트 구성 패키지 아티팩트 만드는 방법](./guest-configuration-create.md)의 모든 단계를 수행하여 사용자 지정 게스트 구성 패키지를 만들고 게시하고, [게스트 구성 패키지 아티팩트를 테스트하는 방법](./guest-configuration-create-test.md)의 모든 단계를 수행하여 개발 환경에서 로컬로 게스트 구성 패키지의 유효성을 검사합니다.

## <a name="policy-requirements-for-guest-configuration"></a>게스트 구성에 대한 정책 요구 사항

게스트 구성 할당의 프로비저닝 및 보고를 자동화하기 위해 정책 정의 `metadata` 섹션에는 게스트 구성 서비스를 위한 두 개의 속성이 포함되어야 합니다. `category` 속성은 “게스트 구성”으로 설정해야 하며, `guestConfiguration` 섹션에는 게스트 구성 할당 관련 정보가 포함되어야 합니다. `New-GuestConfigurationPolicy` cmdlet은 이 텍스트를 자동으로 만듭니다.

다음 예제에서는 `New-GuestConfigurationPolicy`에 의해 자동으로 생성되는 `metadata` 섹션을 보여 줍니다.

```json
    "metadata": {
      "category": "Guest Configuration",
      "guestConfiguration": {
        "name": "test",
        "version": "1.0.0",
        "contentType": "Custom",
        "contentUri": "CUSTOM-URI-HERE",
        "contentHash": "CUSTOM-HASH-VALUE-HERE",
        "configurationParameter": {}
      }
    },
```

`category` 속성은 “게스트 구성”으로 설정해야 합니다. 정의 효과가 “DeployIfNotExists”로 설정된 경우 `then` 섹션에는 게스트 구성 할당에 대한 배포 세부 정보가 포함되어야 합니다. `New-GuestConfigurationPolicy` cmdlet은 이 텍스트를 자동으로 만듭니다.

### <a name="create-an-azure-policy-definition"></a>Azure Policy 정의 만들기

게스트 구성 사용자 지정 정책 패키지를 만들고 업로드한 후에는 게스트 구성 정책 정의를 만듭니다. `New-GuestConfigurationPolicy` cmdlet은 사용자 지정 정책 패키지를 사용하고 정책 정의를 만듭니다.

`New-GuestConfigurationPolicy`의 **PolicyId** 매개 변수에는 고유한 문자열이 필요합니다. GUID(Globally Unique Identifier)를 사용하면 좋습니다. 새 정의에는 `New-GUID` cmdlet을 사용하여 새 GUID를 생성합니다. 정의를 업데이트할 때 **PolicyId** 에 같은 고유 문자열을 사용하여 올바른 정의가 업데이트되었는지 확인합니다.

`New-GuestConfigurationPolicy` cmdlet의 매개 변수는 다음과 같습니다.

- **PolicyId**: 정의를 식별하는 GUID 또는 기타 고유 문자열입니다.
- **ContentUri**: 게스트 구성 콘텐츠 패키지의 퍼블릭 HTTP URI입니다.
- **DisplayName**: 정책 표시 이름입니다.
- **설명**: 정책 설명입니다.
- **Parameter**: hashtable 형식으로 제공되는 정책 매개 변수입니다.
- **버전**: 정책 버전입니다.
- **경로**: 정책 정의가 만들어지는 대상 경로입니다.
- **Platform**: 게스트 구성 정책 및 콘텐츠 패키지용 대상 플랫폼(Windows/Linux)입니다.
- **Mode**: (ApplyAndMonitor, ApplyAndAutoCorrect, Audit) 정책에서 구성을 감사해야 하는지 아니면 배포해야 하는지 선택합니다. 기본값은 “Audit”입니다.
- **Tag** 는 정책 정의에 하나 이상의 태그 필터를 추가합니다.
- **Category** 는 정책 정의의 범주 메타데이터 필드를 설정합니다.

“Mode” 매개 변수에 대한 자세한 내용은 [게스트 구성에 대한 수정 옵션을 구성하는 방법](../concepts/guest-configuration-policy-effects.md) 페이지를 참조하세요.

지정된 경로에서 사용자 지정 구성 패키지를 사용하여 감사하는 정책 정의를 만듭니다.

```powershell
New-GuestConfigurationPolicy `
  -PolicyId 'My GUID' `
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'My audit policy.' `
  -Description 'Details about my policy.' `
  -Path './policies' `
  -Platform 'Windows' `
  -Version 1.0.0 `
  -Verbose
```

지정된 경로에서 사용자 지정 구성 패키지를 사용하여 구성을 배포하는 정책 정의를 만듭니다.

```powershell
New-GuestConfigurationPolicy `
  -PolicyId 'My GUID' `
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'My audit policy.' `
  -Description 'Details about my policy.' `
  -Path './policies' `
  -Platform 'Windows' `
  -Version 1.0.0 `
  -Mode 'ApplyAndAutoCorrect' `
  -Verbose
```

cmdlet 출력은 정책 파일의 정책 표시 이름과 경로가 포함된 개체를 반환합니다. 감사 정책 정의를 만드는 정책 JSON 파일의 이름은 **auditIfNotExists.json** 이며 구성을 적용하기 위해 정책 정의를 만드는 파일의 이름은 **deployIfNotExists.json** 입니다.

#### <a name="filtering-guest-configuration-policies-using-tags"></a>태그를 사용하여 게스트 구성 정책 필터링

게스트 구성에서 cmdlet으로 생성되는 정책 정의에는 선택적으로 태그에 대한 필터가 포함될 수 있습니다. `New-GuestConfigurationPolicy`의 **Tag** 매개 변수는 개별 태그 항목을 포함하는 hashtable 배열을 지원합니다. 태그는 정책 정의의 `If` 섹션에 추가되며 정책 할당으로 수정할 수 없습니다.

태그를 필터링하는 정책 정의의 예제 코드 조각은 아래에 제공되어 있습니다.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original guest configuration content
    }
  ]
}
```

#### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>사용자 지정 게스트 구성 정책 정의에서 매개 변수 사용

게스트 구성은 런타임 시 구성 속성 재정의를 지원합니다. 이 기능은 패키지의 MOF 파일에 있는 값을 정적으로 간주할 필요가 없음을 의미합니다. 재정의 값은 Azure Policy를 통해 제공되며 구성을 작성하거나 컴파일하는 방법을 변경하지 않습니다.

cmdlet `New-GuestConfigurationPolicy` 및 `Get-GuestConfigurationPacakgeComplianceStatus `에는 **Parameters** 라는 매개 변수가 포함됩니다. 이 매개 변수는 각 매개 변수에 대한 모든 세부 정보를 포함하는 hashtable 정의를 사용하고 Azure Policy 정의에 사용되는 각 파일의 필수 섹션을 만듭니다.

다음 예에서는 서비스를 감사할 정책 정의를 만듭니다. 사용자는 정책 할당 시 이를 목록에서 선택할 수 있습니다.

```powershell
# This DSC Resource text:
Service 'UserSelectedNameExample'
  {
    Name = 'ParameterValue'
    Ensure = 'Present'
    State = 'Running'
  }`

# Would require the following hashtable:
$PolicyParameterInfo = @(
  @{
    Name = 'ServiceName'                                           # Policy parameter name (mandatory)
    DisplayName = 'windows service name.'                          # Policy parameter display name (mandatory)
    Description = 'Name of the windows service to be audited.'     # Policy parameter description (optional)
    ResourceType = 'Service'                                       # DSC configuration resource type (mandatory)
    ResourceId = 'UserSelectedNameExample'                         # DSC configuration resource id (mandatory)
    ResourcePropertyName = 'Name'                                  # DSC configuration resource property name (mandatory)
    DefaultValue = 'winrm'                                         # Policy parameter default value (optional)
    AllowedValues = @('BDESVC','TermService','wuauserv','winrm')   # Policy parameter allowed values (optional)
  }
)

New-GuestConfigurationPolicy `
  -PolicyId 'My GUID' `
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'Audit Windows Service.' `
  -Description 'Audit if a Windows Service isn't enabled on Windows machine.' `
  -Path '.\policies' `
  -Parameter $PolicyParameterInfo `
  -Version 1.0.0
```

### <a name="publish-the-azure-policy-definition"></a>Azure Policy 정의 게시

마지막으로 `Publish-GuestConfigurationPolicy` cmdlet을 사용하여 정책 정의를 게시합니다. cmdlet에는 `New-GuestConfigurationPolicy`에서 만든 JSON 파일의 위치를 가리키는 **Path** 매개 변수만 있습니다.

게시 명령을 실행하려면 Azure에서 정책 정의를 만들기 위한 액세스 권한이 필요합니다. 특정 권한 부여 요구 사항은 [Azure Policy 개요](../overview.md) 페이지에 설명되어 있습니다. 가장 적합한 기본 제공 역할은 **리소스 정책 기여자** 입니다.

```powershell
Publish-GuestConfigurationPolicy -Path '.\policies'
```

Azure에서 만든 정책 정의를 사용하는 마지막 단계는 정의를 할당하는 단계입니다. [Portal](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md) 및 [Azure PowerShell](../assign-policy-powershell.md)을 사용하여 정의를 할당하는 방법을 참조하세요.

### <a name="optional-piping-output-from-each-command-to-the-next"></a>선택 사항: 각 명령의 출력을 다음 명령으로 파이핑

게스트 구성의 명령은 이름별로 파이프라인 매개 변수를 지원합니다.
`|` 연산자를 사용하여 각 명령의 출력을 다음 명령으로 파이프라인할 수 있습니다.
각 명령의 출력을 복사/붙여넣을 필요가 없으므로 파이핑을 빠르게 반복해서 실행하면 개발자 환경에서 유용합니다.

`|` 연산자를 사용하여 시퀀스를 실행하려면 다음을 수행합니다.

```powershell
# End to end flow piping output of each command to the next
$ConfigName         = myConfigName
$ResourceGroupName  = myResourceGroupName
$StorageAccountName = myStorageAccountName
$DisplayName        = 'Configure Linux machine per my scenario.'
$Description        = 'Details about my policy.'
New-GuestConfigurationPackage -Name $ConfigName -Configuration ./$ConfigName.mof -Path ./package/ -Type AuditAndSet -Force |
Publish-GuestConfigurationPackage -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName -Force |
New-GuestConfigurationPolicy -PolicyId 'My GUID' -DisplayName $DisplayName -Description $Description -Path './policies' -Platform 'Linux' -Version 1.0.0 -Mode 'ApplyAndAutoCorrect' |
Publish-GuestConfigurationPolicy
```

## <a name="policy-lifecycle"></a>정책 수명 주기

정책 정의의 업데이트를 릴리스하려면 게스트 구성 패키지와 Azure Policy 정의 세부 정보를 모두 변경합니다.

> [!NOTE]
> 게스트 구성 할당의 `version` 속성은 Microsoft에서 호스트하는 패키지에만 영향을 줍니다. 사용자 지정 콘텐츠의 버전을 관리하는 가장 좋은 방법은 버전을 파일 이름에 포함하는 것입니다.

먼저, `New-GuestConfigurationPackage`를 실행할 때, 이전 버전과는 차별되는 고유한 패키지의 이름을 지정합니다. 이름에 버전 번호를 포함할 수 있습니다(예: `PackageName_1.0.0`). 이 예의 숫자는 패키지를 고유하게 만드는 데만 사용되며 패키지를 다른 패키지보다 최신 버전 또는 이전 버전으로 간주하도록 지정하기 위한 것은 아닙니다.

둘째, 다음 각 설명에 따라 `New-GuestConfigurationPolicy` cmdlet에 사용된 매개 변수를 업데이트합니다.

- **버전**: `New-GuestConfigurationPolicy` cmdlet을 실행할 때 현재 게시된 것보다 큰 버전 번호를 지정해야 합니다.
- **contentUri**: `New-GuestConfigurationPolicy` cmdlet을 실행할 때 패키지의 위치에 대한 URI를 지정해야 합니다. 패키지 버전을 파일 이름에 포함하면 각 릴리스에서 속성의 값이 변경됩니다.
- **contentHash**: 이 속성은 `New-GuestConfigurationPolicy` cmdlet에 의해 자동으로 업데이트됩니다. 이는 `New-GuestConfigurationPackage`에서 만든 패키지의 해시 값입니다. 게시하는 `.zip` 파일에 대한 속성이 정확해야 합니다. **contentUri** 속성만 업데이트된 경우 확장에서 콘텐츠 패키지를 수락하지 않습니다.

업데이트된 패키지를 릴리스하는 가장 쉬운 방법은 이 문서에 설명된 프로세스를 반복하고 업데이트된 버전 번호를 제공하는 것입니다. 해당 프로세스는 모든 속성이 올바르게 업데이트되었음을 보장합니다.

## <a name="next-steps"></a>다음 단계

- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당](../assign-policy-portal.md)합니다.
- [게스트 구성 정책 할당에 대한 규정 준수 세부 정보](./determine-non-compliance.md#compliance-details-for-guest-configuration)를 보는 방법을 알아봅니다.
