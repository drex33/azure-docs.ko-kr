---
title: 중첩된 템플릿 환경 배포
description: 중첩된 Azure Resource Manager 템플릿을 배포하여 Azure DevTest Labs를 사용하는 환경을 제공하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 03738697f35df16fb2915fd7ece21a77b205eca1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278325"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>테스트 환경을 위한 중첩된 Azure Resource Manager 템플릿 배포
중첩된 배포를 사용하면 기본 Resource Manager 템플릿 내에서 다른 Azure Resource Manager 템플릿을 실행할 수 있습니다. 배포를 대상 템플릿 및 용도별 템플릿 집합으로 분해할 수 있습니다. 중첩 배포는 테스트, 재사용 및 가독성 이점을 제공합니다. [Azure 리소스를 배포할 때 연결된 템플릿 사용](../azure-resource-manager/templates/linked-templates.md) 문서에서는 몇 가지 코드 샘플을 사용하여 이 솔루션에 대한 유용한 개요를 제공합니다. 이 문서에서는 Azure DevTest Labs와 관련된 예제를 제공합니다. 

## <a name="key-parameters"></a>키 매개 변수
사용자 고유의 Resource Manager 템플릿을 처음부터 만들 수 있지만 Visual Studio [Azure 리소스 그룹 프로젝트를](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) 사용하는 것이 좋습니다. 이 프로젝트를 사용하면 템플릿을 쉽게 개발하고 디버그할 수 있습니다. 중첩된 배포 리소스를 azuredeploy.json에 추가하면 Visual Studio에서는 템플릿의 유연성을 높이는 여러 항목을 추가합니다. 이러한 항목은 다음과 같습니다.

- 보조 템플릿 및 매개 변수 파일이 있는 하위 폴더
- 기본 템플릿 파일 내의 변수 이름
- 새 파일의 스토리지 위치에 대한 두 개의 매개 변수입니다. **_artifactsLocation** 및 **_artifactsLocationSasToken** 은 DevTest Labs에서 사용하는 키 매개 변수입니다. 

DevTest Labs가 환경에서 작동하는 방식에 익숙하지 않은 경우 Azure Resource Manager 템플릿을 사용하여 [다중 VM 환경 및 PaaS 리소스 만들기를 참조하세요.](devtest-lab-create-environment-from-arm.md) 템플릿은 DevTest Labs의 랩에 연결된 리포지토리에 저장됩니다. 이러한 템플릿을 사용하여 새 환경을 만들면 파일이 랩의 Azure Storage 컨테이너로 이동합니다. 중첩된 파일을 찾아서 복사하기 위해 DevTest Labs는 _artifactsLocation 및 _artifactsLocationSasToken 매개 변수를 식별하고 하위 폴더를 스토리지 컨테이너까지 복사합니다. 그런 다음, DevTest Labs는 위치 및 SaS(공유 액세스 서명) 토큰을 매개 변수에 자동으로 삽입합니다. 

## <a name="nested-deployment-example"></a>중첩된 배포 예제
다음은 중첩 배포의 간단한 예입니다.

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

이 템플릿이 포함된 리포지토리의 폴더에는 **NestOne.json** 및 **NestOne.parameters.json** 파일이 포함된 `nestedtemplates` 하위 폴더가 있습니다. **azuredeploy.json** 에서 템플릿 URI는 아티팩트 위치, 중첩된 템플릿 폴더, 중첩된 템플릿 파일 이름을 사용하여 빌드됩니다. 마찬가지로 매개 변수 URI는 아티팩트 위치, 중첩된 템플릿 폴더 및 중첩된 템플릿의 매개 변수 파일을 사용하여 빌드됩니다. 

Visual Studio 동일한 프로젝트 구조의 이미지는 다음과 같습니다. 

![Visual Studio 프로젝트 구조의 스크린샷.](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

기본 폴더에 더 많은 폴더를 추가할 수 있지만 단일 수준보다 더 깊지는 않습니다. 

## <a name="next-steps"></a>다음 단계
환경에 관한 자세한 내용은 다음 문서를 참조하세요. 

- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
- [Azure DevTest Labs에서 랩의 가상 네트워크에 환경 연결](connect-environment-lab-virtual-network.md)