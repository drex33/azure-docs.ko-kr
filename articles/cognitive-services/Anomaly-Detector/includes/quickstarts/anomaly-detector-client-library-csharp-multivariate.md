---
title: Anomaly Detector .NET 다변량 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: c4048c3795f2e7925f70783b5c5b763d4796b9a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910244"
---
C#용 Anomaly Detector 다변량 클라이언트 라이브러리를 시작합니다. 다음 단계에 따라 패키지를 설치하고 서비스에서 제공하는 알고리즘을 사용합니다. 새로운 다변량 변칙 검색 API를 통해 개발자는 기계 학습 기술 또는 레이블이 지정된 데이터 없이도 메트릭 그룹에서 변칙을 검색하는 고급 AI를 쉽게 통합할 수 있습니다. 서로 다른 신호 간의 종속성 및 상호 상관 관계는 자동으로 주요 요소로 계산됩니다. 이를 통해 복잡한 시스템의 오류로부터 사전에 보호할 수 있습니다.

C#용 Anomaly Detector 다변량 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 시계열 그룹에서 시스템 수준 이상 징후를 감지합니다.
* 개별 시계열이 많은 것을 알 수 없는 경우 문제를 감지하기 위해 모든 신호를 확인해야 합니다.
* 시스템 상태의 다양한 양상을 측정하는 수천 개 유형의 센서를 사용하여 고가의 물리적 자산을 사전 예방적으로 유지 관리.

