---
title: 호환성을 위해 유니코드가 아닌 인코딩된 텍스트 변환
description: base64 인코딩 및 Azure Functions 사용하여 텍스트 페이로드를 UTF-8로 변환하여 Azure Logic Apps 유니코드가 아닌 문자를 처리합니다.
ms.date: 10/05/2021
ms.topic: how-to
ms.reviewer: estfan, azla
ms.service: logic-apps
ms.openlocfilehash: 1251a1622e62b7940c25ac810db10f70da560000
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618993"
---
# <a name="support-non-unicode-character-encoding-in-logic-apps"></a>Logic Apps에서 유니코드가 아닌 문자 인코딩을 지원합니다.

텍스트 페이로드를 사용하는 경우 Azure Logic Apps는 텍스트를 UTF-8과 같은 유니코드 형식으로 인코딩합니다. 워크플로에서 다른 인코딩으로 문자를 수신, 전송 또는 처리하는 데 문제가 있을 수 있습니다. 예를 들어 유니코드를 지원하지 않는 레거시 시스템을 사용할 경우 플랫 파일에 손상된 문자가 있을 수 있습니다.

다른 문자 인코딩을 사용하는 텍스트를 사용하려면 유니코드를 지원하지 않는 페이로드에 base64 인코딩을 적용합니다. 이 단계에서는 Logic Apps가 텍스트를 UTF-8 형식으로 가정하지 않도록 제한합니다. 그런 다음 Azure Functions를 사용하여 모든 .NET 지원되는 인코딩을 UTF-8로 변환할 수 있습니다. 

