---
title: 데이터 수집기 API를 사용 하 여 데이터 파이프라인 만들기
description: REST API를 호출할 수 있는 모든 클라이언트에서 Azure Monitor HTTP 데이터 수집기 API를 사용하여 POST JSON 데이터를 Log Analytics 작업 영역에 추가할 수 있습니다. 이 아티클에서는 자동화된 방식으로 파일에 저장된 데이터를 업로드하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: 6f862f6f467d491d1aeb992a0e918244bde40a0d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051404"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>데이터 수집기 API를 사용하여 데이터 파이프라인 만들기

[Azure Monitor 데이터 수집기 API](data-collector-api.md)를 통해 모든 사용자 지정 로그 데이터를 Azure Monitor의 Log Analytics 작업 영역으로 가져올 수 있습니다. 유일한 요구 사항은 데이터가 JSON 형식이고 30MB 이하의 세그먼트로 분할되어야 하는 것입니다. 다양한 방식으로 애플리케이션에서 직접 전송된 데이터에서 일회성 임시 업로드로 연결할 수 있는 완전히 유연한 메커니즘입니다. 이 아티클는 정기적이고 자동으로 파일에 저장된 데이터를 업로드해야 하는 일반적인 시나리오에 대한 일부 시작점을 간략하게 설명합니다. 여기에 표시된 파이프라인이 가장 효율적 또는 최적화되지 않지만 고유한 프로덕션 파이프라인을 빌드하기 위한 시작점으로 사용하려고 합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>예제 문제
이 아티클의 나머지 부분에서는 Application Insights에서 페이지 보기 데이터를 검사합니다. 이 가상 시나리오에서는 Application Insights SDK에서 기본적으로 수집한 지리적 정보를 전 세계의 모든 국가/지역에 대 한 인구를 포함 하는 사용자 지정 데이터로 상관 관계를 지정 하려고 합니다. 가장 많은 마케팅 금액을 지출 해야 하는 위치를 식별 하는 것이 좋습니다. 