[라이브러리 참조 설명서](/dotnet/api/azure.ai.anomalydetector) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/anomalydetector) | [패키지(NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.3)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector 리소스 만들기"  target="_blank">Anomaly Detector 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 선택합니다.
    * 애플리케이션을 Anomaly Detector API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-net-core-application"></a>새 .NET Core 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `anomaly-detector-quickstart-multivariate`라는 새 콘솔 앱을 만듭니다. 이 명령은 단일 C# 소스 파일을 사용하여 간단한 "Hello World" 프로젝트를 만듭니다. *Program.cs* 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart-multivariate
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```dotnetcli
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Anomaly Detector 클라이언트 라이브러리를 설치합니다.

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.3
```

프로젝트 디렉터리에서 *program.cs* 파일을 열고 `directives`를 사용하여 다음을 추가합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Drawing.Text;
using System.IO;
using System.Linq;
using System.Linq.Expressions;
using System.Net.NetworkInformation;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Azure.AI.AnomalyDetector.Models;
using Azure.Core.TestFramework;
using Microsoft.Identity.Client;
using NUnit.Framework;
```

애플리케이션의 `main()` 메서드에서 리소스의 Azure 엔드포인트, API 키 및 사용자 지정 데이터 원본에 대한 변수를 만듭니다.

> [!NOTE]
> 항상 두 키 중 하나를 사용할 수 있습니다. 이는 보안 키 회전을 허용하기 위한 것입니다. 이 빠른 시작의 목적을 위해 첫 번째 키를 사용합니다. 

```csharp
string endpoint = "YOUR_API_KEY";
string apiKey =  "YOUR_ENDPOINT";
string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

Anomaly Detector 다변량 API를 사용하려면 먼저 고유한 모델을 학습해야 합니다. 학습 데이터는 다음 요구 사항을 충족하는 여러 시계열 세트입니다.

각 시계열은 헤더 행으로 "timestamp" 및 "value"(모두 소문자)라는 2개(및 2개만)의 열이 포함된 CSV 파일이어야 합니다. "timestamp" 값은 ISO 8601을 준수해야 합니다. "value"는 소수 자릿수가 있는 정수 또는 소수일 수 있습니다. 예를 들면 다음과 같습니다.

|timestamp | 값|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

각 CSV 파일의 이름을 모델 학습에 사용되는 다른 변수 뒤에 지정해야 합니다. 예를 들면 "temperature.csv" 및 "humidity.csv"입니다. 모든 CSV 파일은 하위 폴더 없이 하나의 zip 파일로 압축되어야 합니다. zip 파일에는 원하는 이름을 사용할 수 있습니다. zip 파일을 Azure Blob Storage에 업로드해야 합니다. zip 파일에 대한 Blob SAS(공유 액세스 서명) URL을 생성한 후에는 학습에 사용할 수 있습니다. Azure Blob Storage에서 SAS URL을 생성하는 방법은 이 문서를 참조하세요.

## <a name="code-examples"></a>코드 예제

이 코드 조각은 .NET용 Anomaly Detector 다변량 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [모델 학습](#train-the-model)
* [변칙 검색](#detect-anomalies)
* [모델 내보내기](#export-model)
* [모델 삭제](#delete-model)

## <a name="authenticate-the-client"></a>클라이언트 인증

엔드포인트 및 키를 사용하여 Anomaly Detector 클라이언트를 인스턴스화합니다.

```csharp
var endpointUri = new Uri(endpoint);
var credential = new AzureKeyCredential(apiKey)

AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);
```

## <a name="train-the-model"></a>모델 학습

모델 학습을 처리하려면 아래와 같이 새 프라이빗 비동기 작업을 만듭니다. `TrainMultivariateModel`을 사용하여 모델을 학습시키고 `GetMultivariateModelAysnc`를 사용하여 학습 완료 시기를 확인합니다.

```csharp
private async Task<Guid?> trainAsync(AnomalyDetectorClient client, string datasource, DateTimeOffset start_time, DateTimeOffset end_time)
{
    try
    {
        Console.WriteLine("Training new model...");

        int model_number = await getModelNumberAsync(client, false).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models before training.", model_number));

        ModelInfo data_feed = new ModelInfo(datasource, start_time, end_time);
        Response response_header = client.TrainMultivariateModel(data_feed);
        response_header.Headers.TryGetValue("Location", out string trained_model_id_path);
        Guid trained_model_id = Guid.Parse(trained_model_id_path.Split('/').LastOrDefault());
        Console.WriteLine(trained_model_id);

        // Wait until the model is ready. It usually takes several minutes
        Response<Model> get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
        while (get_response.Value.ModelInfo.Status != ModelStatus.Ready & get_response.Value.ModelInfo.Status != ModelStatus.Failed)
        {
            System.Threading.Thread.Sleep(10000);
            get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
            Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}, status: {3}.", get_response.Value.ModelId, get_response.Value.CreatedTime, get_response.Value.LastUpdatedTime, get_response.Value.ModelInfo.Status));
        }

        if (get_response.Value.ModelInfo.Status != ModelStatus.Ready)
        {
            Console.WriteLine(String.Format("Trainig failed."));
            IReadOnlyList<ErrorResponse> errors = get_response.Value.ModelInfo.Errors;
            foreach (ErrorResponse error in errors)
            {
                Console.WriteLine(String.Format("Error code: {0}.", error.Code));
                Console.WriteLine(String.Format("Error message: {0}.", error.Message));
            }
            throw new Exception("Training failed.");
        }

        model_number = await getModelNumberAsync(client).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models after training.", model_number));
        return trained_model_id;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Train error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="detect-anomalies"></a>변칙 검색

새로 학습된 모델을 사용하여 변칙을 검색하려면 `detectAsync`라는 `private async Task`를 만듭니다. 새 `DetectionRequest`를 만들고 이를 `DetectAnomalyAsync`에 매개 변수로 전달합니다.

```csharp
private async Task<DetectionResult> detectAsync(AnomalyDetectorClient client, string datasource, Guid model_id,DateTimeOffset start_time, DateTimeOffset end_time)
{
    try
    {
        Console.WriteLine("Start detect...");
        Response<Model> get_response = await client.GetMultivariateModelAsync(model_id).ConfigureAwait(false);

        DetectionRequest detectionRequest = new DetectionRequest(datasource, start_time, end_time);
        Response result_response = await client.DetectAnomalyAsync(model_id, detectionRequest).ConfigureAwait(false);
        var ok = result_response.Headers.TryGetValue("Location", out string result_id_path);
        Guid result_id = Guid.Parse(result_id_path.Split('/').LastOrDefault());
        // get detection result
        Response<DetectionResult> result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
        while (result.Value.Summary.Status != DetectionStatus.Ready & result.Value.Summary.Status != DetectionStatus.Failed)
        {
            System.Threading.Thread.Sleep(2000);
            result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
        }

        if (result.Value.Summary.Status != DetectionStatus.Ready)
        {
            Console.WriteLine(String.Format("Inference failed."));
            IReadOnlyList<ErrorResponse> errors = result.Value.Summary.Errors;
            foreach (ErrorResponse error in errors)
            {
                Console.WriteLine(String.Format("Error code: {0}.", error.Code));
                Console.WriteLine(String.Format("Error message: {0}.", error.Message));
            }
            return null;
        }

        return result.Value;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Detection error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="export-model"></a>모델 내보내기

> [!NOTE]
> 내보내기 명령은 컨테이너화된 환경에서 Anomaly Detector 다변량 모델을 실행할 수 있도록 하는 데 사용됩니다. 이는 현재 다변량에 대해 지원되지 않지만 나중에 지원이 추가될 예정입니다.

이전에 학습시킨 모델을 내보내려면 `exportAysnc`라는 `private async Task`를 만듭니다. `ExportModelAsync`를 사용하고 내보내려는 모델의 모델 ID를 전달합니다.

```csharp
private async Task exportAsync(AnomalyDetectorClient client, Guid model_id, string model_path = "model.zip")
{
    try
    {
        Stream model = await client.ExportModelAsync(model_id).ConfigureAwait(false);
        if (model != null)
        {
            var fileStream = File.Create(model_path);
            model.Seek(0, SeekOrigin.Begin);
            model.CopyTo(fileStream);
            fileStream.Close();
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Export error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="delete-model"></a>모델 삭제

이전에 만든 모델을 삭제하려면 `DeleteMultivariateModelAsync`를 사용하고 삭제할 모델의 모델 ID를 전달합니다. 모델 ID를 검색하려면 `getModelNumberAsync`를 사용하면 됩니다.

```csharp
private async Task deleteAsync(AnomalyDetectorClient client, Guid model_id)
{
    await client.DeleteMultivariateModelAsync(model_id).ConfigureAwait(false);
    int model_number = await getModelNumberAsync(client).ConfigureAwait(false);
    Console.WriteLine(String.Format("{0} available models after deletion.", model_number));
}
private async Task<int> getModelNumberAsync(AnomalyDetectorClient client, bool delete = false)
{
    int count = 0;
    AsyncPageable<ModelSnapshot> model_list = client.ListMultivariateModelAsync(0, 10000);
    await foreach (ModelSnapshot x in model_list)
    {
        count += 1;
        Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}.", x.ModelId, x.CreatedTime, x.LastUpdatedTime));
        if (delete & count < 4)
        {
            await client.DeleteMultivariateModelAsync(x.ModelId).ConfigureAwait(false);
        }
    }
    return count;
}
```

## <a name="main-method"></a>Main 메서드

이제 모든 구성 요소가 있으므로 새로 만든 작업을 호출하는 코드를 기본 메서드에 추가해야 합니다.

```csharp

{
    //read endpoint and apiKey
     string endpoint = "YOUR_API_KEY";
    string apiKey =  "YOUR_ENDPOINT";
    string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
    Console.WriteLine(endpoint);
    var endpointUri = new Uri(endpoint);
    var credential = new AzureKeyCredential(apiKey);

    //create client
    AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);

    // train
    TimeSpan offset = new TimeSpan(0);
    DateTimeOffset start_time = new DateTimeOffset(2021, 1, 1, 0, 0, 0, offset);
    DateTimeOffset end_time = new DateTimeOffset(2021, 1, 2, 12, 0, 0, offset);
    Guid? model_id_raw = null;
    try
    {
        model_id_raw = await trainAsync(client, datasource, start_time, end_time).ConfigureAwait(false);
        Console.WriteLine(model_id_raw);
        Guid model_id = model_id_raw.GetValueOrDefault();

        // detect
        start_time = end_time;
        end_time = new DateTimeOffset(2021, 1, 3, 0, 0, 0, offset);
        DetectionResult result = await detectAsync(client, datasource, model_id, start_time, end_time).ConfigureAwait(false);
        if (result != null)
        {
            Console.WriteLine(String.Format("Result ID: {0}", result.ResultId));
            Console.WriteLine(String.Format("Result summary: {0}", result.Summary));
            Console.WriteLine(String.Format("Result length: {0}", result.Results.Count));
        }

        // export model
        await exportAsync(client, model_id).ConfigureAwait(false);

        // delete
        await deleteAsync(client, model_id).ConfigureAwait(false);
    }
    catch (Exception e)
    {
        String msg = String.Format("Multivariate error. {0}", e.Message);
        if (model_id_raw != null)
        {
            await deleteAsync(client, model_id_raw.GetValueOrDefault()).ConfigureAwait(false);
        }
        Console.WriteLine(msg);
        throw new Exception(msg);
    }
}

```

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `dotnet run` 명령을 사용하여 애플리케이션을 실행합니다.

```dotnetcli
dotnet run
```
## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

* [Anomaly Detector API란?](../../overview-multivariate.md)
* [Anomaly Detector API를 사용하는 경우의 모범 사례입니다.](../../concepts/best-practices-multivariate.md)