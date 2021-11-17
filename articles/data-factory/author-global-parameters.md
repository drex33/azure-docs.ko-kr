---
title: 글로벌 매개 변수
description: 각 Azure Data Factory 환경에 대한 전역 매개 변수 설정
ms.service: data-factory
ms.subservice: authoring
ms.topic: conceptual
author: joshuha-msft
ms.author: joowen
ms.date: 05/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3205d5c3be7fe13cbaa4b881a3de6bffc15e9fd8
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550915"
---
# <a name="global-parameters-in-azure-data-factory"></a>Azure Data Factory의 전역 매개 변수

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

전역 매개 변수는 모든 식의 파이프라인에서 사용할 수 있는 데이터 팩터리 간의 상수입니다. 동일한 매개 변수 이름 및 값을 가진 파이프라인이 여러 개 있는 경우에 유용합니다. CI/CD(연속 통합 및 배포 프로세스)를 사용하여 데이터 팩터리를 승격하는 경우 각 환경에서 이러한 매개 변수를 재정의할 수 있습니다. 

## <a name="creating-global-parameters"></a>전역 매개 변수 만들기

전역 매개 변수를 만들려면 **관리** 섹션의 *전역 매개 변수* 탭으로 이동합니다. **새로 만들기** 를 선택하여 생성 측면 탐색을 엽니다.

:::image type="content" source="media/author-global-parameters/create-global-parameter-1.png" alt-text="전역 매개 변수를 만들기 위해 선택한 새로 만들기 단추를 강조 표시하는 스크린샷.":::

측면 탐색에서 이름을 입력하고 데이터 형식을 선택하며 매개 변수 값을 지정합니다.

:::image type="content" source="media/author-global-parameters/create-global-parameter-2.png" alt-text="새 전역 매개 변수의 이름, 데이터 형식 및 값을 추가하는 위치를 보여주는 스크린샷.":::

전역 매개 변수를 생성된 후에는 매개 변수의 이름을 클릭하여 해당 매개 변수를 편집할 수 있습니다. 한 번에 여러 매개 변수를 변경하려면 **모두 편집** 을 선택합니다.

:::image type="content" source="media/author-global-parameters/create-global-parameter-3.png" alt-text="전역 매개 변수 만들기":::

## <a name="using-global-parameters-in-a-pipeline"></a>파이프라인에서 전역 매개 변수 사용

전역 매개 변수는 모든 [파이프라인 식](control-flow-expression-language-functions.md)에서 사용할 수 있습니다. 파이프라인이 데이터 세트 또는 데이터 흐름과 같은 다른 리소스를 참조하는 경우 해당 리소스의 매개 변수를 통해 전역 매개 변수 값을 전달할 수 있습니다. 전역 매개 변수는 `pipeline().globalParameters.<parameterName>`으로 참조됩니다.

:::image type="content" source="media/author-global-parameters/expression-global-parameters.png" alt-text="전역 매개 변수 사용":::

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> CI/CD의 전역 매개 변수

연속 통합 및 배포 솔루션에는 두 가지 방법으로 전역 매개 변수를 통합할 수 있습니다.

* ARM 템플릿에 전역 매개 변수 포함
* PowerShell 스크립트를 통해 전역 매개 변수 배포

일반적인 사용 사례의 경우 ARM 템플릿에 전역 매개 변수를 포함하는 것이 좋습니다. 이는 [CI/CD 문서](continuous-integration-delivery.md)에 설명된 솔루션과 기본적으로 통합됩니다. 자동 게시 및 Purview 연결의 경우 **PowerShell 스크립트** 메서드가 필요합니다. 나중에 PowerShell 스크립트 메서드에 대해 자세히 알아볼 수 있습니다. 전역 매개 변수는 환경에 따라 자주 변경되므로 기본적으로 ARM 템플릿 매개 변수로 추가됩니다. **관리** 허브에서 ARM 템플릿에 전역 매개 변수를 포함하도록 설정할 수 있습니다.

:::image type="content" source="media/author-global-parameters/include-arm-template.png" alt-text="ARM 템플릿에 포함":::

