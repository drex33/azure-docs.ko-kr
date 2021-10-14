---
title: Azure Functions에 대한 Azure Blob Storage 출력 바인딩
description: Azure Function에 Azure Blob Storage 출력 바인딩 데이터를 제공하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: ede3789b8e0470436612ded9a9712b7aedb8c518
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996459"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Azure Functions에 대한 Azure Blob Storage 출력 바인딩

출력 바인딩을 사용하면 Azure Function에서 Blob 스토리지 데이터를 수정하고 삭제할 수 있습니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](./functions-bindings-storage-blob.md)를 참조하세요.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제는 하나의 Blob 트리거와 두 개의 출력 Blob 바인딩을 사용하는 [C# 함수](functions-dotnet-class-library.md)입니다. *샘플 이미지* 컨테이너에서 이미지 Blob을 만들어서 함수를 트리거합니다. 그러면 이미지 Blob의 소량 및 중간 크기 복사본을 만듭니다.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

<!--Same example for input and output. -->

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트(.csx)](functions-reference-csharp.md) 코드에서 Blob 입력 및 출력 바인딩을 보여줍니다. 함수는 텍스트 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-Copy* 입니다.

*function.json* 파일에서 `queueTrigger` 메타데이터 속성은 `path` 속성에서 Blob 이름을 지정하는 데 사용됩니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="java"></a>[Java](#tab/java)

이 섹션에는 다음 예제가 포함되어 있습니다.

* [HTTP 트리거, OutputBinding 사용](#http-trigger-using-outputbinding-java)
* [큐 트리거, 함수 반환 값 사용](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP 트리거, OutputBinding 사용(Java)

 다음 예제는 `HttpTrigger` 주석을 사용하여 Blob Storage 컨테이너에 있는 파일 이름을 포함하는 매개 변수를 수신하는 Java 함수를 보여 줍니다. 그런 다음, `BlobInput` 주석이 파일을 읽고 파일 내용을 함수에 `byte[]`로 전달합니다. `BlobOutput` 주석은 `OutputBinding outputItem`에 바인딩되고, 이는 입력 Blob의 콘텐츠를 구성된 스토리지 컨테이너에 쓰기 위해 함수에서 사용됩니다.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>큐 트리거, 함수 반환 값 사용(Java)

 다음 예제는 `QueueTrigger` 주석을 사용하여 Blob Storage 컨테이너에 있는 파일 이름을 포함하는 메시지를 수신하는 Java 함수를 보여 줍니다. 그런 다음, `BlobInput` 주석이 파일을 읽고 파일 내용을 함수에 `byte[]`로 전달합니다. `BlobOutput` 주석은 함수 반환 값에 바인딩되고, 이는 입력 Blob의 콘텐츠를 구성된 스토리지 컨테이너에 쓰기 위해 런타임에서 사용됩니다.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 [Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Blob Storage의 개체에 기록될 함수 매개 변수에 대한 `@BlobOutput` 주석을 사용합니다.  매개 변수 형식은 `OutputBinding<T>`이어야 합니다. 여기서 T는 원시 Java 형식 또는 POJO입니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 스크립트](functions-reference-node.md) 코드에서 Blob 입력 및 출력 바인딩을 보여 줍니다. 함수는 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-Copy* 입니다.

*function.json* 파일에서 `queueTrigger` 메타데이터 속성은 `path` 속성에서 Blob 이름을 지정하는 데 사용됩니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 예에서는 [PowerShell 함수](functions-reference-powershell.md)의 출력으로 들어오는 BLOB의 복사본을 만드는 방법을 보여줍니다.

함수의 구성 파일(*function.json*)에서 `trigger` 메타데이터 속성은 `path` 속성에서 출력 BLOB 이름을 지정하는 데 사용됩니다.

> [!NOTE]
> 무한 루프를 방지하려면 입력 및 출력 경로가 서로 다른지 확인합니다.

```json
{
  "bindings": [
    {
      "name": "myInputBlob",
      "path": "data/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in",
      "type": "blobTrigger"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "data/copy/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

PowerShell 코드는 다음과 같습니다.

```powershell
# Input bindings are passed in via param block.
param([byte[]] $myInputBlob, $TriggerMetadata)
Write-Host "PowerShell Blob trigger function Processed blob Name: $($TriggerMetadata.Name)"
Push-OutputBinding -Name myOutputBlob -Value $myInputBlob
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [Python 코드](functions-reference-python.md)에서 Blob 입력 및 출력 바인딩을 보여 줍니다. 함수는 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-Copy* 입니다.

*function.json* 파일에서 `queueTrigger` 메타데이터 속성은 `path` 속성에서 Blob 이름을 지정하는 데 사용됩니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: bytes, outputblob: func.Out[bytes]):
    logging.info(f'Python Queue trigger function processed {len(inputblob)} bytes')
    outputblob.set(inputblob)
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)를 사용합니다.

특성의 생성자는 다음 예제에 표시된 대로 Blob에 대한 경로 및 읽기 또는 쓰기를 나타내는 `FileAccess` 매개 변수를 사용합니다.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

다음 예와 같이 사용할 스토리지 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput` 특성은 함수를 트리거한 BLOB에 대한 액세스 권한을 부여합니다. 특성과 함께 바이트 배열을 사용하는 경우 `dataType`을 `binary`로 설정합니다. 자세한 내용은 [출력 예제](#example)를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

---

전체 예제는 [출력 예제](#example)를 참조하세요.

`StorageAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 스토리지 계정을 지정합니다. 자세한 내용은 [트리거 - 특성](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)을 참조하세요.

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `Blob` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | `blob`로 설정해야 합니다. |
|**direction** | 해당 없음 | 출력 바인딩에 대해 `out`로 설정해야 합니다. 예외는 [사용](#usage) 섹션에서 표시됩니다. |
|**name** | 해당 없음 | 함수 코드에서 Blob을 나타내는 변수의 이름입니다.  `$return`으로 설정하여 함수 반환 값을 참조합니다.|
|**path** |**BlobPath** | BLOB 컨테이너에 대한 경로입니다. |
|**connection** |**연결**| Azure Blob에 연결하는 방법을 지정하는 앱 설정 또는 설정 컬렉션의 이름입니다. [연결](#connections)을 참조하세요.|
|해당 없음 | **Access** | 읽기 또는 쓰기를 나타냅니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

[!INCLUDE [functions-storage-blob-connections](../../includes/functions-storage-blob-connections.md)]

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput` 특성은 함수를 트리거한 BLOB에 대한 액세스 권한을 부여합니다. 특성과 함께 바이트 배열을 사용하는 경우 `dataType`을 `binary`로 설정합니다. 자세한 내용은 [출력 예제](#example)를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<BINDING_NAME>`을 사용하는 BLOB 데이터에 액세스합니다. 여기서 바인딩 이름은 _function.json_ 파일에서 정의됩니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

_function.json_ 파일에서 바인딩의 이름 매개 변수로 지정된 이름과 일치하는 매개 변수를 통해 Blob 데이터에 액세스합니다.

# <a name="python"></a>[Python](#tab/python)

함수 매개 변수를 다음 형식으로 선언하여 Blob Storage에 쓸 수 있습니다.

* 문자열: `func.Out[str]`
* 스트림: `func.Out[func.InputStream]`

자세한 내용은 [출력 예제](#example)를 참조하세요.

---

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 |  참고 |
|---|---|
| Blob | [Blob 오류 코드](/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, 테이블, 큐 |  [스토리지 오류 코드](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, 테이블, 큐 |  [문제 해결](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>다음 단계

- [Blob Storage 데이터 변경 시 함수 실행](./functions-bindings-storage-blob-trigger.md)
- [함수 실행 시 Blob Storage 데이터 읽기](./functions-bindings-storage-blob-input.md)
