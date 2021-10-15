---
title: Azure Stream Analytics를 통한 소셜 미디어 분석
description: 이 문서에서는 트위터 클라이언트 API를 사용하여 소셜 미디어 분석에 Stream Analytics를 사용하는 방법을 설명합니다. 이벤트 생성부터 라이브 대시보드의 데이터에 이르는 단계별 지침이 포함되어 있습니다.
services: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: a2459966b364e2f69b0c0dded6ef5dc1de941dd3
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039109"
---
# <a name="social-media-analysis-with-azure-stream-analytics"></a>Azure Stream Analytics를 통한 소셜 미디어 분석

이 문서에서는 실시간 Twitter 이벤트를 Azure Event Hubs로 가져와서 소셜 미디어 실시간 감정 분석을 위한 감정 분석 솔루션을 구축하는 방법을 설명합니다. Azure Stream Analytics 쿼리를 작성하여 데이터를 분석하고 나중에 사용할 수 있도록 결과를 저장하거나 [Power BI](https://powerbi.com/) 대시보드를 만들어 실시간으로 정보를 제공합니다.

소셜 미디어 분석 도구는 조직에서 추세 항목을 이해하는 데 도움이 됩니다. 추세 항목은 소셜 미디어에 많은 게시물을 발생시키는 주제 및 입장입니다. 의견 마이닝이라는 감정 분석은 소셜 미디어 분석 도구를 사용하여 제품 또는 아이디어 등에 대한 자세를 결정합니다. 

실시간 Twitter 추세 분석은 분석 도구의 아주 좋은 예입니다. 특정 키워드(해시태그)를 수신 대기하고 피드에 대한 감정 분석을 개발할 수 있는 해시 태그 구독 모델을 사용하기 때문입니다.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>시나리오: 소셜 미디어 실시간 감정 분석

뉴스 미디어 웹 사이트를 보유하는 기업은 독자와 직접적으로 관련이 있는 사이트 콘텐츠를 부각시켜 경쟁 우위를 확보하는 데 관심이 있습니다. 이러한 기업은 Twitter 데이터에 대해 실시간으로 감정을 분석하여 독자와 관련된 항목에 대한 소셜 미디어 정보를 사용합니다.

Twitter에서 실시간으로 추세를 분석할 토픽을 식별하기 위해 기업에서는 주요 토픽에 대한 트윗 볼륨 및 감정에 대한 실시간 분석이 필요합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드에서는 Twitter에 연결하고 특정 해시태그(설정 가능)가 있는 트윗을 찾는 클라이언트 애플리케이션을 사용합니다. 애플리케이션을 실행하고 Azure Streaming Analytics를 사용하여 트윗을 분석하려면 다음이 필요합니다.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

* [Twitter](https://twitter.com) 계정입니다.

* Twitter 피드를 읽어오는 TwitterClientCore 애플리케이션입니다. 이 애플리케이션을 가져오려면 [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore)를 다운로드합니다.

* [.NET Core CLI](/dotnet/core/tools/?tabs=netcore2x) 버전 2.1.0을 설치합니다.

다음과 같은 솔루션 아키텍처를 구현할 것입니다.

   ![솔루션을 빌드하는 데 사용되는 여러 서비스 및 애플리케이션을 보여 주는 다이어그램입니다.](./media/stream-analytics-twitter-sentiment-analysis-trends/solution-diagram.png "솔루션 다이어그램")

## <a name="create-an-event-hub-for-streaming-input"></a>스트리밍 입력을 위한 이벤트 허브 만들기

애플리케이션 예제에서 이벤트를 생성하여 Azure Event Hub에 푸시합니다. Azure Event Hubs는 Stream Analytics를 위해 이벤트를 수집하는 기본 방법입니다. 자세한 내용은 [Azure Event Hubs 설명서](../event-hubs/event-hubs-about.md)를 참조하세요.

### <a name="create-an-event-hub-namespace-and-event-hub"></a>이벤트 허브 네임스페이스 및 이벤트 허브 만들기
이 섹션에서는 이벤트 허브 네임스페이스를 만든 후 이벤트 허브를 해당 네임스페이스에 추가합니다. 이벤트 허브 네임스페이스는 관련된 이벤트 버스 인스턴스를 논리적으로 그룹화하는 데 사용됩니다. 

1. Azure Portal에 로그인하고 **리소스 만들기** 를 선택합니다. 그런 다음, **Event Hubs** 를 검색하고 **만들기** 를 선택합니다.

2. **네임스페이스 만들기** 페이지에서 네임스페이스 이름을 입력합니다. 네임스페이스에 어떤 이름이든 사용할 수 있지만 이름은 URL에 대해 유효해야 하며 Azure 전체에서 고유해야 합니다. 
    
3. 가격 책정 계층 및 구독을 선택하고 리소스 그룹을 만들거나 선택합니다. 위치를 선택하고 **만들기** 를 선택합니다. 
 
4. 네임스페이스에서 배포를 완료했으면 리소스 그룹으로 이동한 후 Azure 리소스 목록에서 이벤트 허브 네임스페이스를 찾습니다. 

5. 새 네임스페이스에서 **+&nbsp;Event Hub** 를 선택합니다. 

6. 새 이벤트 허브의 이름을 *socialtwitter-eh* 로 지정합니다. 다른 이름을 사용할 수 있습니다. 이 경우 나중에 이름이 필요하기 때문에 메모해 둡니다. 이벤트 허브에 다른 옵션을 설정하지 않아도 됩니다.
 
7. **만들기** 를 선택합니다.

### <a name="grant-access-to-the-event-hub"></a>이벤트 허브에 대한 액세스 권한 부여

프로세스에서 이벤트 허브로 데이터를 보낼 수 있으려면 이벤트 허브에 액세스 권한을 허용하는 정책이 필요합니다. 액세스 정책은 권한 부여 정보를 포함하는 연결 문자열을 생성합니다.

1.  이벤트 허브 네임스페이스 왼쪽의 탐색 모음에서 **Event Hubs** 를 선택합니다(**엔터티** 섹션에 위치). 그런 다음, 방금 만든 이벤트 허브를 선택합니다.

2.  왼쪽의 탐색 모음에서 **설정** 아래에 있는 **공유 액세스 정책** 을 선택합니다.

    >[!NOTE]
    >이벤트 허브 네임스페이스 및 이벤트 허브에 대한 공유 액세스 정책 옵션이 있습니다. 전체 이벤트 허브 네임스페이스가 아니라 이벤트 허브의 컨텍스트에서 작업 중인지 확인합니다.

3.  액세스 정책 페이지에서 **+추가** 를 선택합니다. 그런 다음, **정책 이름** 에 대해 *socialtwitter-access* 를 입력하고 **관리** 확인란을 선택합니다.
 
4.  **만들기** 를 선택합니다.

5.  정책이 배포되었으면 공유 액세스 정책 목록에서 정책을 선택합니다.

6.  **Connection string primary-key** 로 레이블이 지정된 상자를 찾고 연결 문자열 옆의 복사 단추를 클릭합니다.
 
7.  연결 문자열을 텍스트 편집기에 붙여넣습니다. 몇 가지 편집을 수행한 후 다음 섹션에서 이 연결 문자열이 필요합니다.

   연결 문자열은 다음과 같습니다.
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   연결 문자열에는 세미콜론으로 구분된 여러 키-값 쌍이 포함되어 있습니다(`Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` 및 `EntityPath`).  

   > [!NOTE]
   > 보안을 위해 예제에 있는 연결 문자열의 일부가 제거되었습니다.

## <a name="configure-and-start-the-twitter-client-application"></a>Twitter 클라이언트 애플리케이션 구성 및 시작

클라이언트 애플리케이션이 Twitter에서 트윗 이벤트를 직접 가져옵니다. 이렇게 하려면 Twitter Streaming API를 호출할 수 있는 권한이 필요합니다. 해당 사용 권한을 구성하려면 고유한 자격 증명(예: OAuth 토큰)을 생성하는 애플리케이션을 Twitter에서 만듭니다. 그런 다음 API를 호출할 때 이러한 자격 증명을 사용하도록 클라이언트 애플리케이션을 구성할 수 있습니다. 

### <a name="create-a-twitter-application"></a>Twitter 애플리케이션 만들기
이 방법 가이드에 사용할 수 있는 Twitter 애플리케이션이 아직 없는 경우 만들 수 있습니다. Twitter 계정이 이미 있어야 합니다.

> [!NOTE]
> Twitter에서 애플리케이션을 만들고 키, 비밀 및 토큰을 가져오는 정확한 프로세스는 변경될 수 있습니다. 이러한 지침이 Twitter 사이트에 표시되는 내용과 일치하지 않으면 Twitter 개발자 설명서를 참조하세요.

1. 웹 브라우저에서 [개발자용 Twitter](https://developer.twitter.com/en/apps)로 이동하여 개발자 계정을 만든 다음, **앱 만들기** 를 선택합니다. Twitter 개발자 계정을 신청해야 한다는 메시지가 표시될 수 있습니다. 자유롭게 신청할 수 있으며 신청이 승인되면 확인 이메일이 수신됩니다. 개발자 계정이 승인될 때까지 며칠이 걸릴 수 있습니다.

   ![앱 단추 생성을 보여 주는 스크린샷.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter 애플리케이션 세부 정보")

2. **애플리케이션 만들기** 페이지에서 새 앱에 대한 세부 정보를 제공한 다음, **Twitter 애플리케이션 만들기** 를 선택합니다.

   ![앱의 값을 입력할 수 있는 앱 세부 정보 창을 보여 주는 스크린샷.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter 애플리케이션 세부 정보")

3. 애플리케이션 페이지에서 **키 및 토큰** 탭을 선택하고, **소비자 API 키** 및 **소비자 API 비밀 키** 값을 복사합니다. 또한 **액세스 토큰 및 액세스 토큰 비밀** 아래에서 **만들기** 를 선택하여 액세스 토큰을 생성합니다. **액세스 토큰** 및 **액세스 토큰 비밀** 에 대한 값을 복사합니다.

   Twitter 애플리케이션에 대해 검색한 값을 저장합니다. 나중에 이 값이 필요합니다.

> [!NOTE]
> Twitter 애플리케이션에 대한 키 및 비밀은 Twitter 계정에 대한 액세스를 제공합니다. 이 정보는 Twitter 암호처럼 중요하게 취급합니다. 예를 들어 이 정보를 다른 사람에게 제공하는 애플리케이션에 포함하지 마세요. 

### <a name="configure-the-client-application"></a>클라이언트 애플리케이션 구성

[Twitter 스트리밍 api](https://dev.twitter.com/streaming/overview) 를 사용 하 여 twitter 데이터에 연결 하 고 특정 항목 집합에 대 한 트 윗 이벤트를 수집 하는 클라이언트 응용 프로그램을 만들었습니다.

애플리케이션이 실행되기 전에 Twitter 키 및 이벤트 허브 연결 문자열과 같은 사용자의 특정 정보가 필요합니다.

1. 필수 조건에 나와 있는 것처럼 [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) 애플리케이션을 다운로드해야 합니다.

2. 텍스트 편집기를 사용하여 *App.config* 파일을 엽니다. `<appSettings>` 요소를 다음과 같이 변경합니다.

   * `oauth_consumer_key`를 Twitter 사용자 키(API 키)로 설정합니다. 
   * `oauth_consumer_secret`을 Twitter 사용자 암호(API 비밀 키)로 설정합니다.
   * `oauth_token`을 Twitter 액세스 토큰으로 설정합니다.
   * `oauth_token_secret`을 Twitter 액세스 토큰 암호로 설정합니다.
   * `EventHubNameConnectionString`을 연결 문자열로 설정합니다.
   * `EventHubName`을 이벤트 허브 이름(즉, 엔터티 경로 값)으로 설정합니다.

3. 명령줄을 열고 TwitterClientCore 앱이 있는 디렉터리로 이동합니다. 명령 `dotnet build`를 사용하여 프로젝트를 빌드합니다. `dotnet run` 명령을 사용하여 앱을 실행합니다. 앱은 트윗을 이벤트 허브로 보냅니다.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

이제 Twitter에서 실시간으로 트윗 이벤트가 스트리밍되며 이러한 이벤트를 실시간으로 분석하도록 Stream Analytics 작업을 설정할 수 있습니다.

1. Azure Portal에서 리소스 그룹으로 이동하여 **+ 추가** 를 선택합니다. 그런 다음, **Stream Analytics 작업** 을 검색하고 **만들기** 를 선택합니다.

2. 작업 이름을 `socialtwitter-sa-job`으로 지정하고 구독, 리소스 그룹 및 위치를 지정합니다.

    최상의 성능을 위해 동일한 지역에 작업 및 이벤트 허브를 배치하는 것이 좋으며 지역 간에 데이터를 전송하는 데 비용을 지불하지 않아도 됩니다.

3. **만들기** 를 선택합니다. 그런 다음, 배포가 완료되면 작업으로 이동합니다.

## <a name="specify-the-job-input"></a>작업 입력 지정

1. Stream Analytics 작업의 **작업 토폴로지** 아래에 위치한 왼쪽 메뉴에서 **입력** 을 선택합니다.

2. **+&nbsp;스트림 입력 추가** > **이벤트 허브** 를 선택합니다. 다음 정보로 **새 입력** 양식을 작성합니다.

   |**설정**  |**제안 값**  |**설명**  |
   |---------|---------|---------|
   |입력 별칭| *TwitterStream* | 입력의 별칭을 기입합니다. |
   |Subscription  | \<Your subscription\> |  사용할 Azure 구독을 선택합니다. |
   |이벤트 허브 네임스페이스 | *asa-twitter-eventhub* |
   |이벤트 허브 이름 | *socialtwitter-eh* | 기존 항목 사용을 선택합니다. 그런 다음, 직접 만든 이벤트 허브를 선택합니다.|
   |이벤트 압축 유형| GZip | 데이터 압축 유형입니다.|

   나머지 기본값을 그대로 적용하고 **저장** 을 선택합니다.

## <a name="specify-the-job-query"></a>작업 쿼리 지정

Stream Analytics는 변환을 설명하는 간단하고 선언적인 쿼리 모델을 지원합니다. 이 언어에 대한 자세한 내용은 [Azure Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)를 참조하세요. 이 방법 가이드에서는 Twitter 데이터에 대해 여러 쿼리를 작성하고 테스트하도록 도와줍니다.

항목 간의 멘션 수를 비교하기 위해 [연속 창](/stream-analytics-query/tumbling-window-azure-stream-analytics)을 활용하여 5초마다 항목별 멘션 수를 수집합니다.

1. **개요** 에서 쿼리 상자의 오른쪽 위에 있는 **쿼리 편집** 을 선택합니다. Azure에서는 작업에 대해 구성된 입력 및 출력을 나열하며, 데이터가 출력으로 전송됨에 따라 사용자가 입력 스트림을 변환하는 쿼리를 작성할 수 있습니다.

2. 쿼리 편집기에서 쿼리를 다음으로 변경합니다.

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. 메시지의 이벤트 데이터가 쿼리 아래의 **입력 미리 보기** 창에 표시됩니다. **보기** 가 **JSON** 으로 설정 되었는지 확인합니다. 데이터가 표시되지 않는 경우, 데이터 생성기가 이벤트 허브로 이벤트를 전송하고 입력에 대한 압축 유형으로 **GZip** 을 선택했는지 확인합니다.

4. **쿼리 테스트** 를 선택하고 쿼리 아래의 **테스트 결과** 창에서 결과를 확인합니다.

5. 코드 편집기의 쿼리를 다음으로 변경한 후 **쿼리 테스트** 를 선택합니다.

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. 이 쿼리는 키워드 *Azure* 를 포함하는 모든 트윗을 반환합니다.

## <a name="create-an-output-sink"></a>출력 싱크 만들기

이제 이벤트 스트림, 이벤트를 수집할 이벤트 허브 입력 및 스트림 변환을 수행할 쿼리를 정의했습니다. 마지막 단계는 작업에 대한 출력 싱크를 정의하는 것입니다.  

이 방법 가이드에서는 작업 쿼리에서 집계된 트윗 이벤트를 Azure Blob Storage에 기록합니다.  또한 특정 애플리케이션 요구 사항에 따라 Azure SQL Database, Azure Table Storage, Event Hubs 또는 Power BI에 결과를 푸시할 수도 있습니다.

## <a name="specify-the-job-output"></a>작업 출력 지정

1. 왼쪽 탐색 메뉴의 **작업 토폴로지** 섹션에서 **출력** 을 선택합니다. 

2. **출력** 페이지에서 다음과 같이 **+&nbsp;추가** 및 **Blob Storage/Data Lake Storage Gen2** 를 클릭합니다.

   * **출력 별칭**: 이름 `TwitterStream-Output`을 사용합니다. 
   * **가져오기 옵션**: **구독에서 스토리지를 선택** 을 선택합니다.
   * **Storage 계정**. 사용자의 스토리지 계정을 선택합니다.
   * **컨테이너**. **새로 만들기** 를 선택하고 `socialtwitter`을 입력합니다.
   
4. **저장** 을 선택합니다.   

## <a name="start-the-job"></a>작업 시작

작업 입력, 쿼리 및 출력이 지정됩니다. Stream Analytics 작업을 시작할 준비가 되었습니다.

1. TwitterClientCore 애플리케이션이 실행되고 있는지 확인합니다. 

2. 작업 개요에서 **시작** 을 선택합니다.

3. **작업 시작** 페이지의 **작업 출력 시작 시간** 에서 **지금** 을 선택한 후 **시작** 을 선택합니다.

## <a name="get-support"></a>지원 받기
추가 지원이 필요한 경우 [Azure Stream Analytics용 Microsoft Q&A 질문 페이지](/answers/topics/azure-stream-analytics.html)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](/rest/api/streamanalytics/)