이를 위해 [UN World Population Prospects](https://esa.un.org/unpd/wpp/)와 같은 공용 데이터 원본을 사용합니다. 데이터는 다음과 같은 간단한 스키마를 제공합니다.

![단순한 예제 스키마](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

이 예제에서는 즉시 사용할 수 있는 최신 연도의 데이터를 사용하여 새 파일을 업로드한다고 가정합니다.

## <a name="general-design"></a>일반 디자인
클래식 ETL 형식 논리를 사용하여 파이프라인을 디자인합니다. 아키텍처는 다음과 같습니다.

![데이터 컬렉션 파이프라인 아키텍처](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

이 아티클에서는 데이터를 만들거나 [Azure Blob Storage 계정에 업로드](../../storage/blobs/storage-upload-process-images.md)하는 방법을 다루지 않습니다. 대신 새 파일이 Blob에 업로드되는 즉시 흐름을 선택합니다. 여기에서부터:

1. 프로세스는 새 데이터가 업로드되었는지 검색합니다.  이 예에서는 [Azure 논리 앱](../../logic-apps/logic-apps-overview.md)을 사용하며, 이 앱은 Blob에 업로드되는 새 데이터를 검색하는 트리거를 사용할 수 있습니다.

2. 프로세서는 이 새로운 데이터를 읽고 이를 Azure Monitor에 필요한 형식인 JSON으로 변환합니다.이 예제에서는 처리 코드를 실행하기 위한 간단하고 비용 효율적인 방법으로 [Azure Function](../../azure-functions/functions-overview.md)을 사용합니다. 함수는 새 데이터를 검색 하는 데 사용한 것과 동일한 논리 앱에 의해 시작 됩니다.

3. 마지막으로, JSON 개체를 사용할 수 있다면 Azure Monitor로 전송됩니다. 동일한 논리 앱은 Log Analytics 데이터 수집기 작업에서 기본 제공 기능을 사용하여 데이터를 Azure Monitor로 보냅니다.

Blob Storage, 논리 앱 또는 Azure Function의 자세한 설정을 이 아티클에서 간략히 설명하지 않았지만 자세한 지침은 특정 제품 페이지에서 지원됩니다.

이 파이프라인을 모니터링하려면 Application Insights를 사용하여 Azure Function [세부 정보(여기)](../../azure-functions/functions-monitoring.md)를 모니터링하고, Azure Monitor를 사용하여 논리 앱 [세부 정보(여기)](../../logic-apps/monitor-logic-apps-log-analytics.md)를 모니터링합니다. 

## <a name="setting-up-the-pipeline"></a>파이프라인 설정
파이프라인을 설정하려면 먼저 Blob 컨테이너를 만들고 구성했는지 확인합니다. 마찬가지로 데이터를 전송하려는 Log Analytics 작업 영역을 만들었는지 확인합니다.

## <a name="ingesting-json-data"></a>JSON 데이터 수집
논리 앱을 사용하여 간단히 JSON 데이터를 수집하고 변환이 발생하지 않았으므로 단일 논리 앱에서 전체 파이프라인을 포함할 수 있습니다. Blob 컨테이너와 Log Analytics 작업 영역을 모두 구성하면 새 논리 앱을 만들고 다음과 같이 구성합니다.

![Logic Apps 워크플로 예제](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

논리 앱을 저장하고 테스트를 진행합니다.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>XML, CSV 또는 다른 형식의 데이터를 수집합니다.
현재 논리 앱에는 쉽게 XML, CSV 또는 다른 형식을 JSON 형식으로 변환할 기본 제공 기능이 없습니다. 따라서 다른 수단을 사용하여 이 변환을 완료해야 합니다. 이 문서에서는 Azure Functions의 서버리스 컴퓨팅 기능을 매우 간단하고 저렴한 방법으로 사용합니다. 

이 예제에서는 CSV 파일을 구문 분석하지만 다른 파일 형식은 마찬가지로 처리될 수 있습니다. 특정 데이터 형식에 대해 올바른 논리를 반영하도록 Azure Function의 역직렬화 부분을 수정하면 됩니다.

1.  메시지가 표시되는 경우 Function 런타임 v1 및 사용 기반을 사용하여 새 Azure Function을 만듭니다.  요구한 대로 바인딩을 구성하는 시작점으로 C#에서 대상으로 지정된 **HTTP 트리거** 템플릿을 선택합니다. 
2.  오른쪽 창의 **파일 보기** 탭에서 **project.json** 이라는 새 파일을 만들고, 사용 중인 NuGet 패키지의 다음 코드를 붙여넣습니다.

    ![Azure Functions 예제 프로젝트](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. 오른쪽 창에서 **run.csx** 로 전환하고, 기본 코드를 다음으로 바꿉니다. 

    >[!NOTE]
    >프로젝트의 경우 레코드 모델("PopulationRecord" 클래스)을 고유한 데이터 스키마로 바꿔야 합니다.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. 함수를 저장합니다.
5. 코드가 올바르게 작동하는지 확인하기 위해 함수를 테스트합니다. 오른쪽 창에서 **테스트** 탭으로 전환하여 테스트를 다음과 같이 구성합니다. 샘플 데이터를 포함한 Blob에 대한 링크를 **요청 본문** 텍스트 상자에 배치합니다. **실행** 을 클릭한 후에 **출력** 상자에 JSON 출력이 표시됩니다.

    ![함수 앱 테스트 코드](./media/create-pipeline-datacollector-api/functions-test-01.png)

이제 돌아가서 이전에 빌드하기 시작한 논리 앱이 JSON 형식으로 수집되고 변환된 데이터를 포함하도록 수정해야 합니다.  보기 디자이너를 사용하여 다음과 같이 구성한 다음, 논리 앱을 저장합니다.

![Logic Apps 워크플로 전체 예제](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline&quot;></a>파이프라인 테스트
이제 새 파일을 이전에 구성한 Blob에 업로드하고 논리 앱에서 모니터링할 수 있습니다. 곧 논리 앱 시작의 새 인스턴스를 확인하고 Azure Function에 대해 호출한 다음, Azure Monitor에 데이터를 성공적으로 보내야 합니다. 

>[!NOTE]
>새 데이터 형식을 처음으로 보내는 경우 데이터가 Azure Monitor에 표시되는 데 최대 30분이 걸릴 수 있습니다.


## <a name=&quot;correlating-with-other-data-in-log-analytics-and-application-insights&quot;></a>Log Analytics 및 Application Insights의 다른 데이터와 상관 관계 만들기
사용자 지정 데이터 원본에서 수집한 인구 데이터를 사용하여 Application Insights 페이지 보기 데이터의 상관 관계를 만들려면 Application Insights 분석 창 또는 Log Analytics 작업 영역에서 다음 쿼리를 실행합니다.

``` KQL
app(&quot;fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

출력은 두 개의 데이터 원본이 이제 조인되었음을 표시합니다.  

![검색 결과 예제에서 조인되지 않은 데이터의 상관 관계 만들기](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>프로덕션 파이프라인에 대해 제안된 개선 사항
이 아티클에서는 작업 프로토타입을 제공합니다. 여기에서 논리는 진정한 프로덕션 품질 솔루션에 적용할 수 있습니다. 이러한 프로덕션 품질 솔루션의 경우 다음과 같은 향상된 기능을 사용하는 것이 좋습니다.

* 오류 처리를 추가하고 논리 앱 및 함수에서 논리를 다시 시도하세요.
* 30MB/단일 Log Analytics 수집 API 호출 제한을 초과하지 않도록 하는 논리를 추가합니다. 필요한 경우 작은 세그먼트로 데이터를 분할합니다.
* Blob Storage에 대한 정리 정책을 설정합니다. Log Analytics 작업 영역에 성공적으로 보내면 보관을 위해 사용할 수 있는 원시 데이터를 유지하려는 경우가 아니면 계속 저장할 필요가 없습니다. 
* 모니터링을 전체 파이프라인에 사용하도록 설정했는지 확인하여 추적 지점 및 경고를 적절하게 추가합니다.
* 원본 제어를 활용하여 함수 및 논리 앱에 대한 코드를 관리합니다.
* 적절한 변경 관리 정책을 따르는지 확인하고 스키마가 변경된 경우 함수 및 논리 앱을 적절하게 수정합니다.
* 여러 데이터 형식을 업로드하는 경우 Blob 컨테이너 내의 개별 폴더로 분리하고 논리를 만들어서 데이터 형식을 기반으로 해당 논리를 적용합니다. 


## <a name="next-steps"></a>다음 단계
REST API 클라이언트에서 Log Analytics 작업 영역으로 데이터를 작성하는 [데이터 수집기 API](data-collector-api.md)에 대해 자세히 알아봅니다.
