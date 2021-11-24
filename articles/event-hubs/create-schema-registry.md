---
title: Azure Event Hubs 스키마 레지스트리 만들기
description: 이 문서에서는 Azure Event Hubs 네임스페이스에서 스키마 레지스트리를 만드는 방법을 보여 줍니다.
ms.topic: quickstart
ms.date: 06/01/2021
ms.custom: references_regions, ignite-fall-2021, mode-other
ms.openlocfilehash: 38c769fa2c9c59bae0c3019250b1bc9897be816c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133071965"
---
# <a name="create-an-azure-event-hubs-schema-registry"></a>Azure Event Hubs 스키마 레지스트리 만들기
이 문서에서는 Azure Event Hubs에서 호스트하는 스키마 레지스트리에서 스키마를 사용하여 스키마 그룹을 만드는 방법을 보여 줍니다. Azure Event Hubs의 스키마 레지스트리 기능에 대한 개요는 [Event Hubs의 Azure 스키마 레지스트리](schema-registry-overview.md)를 참조하세요.

> [!NOTE]
> - **기본** 계층에서는 이 기능을 사용할 수 없습니다.
> - 이벤트 허브가 **가상 네트워크** 에 있는 경우 동일한 가상 네트워크의 VM에서 포털에 액세스하지 않는 한, Azure Portal에서 스키마를 만들 수 없습니다. 

## <a name="prerequisites"></a>사전 요구 사항
[Event Hubs 네임스페이스 만들기](event-hubs-create.md#create-an-event-hubs-namespace) 기존 네임스페이스를 사용할 수도 있습니다. 

## <a name="create-a-schema-group"></a>스키마 그룹 만들기
1. **Event Hubs 네임스페이스** 페이지로 이동합니다. 
1. 왼쪽 메뉴에서 **스키마 레지스트리** 를 선택합니다. 스키마 그룹을 만들려면 도구 모음에서 **+ 스키마 그룹** 을 선택합니다. 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="Azure Portal의 스키마 레지스트리 페이지를 보여 주는 이미지":::
1. **스키마 그룹 만들기** 페이지에서 다음 단계를 수행합니다.
    1. 스키마 그룹의 **이름** 을 입력합니다.
    1. **Serialization 형식** 에서 스키마 그룹의 모든 스키마에 적용되는 serialization 형식을 선택합니다. 현재는 **Avro** 형식만 지원되므로 **Avro** 를 선택합니다. 
    1. 그룹의 모든 스키마에 대해 **호환성 모드** 를 선택합니다. **Avro** 의 경우 향후 및 이전 버전과의 호환성 모드가 지원됩니다. 
    1. 그런 다음, **만들기** 를 선택하여 스키마 그룹을 만듭니다. 
    
        :::image type="content" source="./media/create-schema-registry/create-schema-group-page.png" alt-text="스키마 그룹을 만들기 위한 페이지를 보여 주는 이미지":::
1. 스키마 그룹 목록에서 **스키마 그룹** 의 이름을 선택합니다.

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="선택한 목록의 스키마 그룹을 보여 주는 이미지":::    
1. 그룹에 대한 **스키마 그룹** 페이지가 표시됩니다.

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="스키마 그룹 페이지를 보여 주는 이미지":::
    

## <a name="add-a-schema-to-the-schema-group"></a>스키마 그룹에 스키마 추가
이 섹션에서는 Azure Portal을 사용하여 스키마 그룹에 스키마를 추가합니다. 

1. **스키마 그룹** 페이지의 도구 모음에서 **+ 스키마** 를 선택합니다. 
1. **스키마 만들기** 페이지에서 다음 단계를 수행합니다.
    1. **이름** 에 **orderschema** 를 입력합니다.
    1. 텍스트 상자에 다음 **스키마** 를 입력합니다. 스키마를 사용하여 파일을 선택할 수도 있습니다.
    
        ```json
        {
          "namespace": "com.azure.schemaregistry.samples",
          "type": "record",
          "name": "Order",
          "fields": [
            {
              "name": "id",
              "type": "string"
            },
            {
              "name": "amount",
              "type": "double"
            }
          ]
        }
        ```
    1. **만들기** 를 선택합니다. 
1. 스키마 목록에서 **스키마** 를 선택합니다. 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="선택한 스키마를 보여 주는 이미지":::
1. 스키마에 대한 **스키마 개요** 페이지가 표시됩니다. 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="스키마 개요 페이지를 보여 주는 이미지":::    
1. 여러 버전의 스키마가 있는 경우 **버전** 드롭다운 목록에 표시됩니다. 버전을 선택하여 해당 버전 스키마로 전환합니다. 

## <a name="create-a-new-version-of-schema"></a>새 버전의 스키마 만들기

1. 텍스트 상자에서 스키마를 업데이트하고 **유효성 검사** 를 선택합니다. 다음 예에서는 새 필드 `description`가 스키마에 추가되었습니다. 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="스키마 업데이트 페이지를 보여 주는 이미지":::    
    
1. 유효성 검사 상태 및 변경 내용을 검토하고 **저장** 을 선택합니다. 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="유효성 검사 상태, 변경 내용, 저장이 있는 검토 페이지를 보여 주는 이미지":::     
1. **스키마 개요** 페이지의 **버전** 에 대해 `2`가 선택된 것을 볼 수 있습니다. 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="새 버전의 스키마를 보여 주는 이미지":::    
1. 스키마의 버전 1을 표시하려면 `1`을 선택합니다. 


## <a name="next-steps"></a>다음 단계
스키마 레지스트리에 대한 자세한 내용은 [Event Hubs의 Azure 스키마 레지스트리](schema-registry-overview.md)를 참조하세요.
