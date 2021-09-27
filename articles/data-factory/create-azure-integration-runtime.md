---
title: 새 Azure Integration Runtime 만들기
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 데이터를 복사하고 변환 작업을 디스패치하는 Azure 통합 런타임을 만드는 방법에 대해 알아봅니다.
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.date: 09/09/2021
author: lrtoyou1223
ms.author: lle
ms.custom: devx-track-azurepowershell, synapse
ms.openlocfilehash: 063d4138a86fb5235562ebb0d2592640f370806e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760330"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Azure 통합 런타임을 만들고 구성하는 방법
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

IR(통합 런타임)은 서로 다른 네트워크 환경에서 데이터 통합 기능을 제공하기 위해 Azure Data Factory와 Synapse 파이프라인에서 사용하는 컴퓨팅 인프라입니다. IR에 대한 자세한 내용은 [통합 런타임](concepts-integration-runtime.md)을 참조하세요.

Azure IR은 완전히 관리되는 컴퓨팅을 제공하여 기본적으로 데이터 이동을 수행하고 HDInsight와 같은 컴퓨팅 서비스로 데이터 변환 작업을 디스패치합니다. Azure 환경에서 호스트되며 액세스 가능한 공용 엔드포인트를 통해 공용 네트워크 환경의 리소스에 연결할 수 있습니다.

이 문서에서는 Azure 통합 런타임을 만들고 구성할 수 있는 방법을 소개합니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>기본 Azure IR
기본적으로 각 데이터 팩터리 또는 Synapse 작업 영역에는 백 엔드에 클라우드 데이터 저장소의 작업 및 공용 네트워크의 컴퓨팅 서비스를 지원하는 Azure IR이 있습니다. 해당 Azure IR 위치는 자동 해결됩니다. **connectVia** 속성이 연결된 서비스 정의에 지정되지 않은 경우 기본 Azure IR이 사용됩니다. IR의 위치를 명시적으로 정의하려는 경우 또는 관리 목적으로 다른 IR에 대한 작업 실행을 가상으로 그룹화하려는 경우에만 Azure IR을 명시적으로 만들어야 합니다. 

## <a name="create-azure-ir"></a>Azure IR 만들기

Azure IR을 만들고 설정하려면 다음 절차를 사용합니다.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Azure PowerShell을 통해 Azure IR 만들기
통합 런타임은 **Set-AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet을 사용하여 만들 수 있습니다. Azure IR을 만들려면 명령에 이름, 위치 및 형식을 지정합니다. 다음은 위치가 "West Europe"으로 설정된 Azure IR을 만드는 샘플 명령입니다.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Azure IR의 경우 형식은 **Managed** 로 설정되어야 합니다. 컴퓨팅 세부 정보는 클라우드에서 탄력적으로 완전히 관리되므로 지정할 필요가 없습니다. Azure-SSIS IR을 만들려는 경우 노드 크기 및 노드 개수와 같은 컴퓨팅 세부 정보를 지정합니다. 자세한 내용은 [Azure SSIS IR 만들기 및 구성](create-azure-ssis-integration-runtime.md)을 참조하세요.

기존 Azure IR을 Set-AzDataFactoryV2IntegrationRuntime PowerShell cmdlet을 사용하여 해당 위치를 변경하도록 구성할 수 있습니다. Azure IR의 위치에 대한 자세한 내용은 [통합 런타임 소개](concepts-integration-runtime.md)를 참조하세요.

### <a name="create-an-azure-ir-via-ui"></a>UI를 통해 Azure IR 만들기
UI를 사용하여 애플리케이션을 만들려면 다음 단계를 수행합니다.

1. 해당 서비스 홈페이지의 맨 왼쪽 창에 있는 [관리 탭](./author-management-hub.md)을 선택합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
    
    :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="홈페이지 관리 단추":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/get-started-page-manage-button-synapse.png" alt-text="홈 페이지 관리 단추":::

2. 왼쪽 창에서 **통합 런타임** 을 선택한 다음, **+새로 만들기** 를 선택합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/manage-new-integration-runtime.png" alt-text="왼쪽 창과 + 새로 만들기 단추에서 통합 런타임을 강조 표시하는 스크린샷입니다.":::
   
    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/manage-new-integration-runtime-synapse.png" alt-text="왼쪽 창과 + 새로 만들기 단추에서 통합 런타임을 강조 표시하는 스크린샷입니다.":::

3. **통합 런타입 설치** 페이지에서 **Azure, 자체 호스트** 를 차례로 선택하고 **계속** 을 선택합니다. 

1. 다음 페이지에서 **Azure** 를 선택하여 Azure IR을 만든 후 **계속** 을 선택합니다.
   :::image type="content" source="media/create-azure-integration-runtime/new-azure-integration-runtime.png" alt-text="Integration Runtime 만들기":::

1. Azure IR의 이름을 입력하고 **만들기** 를 선택합니다.
   :::image type="content" source="media/create-azure-integration-runtime/create-azure-integration-runtime.png" alt-text="Azure IR 만들기":::

1. 생성가 완료되면 팝업 알림이 표시됩니다. **통합 런타임** 페이지에서 새로 생성된 IR이 목록에 표시되는지 확인합니다.

## <a name="use-azure-ir"></a>Azure IR 사용

Azure IR을 만든 후 연결된 서비스 정의에서 참조할 수 있습니다. 다음은 Azure Storage 연결된 서비스에서 이전에 만든 Azure 통합 런타임을 참조할 수 있는 방법에 대한 샘플입니다.

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>다음 단계
다른 형식의 통합 런타임을 만드는 방법은 다음 문서를 참조하세요.

- [자체 호스팅 통합 런타임 만들기](create-self-hosted-integration-runtime.md)
- [Azure SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md)
