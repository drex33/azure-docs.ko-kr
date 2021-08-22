---
title: Linux용 게스트 구성 정책을 만드는 방법
description: Linux용 Azure Policy 게스트 구성 정책을 만드는 방법에 대해 알아봅니다.
ms.date: 03/31/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 89f4e64f6448f93a4b746ae4301450707f832cde
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112287014"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Linux용 게스트 구성 정책을 만드는 방법

사용자 지정 정책을 만들기 전에 [Azure Policy 게스트 구성](../concepts/guest-configuration.md)에서 개요 정보를 읽어 보세요.

Windows용 게스트 구성 정책을 만드는 방법에 대한 자세한 내용은 [Windows용 게스트 구성 정책을 만드는 방법](./guest-configuration-create.md) 페이지를 참조하세요.

Linux를 감사할 때 게스트 구성은 [Chef InSpec](https://community.chef.io/tools/chef-inspec)를 사용합니다. InSpec 프로필은 컴퓨터가 충족해야 하는 조건을 정의합니다. 구성을 평가하지 못한 경우 **auditIfNotExists** 정책 효과가 트리거되고 컴퓨터를 **미준수** 로 간주합니다.

[Azure Policy 게스트 구성](../concepts/guest-configuration.md)은 컴퓨터 내의 설정을 감사하는 데에만 사용할 수 있습니다. 컴퓨터 내 설정 수정은 아직 사용할 수 없습니다.

다음 작업을 사용하여 Azure 또는 비 Azure 컴퓨터 상태의 유효성을 검사하는 고유한 구성을 만듭니다.

> [!IMPORTANT]
> Azure Government 및 Azure 중국 21Vianet 환경에서 게스트 구성을 사용하는 사용자 지정 정책 정의는 미리 보기 기능입니다.
>
> 게스트 구성 확장은 Azure Virtual Machines에서 감사를 수행하는 데 필요합니다. 모든 Linux 컴퓨터에서 확장을 대규모로 배포하려면 정책 정의(`Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`)를 할당합니다.
>
> 사용자 지정 콘텐츠 패키지에서 비밀 또는 기밀 정보를 사용하지 마세요.

## <a name="install-the-powershell-module"></a>PowerShell 모듈 설치

게스트 구성 모듈은 다음과 같은 사용자 지정 콘텐츠를 만드는 프로세스를 자동화합니다.

- 게스트 구성 콘텐츠 아티팩트 만들기(.zip)
- 자동화된 아티팩트 테스트
- 정책 정의 만들기
- 정책 게시

모듈은 로컬에서 실행되는 PowerShell 6.2 이상 또는 [Azure Cloud Shell](https://shell.azure.com) 또는 [Azure PowerShell Core Docker 이미지](https://hub.docker.com/r/azuresdk/azure-powershell-core)를 사용하는 Windows, macOS 또는 Linux를 실행하는 컴퓨터에 설치할 수 있습니다.

> [!NOTE]
> 구성 컴파일은 Linux에서 지원되지 않습니다.

### <a name="base-requirements"></a>기본 요구 사항

모듈을 설치할 수 있는 운영 체제는 다음과 같습니다.

- Linux
- macOS
- Windows

> [!NOTE]
> OMI에 대한 종속성으로 인해 `Test-GuestConfigurationPackage` cmdlet에는 OpenSSL 버전 1.0이 필요합니다. 이로 인해 OpenSSL 1.1 이상의 환경에서 오류가 발생합니다.
>
> `Test-GuestConfigurationPackage` cmdlet은 Windows에서만 게스트 구성 모듈 버전 2.1.0용으로 실행할 수 있습니다.

게스트 구성 리소스 모듈에는 다음 소프트웨어가 필요합니다.

- PowerShell 6.2 이상. 아직 설치되지 않은 경우 [다음 지침](/powershell/scripting/install/installing-powershell)을 따릅니다.
- Azure PowerShell 1.5.0 이상. 아직 설치되지 않은 경우 [다음 지침](/powershell/azure/install-az-ps)을 따릅니다.
  - Az 모듈 'Az.Accounts' 및 'Az.Resources'만 필요합니다.

### <a name="install-the-module"></a>모듈 설치

PowerShell에서 **GuestConfiguration** 모듈을 설치하려면 다음을 수행합니다.

1. PowerShell 프롬프트에서 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. 모듈을 가져왔는지 확인합니다.

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Linux용 게스트 구성 아티팩트 및 정책

Linux 환경에서도 게스트 구성은 Desired State Configuration을 언어 추상화로 사용합니다. 구현은 네이티브 코드(C++)를 기반으로 하므로 PowerShell을 로드할 필요가 없습니다. 하지만 환경에 대한 세부 정보를 설명하는 구성 MOF가 필요합니다.
DSC는 InSpec에서 실행 방법, 매개 변수가 제공되는 방법 및 출력이 서비스에 반환되는 방법을 표준화하기 위한 래퍼 역할을 합니다. 사용자 지정 InSpec 콘텐츠로 작업하는 경우 DSC에 대한 지식이 거의 필요하지 않습니다.

#### <a name="configuration-requirements"></a>구성 요구 사항

사용자 지정 구성의 이름은 모든 위치에서 일관되어야 합니다. 콘텐츠 패키지용 .zip 파일의 이름, MOF 파일의 구성 이름 및 Azure Resource Manager 템플릿(ARM 템플릿)에 있는 게스트 할당 이름은 동일해야 합니다.

PowerShell cmdlet은 패키지를 만드는 데 도움이 됩니다. 루트 수준 폴더 또는 버전 폴더는 필요하지 않습니다. 패키지 형식은 .zip 파일이어야 합니다. 또한 압축을 풀었을 때 전체 크기가 100MB를 초과할 수 없습니다.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux에서 사용자 지정 게스트 구성

Linux에서 게스트 구성 시 `ChefInSpecResource` 리소스를 사용하여 엔진에 [InSpec 프로필](https://docs.chef.io/inspec/profiles/)의 이름을 제공합니다. **이름** 은 유일하게 필요한 리소스 속성입니다. 아래에 설명된 바와 같이 YAML 파일과 Ruby 스크립트 파일을 만듭니다.

먼저, InSpec에서 사용하는 YAML 파일을 만듭니다. 이 파일은 환경에 대한 기본 정보를 제공합니다. 예를 들면 다음과 같습니다.

```yaml
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

프로젝트 디렉터리의 `linux-path`라는 폴더에 이름이 `inspec.yml`인 이 파일을 저장합니다.

그런 다음, 컴퓨터를 감사하는 데 사용되는 InSpec 언어 추상화를 사용하여 Ruby 파일을 만듭니다.

```ruby
describe file('/tmp') do
    it { should exist }
end
```

`linux-path` 디렉터리 내 `controls`라는 새 폴더에 이름이 `linux-path.rb`인 이 파일을 저장합니다.

마지막으로 구성을 만들고, **PSDesiredStateConfiguration** 리소스 모듈을 가져오며, 구성을 컴파일합니다.

```powershell
# import PSDesiredStateConfiguration module
import-module PSDesiredStateConfiguration

# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
AuditFilePathExists -out ./Config
```

프로젝트 폴더에 이름이 `config.ps1`인 이 파일을 저장합니다. 터미널에서 `./config.ps1`을 실행하여 PowerShell에서 실행합니다. 새 MOF 파일이 생성됩니다.

`Node AuditFilePathExists` 명령은 기술적으로 필요하지 않지만, 기본값 `localhost.mof`가 아닌 `AuditFilePathExists.mof`라는 파일을 생성합니다. .MOF 파일 이름을 구성에 따라 지정하면 대규모 작업을 수행할 때 많은 파일을 쉽게 구성할 수 있습니다.

이제 프로젝트 구조가 다음과 같이 표시됩니다.

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        inspec.yml
        / controls
            linux-path.rb
```

지원 파일은 한 패키지에 포함되어야 합니다. 완성된 패키지는 게스트 구성에서 Azure Policy 정의를 만드는 데 사용됩니다.

`New-GuestConfigurationPackage` cmdlet은 패키지를 만듭니다. Linux 콘텐츠를 만들 때 `New-GuestConfigurationPackage` cmdlet의 매개 변수는 다음과 같습니다.

- **Name**: 게스트 구성 패키지 이름입니다.
- **구성**: 컴파일된 구성 문서 전체 경로입니다.
- **경로**: 출력 폴더 경로입니다. 이 매개 변수는 선택 사항입니다. 지정하지 않으면 패키지가 현재 디렉터리에 만들어집니다.
- **ChefInspecProfilePath**: InSpec 프로필의 전체 경로입니다. 이 매개 변수는 Linux를 감사할 콘텐츠를 만드는 경우에만 지원됩니다.

다음 명령을 실행하여 이전 단계에 지정된 구성을 사용하는 패키지를 만듭니다.

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

구성 패키지를 만든 후 Azure에 게시하기 전에 워크스테이션 또는 CI/CD 환경(연속 통합 및 연속 배포)에서 패키지를 테스트할 수 있습니다. GuestConfiguration cmdlet `Test-GuestConfigurationPackage`에는 Azure 컴퓨터 내에서 사용되는 것과 동일한 에이전트가 개발 환경에 포함되어 있습니다. 이 솔루션을 사용하여 청구되는 클라우드 환경에 릴리스하기 전 로컬에서 통합 테스트를 수행할 수 있습니다.

에이전트는 실제로 로컬 환경을 평가하므로, 대부분의 경우 감사할 계획인 OS 플랫폼과 동일한 플랫폼에서 Test- cmdlet을 실행해야 합니다.

`Test-GuestConfigurationPackage` cmdlet의 매개 변수는 다음과 같습니다.

- **Name**: 게스트 구성 정책 이름입니다.
- **Parameter**: hashtable 형식으로 제공되는 정책 매개 변수입니다.
- **경로**: 게스트 구성 패키지의 전체 경로입니다.

다음 명령을 실행하여 이전 단계에서 만든 패키지를 테스트합니다.

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

cmdlet은 PowerShell 파이프라인의 입력도 지원합니다. `New-GuestConfigurationPackage` cmdlet의 출력을 `Test-GuestConfigurationPackage` cmdlet으로 파이프합니다.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefInspecProfilePath './' | Test-GuestConfigurationPackage
```

다음 단계는 Azure Blob Storage에 파일을 게시하는 과정입니다. `Publish-GuestConfigurationPackage` 명령에는 `Az.Storage` 모듈이 필요합니다.

`Publish-GuestConfigurationPackage` cmdlet의 매개 변수는 다음과 같습니다.

- **Path**: 게시할 패키지의 위치
- **ResourceGroupName**: 스토리지 계정이 있는 리소스 그룹의 이름
- **StorageAccountName**: 패키지를 게시해야 하는 스토리지 계정의 이름
- **StorageContainerName**: (기본값: _guestconfiguration_) 스토리지 계정의 스토리지 컨테이너 이름
- **Force**: 스토리지 계정에서 이름이 같은 기존 패키지를 덮어씁니다.

다음 예제에서는 패키지를 스토리지 컨테이너 이름 'guestconfiguration'에 게시합니다.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditFilePathExists/AuditFilePathExists.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
```

게스트 구성 사용자 지정 정책 패키지를 만들고 업로드한 후에는 게스트 구성 정책 정의를 만듭니다. `New-GuestConfigurationPolicy` cmdlet은 사용자 지정 정책 패키지를 사용하고 정책 정의를 만듭니다.

`New-GuestConfigurationPolicy` cmdlet의 매개 변수는 다음과 같습니다.

- **ContentUri**: 게스트 구성 콘텐츠 패키지의 공용 HTTP(s) URI입니다.
- **DisplayName**: 정책 표시 이름입니다.
- **설명**: 정책 설명입니다.
- **Parameter**: hashtable 형식으로 제공되는 정책 매개 변수입니다.
- **버전**: 정책 버전입니다.
- **경로**: 정책 정의가 만들어지는 대상 경로입니다.
- **Platform**: 게스트 구성 정책 및 콘텐츠 패키지용 대상 플랫폼(Windows/Linux)입니다.
- **Tag** 는 정책 정의에 하나 이상의 태그 필터를 추가합니다.
- **Category** 는 정책 정의의 범주 메타데이터 필드를 설정합니다.

다음 예제에서는 사용자 지정 정책 패키지에서 지정된 경로에 정책 정의를 만듭니다.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

`New-GuestConfigurationPolicy`에서 만들어지는 파일은 다음과 같습니다.

- **auditIfNotExists.json**

cmdlet 출력은 정책 파일의 이니셔티브 표시 이름과 경로가 포함된 개체를 반환합니다.

마지막으로 `Publish-GuestConfigurationPolicy` cmdlet을 사용하여 정책 정의를 게시합니다. cmdlet에는 `New-GuestConfigurationPolicy`에서 만든 JSON 파일의 위치를 가리키는 **Path** 매개 변수만 있습니다.

Publish 명령을 실행하려면 Azure에서 정책을 만들기 위한 액세스 권한이 필요합니다. 특정 권한 부여 요구 사항은 [Azure Policy 개요](../overview.md) 페이지에 설명되어 있습니다. 가장 적합한 기본 제공 역할은 **리소스 정책 기여자** 입니다.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path './policies'
```

`Publish-GuestConfigurationPolicy` cmdlet은 PowerShell 파이프라인의 경로를 허용합니다. 이 기능은 정책 파일을 만들어 단일 파이프 명령 집합에 게시할 수 있음을 의미합니다.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
| Publish-GuestConfigurationPolicy
```

Azure에서 만든 정책을 사용하는 마지막 단계는 정의를 할당하는 과정입니다. [Portal](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md) 및 [Azure PowerShell](../assign-policy-powershell.md)을 사용하여 정의를 할당하는 방법을 참조하세요.

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>사용자 지정 게스트 구성 정책에서 매개 변수 사용

게스트 구성은 런타임 시 구성 속성 재정의를 지원합니다. 이 기능은 패키지의 MOF 파일에 있는 값을 정적으로 간주할 필요가 없음을 의미합니다. 재정의 값은 Azure Policy를 통해 제공되며 구성을 작성하거나 컴파일하는 방법을 변경하지 않습니다.

InSpec을 사용하는 매개 변수는 일반적으로 런타임 시 입력으로 또는 특성을 사용하는 코드로 처리됩니다. 게스트 구성은 정책이 할당될 때 입력을 제공할 수 있도록 이 프로세스를 난독 처리합니다. 특성 파일은 컴퓨터 내에 자동으로 만들어집니다. 프로젝트에서 파일을 만들고 추가할 필요가 없습니다. Linux 감사 프로젝트에 매개 변수를 추가하는 두 가지 단계가 있습니다.

컴퓨터에서 감사할 항목을 스크립팅하는 Ruby 파일에 대한 입력을 정의합니다. 예를 들면 다음과 같습니다.

```ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

임의의 문자열을 값으로 하여 구성의 **AttributesYmlContent** 속성을 추가합니다. 게스트 구성 에이전트는 InSpec에서 특성을 저장하는 데 사용되는 YAML 파일을 자동으로 만듭니다.
다음 예제를 참조하세요.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "fromParameter"
        }
    }
}
```

이 문서에 제공된 예제를 사용하여 MOF 파일을 다시 컴파일합니다.

cmdlet `New-GuestConfigurationPolicy` 및 `Test-GuestConfigurationPolicyPackage`에는 **Parameters** 라는 매개 변수가 포함됩니다. 이 매개 변수는 각 매개 변수에 대한 세부 정보를 모두 포함하는 해시 테이블을 사용하며, 각 Azure Policy 정의를 만드는 데 사용되는 파일의 필수 섹션을 모두 자동으로 만듭니다.

다음 예제에서는 사용자가 정책 할당 시 경로를 제공하는 파일 경로를 감사할 정책 정의를 만듭니다.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = 'File path to be audited.'      # Policy parameter description (optional)
        ResourceType = 'ChefInSpecResource'           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = 'AttributesYmlContent' # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

$uri = 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D'

New-GuestConfigurationPolicy -ContentUri $uri `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Platform 'Linux' `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>정책 수명 주기

정책 업데이트를 릴리스하려면 게스트 구성 패키지와 Azure Policy 정의 세부 정보를 모두 변경합니다.

> [!NOTE]
> 게스트 구성 할당의 `version` 속성은 Microsoft에서 호스팅하는 패키지에만 영향을 줍니다. 사용자 지정 콘텐츠의 버전을 관리하는 가장 좋은 방법은 버전을 파일 이름에 포함하는 것입니다.

먼저, `New-GuestConfigurationPackage`를 실행할 때, 이전 버전과는 차별되는 고유한 패키지의 이름을 지정합니다. 이름에 버전 번호를 포함할 수 있습니다(예: `PackageName_1.0.0`). 이 예의 숫자는 패키지를 고유하게 만드는 데만 사용되며 패키지를 다른 패키지보다 최신 버전 또는 이전 버전으로 간주하도록 지정하기 위한 것은 아닙니다.

둘째, 다음 각 설명에 따라 `New-GuestConfigurationPolicy` cmdlet에 사용된 매개 변수를 업데이트합니다.

- **버전**: `New-GuestConfigurationPolicy` cmdlet을 실행할 때 현재 게시된 것보다 큰 버전 번호를 지정해야 합니다.
- **contentUri**: `New-GuestConfigurationPolicy` cmdlet을 실행할 때 패키지의 위치에 대한 URI를 지정해야 합니다. 패키지 버전을 파일 이름에 포함하면 각 릴리스에서 속성의 값이 변경됩니다.
- **contentHash**: 이 속성은 `New-GuestConfigurationPolicy` cmdlet에 의해 자동으로 업데이트됩니다. 이는 `New-GuestConfigurationPackage`에서 만든 패키지의 해시 값입니다. 게시하는 `.zip` 파일에 대한 속성이 정확해야 합니다. **contentUri** 속성만 업데이트된 경우 확장에서 콘텐츠 패키지를 수락하지 않습니다.

업데이트된 패키지를 릴리스하는 가장 쉬운 방법은 이 문서에 설명된 프로세스를 반복하고 업데이트된 버전 번호를 제공하는 것입니다. 해당 프로세스는 모든 속성이 올바르게 업데이트되었음을 보장합니다.

### <a name="filtering-guest-configuration-policies-using-tags"></a>태그를 사용하여 게스트 구성 정책 필터링

게스트 구성 모듈에서 cmdlet에 의해 만들어진 정책에는 선택적으로 태그에 대한 필터가 포함될 수 있습니다. `New-GuestConfigurationPolicy`의 **-Tag** 매개 변수는 개별 태그 전체를 포함하는 해시 테이블의 배열을 지원합니다. 태그는 정책 정의의 `If` 섹션에 추가되며 정책 할당으로 수정할 수 없습니다.

태그를 필터링하는 정책 정의의 예제 조각은 다음과 같습니다.

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
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>선택 사항: 게스트 구성 패키지 서명

게스트 구성 사용자 지정 정책은 SHA256 해시를 사용하여 변경되지 않은 정책 패키지의 유효성을 검사합니다.
또한 필요에 따라 고객은 인증서를 사용하여 패키지에 서명하고 게스트 구성 확장에서 강제로 서명된 콘텐츠만 허용하도록 할 수 있습니다.

이 시나리오를 사용하려면 두 단계를 완료해야 합니다. cmdlet을 실행하여 콘텐츠 패키지에 서명하고 코드에 서명해야 하는 컴퓨터에 태그를 추가합니다.

서명 유효성 검사 기능을 사용하려면 `Protect-GuestConfigurationPackage` cmdlet을 실행하여 게시하기 전 패키지에 서명합니다. 이 cmdlet에는 '코드 서명' 인증서가 필요합니다.

`Protect-GuestConfigurationPackage` cmdlet의 매개 변수는 다음과 같습니다.

- **경로**: 게스트 구성 패키지의 전체 경로입니다.
- **PublicGpgKeyPath**: 공개 GPG 키 경로입니다. 이 매개 변수는 Linux용 콘텐츠에 서명하는 경우에만 지원됩니다.

Linux 컴퓨터에서 사용할 GPG 키를 만드는 방법에 대한 좋은 참고 자료는 GitHub의 문서, [ 새 GPG 키 만들기](https://help.github.com/en/articles/generating-a-new-gpg-key)에 나와 있습니다.

GuestConfiguration 에이전트는 Linux 컴퓨터의 `/usr/local/share/ca-certificates/extra` 경로에 인증서 공개 키가 있어야 합니다. 노드에서 서명된 콘텐츠를 확인하려면 사용자 지정 정책을 적용하기 전 컴퓨터에 인증서 공개 키를 설치합니다. 이 프로세스는 VM 내에서 또는 Azure Policy를 사용하여 수행할 수 있습니다. 예제 템플릿이 [여기에 나와](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-push-certificate-windows) 있습니다.
Key Vault 액세스 정책은 배포하는 동안 Compute 리소스 공급자가 인증서에 액세스할 수 있도록 허용해야 합니다. 자세한 단계는 [Azure Resource Manager에서 가상 머신에 대한 Key Vault 설정](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)을 참조하세요.

콘텐츠를 게시한 후에는 이름이 `GuestConfigPolicyCertificateValidation`이고 값이 `enabled`인 태그를 코드 서명이 필요한 모든 가상 머신에 추가합니다. Azure Policy를 사용하여 태그를 대규모로 제공할 수 있는 방법은 [태그 샘플](../samples/built-in-policies.md#tags)을 참조하세요. 이 태그가 준비되면 `New-GuestConfigurationPolicy` cmdlet을 이용하여 생성된 정책 정의에서 게스트 구성 확장을 통해 요구 사항을 사용합니다.

## <a name="next-steps"></a>다음 단계

- [게스트 구성](../concepts/guest-configuration.md)을 사용하여 VM을 감사하는 방법을 알아봅니다.
- [프로그래밍 방식으로 정책을 생성](./programmatically-create.md)하는 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](./get-compliance-data.md) 방법을 알아봅니다.