이 솔루션은 *다중 테넌트* 및 *단일 테넌트* 워크플로 모두에서 작동합니다. [이 솔루션을 AS2 커넥터와 함께 사용](#convert-payloads-for-as2)할 수도 있습니다.

## <a name="convert-payload-encoding"></a>페이로드 인코딩 변환

먼저 트리거가 콘텐츠 형식을 올바르게 식별할 수 있는지 확인합니다. 이 단계를 수행하면 Logic Apps에서 더 이상 텍스트를 UTF-8로 간주하지 않습니다. 

**콘텐츠 형식 유추** 속성이 있는 트리거 및 작업에서 **아니요를** 선택합니다.  일반적으로 작업의 **매개 변수 추가** 목록에서 이 속성을 찾을 수 있습니다. 그러나 작업에 이 속성이 포함되어 있지 않으면 콘텐츠 형식이 인바운드 메시지에 의해 설정됩니다.

다음 목록에서는 콘텐츠 형식을 자동으로 유추하지 않도록 설정할 수 있는 일부 커넥터를 보여 드립니다.
* [OneDrive](/connectors/onedrive/)
* [Azure Blob Storage](/connectors/azureblob/)
* [Azure File Storage](/connectors/azurefile/)
* [파일 시스템](/connectors/filesystem/)
* [Google 드라이브](/connectors/googledrive/)
* [SFTP - SSH](/connectors/sftpwithssh/)
 
콘텐츠에 요청 트리거를 사용하는 경우 `text/plain` `charset` 호출의 헤더에 있는 매개 변수를 설정해야 `Content-Type` 합니다. 그렇지 않으면 문자가 손상되거나 매개 변수가 페이로드의 인코딩 형식과 일치하지 않을 수 있습니다. 자세한 내용은 [ `text/plain` 콘텐츠 형식을 처리하는 방법을](logic-apps-content-type.md#text-plain)검토하세요.

예를 들어, HTTP 트리거는 `Content-Type` 헤더가 올바른 `charset` 매개 변수로 설정된 경우 수신 콘텐츠를 UTF-8로 변환합니다.

```json
{
    "headers": {
        <...>
        "Content-Type": "text/plain; charset=windows-1250"
        },
        "body": "non UTF-8 text content"
}
```

`Content-Type` 헤더를 `application/octet-stream`로 설정하면 UTF-8이 아닌 문자가 수신될 수도 있습니다. 자세한 내용은 [콘텐츠 형식`application/octet-stream`을 처리하는 방법](logic-apps-content-type.md#applicationxml-and-applicationoctet-stream)을 참조하세요.

## <a name="base64-encode-content"></a>Base64 인코딩 콘텐츠

콘텐츠를 [base64 인코딩](workflow-definition-language-functions-reference.md#base64)하기 전에 [해당 텍스트를 UTF-8로 변환](#convert-payload-encoding)했는지 확인합니다. 텍스트를 UTF-8로 변환하기 전에 콘텐츠를 문자열로 base64 디코딩하는 경우 문자가 손상될 수 있습니다.

그리고 .NET 지원되는 인코딩을 다른 .NET 지원되는 인코딩으로 변환합니다. [Azure Functions 코드 예제](#azure-functions-version) 또는 [.NET 코드 예제](#net-version)를 검토합니다.

> [!TIP]
> *단일 테넌트* 논리 앱의 경우 변환 함수를 로컬로 실행하여 성능을 향상시키고 대기 시간을 줄일 수 있습니다.

### <a name="azure-functions-version"></a>Azure Functions 버전

이 예제는 Azure Functions 버전 2에 대한 것입니다. 

```csharp
using System;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

public static class ConversionFunctionv2 {
  [FunctionName("ConversionFunctionv2")]
  public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, TraceWriter log) {
    log.Info("C# HTTP trigger function processing a request.");

    Encoding inputEncoding = null;

    string requestBody = new StreamReader(req.Body).ReadToEnd();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    if (data == null || data.text == null || data.encodingInput == null || data.encodingOutput == null) {
      return new BadRequestObjectResult("Please pass text/encodingOutput properties in the input JSON object.");
    }

    Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);

    try {
      string encodingInput = data.encodingInput.Value;
      inputEncoding = Encoding.GetEncoding(name: encodingInput);
    } catch (ArgumentException) {
      return new BadRequestObjectResult($"Input character set value '{data.encodingInput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
    }

    Encoding encodingOutput = null;
    try {
      string outputEncoding = data.encodingOutput.Value;
      encodingOutput = Encoding.GetEncoding(outputEncoding);
    } catch (ArgumentException) {
      return new BadRequestObjectResult($"Output character set value '{data.encodingOutput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
    }

    return (ActionResult) new JsonResult(
      value: new {
        text = Convert.ToBase64String(
          Encoding.Convert(
            srcEncoding: inputEncoding,
            dstEncoding: encodingOutput,
            bytes: Convert.FromBase64String((string) data.text)))
      });
  }
}
```

### <a name="net-version"></a>.NET 버전

다음 예제는 **.NET 표준** 및 Azure Functions **버전 2** 에서 사용하기 위한 것입니다.

```csharp
    using System;
    using System.IO;
    using System.Text;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json;

    public static class ConversionFunctionNET
    {
        [FunctionName("ConversionFunctionNET")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequest req, TraceWriter log)
        {
            log.Info("C# HTTP trigger function processing a request.");

            Encoding inputEncoding = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            if (data == null || data.text == null || data.encodingInput == null || data.encodingOutput == null)
            {
                return new BadRequestObjectResult("Please pass text/encodingOutput properties in the input JSON object.");
            }

            Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);

            try
            {
                string encodingInput = data.encodingInput.Value;
                inputEncoding = Encoding.GetEncoding(name: encodingInput);
            }
            catch (ArgumentException)
            {
                return new BadRequestObjectResult($"Input character set value '{data.encodingInput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
            }

            Encoding encodingOutput = null;
            try
            {
                string outputEncoding = data.encodingOutput.Value;
                encodingOutput = Encoding.GetEncoding(outputEncoding);
            }
            catch (ArgumentException)
            {
                return new BadRequestObjectResult($"Output character set value '{data.encodingOutput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
            }

            return (ActionResult)new JsonResult(
                value: new
                {
                    text = Convert.ToBase64String(
                        Encoding.Convert(
                            srcEncoding: inputEncoding,
                            dstEncoding: encodingOutput,
                            bytes: Convert.FromBase64String((string)data.text)))
                });
        }
    }
```

이와 동일한 개념을 사용하여 [워크플로에서 유니코드가 아닌 페이로드를 보낼](#send-non-unicode-payload) 수도 있습니다.

## <a name="sample-payload-conversions"></a>샘플 페이로드 변환

이 예제에서 base64로 인코딩된 샘플 입력 문자열은 악센트 부호가 있는 문자를 포함하는 개인 이름(*H&eacute;lo&iuml;se*)입니다.

예제 입력:

```json
{  
    "text": "SMOpbG/Dr3Nl",
    "encodingInput": "utf-8",
    "encodingOutput": "windows-1252"
}
```

예제 출력:

```json
{
    "text": "U01PcGJHL0RyM05s"
}
```

## <a name="send-non-unicode-payload"></a>유니코드가 아닌 페이로드 보내기

워크플로에서 유니코드가 아닌 페이로드를 보내야 하는 경우 [페이로드를 역방향의 UTF-8로 변환](#convert-payload-encoding)하는 단계를 수행합니다. 시스템 내에서 가능한 오랫동안 텍스트를 UTF-8로 유지합니다. 그런 다음 동일한 함수를 사용하여 base64로 인코딩된 UTF-8 문자를 필요한 인코딩으로 변환합니다. 그런 다음 텍스트에 base64 디코딩을 적용하고 페이로드를 보냅니다.

## <a name="convert-payloads-for-as2"></a>AS2용 페이로드 변환

[AS2 v2 커넥터](logic-apps-enterprise-integration-as2.md)에서 유니코드가 아닌 페이로드로 이 솔루션을 사용할 수도 있습니다. AS2로 전달하는 페이로드를 UTF-8로 변환하지 않으면 페이로드 해석에 문제가 발생할 수 있습니다. 이러한 문제로 인해 잘못 해석된 문자 때문에 파트너 간의 MIC 해시가 일치하지 않을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [엔터프라이즈 통합 팩을 사용하여 Azure Logic Apps에서 플랫 파일 인코딩 및 디코딩](logic-apps-enterprise-integration-flatfile.md)
