---
title: Application Insights에서 원격 분석 연속 내보내기 | Microsoft Docs
description: Microsoft Azure에서 스토리지에 진단 및 사용량 데이터를 내보내고 여기에서 다운로드합니다.
ms.topic: conceptual
ms.date: 02/19/2021
ms.custom: references_regions
ms.openlocfilehash: 1d4a9a6e60d39b01ea1ebe99289c9bb3a1c50c2b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233529"
---
# <a name="export-telemetry-from-application-insights"></a>Application Insights에서 원격 분석 내보내기
표준 보존 기간 보다 오랫동안 원격 분석을 유지하시겠습니까? 또는 일부 특수한 방식으로 처리하시겠습니까? 그렇다면 연속 내보내기가 적합합니다. Application Insights 포털에 표시되는 이벤트는 JSON 형식으로 Microsoft Azure에서 스토리지로 내보낼 수 있습니다. 여기에서 데이터를 다운로드하고 프로세스에 필요한 모든 코드를 작성할 수 있습니다.  

> [!IMPORTANT]
> 연속 내보내기는 더 이상 사용되지 않습니다. [작업 영역 기반 Application Insights 리소스로 마이그레이션하여](convert-classic-resource.md) 원격 분석 내보내기에 대한 [진단 설정](#diagnostic-settings-based-export)을 사용합니다.

> [!NOTE]
> 연속 내보내기는 클래식 Application Insights 리소스에 대해서만 지원됩니다. [작업 영역 기반 Application Insights 리소스](./create-workspace-resource.md)는 [진단 설정](./create-workspace-resource.md#export-telemetry)을 사용해야 합니다.
>

연속 내보내기를 설정하기 전에 고려하려는 일부 대안이 있습니다.

* 메트릭 또는 검색 탭 맨 위에 있는 내보내기 단추를 사용하면 테이블 및 차트를 Excel 스프레드시트로 전송할 수 있습니다.

* [분석](../logs/log-query-overview.md)은 원격 분석을 위한 강력한 쿼리 언어를 제공합니다. 결과를 내보낼 수도 있습니다.
* [Power BI에서 데이터를 탐색](./export-power-bi.md)하려는 경우 연속 내보내기를 사용하지 않고 탐색할 수 있습니다.
* [데이터 액세스 REST API](https://dev.applicationinsights.io/)를 사용하여 원격 분석에 프로그래밍 방식으로 액세스할 수 있습니다.
* 또한 [PowerShell을 통해 연속 내보내기](/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport) 설정에 액세스할 수도 있습니다.

연속 내보내기를 통해 스토리지에 데이터를 복사한 후에도(원하는 기간 동안 스토리지에 유지할 수 있음) 일반적인 [보존 기간](./data-retention-privacy.md) 동안 Application Insights를 계속 사용할 수 있습니다.

## <a name="supported-regions"></a>지원되는 지역

연속 내보내기는 다음 지역에서 지원됩니다.

* 동남아시아
* 캐나다 중부
* 인도 중부
* 북유럽
* 영국 남부
* 오스트레일리아 동부
* 일본 동부
* 한국 중부
* 프랑스 중부
* 동아시아
* 미국 서부
* 미국 중부
* 미국 동부 2
* 미국 중남부
* 미국 서부 2
* 남아프리카 북부
* 미국 중북부
* 브라질 남부
* 스위스 북부
* 오스트레일리아 남동부
* 영국 서부
* 독일 중서부
* 스위스 서부
* 오스트레일리아 중부 2
* 아랍에미리트 중부
* 브라질 남동부
* 오스트레일리아 중부
* 아랍에미리트 북부
* 노르웨이 동부
* 일본 서부

> [!NOTE]
> 내보내기가 2021년 2월 23일 이전에 구성된 경우, 연속 내보내기는 **미국 동부** 및 **서유럽** 의 애플리케이션에서 계속 작동합니다. 애플리케이션을 만든 시기에 관계 없이 **미국 동부** 또는 **서유럽** 의 애플리케이션에서는 새 연속 내보내기 규칙을 구성할 수 없습니다.

## <a name="continuous-export-advanced-storage-configuration"></a>연속 내보내기 고급 스토리지 구성

연속 내보내기는 다음 Azure 스토리지 기능/구성을 **지원하지 않습니다**.

* Azure Blob 스토리지와 함께 [VNET/Azure Storage 방화벽](../../storage/common/storage-network-security.md) 사용.

* [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

## <a name="create-a-continuous-export"></a><a name="setup"></a> 연속 내보내기 만들기

> [!NOTE]
> 애플리케이션은 하루에 3TB 이상의 데이터를 내보낼 수 없습니다. 하루에 3TB를 초과해서 내보내면 내보내기가 비활성화됩니다. 제한 없이 내보내려면 [진단 설정 기반 내보내기](#diagnostic-settings-based-export)를 사용합니다.

1. 왼쪽에서 구성 중인 앱에 대한 Application Insights 리소스에서 연속 내보내기를 열고 **추가** 를 선택합니다.

2. 내보낼 원격 분석 데이터 유형을 선택합니다.

3. 데이터를 저장할 [Azure Storage 계정](../../storage/common/storage-introduction.md)을 만들거나 선택합니다. 스토리지 가격 책정 옵션에 대한 자세한 내용은 [공식 책정 가격 페이지](https://azure.microsoft.com/pricing/details/storage/)를 참조하세요.

     추가, 내보내기 대상, Storage 계정을 클릭한 다음, 새 저장소를 만들거나 기존 저장소를 선택합니다.

    > [!Warning]
    > 기본적으로 스토리지 위치는 Application Insights 리소스와 동일한 지역으로 설정됩니다. 다른 지역에 저장하는 경우 전송 요금이 발생할 수 있습니다.

4. 스토리지에서 컨테이너를 만들거나 선택합니다.

> [!NOTE]
> 내보내기를 만들면 새로 수집된 데이터가 Azure Blob Storage로 이동하기 시작합니다. 연속 내보내기는 연속 내보내기가 활성화된 후 생성/수집된 새 원격 분석만 전송 합니다. 연속 내보내기를 사용하도록 설정하기 전에 있던 데이터는 내보내지 않으며, 연속 내보내기를 사용하여 이전에 만든 데이터를 소급하여 내보낼 수 있는 방법은 지원되지 않습니다.

데이터가 스토리지에 표시되려면 1시간 정도 지연될 수 있습니다.

첫 번째 내보내기가 완료되면 Azure Blob 스토리지 컨테이너에서 다음과 유사한 구조를 찾을 수 있습니다. (이는 수집하는 데이터에 따라 달라집니다.)

|속성 | Description |
|:----|:------|
| [가용성](export-data-model.md#availability) | [가용성 웹 테스트](./monitor-web-app-availability.md)를 보고합니다.  |
| [이벤트](export-data-model.md#events) | [TrackEvent()](./api-custom-events-metrics.md#trackevent)에 의해 생성된 사용자 지정 이벤트입니다. 
| [예외](export-data-model.md#exceptions) |서버 및 브라우저의 [예외](./asp-net-exceptions.md) 를 보고합니다.
| [메시지](export-data-model.md#trace-messages) | [TrackTrace](./api-custom-events-metrics.md#tracktrace) 및 [로깅 어댑터](./asp-net-trace-logs.md)에서 전송합니다.
| [Metrics](export-data-model.md#metrics)(메트릭) | 메트릭 API 호출로 생성됩니다.
| [PerformanceCounters](export-data-model.md) | Application Insights에서 수집한 성능 카운터입니다.
| [요청](export-data-model.md#requests)| [TrackRequest](./api-custom-events-metrics.md#trackrequest)에서 전송합니다. 표준 모듈이 서버에서 측정된 서버 응답 시간을 보고하는 데 사용됩니다.| 

### <a name="to-edit-continuous-export"></a>연속 내보내기를 편집하려면

연속 내보내기를 클릭하고 편집할 스토리지 계정을 선택합니다.

### <a name="to-stop-continuous-export"></a>연속 내보내기를 중지하려면

스트림을 중지하려면 사용 안 함을 클릭합니다. 사용을 다시 클릭하면 내보내기는 새 데이터로 다시 시작합니다. 내보내기를 사용하지 않는 동안 포털에 도착한 데이터는 받지 못합니다.

내보내기를 영구적으로 중지하려면 삭제합니다. 이렇게 해도 스토리지에서 데이터를 삭제하지 않습니다.

### <a name="cant-add-or-change-an-export"></a>내보내기를 추가 또는 변경할 수 있나요?
* 내보내기를 추가 또는 변경하려면 소유자, 기여자 또는 Application Insights 기여자 액세스 권한이 필요합니다. [역할에 대해 알아봅니다][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a> 어떤 이벤트를 얻나요?
클라이언트 IP 주소에서 계산하는 위치 데이터를 추가한다는 점을 제외하고 내보낸 데이터는 애플리케이션에서 수신하는 원시 원격 분석입니다.

[샘플링](./sampling.md) 에서 무시된 데이터는 내보낸 데이터에 포함되지 않습니다.

계산된 다른 메트릭은 포함되지 않습니다. 예를 들어 평균 CPU 사용률을 내보내지 않지만 평균이 계산된 곳에서 원시 원격 분석을 내보냅니다.

데이터에는 설정한 [가용성 웹 테스트](./monitor-web-app-availability.md)의 결과도 포함됩니다.

> [!NOTE]
> **샘플링** 애플리케이션에서 많은 양의 데이터를 전송하는 경우 샘플링 기능이 작동하여 생성된 원격 분석의 일부만 보낼 수 있습니다. [샘플링에 대해 자세히 알아봅니다.](./sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a> 데이터 검사
포털에서 직접 스토리지를 검사할 수 있습니다. 맨 왼쪽 메뉴에서 "Azure 서비스"라고 표시된 맨위에 있는 홈을 클릭하여 **Storage 계정**, 스토리지 계정 이름을 차례로 선택하고, 개요 페이지의 서비스에서 **Blob** 을 선택하고, 마지막으로 컨테이너 이름을 선택합니다.

Visual Studio에서 Azure Storage를 검사하려면 **보기**, **클라우드 탐색기** 를 차례로 엽니다. (해당 메뉴 명령이 없는 경우 Azure SDK를 설치해야 합니다. **새 프로젝트** 대화 상자를 열고, 시각적 개체 C#/클라우드를 확장하고 **.NET용 Microsoft Azure SDK 가져오기** 를 선택합니다.)

blob 저장소를 열면 blob 파일 집합이 포함된 컨테이너가 보입니다. Application Insights 리소스 이름, 계측 키, 원격 분석 유형/날짜/시간에서 파생된 각 파일의 URI입니다. 리소스 이름은 모두 소문자이고 계측 키에서 대시를 생략합니다.

![적합한 도구를 사용하여 blob 저장소 검사](./media/export-telemetry/04-data.png)

날짜 및 시간은 UTC이며 생성된 시간이 아니라 원격 분석이 저장소에 보관된 시기입니다. 따라서 데이터를 다운로드할 코드를 작성하는 경우 데이터를 선형으로 이동할 수 있습니다.

경로의 형식은 다음과 같습니다.

```console
$"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"
```

Where

* `blobCreationTimeUtc`는 내부 준비 스토리지에서 Blob이 생성된 시간입니다.
* `blobDeliveryTimeUtc` 는 Blob이 내보내기 대상 스토리지에 복사되는 시간입니다.

## <a name="data-format"></a><a name="format"></a> 데이터 형식
* 각 blob은 다중 '\n'-separated 행을 포함하는 텍스트 파일입니다. 여기에는 약 30초 동안 처리된 원격 분석 데이터가 있습니다.
* 각 행은 요청 또는 페이지 보기와 같은 원격 분석 데이터 요소를 나타냅니다.
* 각 행은 서식이 지정되지 않은 JSON 파일입니다. 행을 보려면 Visual Studio에서 Blob을 열고 **편집** > **고급** > **서식 파일** 을 선택합니다.

   ![적합한 도구를 사용하여 원격 분석 보기](./media/export-telemetry/06-json.png)

시간 기간은 틱 단위이며 10,000틱은 1ms입니다. 예를 들어 이러한 값은 브라우저에서 요청을 보내는 데 1ms, 받는 데 3ms, 브라우저에서 페이지를 처리하는 데 1.8s를 보여줍니다.

```json
"sendRequest": {"value": 10000.0},
"receiveRequest": {"value": 30000.0},
"clientProcess": {"value": 17970000.0}
```

[속성 형식 및 값에 대한 자세한 데이터 모델 참조입니다.](export-data-model.md)

## <a name="processing-the-data"></a>데이터 처리
작은 규모에서 데이터를 분리하고, 스프레드시트에서 읽는 등의 처리를 위한 코드를 작성할 수 있습니다. 다음은 그 예입니다.

```csharp
private IEnumerable<T> DeserializeMany<T>(string folderName)
{
   var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
   foreach (var file in files)
   {
      using (var fileReader = File.OpenText(file))
      {
         string fileContent = fileReader.ReadToEnd();
         IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
         foreach (var entity in entities)
         {
            yield return JsonConvert.DeserializeObject<T>(entity);
         }
      }
   }
}
```

더 큰 코드 샘플에 대해서는 [작업자 역할 사용][exportasa]을 참조하세요.

## <a name="delete-your-old-data"></a><a name="delete"></a>이전 데이터 삭제
필요한 경우 스토리지 용량을 관리하고 이전 데이터를 삭제해야 합니다.

## <a name="if-you-regenerate-your-storage-key"></a>스토리지 키를 다시 생성하는 경우...
스토리지 키를 변경하는 경우 연속 내보내기 작업이 중지됩니다. Azure 계정에 알림이 표시됩니다.

연속 내보내기 탭을 열고 내보내기를 편집합니다. 내보내기 대상을 편집하되 선택한 것과 동일한 스토리지는 그대로 둡니다. 확인을 클릭하여 확인합니다.

연속 내보내기가 다시 시작됩니다.

## <a name="export-samples"></a>내보내기 샘플

* [Stream Analytics를 사용하여 SQL로 내보내기][exportasa]
* [Stream Analytics 샘플 2](../../stream-analytics/app-insights-export-stream-analytics.md)

더 큰 규모에서는 [HDInsight](https://azure.microsoft.com/services/hdinsight/) (클라우드의 Hadoop 클러스터)를 고려합니다. HDInsight는 빅 데이터에 대한 다양한 관리 분석 기술을 제공하므로 이를 사용하여 Application Insights에서 내보낸 데이터를 처리할 수 있습니다.

## <a name="q--a"></a>질문과 대답
* *하지만 원하는 모든 것은 차트의 일회성 다운로드입니다.*  

    예, 수행할 수 있습니다. 탭 맨 위에서 **데이터 내보내기** 를 클릭합니다.
* *내보내기를 설정했지만 내 저장소에 데이터가 없습니다.*

    내보내기를 설정한 후 Application Insights가 앱에서 원격 분석을 받았나요? 새 데이터만 받게 됩니다.
* *내보내기를 설정하려 했지만 액세스가 거부되었습니다.*

    계정이 조직 소유인 경우 소유자 또는 참여자 그룹의 구성원이어야 합니다.
* *나만의 온-프레미스 저장소로 직접 내보낼 수 있나요?*

    아니요. 죄송합니다. 우리의 내보내기 엔진은 현재 Azure Storage에서만 작동합니다.  
* *내 저장소에 보관하는 데이터량에 제한이 있나요?*

    아니요. 내보내기를 삭제할 때까지 푸싱한 데이터를 유지합니다. Windows에서 Azure File Storage 시작문서의 지침에 따라 Azure 파일 공유를 만듭니다. 사용자가 이용하는 스토리지 크기는 사용자가 제어하기 나름입니다.  
* *스토리지에서 몇 개의 BLOB를 볼 수 있나요?*

  * 내보내려고 선택한 모든 데이터 형식에 대해 새 blob(데이터 파일이 사용 가능한 경우)이 매 분마다 만들어 집니다.
  * 또한, 트래픽이 많은 애플리케이션은 추가 파티션이 할당됩니다. 이 경우 각 단위는 매분마다 Blob을 만듭니다.
* *내 스토리지 키를 다시 생성하거나 컨테이너의 이름을 변경한 경우에는 현재 내보내기가 동작하지 않습니다.*

    내보내기를 편집하고 내보내기 대상 탭을 엽니다. 이전과 마찬가지로 선택된 것과 동일한 스토리지는 그대로 두고 확인을 클릭하여 확인합니다. 내보내기가 다시 시작됩니다. 지난 몇 일 이내에 변경된 경우 데이터가 손실됩니다.
* *내보내기를 일시 중지할 수 있나요?*

    예. 사용 안함을 클릭합니다.

## <a name="code-samples"></a>코드 샘플

* [Stream Analytics 샘플](../../stream-analytics/app-insights-export-stream-analytics.md)
* [Stream Analytics를 사용하여 SQL로 내보내기][exportasa]
* [속성 형식 및 값에 대한 자세한 데이터 모델 참조입니다.](export-data-model.md)

## <a name="diagnostic-settings-based-export"></a>진단 설정 기반 내보내기

진단 설정 기반 내보내기는 연속 내보내기와는 다른 스키마를 사용합니다. 또한 연속 내보내기와는 다른 기능을 지원합니다.

* Vnet, 방화벽 및 프라이빗 링크를 사용하는 Azure Storage 계정
* 이벤트 허브로 내보내기

진단 설정 기반 내보내기로 마이그레이션하려면 다음을 수행합니다.

1. 현재 연속 내보내기를 사용하지 않도록 설정합니다.
2. [작업 영역 기반으로 애플리케이션을 마이그레이션합니다](convert-classic-resource.md).
3. [진단 설정 내보내기를 사용하도록 설정합니다](create-workspace-resource.md#export-telemetry). Application Insights 리소스 내에서 **진단 설정 > 진단 설정 추가** 를 선택합니다.

<!--Link references-->

[exportasa]: ../../stream-analytics/app-insights-export-sql-stream-analytics.md
[roles]: ./resources-roles-access-control.md