> [!NOTE]
> **ARM 템플릿** 구성에 포함은 "Git 모드"에서만 사용할 수 있습니다. 현재 “라이브 모드” 또는 “Data Factory” 모드에서는 사용할 수 없습니다. 자동 게시 또는 Purview 연결의 경우 전역 매개 변수 포함 메서드를 사용하지 말고 PowerShell 스크립트 메서드를 사용하세요. 

> [!WARNING]
>매개 변수 이름에는 '-'를 사용할 수 없습니다. {"code":"BadRequest","message":"ErrorCode=InvalidTemplate,ErrorMessage=The expression >'pipeline().globalParameters.myparam-dbtest-url' is not valid: .....}" 오류 코드가 표시됩니다. 그러나 매개 변수 이름에 '_'를 사용할 수 있습니다. 

ARM 템플릿에 전역 매개 변수를 추가하면 다른 환경의 고객 관리형 키 또는 git 구성과 같은 다른 팩터리 수준 설정을 재정의하는 팩터리 수준 설정이 추가됩니다. UAT 또는 PROD와 같은 높은 수준의 환경에서 이러한 설정을 사용하도록 설정한 경우 아래 강조 표시된 단계에서 PowerShell 스크립트를 통해 전역 매개 변수를 배포하는 것이 좋습니다. 


### <a name="deploying-using-powershell"></a>PowerShell을 사용하여 배포

다음 단계에서는 PowerShell을 통해 전역 매개 변수를 배포하는 방법을 간략하게 설명합니다. 이는 대상 팩터리에 고객 관리형 키와 같은 팩터리 수준 설정이 있는 경우에 유용합니다.

팩터리를 게시하거나 전역 매개 변수를 사용하여 ARM 템플릿을 내보낼 때 *your-factory-name_GlobalParameters.json* 이라는 파일과 함께 *globalParameters* 라는 폴더가 생성됩니다. 이 파일은 게시된 팩터리의 각 전역 매개 변수 형식 및 값을 포함하는 JSON 개체입니다.

:::image type="content" source="media/author-global-parameters/global-parameters-adf-publish.png" alt-text="전역 매개 변수 게시":::

TEST 또는 PROD와 같은 새 환경에 배포하는 경우 이 전역 매개 변수 파일의 복사본을 만들고 적절한 환경별 값을 덮어쓰는 것이 좋습니다. 원래 전역 매개 변수 파일을 다시 게시하면 파일은 덮어쓰므로 다른 환경의 복사본은 그대로 유지됩니다.

예를 들어 'ADF-DEV'라는 팩터리가 있고 값이 'dev'인 'environment'라는 문자열 형식의 전역 매개 변수를 사용하는 경우 *ADF-DEV_GlobalParameters.json* 이라는 파일을 게시하면 생성됩니다. 'ADF_TEST'라는 테스트 팩터리에 배포하는 경우 JSON 파일의 복사본(예: ADF-TEST_GlobalParameters.json이라고 함)을 만들고 매개 변수 값을 환경별 값으로 바꿉니다. 매개 변수 'environment'는 이제 'test' 값을 가질 수 있습니다. 

:::image type="content" source="media/author-global-parameters/powershell-task.png" alt-text="전역 매개 변수 배포":::

아래 PowerShell 스크립트를 사용하여 전역 매개 변수를 추가 환경으로 승격합니다. ARM 템플릿 배포 전에 Azure PowerShell DevOps 작업을 추가합니다. DevOps 작업에서 새 매개 변수 파일, 대상 리소스 그룹 및 대상 데이터 팩터리의 위치를 지정해야 합니다.

> [!NOTE]
> PowerShell을 사용하여 전역 매개 변수를 배포하려면 Az 모듈 버전 4.4.0 이상을 사용해야 합니다.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>다음 단계

* Azure Data Factory의 [연속 통합 및 배포 프로세스](continuous-integration-delivery-improvements.md)에 대해 알아보기
* [제어 흐름 식 언어](control-flow-expression-language-functions.md)를 사용하는 방법 알아보기
