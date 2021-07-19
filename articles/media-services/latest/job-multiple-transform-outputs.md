---
title: 여러 변환 출력을 사용하여 작업 만들기
description: 이 항목에서는 여러 변환 출력을 사용하여 Azure Media Services 작업을 만드는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5ada86e0ce44977789eaa5d6327c0aad2883b47d
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108849"
---
# <a name="create-a-job-with-multiple-transform-outputs"></a>여러 변환 출력을 사용하여 작업 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 항목에서는 두 개의 변환 출력을 사용하여 변환을 만드는 방법을 보여 줍니다. 첫 번째는 기본 제공 [AdaptiveStreaming](encode-concept.md#builtinstandardencoderpreset) 사전 설정을 사용하여 적응 비트 전송률 스트리밍을 위해 인코딩할 입력을 호출합니다. 두 번째는 [AudioAnalyzerPreset](analyze-video-audio-files-concept.md#built-in-presets)을 사용하여 처리할 입력 비디오에 오디오 신호를 호출합니다. 변환을 만든 후 그에 따라 비디오를 처리하는 작업을 제출할 수 있습니다. 이 예제에서는 두 개의 변환 출력을 지정하므로 두 개의 작업 출력을 지정해야 합니다. 두 작업 출력을 동일한 자산(아래에 표시된 것처럼)으로 보내도록 선택하거나 별도의 자산에 결과를 쓸 수 있습니다.

> [!TIP]
> 개발을 시작하기 전에 [Media Services v3 API로 개발](media-services-apis-overview.md)(API 액세스, 명명 규칙 등에 대한 정보 포함)을 검토하세요.

## <a name="create-a-transform"></a>변환 만들기

다음 코드는 두 개의 출력을 생성하는 변환을 만드는 방법을 보여 줍니다.

```csharp
private static async Task<Transform> GetOrCreateTransformAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = await client.Transforms.GetAsync(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // You need to specify what you want it to produce as an output
        TransformOutput[] output = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    // This sample uses the built-in encoding preset for Adaptive Bitrate Streaming.
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            },
            // Create an analyzer preset with video insights.
            new TransformOutput(new AudioAnalyzerPreset("en-US"))
        };

        // Create the Transform with the output defined above
        transform = await client.Transforms.CreateOrUpdateAsync(resourceGroupName, accountName, transformName, output);
    }

    return transform;
}
```

## <a name="submit-a-job"></a>작업 제출

HTTPS URL 입력 및 두 개의 작업 출력을 사용하여 작업을 만듭니다.

```csharp
private static async Task<Job> SubmitJobAsync(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string transformName)
{
    // Output from the encoding Job must be written to an Asset, so let's create one
    string outputAssetName1 = $"output-" + Guid.NewGuid().ToString("N");
    Asset outputAsset = await client.Assets.CreateOrUpdateAsync(resourceGroup, accountName, outputAssetName1, new Asset());

    // This example shows how to encode from any HTTPs source URL - a new feature of the v3 API.  
    // Change the URL to any accessible HTTPs URL or SAS URL from Azure.
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        // Since we are specifying two Transform Outputs, two Job Outputs are needed.
        // In this example, the first Job Output is for the results from adaptive bitrate encoding,
        // and the second is for the results from audio analysis. In this example, both are written to the
        // same output Asset. Or, you can specify different Assets.
        
        new JobOutputAsset(outputAsset.Name),
        new JobOutputAsset(outputAsset.Name)

    };

    // In this example, we are using a unique job name.
    //
    // If you already have a job with the desired name, use the Jobs.Get method
    // to get the existing job. In Media Services v3, Get methods on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).
    Job job;
    try
    {
        string jobName = $"job-" + Guid.NewGuid().ToString("N");
        job = await client.Jobs.CreateAsync(
                    resourceGroup,
                    accountName,
                    transformName,
                    jobName,
                    new Job
                    {
                        Input = jobInput,
                        Outputs = jobOutputs,
                    });
    }
    catch (Exception exception)
    {
        if (exception.GetBaseException() is ApiErrorException apiException)
        {
            Console.Error.WriteLine(
                $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
        }
        throw exception;
    }

    return job;
}
```
## <a name="job-error-codes"></a>작업 오류 코드

[오류 코드](/rest/api/media/jobs/get#joberrorcode)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[.NET을 사용한 Azure Media Services v3 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/) 
