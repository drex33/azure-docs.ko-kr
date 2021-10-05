---
title: Azure Cosmos DB 커넥터에 대한 Power BI 자습서
description: 이 Power BI 자습서를 사용하여 JSON을 가져오고, 통찰력 있는 보고서를 만들고, Azure Cosmos DB 및 Power BI 커넥터를 사용하여 데이터를 시각화할 수 있습니다.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/4/2021
ms.author: rosouz
ms.openlocfilehash: 0f9c230cc7fe7a21f7b07b74760c7ef03299375d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455951"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Power BI 커넥터를 사용하여 Azure Cosmos DB 데이터 시각화
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

[Power BI](https://powerbi.microsoft.com/)는 대시보드 및 보고서를 만들고 공유할 수 있는 온라인 서비스입니다. Power BI Desktop은 다양한 데이터 원본에서 데이터를 검색할 수 있는 보고서 제작 도구입니다. Azure Cosmos DB는 Power BI Desktop에서 사용할 수 있는 데이터 원본 중 하나입니다. Power BI용 Azure Cosmos DB 커넥터를 사용하여 Azure Cosmos DB 계정에 Power BI Desktop을 연결할 수 있습니다.  Azure Cosmos DB 데이터를 Power BI로 가져온 후 변환하고, 보고서를 만든 후 Power BI에 게시할 수 있습니다.

또 다른 옵션은 [Azure Cosmos DB용 Azure Synapse Link](../synapse-link.md)를 사용하여 실시간에 가까운 보고서를 만드는 것입니다. Azure Synapse Link를 사용하여 트랜잭션 워크로드에 대한 성능 또는 비용에 영향을 주지 않고 ETL 파이프라인 없이 Power BI에 연결하여 Azure Cosmos DB 데이터를 분석할 수 있습니다. [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) 또는 [import](/power-bi/connect-data/service-dataset-modes-understand#import-mode) 모드를 사용할 수 있습니다. 자세한 내용은 [여기](../synapse-link-power-bi.md)를 클릭하세요.


이 문서에서는 Azure Cosmos DB 계정을 Power BI Desktop에 연결하는 데 필요한 단계를 설명합니다. 연결된 후에는 컬렉션으로 이동하고, 데이터를 추출하고, JSON 데이터를 표 형식으로 변환한 후 Power BI에 보고서를 게시합니다.

> [!NOTE]
> Azure Cosmos DB용 Power BI 커넥터는 Power BI Desktop에 연결됩니다. Power BI Desktop에서 만든 보고서를 PowerBI.com에 게시할 수 있습니다. Azure Cosmos DB 데이터의 직접 추출은 PowerBI.com에서 수행할 수 없습니다. 

> [!NOTE]
> Azure Cosmos DB와 Power BI 커넥터 연결은 현재 Azure Cosmos DB SQL API 및 Gremlin API 계정에서만 지원됩니다.

> [!NOTE]
> Azure Synapse Link를 사용하여 거의 실시간으로 Power BI 대시보드를 만드는 작업은 현재 Azure Cosmos DB SQL API 및 MongoDB용 Azure Cosmos DB API에 지원됩니다.

## <a name="prerequisites"></a>사전 요구 사항
이 Power BI 자습서의 지침을 따르기 전에 다음 리소스에 액세스할 수 있는지 확인하세요.

* [최신 버전의 Power BI Desktop 다운로드](https://powerbi.microsoft.com/desktop).

* GitHub에서 [샘플 화산 데이터](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/main/SampleData/VolcanoData.json)를 다운로드합니다.

* [Azure Cosmos 데이터 마이그레이션 도구](../import-data.md)를 사용하여 [Azure Cosmos DB 데이터베이스 계정을 만들고](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) 화산 데이터를 가져옵니다. 데이터를 가져올 때 데이터 마이그레이션 도구의 원본 및 대상에 대한 다음 설정을 고려합니다.

   * **원본 매개 변수** 

       * **다음에서 가져오기:** JSON 파일

   * **대상 매개 변수** 

      * **연결 문자열:** `AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **파티션 키:** /Country 

      * **컬렉션 처리량:** 1000 

PowerBI.com에서 보고서를 공유하려면 PowerBI.com에 계정이 있어야 합니다.  Power BI 및 Power BI Pro에 대한 자세한 내용은 [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing)을 참조하세요.

## <a name="lets-get-started"></a>이제 시작하겠습니다.
이 자습서에서는 전세계 화산을 연구하는 지질학자라고 보겠습니다. 화산 데이터는 Azure Cosmos DB 계정에 저장되며, JSON 문서 형식은 다음과 같습니다.

```json
{
    "Volcano Name": "Rainier",
        "Country": "United States",
        "Region": "US-Washington",
        "Location": {
          "type": "Point",
          "coordinates": [
            -121.758,
            46.87
          ]
        },
        "Elevation": 4392,
        "Type": "Stratovolcano",
        "Status": "Dendrochronology",
        "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Azure Cosmos DB 계정에서 화산 데이터를 검색하고 대화형 Power BI 보고서에서 데이터를 시각화할 것입니다.

1. Power BI Desktop을 실행합니다.

2. 시작 화면에서 직접 **데이터를 가져오고**, **최근 원본** 또는 **다른 보고서를 열 수** 있습니다. 화면을 닫으려면 오른쪽 상단 모서리의 “X”를 선택합니다. Power BI 데스크톱의 **보고서** 뷰가 표시됩니다.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview.png" alt-text="Power BI 데스크톱 보고서 보기 - Power BI 커넥터":::

3. **홈** 리본 메뉴를 선택한 다음 **데이터 가져오기** 를 클릭합니다.  **데이터 가져오기** 창이 나타납니다.

4. **Azure** 를 클릭하고 **Azure Cosmos DB(베타)** 를 선택한 다음, **연결** 을 클릭합니다. 

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbigetdata.png" alt-text="Power BI 데스크톱 데이터 가져오기 - Power BI 커넥터":::

5. **커넥터 미리 보기** 페이지에서 **계속** 을 클릭합니다. **Azure Cosmos DB** 창이 나타납니다.

6. 아래와 같이 데이터를 검색할 Azure Cosmos DB 계정 엔드포인트 URL을 지정한 다음, **확인** 을 클릭합니다. 자신의 계정을 사용하려는 경우 Azure Portal의 **키** 블레이드에 있는 URI 상자에서 URL을 검색할 수 있습니다. 필요에 따라 데이터베이스 이름, 컬렉션 이름을 제공하거나 탐색기를 사용하여 데이터를 가져오는 위치를 식별하는 데이터베이스 및 컬렉션을 선택할 수도 있습니다.
   
7. 처음으로 이 엔드포인트에 연결하는 경우 계정 키를 입력하라는 메시지가 표시됩니다. 자신의 계정을 사용하는 경우 Azure Portal의 **읽기 전용 키** 블레이드에 있는 **기본 키** 상자에서 키를 검색합니다. 적절한 키를 입력하고 **연결** 을 클릭합니다.
   
   보고서를 작성할 때는 읽기 전용 키를 사용하는 것이 좋습니다. 이렇게 하면 불필요하게 기본 키가 잠재적인 보안 위험에 노출되는 것을 방지할 수 있습니다. 읽기 전용 키는 Azure Portal의 **키** 블레이드에서 사용할 수 있습니다. 
    
8. 계정이 성공적으로 연결되면 **탐색기** 창이 표시됩니다. **탐색기** 는 계정의 데이터베이스 목록을 표시합니다.

9. 보고서의 데이터를 가져올 데이터베이스를 클릭하여 확장하고, ``volcanodb``(데이터베이스 이름은 다를 수 있음)를 선택합니다.   

10. 이제 검색할 데이터포를 함하는 컬렉션을 선택하고 **volcano1**(컬렉션 이름은 다를 수 있음)을 선택합니다.
    
    미리 보기 창에는 **레코드** 항목의 목록이 표시됩니다.  문서는 Power BI에서 **레코드** 형식으로 나타납니다. 마찬가지로, 문서 내의 중첩된 JSON 블록도 **레코드** 입니다.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbinavigator.png" alt-text="Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 탐색기 창":::

12. **편집** 을 클릭하여 새 창에서 쿼리 편집기를 시작해 데이터를 변환합니다.

## <a name="flattening-and-transforming-json-documents"></a>JSON 문서 평면화 및 변환
1. Power BI 쿼리 편집기 창으로 전환합니다. 가운데 창에 **문서** 열이 표시됩니다.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png" alt-text="Power BI 데스크톱 쿼리 편집기":::

1. **문서** 열 머리글의 오른쪽에서 확장 아이콘을 클릭합니다.  필드 목록이 있는 상황에 맞는 메뉴가 표시됩니다.  화산 이름, 국가, 지역, 위치, 상승, 유형, 상태, 마지막 분출일 등 보고서에 필요한 필드를 선택합니다. **원래 열 이름을 접두사로 사용** 확인란의 선택을 취소하고 **확인** 을 클릭합니다.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png" alt-text="Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 문서 확장":::

1. 가운데 창에는 선택한 필드와 함께 결과의 미리 보기가 표시됩니다.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png" alt-text="Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 결과 평면화":::

1. 이 예제에서 위치 속성은 문서의 GeoJSON 블록입니다.  보이는 것처럼 위치는 Power BI 데스크톱에서 **레코드** 형식으로 나타납니다.  

1. Document.Location 열 헤더의 오른쪽에 있는 확장 아이콘을 클릭합니다.  유형 및 좌표 필드가 있는 상황에 맞는 메뉴가 표시됩니다.  좌표 필드를 선택하고, **원래 열 이름을 접두사로 사용** 의 선택을 취소하고, **확인** 을 클릭합니다.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png" alt-text="Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 위치 레코드":::

1. 이제 가운데 창에는 **목록** 유형의 좌표 열이 표시됩니다.  자습서의 시작 부분에서 본 것처럼 이 자습서의 GeoJSON 데이터는 지점 유형으로, 위도와 경도 값이 좌표 배열에 기록됩니다.
   
   좌표 [0] 요소는 경도를, 좌표 [1]은 위도를 나타냅니다.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png" alt-text="Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 좌표 목록":::

1. 좌표 배열을 평면화하기 위해 이름이 LatLong이라고 하는 **사용자 지정 열** 을 만듭니다.  **열 추가** 리본을 선택하고 **사용자 지정 열** 을 클릭합니다.  **사용자 지정 열** 창이 나타납니다.

1. LatLong 등, 새 열에 대한 이름을 입력합니다.

1. 다음으로 새 열에 사용자 지정 수식을 지정합니다.  이 예에서는 `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`수식을 사용하여 쉼표로 구분하여 위도와 경도 값을 연결합니다. **확인** 을 클릭합니다.
   
   DAX 함수를 포함한 데이터 분석 식(DAX)에 대한 자세한 내용은 [Power BI 데스크톱의 DAX 기본 사항](/power-bi/desktop-quickstart-learn-dax-basics)을 참조하세요.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png" alt-text="Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 사용자 지정 열 추가":::

1. 이제 가운데 창에 값이 채워진 새 LatLong 열이 표시됩니다.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png" alt-text="Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 사용자 지정 LatLong 열":::
    
    새 열에 오류를 수신하는 경우 쿼리 설정에서 적용된 단계가 다음 그림과 일치하는지 확인합니다.
    
    :::image type="content" source="./media/powerbi-visualize/power-bi-applied-steps.png" alt-text="적용된 단계는 원본, 탐색, 확장된 문서, 확장된 Document.Location, 추가된 사용자 지정이어야 합니다.":::
    
    단계가 다른 경우 추가 단계를 삭제하고 사용자 지정 열을 다시 추가해 보십시오. 

1. **닫고 적용** 을 클릭하여 데이터 모델을 저장합니다.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicloseapply.png" alt-text="Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 닫기 및 적용":::

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>보고서 작성

Power BI Desktop 보고서 보기에서는 데이터를 시각화하는 보고서 만들기를 시작할 수 있습니다.  필드를 끌어서 **보고서** 캔버스에 놓으면 보고서를 만들 수 있습니다.

:::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview2.png" alt-text="Power BI Desktop 보고서 뷰 - 필수 필드 끌어서 놓기":::

보고서 보기에는 다음과 같은 항목이 있습니다.

1. **필드** 창. 보고서에 사용할 수 있는 필드와 데이터 모델 목록이 있습니다.
1. **시각화** 창. 보고서는 단일 또는 여러 시각화 요소를 포함할 수 있습니다.  **시각화** 창에서 필요에 부합하는 시각화 형식을 선택합니다.
1. **보고서** 캔버스. 보고서의 시각적 요소를 빌드할 수 있습니다.
1. **보고서** 페이지. Power BI 데스크톱에서 여러 보고서 페이지를 추가할 수 있습니다.

다음은 간단한 대화형 지도 보기 보고서를 만드는 기본적은 단계를 보여줍니다.

1. 이 예에서는 각 화산의 위치를 나타내는 지도를 만듭니다.  **시각화** 창에서 위의 스크린샷에 강조 표시된 대로 지도 시각 유형을 클릭합니다.  **보고서** 캔버스에 지도 시각 형식이 칠해져 나타납니다.  **시각화** 창에도 지도 시각 유형과 관련한 속성 집합이 표시됩니다.
1. **필드** 창에서 LatLong 필드를 끌어서 **시각화** 창의 **위치** 속성에 놓습니다.
1. 다음으로 화산 이름 필드를 끌어서 **범례** 에 놓습니다.  
1. 이제 상승 필드를 끌어서 **크기** 속성에 놓습니다.  
1. 이제 맵에 화산의 상승 위치에 따른 크기의 버블로 각 화산의 위치를 시각적으로 나타내는 버블 집합이 표시됩니다.
1. 이제 기본 보고서를 만들었습니다.  다른 시각화 요소를 추가하여 보고서를 상세히 사용자 지정할 수 있습니다.  여기서는 화산 유형 슬라이서를 추가하여 보고서를 대화형으로 구성했습니다.  
   
1. 파일 메뉴에서 **저장** 을 클릭하고 파일을 PowerBITutorial.pbix로 저장합니다.

## <a name="publish-and-share-your-report"></a>보고서 게시 및 공유
보고서를 공유하려면 PowerBI.com에 계정이 있어야 합니다.

1. Power BI 데스크톱에서 **홈** 리본을 클릭합니다.
1. **게시** 를 클릭합니다.  PowerBI.com 계정의 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다.
1. 자격 증명이 인증되면 보고서가 선택한 대상에 게시됩니다.
1. **Power BI에서 'PowerBITutorial.pbix' 열기** 를 클릭하여 PowerBI.com에서 보고서를 보고 공유합니다.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png" alt-text="Power BI에 게시 성공! Power BI에서 자습서 열기":::

## <a name="create-a-dashboard-in-powerbicom"></a>PowerBI.com에서 대시보드 만들기
이제 보고서가 있으니 PowerBI.com에서 공유하겠습니다.

보고서를 Power BI 데스크톱에서 PowerBI.com에 게시하는 경우 PowerBI.com 테넌트에 **보고서** 및 **데이터 세트** 를 생성합니다. 예를 들어 **PowerBITutorial** 이라는 보고서를 PowerBI.com에 게시한 후 PowerBI.com의 **보고서** 및 **데이터 세트** 섹션 모두에서 PowerBITutorial이 표시됩니다.

   :::image type="content" source="./media/powerbi-visualize/powerbi-reports-datasets.png" alt-text="PowerBI.com에서 새 보고서 및 데이터 세트의 스크린샷":::

공유할 수 있는 대시보드를 만들려면 PowerBI.com 보고서의 **라이브 페이지 고정** 단추를 클릭합니다.

   :::image type="content" source="./media/powerbi-visualize/power-bi-pin-live-tile.png" alt-text="PowerBI.com에 보고서를 고정하는 방법에 대한 스크린샷":::

그런 다음 [보고서에서 타일을 고정](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) 의 지침을 따라 새 대시보드를 만듭니다. 

또한 대시보드를 만들기 전에 보고서에 대한 임시 수정을 수행할 수 있습니다. 그러나 Power BI 데스크톱을 사용하여 수정 작업을 수행하고 보고서를 PowerBI.com 다시 게시하는 것이 좋습니다.

## <a name="refresh-data-in-powerbicom"></a>PowerBI.com에서 데이터 새로 고침
임시 및 예약의 두 가지 방법으로 데이터를 새로 고칠 수 있습니다.

임시 새로 고침의 경우 **지금 새로 고침** 을 클릭하여 데이터를 새로 고칩니다.

예약된 새로 고침의 경우 다음을 수행합니다.

1. **설정** 으로 이동하여 **데이터 세트** 탭을 엽니다.

2. **예약된 새로 고침** 을 클릭하고 일정을 설정합니다.


## <a name="next-steps"></a>다음 단계
* Power BI에 대한 자세한 내용은 [Power BI 시작](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)을 참조하세요.
* Azure Cosmos DB에 대한 자세한 내용은 [Azure Cosmos DB 설명서 방문 페이지](https://azure.microsoft.com/documentation/services/cosmos-db/)를 참조하세요.
