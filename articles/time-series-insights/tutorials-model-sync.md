---
title: Azure Digital Twins와 Time Series Insights 간의 모델 동기화 | Microsoft Docs
description: ADT의 자산 모델을 Azure TSI의 자산 모델로 변환하는 데 사용되는 모범 사례 및 도구
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/19/2021
ms.custom: dpalled
ms.openlocfilehash: 7e7c38001578caa5e23b6c0458f70171d8dcf630
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113564503"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Azure Digital Twins와 Time Series Insights Gen2 간의 모델 동기화

이 문서에서는 ADT(Azure Digital Twins)의 자산 모델을 TSI(Azure Time Series Insights)의 자산 모델로 변환하는 데 사용되는 모범 사례 및 도구에 대해 설명합니다.  이 문서는 Azure Time Series Insights와 Azure Digital Twins의 통합을 설명하는 두 부분으로 구성된 자습서 시리즈의 두 번째 부분입니다. Azure Digital Twins를 Time Series Insights에 통합하면 Digital Twins의 원격 분석 및 계산된 속성에 대한 기록을 보관하고 추적할 수 있습니다. 이 자습서 시리즈는 Azure Digital Twins에 Time Series Insights를 통합하는 개발자를 대상으로 합니다. 1부에서는 [Azure Digital Twins의 실제 시계열 데이터를 Time Series Insights에 전달하는 데이터 파이프라인을 설정하는 방법](../digital-twins/how-to-integrate-time-series-insights.md)을 설명하고, 자습서 시리즈의 두 번째 부분에서는 Azure Digital Twins와 Time Series Insights 간의 자산 모델 동기화에 대해 설명합니다. 이 자습서에서는 TS ID(시계열 ID)에 대한 명명 규칙을 선택 및 설정하고 TSM(시계열 모델)에서 수동으로 계층을 설정하는 모범 사례에 대해 설명합니다.

## <a name="choosing-a-time-series-id"></a>시계열 ID 선택

시계열 ID는 Time Series Insights 자산을 식별하는 데 사용되는 고유 식별자입니다. 시계열 데이터(시간-값 쌍 필드의 원격 분석)는 TSID에 나열된 변수를 사용하여 표현됩니다. Azure Digital Twins에서 트윈 속성과 원격 분석은 각각 트윈으로 생성된 트윈 및 측정의 상태를 나타내는 데 사용됩니다. TSM의 현재 디자인에 따라, TSID는 고유해야 합니다. Azure Digital Twins에서 트윈의 트윈 ID를 사용하거나 속성 또는 원격 분석 이름과 함께 사용하면 항상 TSM에서 고유한 TS ID를 만듭니다. 일반적인 경우에는 **_`<Twin ID>`_** 가 TSID가 되며 속성 및 원격 분석 이름은 TSM의 변수가 됩니다. 그러나 **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** 와 같은 복합 키 형식을 사용하여 Time Series Insights의 자산 계층을 평면화하는 것이 좋은 사용 사례도 있습니다. 해당 사례를 설명하는 예시를 살펴보겠습니다. 트윈으로 모델링되고 트윈 ID가 Room22인 건물 내 공간을 생각해 보겠습니다. TSM에서 온도 설정 속성은 **_TSID Room22_TempSetting_** 으로 변환되고, 온도 측정값은 **_Room22_TempMea_** 로 변환됩니다.

