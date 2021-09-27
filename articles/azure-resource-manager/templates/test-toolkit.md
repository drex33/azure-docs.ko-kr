---
title: ARM 템플릿 테스트 도구 키트
description: 템플릿에서 Azure Resource Manager 템플릿(ARM 템플릿) 테스트 도구 키트를 실행하는 방법을 설명합니다. 도구 키트를 사용하여 권장 사례를 구현했는지 확인할 수 있습니다.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c02d7183304ec3474427bf1687d67d7dd7058d44
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576659"
---
# <a name="use-arm-template-test-toolkit"></a>ARM 템플릿 테스트 도구 키트 사용

[Azure Resource Manager 템플릿(ARM 템플릿) 테스트 도구 키트](https://aka.ms/arm-ttk)는 템플릿에서 권장 사례를 사용하는지 여부를 확인합니다. 템플릿이 권장 사례를 준수하지 않는 경우 제안된 변경 내용과 함께 경고 목록을 반환합니다. 테스트 도구 키트를 사용하여 템플릿 개발에서 일반적인 문제를 방지하는 방법을 배울 수 있습니다. 이 문서에서는 테스트 도구 키트를 실행하는 방법 및 테스트를 추가 또는 제거하는 방법을 설명합니다. 테스트를 실행하는 방법 또는 특정 테스트를 실행하는 방법에 대한 자세한 내용은 [테스트 매개 변수](#test-parameters)를 참조하세요.

도구 키트는 PowerShell 또는 CLI의 명령에서 실행할 수 있는 PowerShell 스크립트 집합입니다. 이러한 테스트는 권장 사항이며, 요구 사항이 아닙니다. 목표와 관련된 테스트를 결정하고 실행되는 테스트를 사용자 지정할 수 있습니다.

이 도구 키트에는 네 가지 테스트 집합이 포함되어 있습니다.

- [ARM 템플릿에 대한 테스트 사례](template-test-cases.md)
- [매개 변수 파일에 대한 테스트 사례](parameter-file-test-cases.md)
- [createUiDefinition.json에 대한 테스트 사례](createUiDefinition-test-cases.md)
- [모든 파일에 대한 테스트 사례](all-files-test-cases.md)

### <a name="microsoft-learn"></a>Microsoft Learn

ARM 템플릿 테스트 도구 키트에 대한 자세한 내용과 실습 지침은 **Microsoft Learn** [ARM 템플릿 테스트 Toolkit 사용하여 Azure 리소스 유효성 검사를](/learn/modules/arm-template-test) 참조하세요.

## <a name="install-on-windows"></a>Windows에 설치

1. PowerShell이 아직 없는 경우 [Windows에 PowerShell을 설치](/powershell/scripting/install/installing-powershell-core-on-windows)합니다.

1. 테스트 도구 키트에 대한 [최신 .zip 파일을 다운로드](https://aka.ms/arm-ttk-latest)하고 압축을 풉니다.

1. PowerShell을 시작합니다.

1. 테스트 도구 키트를 추출한 폴더로 이동합니다. 해당 폴더 내에서 _arm-ttk_ 폴더로 이동합니다.

1. [실행 정책](/powershell/module/microsoft.powershell.core/about/about_execution_policies)이 인터넷에서 스크립트를 차단하는 경우 스크립트 파일의 차단을 해제해야 합니다. _arm-ttk_ 폴더에 있는지 확인하십시오.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. 모듈을 가져옵니다.

   ```powershell
   Import-Module .\arm-ttk.psd1
   ```

1. 테스트를 실행하려면 다음 명령을 사용합니다.

   ```powershell
   Test-AzTemplate -TemplatePath \path\to\template
   ```

## <a name="install-on-linux"></a>Linux에 설치

1. PowerShell이 아직 없는 경우 [Linux에 PowerShell을 설치](/powershell/scripting/install/installing-powershell-core-on-linux)합니다.

1. 테스트 도구 키트에 대한 [최신 .zip 파일을 다운로드](https://aka.ms/arm-ttk-latest)하고 압축을 풉니다.

1. PowerShell을 시작합니다.

   ```bash
   pwsh
   ```

1. 테스트 도구 키트를 추출한 폴더로 이동합니다. 해당 폴더 내에서 _arm-ttk_ 폴더로 이동합니다.

1. [실행 정책](/powershell/module/microsoft.powershell.core/about/about_execution_policies)이 인터넷에서 스크립트를 차단하는 경우 스크립트 파일의 차단을 해제해야 합니다. _arm-ttk_ 폴더에 있는지 확인하십시오.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. 모듈을 가져옵니다.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. 테스트를 실행하려면 다음 명령을 사용합니다.

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="install-on-macos"></a>macOS에 설치

1. PowerShell이 아직 없는 경우 [macOS에 PowerShell을 설치](/powershell/scripting/install/installing-powershell-core-on-macos)합니다.

1. `coreutils` 설치:

   ```bash
   brew install coreutils
   ```

1. 테스트 도구 키트에 대한 [최신 .zip 파일을 다운로드](https://aka.ms/arm-ttk-latest)하고 압축을 풉니다.

1. PowerShell을 시작합니다.

   ```bash
   pwsh
   ```

1. 테스트 도구 키트를 추출한 폴더로 이동합니다. 해당 폴더 내에서 _arm-ttk_ 폴더로 이동합니다.

1. [실행 정책](/powershell/module/microsoft.powershell.core/about/about_execution_policies)이 인터넷에서 스크립트를 차단하는 경우 스크립트 파일의 차단을 해제해야 합니다. _arm-ttk_ 폴더에 있는지 확인하십시오.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. 모듈을 가져옵니다.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. 테스트를 실행하려면 다음 명령을 사용합니다.

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="result-format"></a>결과 형식

통과한 테스트는 **녹색** 으로 표시되고 앞에 `[+]` 표시가 붙습니다.

실패한 테스트는 **빨간색** 으로 표시되고 앞에 `[-]` 표시가 붙습니다.

경고가 있는 테스트는 **노란색** 으로 표시되고 앞에 `[?]`가 붙습니다.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="테스트 결과를 봅니다.":::

텍스트 결과는 다음과 같습니다.

```powershell
deploymentTemplate
[+] adminUsername Should Not Be A Literal (6 ms)
[+] apiVersions Should Be Recent In Reference Functions (9 ms)
[-] apiVersions Should Be Recent (6 ms)
    Api versions must be the latest or under 2 years old (730 days) - API version 2019-06-01 of
    Microsoft.Storage/storageAccounts is 760 days old
    Valid Api Versions:
    2021-04-01
    2021-02-01
    2021-01-01
    2020-08-01-preview

[+] artifacts parameter (4 ms)
[+] CommandToExecute Must Use ProtectedSettings For Secrets (9 ms)
[+] DependsOn Best Practices (5 ms)
[+] Deployment Resources Must Not Be Debug (6 ms)
[+] DeploymentTemplate Must Not Contain Hardcoded Uri (4 ms)
[?] DeploymentTemplate Schema Is Correct (6 ms)
    Template is using schema version '2015-01-01' which has been deprecated and is no longer
    maintained.
```

## <a name="test-parameters"></a>테스트 매개 변수

`-TemplatePath` 매개 변수를 제공하는 경우 도구 키트는 해당 폴더에서 _azuredeploy.json_ 또는 _maintemplate.json_ 이라는 이름의 템플릿을 찾습니다. 이 템플릿을 먼저 테스트한 다음 폴더와 해당 하위 폴더에 있는 다른 모든 템플릿을 테스트합니다. 다른 템플릿은 연결된 템플릿으로 테스트됩니다. 경로에 [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md)이라는 파일이 포함된 경우 UI 정의와 관련된 테스트를 실행합니다. 매개 변수 파일과 폴더의 모든 JSON 파일에 대해서도 테스트가 실행됩니다.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

해당 폴더의 파일 하나를 테스트하려면 `-File` 매개 변수를 추가합니다. 그러나 폴더에는 여전히 _azuredeploy.json_ 또는 _maintemplate.json_ 이라는 주 템플릿이 있어야 합니다.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

기본값으로 모든 테스트가 실행됩니다. 실행할 개별 테스트를 지정하려면 `-Test` 매개 변수를 사용하고 테스트 이름을 제공합니다. 테스트 이름은 [ARM 템플릿](template-test-cases.md), [매개 변수 파일](parameter-file-test-cases.md), [createUiDefinition.json](createUiDefinition-test-cases.md) 및 [모든 파일](all-files-test-cases.md)을 참조하세요.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>테스트 사용자 지정

기본 테스트를 사용자 지정하거나 고유한 테스트를 작성할 수 있습니다. 테스트를 영구적으로 제거하려면 폴더에서 _.test.ps1_ 파일을 삭제합니다.

이 도구 키트에는 특정 파일 형식에 대해 실행되는 기본 테스트가 포함된 4개의 폴더가 있습니다.

- ARM 템플릿: _\arm-ttk\testcases\deploymentTemplate_
- 매개 변수 파일: _\arm-ttk\testcases\deploymentParameters_
- [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md): _\arm-ttk\testcases\CreateUIDefinition_
- 모든 파일: _\arm-ttk\testcases\AllFiles_

### <a name="add-a-custom-test"></a>사용자 지정 테스트 추가

사용자 고유의 테스트를 추가하려면 _Your-Custom-Test-Name.test.ps1_ 명명 규칙을 사용하여 파일을 만듭니다.

테스트에서 템플릿을 개체 매개 변수 또는 문자열 매개 변수로 가져올 수 있습니다. 일반적으로는 둘 중 하나를 사용하지만 둘 다 사용할 수도 있습니다.

템플릿의 섹션을 가져와 해당 속성을 반복해야 하는 경우 개체 매개 변수를 사용합니다. 개체 매개 변수를 사용하는 테스트의 형식은 다음과 같습니다.

```powershell
param(
  [Parameter(Mandatory=$true,Position=0)]
  [PSObject]
  $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

템플릿 개체의 속성은 다음과 같습니다.

- `$schema`
- `contentVersion`
- `parameters`
- `variables`
- `resources`
- `outputs`

예를 들어 `$TemplateObject.parameters`를 사용하여 매개 변수 컬렉션을 가져올 수 있습니다.

전체 템플릿에서 문자열 작업을 수행해야 하는 경우 문자열 매개 변수를 사용합니다. 문자열 매개 변수를 사용하는 테스트의 형식은 다음과 같습니다.

```powershell
param(
  [Parameter(Mandatory)]
  [string]
  $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

예를 들어 문자열 매개 변수의 정규식을 실행하여 특정 구문이 사용되는지 확인할 수 있습니다.

테스트 구현에 대한 자세한 내용을 알아보려면 해당 폴더의 다른 테스트를 살펴보세요.

## <a name="integrate-with-azure-pipelines"></a>Azure 파이프라인과 통합

Azure 파이프라인에 테스트 도구 키트를 추가할 수 있습니다. 파이프라인을 사용하면 템플릿이 업데이트 될 때마다 테스트를 실행하거나 배포 프로세스의 일부로 실행할 수 있습니다.

테스트 도구 키트를 파이프라인에 추가하는 가장 쉬운 방법은 타사 확장을 사용하는 것입니다. 사용할 수 있는 두 가지 확장은 다음과 같습니다.

- [ARM TTK 테스트 실행](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
- [ARM 템플릿 테스터](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

또는 사용자 공유의 작업을 구현할 수 있습니다. 다음 예제에서 테스트 도구 키트를 다운로드하는 방법을 보여 줍니다.

```json
{
  "environment": {},
  "enabled": true,
  "continueOnError": false,
  "alwaysRun": false,
  "displayName": "Download TTK",
  "timeoutInMinutes": 0,
  "condition": "succeeded()",
  "task": {
    "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
    "versionSpec": "2.*",
    "definitionType": "task"
  },
  "inputs": {
    "targetType": "inline",
    "filePath": "",
    "arguments": "",
    "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
    "errorActionPreference": "stop",
    "failOnStderr": "false",
    "ignoreLASTEXITCODE": "false",
    "pwsh": "true",
    "workingDirectory": ""
  }
}
```

다음 예제는 테스트를 실행하는 방법을 보여줍니다.

```json
{
  "environment": {},
  "enabled": true,
  "continueOnError": true,
  "alwaysRun": false,
  "displayName": "Run Best Practices Tests",
  "timeoutInMinutes": 0,
  "condition": "succeeded()",
  "task": {
    "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
    "versionSpec": "2.*",
    "definitionType": "task"
  },
  "inputs": {
    "targetType": "inline",
    "filePath": "",
    "arguments": "",
    "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
    "errorActionPreference": "continue",
    "failOnStderr": "true",
    "ignoreLASTEXITCODE": "false",
    "pwsh": "true",
    "workingDirectory": ""
  }
}
```

## <a name="next-steps"></a>다음 단계

- 템플릿 테스트에 대한 자세한 내용은 [ARM 템플릿에 대한 테스트 사례](template-test-cases.md)를 참조하세요.
- 매개 변수 파일을 테스트하려면 [매개 변수 파일에 대한 테스트 사례](parameters.md)를 참조하세요.
- createUiDefinition 테스트는 [createUiDefinition.json에 대한 테스트 사례](createUiDefinition-test-cases.md)를 참조하세요.
- 모든 파일에 대한 테스트에 대해 알아보려면 [모든 파일에 대한 테스트 사례](all-files-test-cases.md)를 참조하세요.
- 테스트 도구 키트를 사용하는 Microsoft Learn 모듈은 [ARM 템플릿 테스트 도구 키트를 사용하여 Azure 리소스 유효성 검사](/learn/modules/arm-template-test/)를 참조하세요.
