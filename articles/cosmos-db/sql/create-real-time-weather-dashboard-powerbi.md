---
title: Azure Cosmos DB, Azure Analysis Services 및 Power BI를 사용하여 실시간 대시보드 만들기
description: Azure Cosmos DB 및 Azure Analysis Services를 사용하여 Power BI에서 라이브 날씨 대시보드를 만드는 방법을 알아봅니다.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/04/2019
ms.reviewer: sngun
ms.openlocfilehash: a2a9096ac6f8762500d1060dbab7277b2dafe9dd
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116378"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Azure Cosmos DB 및 Power BI를 사용하여 실시간 대시보드 만들기
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Azure Cosmos DB OLTP 커넥터 및 Azure Analysis Services를 사용하여 Power BI에서 라이브 날씨 대시보드를 만드는 데 필요한 단계를 설명합니다. Power BI 대시보드는 지역의 온도 및 강우량에 대한 거의 실시간 정보를 보여 주는 차트를 표시합니다.

또 다른 옵션은 [Azure Cosmos DB용 Azure Synapse Link](../synapse-link.md)를 사용하여 거의 실시간 보고서를 만드는 것입니다. Azure Synapse Link를 사용하여 트랜잭션 워크로드에 대한 성능 또는 비용에 영향을 주지 않고 ETL 파이프라인 없이 Power BI에 연결하여 Azure Cosmos DB 데이터를 분석할 수 있습니다. [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) 또는 [import](/power-bi/connect-data/service-dataset-modes-understand#import-mode) 모드를 사용할 수 있습니다. 자세한 내용은 [여기](../synapse-link-power-bi.md)를 클릭하세요.


## <a name="reporting-scenarios"></a>보고 시나리오

Azure Cosmos DB에 저장된 데이터에 대한 보고 대시보드를 설정하는 방법에는 여러 가지가 있습니다. 다음 표에서는 부실 요구 사항 및 데이터의 크기에 따라 각 시나리오의 보고 설정에 대해 설명합니다.


|시나리오 |설치 프로그램 |
|---------|---------|
|1. 집계를 사용하여 대규모 데이터 세트에 대한 실시간 보고서 생성     | **옵션 1:** [Power BI 및 Azure Synapse Link(DirectQuery 포함)](../synapse-link-power-bi.md)<br />  **옵션 2:** [Power BI 및 Spark 커넥터(DirectQuery 포함) + Azure Databricks + Azure Cosmos DB Spark 커넥터.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)<br />  **옵션 3:** Power BI 및 Azure Analysis Services Connector(DirectQuery 포함) + Azure Analysis Services + Azure Databricks + Cosmos DB Spark 커넥터.     |
|2. 대용량 데이터 세트에 대한 실시간 보고서 생성(10GB 이상)    |  **옵션 1:** [Power BI 및 Azure Synapse Link(DirectQuery 포함)](../synapse-link-power-bi.md)<br />  **옵션 2:** [Power BI 및 Azure Analysis Services Connector( DirectQuery 포함) + Azure Analysis Services](create-real-time-weather-dashboard-powerbi.md)       |
|3. 대용량 데이터 세트(10GB 미만)에 대한 임시 보고서 생성     |  [가져오기 모드 및 증분 새로 고침을 사용하는 Power BI Azure Cosmos DB 커넥터](create-real-time-weather-dashboard-powerbi.md)       |
|4. 정기적 새로 고침을 사용하여 임시 보고서 생성   |  [가져오기 모드를 사용하는 Power BI Azure Cosmos DB 커넥터(예약된 정기적 새로 고침)](powerbi-visualize.md)       |
|5. 임시 보고서 생성(새로 고침 안 함)    |  [가져오기 모드를 사용하는 Power BI Azure Cosmos DB 커넥터](powerbi-visualize.md)       |


시나리오 4와 5는 [Azure Cosmos DB Power BI 커넥터를 사용](powerbi-visualize.md)하여 쉽게 설정할 수 있습니다. 이 문서에서는 시나리오 2(옵션 2) 및 3의 설정에 대해 설명합니다.

### <a name="power-bi-with-incremental-refresh"></a>증분 새로 고침을 사용하는 Power BI

Power BI에는 증분 새로 고침을 구성할 수 있는 모드가 있습니다. 이 모드에서는 Azure Analysis Services 파티션을 만들고 관리할 필요가 없습니다. 증분 새로 고침을 설정하여 대량 데이터 세트의 최신 업데이트만 필터링할 수 있습니다. 그러나 이 모드는 데이터세트 제한이 10GB인 Power BI Premium 서비스에서만 작동합니다.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis 커넥터 + Azure Analysis Services

Azure Analysis Services는 클라우드에서 엔터프라이즈급 데이터 모델을 호스트하는 완전 관리형 PaaS(platform as a service)를 제공합니다. 대량 데이터 세트를 Azure Cosmos DB에서 Azure Analysis Services로 로드할 수 있습니다. 항상 전체 데이터 세트를 쿼리할 필요를 없애기 위해 데이터 세트를 Azure Analysis Services 파티션으로 세분화하고 서로 다른 빈도로 독립적으로 새로 고칠 수 있습니다.

## <a name="power-bi-incremental-refresh"></a>Power BI 증분 새로 고침

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>날씨 데이터를 Azure Cosmos DB에 수집

Azure Cosmos DB에 [날씨 데이터](https://catalog.data.gov/dataset?groups=climate5434&#topic=climate_navigation)를 로드하도록 수집 파이프라인을 설정합니다. HTTP 원본 및 Cosmos DB 싱크를 통해 최신 날씨 데이터를 Azure Cosmos DB에 정기적으로 로드하도록 [ADF(Azure Data Factory)](../../data-factory/connector-azure-cosmos-db.md) 작업을 설정할 수 있습니다.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Azure Cosmos DB에 Power BI 연결

1. **Azure Cosmos 계정을 Power BI에 연결** - Power BI Desktop를 열고 Azure Cosmos DB 커넥터를 사용하여 올바른 데이터베이스 및 컨테이너를 선택합니다.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png" alt-text="Azure Cosmos DB Power BI 커넥터":::

1. **증분 새로 고침 구성** - [Power BI를 사용하여 증분 새로 고침](/power-bi/service-premium-incremental-refresh) 문서의 단계에 따라 데이터 세트에 대한 증분 새로 고침을 구성합니다. 다음 스크린샷에 표시된 것처럼 **RangeStart** 및 **RangeEnd** 매개 변수를 추가합니다.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png" alt-text="범위 매개 변수 구성":::

   데이터 세트에는 텍스트 형식의 날짜 열이 있으므로 다음 필터를 사용하려면 **RangeStart** 및 **RangeEnd** 매개 변수를 변환해야 합니다. **고급 편집기** 창에서 쿼리를 수정하고 다음 텍스트를 추가하여 RangeStart 및 RangeEnd 매개 변수를 기준으로 행을 필터링합니다.

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   원본 데이터 세트에 있는 열 및 데이터 형식에 따라 RangeStart 및 RangeEnd 필드를 적절히 변경할 수 있습니다.

   
   |속성  |데이터 형식  |Assert  |
   |---------|---------|---------|
   |_ts        |   숫자      |  [_ts] > Duration.TotalSeconds(RangeStart - #datetime(1970, 1, 1, 0, 0, 0)) and [_ts] < Duration.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0, 0)))       |
   |Date(예:- 2019-08-19)     |   String      | [Document.date]> DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |날짜(예:- 2019-08-11 12:00:00)   |  String       |  [Document.date]> DateTime.ToText(RangeStart," yyyy-mm-dd HH:mm:ss") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-dd HH:mm:ss")       |


1. **새로 고침 정책 정의** - 표에 대한 **상황에 맞는 메뉴** 에서 **증분 새로 고침** 탭으로 이동하여 새로 고침 정책을 정의합니다. **매일** 새로 고치도록 새로 고침 정책을 설정하고 마지막 달 데이터를 저장합니다.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png" alt-text="새로 고침 정책 정의":::

   *M 쿼리를 폴딩할지 확인할 수 없음* 이라는 경고는 무시해도 됩니다. Azure Cosmos DB 커넥터는 필터 쿼리를 폴딩합니다.

1. **데이터 로드 및 보고서 생성** - 이전에 로드한 데이터를 사용하여 온도 및 강우량을 보고하는 차트를 만듭니다.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="데이터 로드 및 보고서 생성":::

1. **Power BI Premium에 보고서 게시** - 증분 새로 고침은 Premium 전용 기능이기 때문에 게시 대화 상자는 Premium 용량에서만 작업 영역을 선택할 수 있습니다. 첫 번째 새로 고침에서 기록 데이터를 가져오는 데 시간이 오래 걸릴 수 있습니다. 후속 데이터 새로 고침은 증분 새로 고침을 사용하기 때문에 훨씬 더 빠릅니다.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis 커넥터 + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>날씨 데이터를 Azure Cosmos DB에 수집 

Azure Cosmos DB에 [날씨 데이터](https://catalog.data.gov/dataset?groups=climate5434&#topic=climate_navigation)를 로드하도록 수집 파이프라인을 설정합니다. HTTP 원본 및 Cosmos DB 싱크를 통해 최신 날씨 데이터를 Azure Cosmos DB에 정기적으로 로드하도록 ADF(Azure Data Factory) 작업을 설정할 수 있습니다.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Azure Cosmos 계정에 Azure Analysis Services 연결

1. **새 Azure Analysis Services 클러스터 만들기** - Azure Cosmos 계정 및 Databricks 클러스터와 동일한 지역에 [Azure Analysis Services 인스턴스를 만듭니다](../../analysis-services/analysis-services-create-server.md).

1. **Visual Studio에서 새 Analysis Services 테이블 형식 프로젝트 만들기** -  [SSDT(SQL Server Data Tools)를 설치](/sql/ssdt/download-sql-server-data-tools-ssdt)하고 Visual Studio에서 Analysis Services 테이블 형식 프로젝트를 만듭니다.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png" alt-text="Azure Analysis Services 프로젝트 만들기":::

   **통합 작업 영역** 인스턴스를 선택하고 호환성 수준을 **SQL Server 2017/Azure Analysis Services(1400)** 로 설정

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png" alt-text="Azure Analysis Services 테이블 형식 모델 디자이너":::

1. **Azure Cosmos DB 데이터 원본 추가** - **모델**> **데이터 원본** > **새 데이터 원본** 으로 이동하고 다음 스크린샷에 표시된 것처럼 Azure Cosmos DB 데이터 원본을 추가합니다.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/add-data-source.png" alt-text="Cosmos DB 데이터 원본 추가":::

   **계정 URI**, **데이터베이스 이름** 및 **컨테이너 이름** 을 제공하여 Azure Cosmos DB에 연결합니다. 이제 Azure Cosmos 컨테이너의 데이터를 Power BI로 가져왔는지 확인할 수 있습니다.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png" alt-text="Azure Cosmos DB 데이터 미리 보기":::

1. **Analysis Services 모델 생성** - 쿼리 편집기를 열고 로드된 데이터 세트를 최적화하는 데 필요한 작업을 수행합니다.

   * 날씨 관련 열(온도 및 강우량)만 추출

   * 테이블에서 월 정보를 추출합니다. 이 데이터는 다음 섹션에 설명된 대로 파티션을 만드는 데 유용합니다.

   * 온도 열을 숫자로 변환

   결과 M 식은 다음과 같습니다.

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   또한 온도 열의 데이터 형식을 10진수로 변경하여 이러한 값을 Power BI에 표시할 수 있는지 확인합니다.

1. **파티션 만들기** - Azure Analysis Services에서 파티션을 만들어 데이터 세트를 다른 주기로 독립적으로 새로 고칠 수 있는 논리 파티션으로 나눕니다. 이 예에서는 데이터 세트를 가장 최근 달의 데이터와 나머지 데이터로 나누는 두 개의 파티션을 만듭니다.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png" alt-text="Analysis Services 파티션 만들기":::

   Azure Analysis Services에서 다음과 같은 두 개의 파티션을 만듭니다.

   * **최신 달** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **기록** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Azure Analysis Server에 모델 배포** -Azure Analysis Services 프로젝트를 마우스 오른쪽 단추로 클릭하고 **배포** 를 선택합니다. **배포 서버 속성** 창에서 서버 이름을 추가합니다.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png" alt-text="Azure Analysis Services 모델 배포":::

1. **파티션 새로 고침 및 병합 구성** -Azure Analysis Services에서는 파티션을 독립적으로 처리할 수 있습니다. **최신 월** 파티션을 최신 데이터로 지속적으로 업데이트하려면 새로 고침 간격을 5분으로 설정합니다. [REST API](../../analysis-services/analysis-services-async-refresh.md), [Azure Automation](../../analysis-services/analysis-services-refresh-azure-automation.md) 또는 [논리 앱](../../analysis-services/analysis-services-refresh-logic-app.md)을 사용하여 데이터를 새로 고칠 수 있습니다. 기록 파티션에서는 데이터를 새로 고칠 필요가 없습니다. 또한 최신 월 파티션을 기록 파티션에 통합하고 새로운 최신 월 파티션을 만드는 코드를 작성해야 합니다.

## <a name="connect-power-bi-to-analysis-services"></a>Power BI를 Analysis Services에 연결

1. **Azure Analysis Services 데이터베이스 커넥터를 사용하여 Azure Analysis Server에 연결** - 다음 스크린샷에 표시된 대로 **라이브 모드** 를 선택하고 Azure Analysis Services 인스턴스에 연결합니다.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png" alt-text="Azure Analysis Services에서 데이터 가져오기":::

1. **데이터 로드 및 보고서 생성** - 이전에 로드한 데이터를 사용하여 온도 및 강우량을 보고하는 차트를 만듭니다. 라이브 연결을 만드는 중이므로 이전 단계에서 배포한 Azure Analysis Services 모델의 데이터에 대해 쿼리를 실행해야 합니다. 온도 차트는 새 데이터가 Azure Cosmos DB에 로드되고 5분 이내에 업데이트됩니다.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="데이터 로드 및 보고서 생성":::

## <a name="next-steps"></a>다음 단계

* Power BI에 대한 자세한 내용은 [Power BI 시작](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)을 참조하세요.

* [Qlik Sense를 Azure Cosmos DB에 연결하고 데이터 시각화](../visualize-qlik-sense.md)