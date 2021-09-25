---
title: Azure Application Insights에서 SQL로 내보내기 | Microsoft Docs
description: Stream Analytics를 사용하여 Application Insights 데이터를 SQL로 계속 내보냅니다.
ms.topic: conceptual
ms.date: 09/11/2017
ms.service: stream-analytics
ms.openlocfilehash: bcf3bc5995a6d9c8c37ab92e303b7d39b0b132fe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634185"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>연습: Stream Analytics를 사용하여 Application Insights에서 SQL로 내보내기
이 문서에서는 [연속 내보내기][export] 및 [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)를 사용하여 [Azure Application Insights][start]에서 Azure SQL Database로 원격 분석 데이터를 이동하는 방법을 보여 줍니다. 

연속 내보내기는 원격 분석 데이터를 JSON 형식으로 Azure Storage로 이동합니다. Azure Stream Analytics를 사용하여 JSON 개체를 구문 분석하고 데이터베이스 테이블에 행을 만들 것입니다.

보다 일반적으로 말하자면, 연속 내보내기는 앱에서 Application Insights에 보내는 원격 분석을 자체적으로 분석하는 방법입니다. 데이터 집계와 같은 원격 분석을 사용하여 이 코드 샘플이 기타 작업을 수행하도록 적용할 수 있습니다.

모니터링하려는 앱이 이미 있다고 가정하고 시작합니다.

이 예제에서는 페이지 보기 데이터를 사용하지만, 동일한 패턴을 사용자 지정 이벤트 및 예외와 같은 다른 데이터 형식으로 쉽게 확장할 수 있습니다. 

