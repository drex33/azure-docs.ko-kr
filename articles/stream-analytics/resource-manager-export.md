---
title: Azure Stream Analytics 작업 Azure Resource Manager 템플릿 내보내기
description: 이 문서에서는 Azure Stream Analytics 작업에 대한 Azure Resource Manager 템플릿을 내보내는 방법을 설명합니다.
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: c67d6895fe4dde54992c7a79067a45dcdff18305
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586422"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Azure Stream Analytics 작업 Azure Resource Manager 템플릿 내보내기

[Azure Resource Manager templates](../azure-resource-manager/templates/overview.md)은 코드로 인프라를 구현하도록 합니다. 해당 템플릿은 리소스에 대한 인프라 및 구성을 정의하는 JSON(JavaScript Object Notation) 파일입니다. 배포할 리소스와 해당 리소스의 속성을 지정합니다.

Azure Resource Manager 템플릿을 내보내서 Azure Stream Analytics 작업을 재배치할 수 있습니다.

## <a name="open-a-job-in-vs-code"></a>VS Code에서 작업을 시작합니다.

템플릿을 내보내려면 먼저 Visual Studio Code에서 기존 Stream Analytics 작업을 열어야 합니다. 

작업을 로컬 프로젝트로 내보내려면 Azure portal의 **Stream Analytics Explorer** 에서 내보낼 작업을 찾습니다. **쿼리** 페이지에서 **Visual Studio에서 열기** 를 선택합니다. 그런 다음 **Visual Studio Code** 를 선택합니다.

![Visual Studio Code에서 Stream Analytics 작업 시작](./media/resource-manager-export/open-job-vs-code.png)

Visual Studio Code를 사용하여 Stream Analytics 작업을 관리하는 방법에 대한 자세한 내용은 [Visual Studio Code 빠른 시작](quick-create-visual-studio-code.md)을 참조하세요.

## <a name="compile-the-script"></a>스크립트 컴파일 

다음 단계는 작업 스크립트를 Azure Resource Manager 템플릿으로 컴파일하는 것입니다. 스크립트를 컴파일하기 전에 작업에 하나 이상의 입력과 하나의 출력이 구성되어 있는지 확인합니다. 입력 또는 출력이 구성되지 않은 경우에는 입력 및 출력을 먼저 구성해야 합니다.

1. Visual Studio Code에서 작업의 *Transformation.asaql* 파일로 이동합니다.

   ![Visual Studio Code의 Transformation.asaql 파일](./media/resource-manager-export/transformation-asaql.png)

1. *Transformation.asaql* 파일을 마우스 오른쪽 단추로 클릭하고 메뉴에서 **ASA: Compile Script** 를 선택합니다.

1. Stream Analytics 작업 영역에 **배포** 폴더가 표시됨을 유의합니다.

1. 배포에 사용되는 Azure Resource Manager 템플릿인 *JobTemplate.json* 파일을 탐색합니다.

## <a name="complete-the-parameters-file"></a>매개 변수 파일을 완료합니다

다음으로, Azure Resource Manager 템플릿 매개 변수 파일을 완성합니다.

1. Visual Studio Code에서 Stream Analytics 작업 영역의 **배포** 폴더에 있는 *JobTemplate.parameters.json* 파일을 엽니다.

1. 입력 및 출력 키가 null임에 유의하세요. Null 값을 입력 및 출력 리소스에 대한 실제 액세스 키로 바꿉니다.

1. 매개 변수 파일을 저장합니다.

## <a name="deploy-using-templates"></a>사용 템플릿을 배포합니다

이전 섹션에서 생성한 Azure Resource Manager 템플릿을 사용하여 Azure Stream Analytics 작업을 배포할 준비가 되었습니다.

PowerShell 창에서 다음 명령을 실행합니다. *ResourceGroupName*, *TemplateFile* 및 *TemplateParameterFile* 을 실제 리소스 그룹 이름으로 바꾸고, 작업 영역의 **배포 폴더에** 있는 *JobTemplate.json* 및 *JobTemplate.parameters.json* 파일의 전체 파일 경로를 다시 지정해야 합니다.

Azure PowerShell 구성하지 않은 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps) 단계를 수행합니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code를 사용하여 라이브 입력에 대해 로컬로 Azure Stream Analytics 작업 테스트](visual-studio-code-local-run-live-input.md)

* [Visual Studio Code를 사용하여 Azure Stream Analytics 작업 탐색(미리 보기)](visual-studio-code-explore-jobs.md)
