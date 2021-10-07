---
title: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Data Factory 만들기
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다.
ms.service: data-factory
ms.subservice: tutorials
tags: azure-resource-manager
author: ssabat
ms.author: susabat
ms.reviewer: jburchel, jingwang
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 07/05/2021
ms.openlocfilehash: 9a567c5b61449cdfd72fac1a946760bccce2bebe
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124769715"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure Data Factory 만들기

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [현재 버전](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure 데이터 팩터리를 만드는 방법을 보여 줍니다. 이 데이터 팩터리에서 만든 파이프라인은 Azure Blob Storage의 한 폴더에서 다른 폴더로 데이터를 **복사합니다**. Azure Data Factory를 사용하여 데이터를 **변환** 하는 방법에 대한 자습서는 [자습서: Spark를 사용하여 데이터 변환](transform-data-using-spark.md)을 참조하세요.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> 이 문서는 Data Factory 서비스의 자세한 소개를 제공하지 않습니다. Azure Data Factory 서비스 소개는 [Azure Data Factory 소개](introduction.md)를 참조하세요.

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[:::image type="content" source="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.datafactory%2Fdata-factory-v2-blob-to-blob-copy%2Fazuredeploy.json" alt-text="Azure에 배포](../media/template-deployments/deploy-to-azure.svg)":::

## <a name="prerequisites"></a>필수 구성 요소

### <a name="azure-subscription"></a>Azure 구독

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

### <a name="create-a-file"></a>파일 만들기

**메모장** 과 같은 텍스트 편집기를 열고 다음 내용을 사용하여 **emp.txt** 라는 파일을 만듭니다.

```emp.txt
John, Doe
Jane, Doe
```

**C:\ADFv2QuickStartPSH** 폴더에 이 파일을 저장합니다. 이 폴더가 아직 없으면 만듭니다.

## <a name="review-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.datafactory/data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

템플릿에 Azure 리소스가 정의되어 있습니다.

- [Microsoft.Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts): 스토리지 계정을 정의합니다.
- [Microsoft.DataFactory/factories](/azure/templates/microsoft.datafactory/factories): Azure Data Factory를 만듭니다.
- [Microsoft.DataFactory/factories/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices): Azure Data Factory 연결된 서비스를 만듭니다.
- [Microsoft.DataFactory/factories/datasets](/azure/templates/microsoft.datafactory/factories/datasets): Azure Data Factory 데이터 세트를 만듭니다.
- [Microsoft.DataFactory/factories/pipelines](/azure/templates/microsoft.datafactory/factories/pipelines): Azure Data Factory 파이프라인을 만듭니다.

더 많은 Azure Data Factory 템플릿 샘플은 [빠른 시작 템플릿 갤러리](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)에서 찾을 수 있습니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿은 Azure Data Factory 계정, 스토리지 계정 및 Blob 컨테이너를 만듭니다.

    [:::image type="content" source="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.datafactory%2Fdata-factory-v2-blob-to-blob-copy%2Fazuredeploy.json" alt-text="Azure에 배포](../media/template-deployments/deploy-to-azure.svg)":::

2. 다음 값을 선택하거나 입력합니다.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="ADF ARM 템플릿 배포":::

    지정되지 않은 경우 기본값을 사용하여 Azure Data Factory 리소스를 만듭니다.

    - **구독**: Azure 구독을 선택합니다.
    - **리소스 그룹**: **새로 만들기** 를 선택하고 리소스 그룹의 고유한 이름을 입력한 다음, **확인** 을 선택합니다.
    - **지역**: 위치를 선택합니다.  예: *미국 동부*
    - **Data Factory 이름**: 기본값을 사용합니다.
    - **위치**: 기본값을 사용합니다.
    - **스토리지 계정 이름**: 기본값을 사용합니다.
    - **Blob 컨테이너**: 기본값을 사용합니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

1. **리소스 그룹으로 이동** 을 선택합니다.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="리소스 그룹":::

2.  Azure Data Factory가 생성되었는지 확인합니다.
    1. Azure Data Factory 이름은 - datafactory\<uniqueid\>형식입니다.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="샘플 Data Factory":::

2. 스토리지 계정이 생성되었는지 확인합니다.
    1. 스토리지 계정 이름은 - 스토리지\<uniqueid\> 형식입니다.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="Storage 계정":::

3. 생성한 스토리지 계정을 선택한 다음, **컨테이너** 를 선택합니다.
    1. **컨테이너** 페이지에서 원하는 Blob 컨테이너를 선택합니다.
        1. Blob 컨테이너 이름은 - Blob\<uniqueid\>형식입니다.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="Blob 컨테이너":::

### <a name="upload-a-file"></a>파일 업로드

1. **컨테이너** 페이지에서 **업로드** 를 선택합니다.

2. 오른쪽 창에서 **파일** 상자를 선택한 다음, 이전에 만든 **emp.txt** 파일을 찾아 선택합니다.

3. **고급** 제목을 펼칩니다.

4. **폴더에 업로드** 상자에서 *입력* 을 입력합니다.

5. **업데이트** 단추를 선택합니다. 목록에서 **emp.txt** 파일 및 업로드 상태를 참조하세요.

6. **닫기** 아이콘(**X**)을 선택하여 **Blob 업로드** 페이지를 닫습니다.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="입력 폴더에 파일 업로드":::

이 빠른 시작의 끝에서 출력을 확인하는 데 사용할 수 있으므로 컨테이너 페이지를 열어 둡니다.

### <a name="start-trigger"></a>트리거 시작

1. **데이터 팩터리** 페이지로 이동하여 이전에 만든 데이터 팩터리를 선택합니다.

2. **Azure Data Factory Studio 열기** 타일에서 **열기** 를 선택합니다.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-open-tile.png" alt-text="작성자 및 모니터":::

2. **작성자** 탭 :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false":::을 선택합니다.

3. 만든 파이프라인 - ArmtemplateSampleCopyPipeline을 선택합니다.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="ARM 템플릿 파이프라인":::

4. **트리거 추가** > **지금 트리거** 를 선택합니다.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="트리거":::

5. 오른쪽 창의 **파이프라인 실행** 에서 **확인** 을 선택합니다.

### <a name="monitor-the-pipeline"></a>파이프라인 모니터링

1. **모니터** 탭 :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false":::을 선택합니다.

2. 파이프라인 실행과 연결된 작업 실행이 표시됩니다. 이 빠른 시작의 파이프라인에는 복사 유형의 작업 하나밖에 없습니다. 따라서 해당 작업에 대한 실행이 표시됩니다.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="실행 성공":::

### <a name="verify-the-output-file"></a>출력 파일 확인

파이프라인은 자동으로 Blob 컨테이너에서 출력 폴더를 만듭니다. 그런 다음 입력 폴더에서 출력 폴더로 emp.txt 파일을 복사합니다.

1. Azure Portal의 **컨테이너** 페이지에서 **새로 고침** 을 선택하여 출력 폴더를 확인합니다.

2. 폴더 목록에서 **출력** 을 선택합니다.

3. **emp.txt** 가 출력 폴더에 복사되었는지 확인합니다.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="출력":::

## <a name="clean-up-resources"></a>리소스 정리

빠른 시작에서 만든 리소스는 두 가지 방법으로 정리할 수 있습니다. 리소스 그룹의 모든 리소스를 포함하고 있는 [Azure 리소스 그룹을 삭제](../azure-resource-manager/management/delete-resource-group.md)할 수 있습니다. 다른 리소스를 그대로 유지하려면 이 자습서에서 만든 데이터 팩터리만 삭제합니다.

리소스 그룹을 삭제하면 그 안에 포함된 데이터 팩터리를 포함한 모든 리소스가 삭제됩니다. 다음 명령을 실행하여 전체 리소스 그룹을 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

전체 리소스 그룹이 아니라 데이터 팩터리만 삭제하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 사용하여 Azure Data Factory를 만들고 배포의 유효성을 검사했습니다. Azure Data Factory 및 Azure Resource Manager에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Data Factory 설명서](index.yml)
- [Azure Resource Manager](../azure-resource-manager/management/overview.md)에 대해 자세히 알아보기
- 다른 [Azure Data Factory ARM 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular) 가져오기