> [!IMPORTANT]
> 연속 내보내기는 더 이상 사용되지 않으며 클래식 Application Insights 리소스에 대해서만 지원됩니다. [작업 영역 기반 Application Insights 리소스로 마이그레이션하여](../azure-monitor/app/convert-classic-resource.md) 원격 분석 내보내기에 대한 [진단 설정을](../azure-monitor/app/export-telemetry.md#diagnostic-settings-based-export) 사용합니다.

## <a name="add-application-insights-to-your-application"></a>애플리케이션에 Application Insights 추가 
시작하기:

1. [웹 페이지용 Application Insights를 설치합니다](../azure-monitor/app/javascript.md). 
   
    (이 예제에서는 클라이언트 브라우저에서 페이지 보기 데이터를 처리하는 데 초점을 두었지만 [Java](../azure-monitor/app/java-in-process-agent.md) 또는 [ASP.NET](../azure-monitor/app/asp-net.md) 앱의 서버 쪽에 대한 Application Insights, 프로세스 요청, 종속성 및 기타 서버 원격 분석도 설정할 수 있습니다.)
2. 앱을 게시하고 Application Insights 리소스에 표시되는 원격 분석 데이터를 확인합니다.

## <a name="create-storage-in-azure"></a>Azure에서 스토리지 만들기
연속 내보내기는 항상 Azure Storage 계정에 데이터를 출력하므로 스토리지를 먼저 만들어야 합니다.

1. [Azure Portal][portal]에서 구독에 스토리지 계정을 만듭니다.
   
    ![Azure Portal 스크린샷, 새로 만들기, 데이터, Storage 선택한 다음, 클래식, 만들기를 선택하고 Storage 이름을 제공합니다.](./media/app-insights-export-sql-stream-analytics/040-store.png)
2. 컨테이너 만들기
   
    ![새 스토리지의 스크린샷, 컨테이너, 컨테이너 타일, 추가를 선택합니다.](./media/app-insights-export-sql-stream-analytics/050-container.png)
3. 스토리지 액세스 키 복사
   
    스트림 분석 서비스에 대한 입력을 설정하려면 곧 이 키가 필요합니다.
   
    ![ 스토리지의 스크린샷, 설정 열기, 키, 기본 액세스 키의 복사본 만들기](./media/app-insights-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Azure Storage로 연속 내보내기 시작
1. Azure 포털에서 애플리케이션에 대해 만든 Application Insights 리소스를 찾습니다.
   
    ![Azure Portal 스크린샷으로 찾아보기, 애플리케이션 Insights, 애플리케이션을 선택합니다.](./media/app-insights-export-sql-stream-analytics/060-browse.png)
2. 연속 내보내기를 만듭니다.
   
    ![애플리케이션 Insights 리소스의 스크린샷, 설정, 연속 내보내기, 추가를 선택합니다.](./media/app-insights-export-sql-stream-analytics/070-export.png)

    이전에 만든 스토리지 계정을 선택합니다.

    ![연속 내보내기 스크린샷 다음, 추가를 선택합니다.](./media/app-insights-export-sql-stream-analytics/080-add.png)

    보려는 이벤트 유형을 설정합니다.

    ![연속 내보내기 추가 및 이벤트 유형 선택 스크린샷](./media/app-insights-export-sql-stream-analytics/085-types.png)


1. 일부 데이터가 누적되도록 합니다. 한동안 사용자가 애플리케이션을 사용하도록 놓아둡니다. 원격 분석이 제공되어 [메트릭 탐색기](../azure-monitor/essentials/metrics-charts.md)에서 통계 차트가, [진단 검색](../azure-monitor/app/diagnostic-search.md)에서 개별 이벤트가 표시됩니다. 
   
    또한 데이터를 스토리지로 내보냅니다. 
2. 포털 또는 Visual Studio에서 내보낸 데이터를 검사합니다. 포털에서 **찾아보기**, 스토리지 계정을 선택한 다음 **컨테이너** 를 선택합니다. Visual Studio에서 **보기/클라우드 탐색기** 를 선택하고 Azure/스토리지를 엽니다. (이 메뉴 옵션이 없는 경우 Azure SDK를 설치해야 합니다. 새 프로젝트 대화 상자를 열고 시각적 개체 C# / 클라우드 / .NET용 Microsoft Azure SDK 가져오기를 엽니다.)
   
    ![Visual Studio, 서버 브라우저 열기, Azure, Storage 스크린샷](./media/app-insights-export-sql-stream-analytics/087-explorer.png)
   
    애플리케이션 이름 및 계측 키에서 파생된 경로 이름의 공통 부분을 적어 둡니다. 

이벤트는 JSON 형식으로 blob 파일에 기록됩니다. 각 파일에는 하나 이상의 이벤트가 있을 수 있습니다. 따라서 이벤트 데이터를 읽고 원하는 필드를 필터링하려고 합니다. 데이터로 온갖 종류의 작업을 수행할 수 있지만, 지금은 Stream Analytics를 사용하여 데이터를 SQL Database로 이동하려고 합니다. 이렇게 하면 흥미로운 많은 쿼리를 쉽게 실행할 수 있습니다.

## <a name="create-an-azure-sql-database"></a>Azure SQL Database 생성
다시 한 번 [Azure Portal][portal]의 구독에서 시작하여 데이터를 작성할 데이터베이스(및 이미 있는 경우 새 서버)를 만듭니다.

![Azure Portal, 만들기, Data+ 스토리지 및 SQL Database 스크린샷.](./media/app-insights-export-sql-stream-analytics/090-sql.png)

서버에서 Azure 서비스에 대한 액세스를 허용하는지 확인합니다.

![Azure Portal 및 방화벽 설정의 SQL 서버 스크린샷.](./media/app-insights-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-database"></a>Azure SQL Database에 테이블 만들기
기본 관리 도구로 이전 섹션에서 만든 데이터베이스에 연결합니다. 이 연습에서는 SSMS( [SQL Server 관리 도구](/sql/ssms/sql-server-management-studio-ssms) )를 사용합니다.

![Azure SQL Database 연결 스크린샷](./media/app-insights-export-sql-stream-analytics/31-sql-table.png)

새 쿼리를 만들고 다음 T-SQL을 실행합니다.

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![SQL Server Management Studio PageViewsTable 만들기의 스크린샷](./media/app-insights-export-sql-stream-analytics/34-create-table.png)

이 샘플에서 페이지 보기에서 데이터를 사용합니다. 사용 가능한 다른 데이터를 보려면 JSON 출력을 검사하고 [데이터 모델 내보내기](../azure-monitor/app/export-data-model.md)를 참조합니다.

## <a name="create-an-azure-stream-analytics-instance"></a>Azure Stream Analytics 인스턴스 만들기
[Azure Portal](https://portal.azure.com/)에서 Azure Stream Analytics 서비스를 선택하고 새 Stream Analytics 작업을 만듭니다.

![만들기 단추가 강조 표시된 Stream Analytics 작업 페이지를 보여 주는 스크린샷.](./media/app-insights-export-sql-stream-analytics/001.png)

![새 스트림 분석 작업의 스크린샷](./media/app-insights-export-sql-stream-analytics/002.png)

새 작업이 만들어질 때 **리소스로 이동** 을 선택합니다.

![배포 성공 메시지 및 리소스로 이동 단추를 보여 주는 스크린샷.](./media/app-insights-export-sql-stream-analytics/003.png)

#### <a name="add-a-new-input"></a>새 입력 추가

![추가 단추가 선택된 입력 페이지를 보여 주는 스크린샷.](./media/app-insights-export-sql-stream-analytics/004.png)

연속 내보내기 Blob에서 입력을 가져오도록 설정합니다.

![입력 별칭, 원본, 스토리지 계정 드롭다운 메뉴 옵션이 선택된 새 입력 창을 보여 주는 스크린샷.](./media/app-insights-export-sql-stream-analytics/0005.png)

이제 앞에서 기록해 둔 Storage 계정의 기본 액세스 키가 필요합니다. 이 키를 Storage 계정 키로 설정합니다.

#### <a name="set-path-prefix-pattern"></a>경로 접두사 패턴 설정

**날짜 형식을 YYYY-MM-DD(파선 포함)로 설정해야 합니다.**

경로 접두사 패턴은 Stream Analytics가 스토리지에서 입력 파일을 찾는 방법을 지정합니다. 연속 내보내기에서 데이터를 저장하는 방법과 일치하도록 설정해야 합니다. 다음과 같이 설정합니다.

```sql
webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}
```

이 예제에서:

* `webapplication27`은 Application Insights 리소스의 이름으로, **모두 소문자** 입니다. 
* `1234...`은 **대시를 제거한** Application Insights 리소스의 계측 키입니다. 
* `PageViews` 는 분석하려는 데이터의 형식입니다. 사용 가능한 형식은 연속 내보내기에 설정한 필터에 따라 다릅니다. 내보낸 데이터를 검사하여 사용 가능한 다른 형식을 확인하고 [데이터 모델 내보내기](../azure-monitor/app/export-data-model.md)를 참조합니다.
* `/{date}/{time}` 은 문자로 기록된 패턴입니다.

Application Insights 리소스의 이름 및 iKey를 가져오려면 해당 개요 페이지에서 필수 항목을 열거나 설정을 엽니다.

> [!TIP]
> 샘플 함수를 사용하여 입력 경로가 올바르게 설정되었는지 확인합니다. 실패한 경우 선택한 샘플 시간 범위에 대한 스토리지에 데이터가 있는지 확인합니다. 입력 정의를 편집하고 스토리지 계정, 경로 접두사 및 날짜 형식이 올바르게 설정되었는지 확인합니다.

 
## <a name="set-query"></a>쿼리 설정
쿼리 섹션을 엽니다.

기본 쿼리를 다음으로 바꿉니다.

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

처음 몇 가지 속성은 페이지 보기 데이터에만 해당됩니다. 다른 원격 분석 유형 내보내기에 다른 속성이 있습니다. [속성 형식 및 값에 대한 자세한 데이터 모델 참조](../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>데이터베이스에 출력 설정
SQL을 출력으로 선택합니다.

![출력이 선택된 스트림 분석의 스크린샷.](./media/app-insights-export-sql-stream-analytics/006.png)

데이터베이스를 지정합니다.

![데이터베이스 세부 정보가 있는 새 출력의 스크린샷.](./media/app-insights-export-sql-stream-analytics/007.png)

마법사를 닫고 출력이 설정되었다는 알림이 표시될 때까지 기다립니다.

## <a name="start-processing"></a>처리 시작
작업 모음에서 작업을 시작합니다.

![시작이 선택된 스트림 분석의 스크린샷.](./media/app-insights-export-sql-stream-analytics/008.png)

지금부터의 데이터를 처리할 것인지 아니면 이전 데이터부터 처리할 것인지를 선택할 수 있습니다. 후자는 연속 내보내기를 이미 한동안 실행한 경우 유용합니다.

몇 분 후에 SQL Server 관리 도구로 돌아가서 데이터 흐름을 확인합니다. 예를 들어 다음과 같은 쿼리를 사용합니다.

```sql
SELECT TOP 100 *
FROM [dbo].[PageViewsTable]
```

## <a name="next-steps"></a>다음 단계
* [Stream Analytics를 사용하여 Power BI로 내보내기](../azure-monitor/app/export-power-bi.md)
* [속성 형식 및 값에 대한 자세한 데이터 모델 참조](../azure-monitor/app/export-data-model.md)
* [Application Insights에서 연속 내보내기](../azure-monitor/app/export-telemetry.md)

<!--Link references-->

[diagnostic]: ../azure-monitor/app/diagnostic-search.md
[export]: ../azure-monitor/app/export-telemetry.md
[metrics]: ../azure-monitor/essentials/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ../azure-monitor/app/app-insights-overview.md