[![시계열 ID 선택](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>시계열 컨텍스트화

Time Series Insights 데이터의 컨텍스트화(특성상 주로 공간적)는 자산 계층 구조를 통해 이루어지며, Time Series Insights 탐색기에서 트리 뷰를 통해 데이터를 쉽게 탐색하는 데 사용됩니다. Time Series Insights의 TSM(시계열 모델)을 사용하여 시계열 형식 및 계층을 정의합니다. TSM의 형식은 변수를 정의하는 데 도움이 되지만 계층 수준 및 인스턴스 필드 값은 Time Series Insights 탐색기에서 트리 뷰를 구성하는 데 사용됩니다. TSM에 대한 자세한 내용은 [온라인 Time Series Insights 설명서](./concepts-model-overview.md)를 참조하세요.

Azure Digital Twins에서 자산 간의 연결은 트윈 관계를 사용하여 표현됩니다. 트윈 관계는 단순히 연결된 자산의 그래프입니다. 그러나 Time Series Insight에서 자산 간의 관계는 본질적으로 계층적입니다. 즉, 자산은 부모-자식과 같은 관계를 공유하고 트리 구조를 사용하여 표시됩니다. Azure Digital Twins 관계 정보를 Time Series Insights 계층 구조로 변환하려면 Azure Digital Twins에서 관련 계층 관계를 선택해야 합니다. Azure Digital Twins는 DTDL(Digital Twin Definition Language)이라는 개방형 표준 모델링 언어를 사용합니다. DTDL에서는 모델이 JSON-LD라는 JSON의 변형을 사용하여 설명됩니다. 사양에 대한 자세한 내용은 [DTDL 설명서](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)를 참조하세요.

[![자산 간 연결](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Azure Digital Twins의 그래프 표시를 Time Series Insights의 트리 구조로 변환

자습서의 다음 섹션에서는 Azure Digital Twins의 그래프 구조를 Time Series Insights의 트리 구조로 수동으로 변환하는 몇 가지 핵심 시나리오를 소개합니다.

샘플 시스템: 이 자습서에서는 다음 예제를 사용하여 아래에 언급된 개념을 설명합니다. 하나의 층과 두 개의 방이 있는 단순한 가상의 빌딩 관리 시스템입니다. 여기에는 온도 조절 장치가 각 방에 하나씩, 그리고 층에서 공용으로 사용하는 또 다른 온도 조절 장치 하나, 총 3개의 온도 조절 장치가 있습니다. 또한 방 간의 파이프 연결을 통해 Room21에서 Room22로의 급수 흐름을 측정하는 급수 흐름 측정기도 있습니다. 트윈 간의 공간 관계에는 두 유형의 관계가 모두 존재합니다.

1. 가장 일반적인 관계는 계층 관계입니다. 예를 들면 Building40-> Floor01-> FloorTS *-> 온도입니다.
1. 그리 일반적이지 않은 관계는 순환 관계입니다. 예를 들어 Building40에서 시작하여 FlowMtr은 서로 다른 두 경로를 통해 추적할 수 있습니다.

   1. Building40 -> Floor01 -> Room21 -> FlowMtr* -> Flow
   1. Building40 -> Floor01 -> Room22 -> FlowMtr* -> Flow  
      여기서 ‘Flow’는 Room21와 Room22 간의 물 흐름을 측정하는 실제 원격 분석입니다.

> [!Note]
>
> `*` FloorTS는 FloorThermoStat를 나타내고 FlowMtr은 흐름 측정기를 나타내며 일반적으로 TSID으로 선택됩니다. 온도 및 흐름은 Time Series Insights에서 변수라고 하는 원시 원격 분석입니다.

여러 분기에서 하나의 자산을 표현할 수 없는 Time Series Insights의 현재 제한 사항을 고려하여, 다음 섹션에서는 Time Series Insights의 계층 및 순환 관계 모델링에 대해 설명합니다.

## <a name="case-1-hierarchical-parent-child-relationship"></a>사례 1: 계층(부모-자식) 관계

가장 일반적인 트윈 간 관계 유형입니다. 다음 그림에서는 순수 부모-자식 관계를 모델링하는 방법에 대해 설명합니다. 인스턴스 필드 및 TSID는 아래와 같이 트윈 ID에서 파생됩니다. 인스턴스 필드는 Time Series Insights 탐색기를 사용하여 수동으로 업데이트할 수 있지만 아래의 ‘API를 사용하여 인스턴스 필드 업데이트’ 섹션에서는 Azure 함수를 사용하여 Azure Digital Twins에서 모델 변경 사항을 수신하고 Time Series Insights에서 인스턴스 필드를 업데이트하는 방법을 설명합니다.

[![트윈 ID 매핑](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![트윈 ID 매핑 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>사례 2: 순환 관계

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Azure Digital Twins 순환 관계와 Time Series Insights의 단일 계층 관계

TSID는 고유해야 하며 하나의 계층에서만 나타날 수 있기 때문에, 이 사례는 트윈 _‘Room21’_ 바로 아래에 있는 _‘Flow’_ 라는 원격 분석이 있는 _‘FlowMtr’_ 를 나타냅니다. 나중에 Time Series Insights가 TSM에서 여러 개의 시간 계열 표현을 지원할 수 있게 되면 원격 분석 _‘Flow’_ 는 _‘Room 21’_ 및 _‘Room 22’_ 아래에 표시됩니다.

다음 스크린샷에서는 Azure Digital Twins의 트윈 ID를 TSM의 인스턴스 필드와 Time Series Insights 결과 계층 구조에 수동으로 매핑하는 방법을 보여 줍니다.

[![Azure Digital Twins의 트윈 ID 매핑](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>중복 항목을 사용하는 Time Series Insights의 여러 계층에 대한 Azure Digital Twins의 순환 관계

자습서의 1부에서는 클라이언트 프로그램(Azure 함수)을 통해 IoT Hub 또는 다른 이벤트 원본에서 원격 분석 데이터를 Azure Digital Twins로 전송하는 방법을 설명합니다. 이 방식은 동일한 클라이언트 프로그램을 사용하여 부모 트윈의 관련 속성을 업데이트하도록 제안합니다. 지정된 예제에서 IoT Hub에서 FlowMtr 원격 분석을 읽고 FlowMtr 트윈에서 ‘Flow’ 속성을 업데이트하는 동안 프로그램은 원본의 가능한 모든 부모 트윈에서 해당 속성을 업데이트할 수도 있습니다. 이 예제에서는 Room21의 ‘outflowmea’(‘outflow’ 관계를 사용하여 식별) 속성과 Room22의 ‘inflowmea’ 속성입니다.  아래 스크린샷은 Time Series Insights 탐색기의 최종 사용자 환경을 보여 줍니다. 이 접근 방식을 사용하면 데이터 중복이 발생함에 유의해야 합니다.

[![Time Series Insights 탐색기](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

아래 코드 조각에서는 클라이언트 애플리케이션에서 Azure Digital Twins API를 사용하여 트윈 관계를 탐색하는 방법을 보여 줍니다.

> [!Note]
>
> 이 코드 조각 예제에서는 독자가 자습서 [1부](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app) 내용을 잘 이해하고 있고 ‘ProcessHubToDTEvents’ 함수 내에서 이 코드 변경이 수행되었다고 가정합니다.

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>API를 사용하여 인스턴스 필드 업데이트

본 자습서 섹션에서는 Time Series Insights 모델 API를 사용하여 프로그래밍 방식으로 Azure Digital Twins에서의 트윈 생성, 삭제 또는 트윈 간 관계 변경과 같은 모델 변경 사항을 수신하고 인스턴스 필드 및 계층을 업데이트하는 방법을 설명합니다. Time Series Insights 모델 업데이트 방식은 일반적으로 Azure 함수를 통해 구현됩니다. Azure Digital Twins에서 트윈 추가 또는 삭제와 같은 이벤트 알림은 라우팅된 다운스트림 서비스(예: Azure 함수에 공급될 수 있는 Event Hubs)가 될 수 있습니다. 이벤트 라우팅 및 필터링에 대한 자세한 내용은 [여기](../digital-twins/how-to-manage-routes-portal.md)를 참조하세요.  이 섹션의 나머지 부분에서는 Azure 함수에서 Time Series Insights 모델 API를 사용하여 Azure Digital Twins에서 트윈 추가(모델 변경의 한 가지 유형)에 따라 Time Series Insights 모델을 업데이트하는 방법을 설명합니다.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>트윈 추가 이벤트 알림 수신 및 식별

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Time Series Insights 클라이언트 만들기 및 인스턴스 세부 정보 추가

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>인스턴스에 계층 정보 적용

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>다음 단계

자습서 시리즈 중 세 번째는 Time Series Insights API를 사용하여 Azure Digital Twins에서 기록 데이터를 쿼리하는 방법을 안내합니다. 이 섹션은 현재 작성 중이며 작성이 완료되면 업데이트될 예정입니다. 그 동안에는 [Time Series Insights 데이터 쿼리 API 설명서](./concepts-query-overview.md)를 참조하세요.
