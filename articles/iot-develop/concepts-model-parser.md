---
title: 디지털 트윈 모델 파서 이해 | Microsoft Docs
description: 개발자는 DTDL 파서를 사용하여 모델의 유효성을 검사하는 방법을 알아봅니다.
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: eec6c2e70ff11d9da162db79867279abb10d4506
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399279"
---
# <a name="understand-the-digital-twins-model-parser"></a>디지털 트윈 모델 파서 이해

DTDL(Digital Twins Definition Language)은 [DTDL 사양](https://github.com/Azure/opendigitaltwins-dtdl)에 설명되어 있습니다. _디지털 트윈 모델 파서_ NuGet 패키지를 사용하여 여러 파일에 정의된 모델의 유효성을 검사하고 쿼리할 수 있습니다.

## <a name="install-the-dtdl-model-parser"></a>DTDL 모델 파서 설치

파서는 ID가 [Microsoft.Azure.DigitalTwins.Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)인 NuGet.org에서 사용할 수 있습니다. 파서를 설치하려면 Visual Studio 또는 `dotnet` CLI의 NuGet 패키지 관리자처럼 호환되는 NuGet 패키지 관리자를 사용합니다.

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> 이 문서를 작성할 당시 파서 버전은 `3.12.7`입니다.

## <a name="use-the-parser-to-validate-a-model"></a>파서를 사용하여 모델 유효성 검사

모델은 JSON 파일에 설명된 하나 이상의 인터페이스로 구성될 수 있습니다. 파서를 사용하여 지정된 폴더의 모든 파일을 로드하고 파서를 사용하여 파일 간의 모든 참조를 포함하여 전체 파일의 유효성을 검사할 수 있습니다.

1. 모든 모델 콘텐츠 목록을 사용하여 `IEnumerable<string>`을 만듭니다.

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. `ModelParser`를 인스턴스화하고 `ParseAsync`를 호출합니다.

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. 유효성 검사 오류를 확인합니다. 파서가 오류를 발견하면 오류 목록과 함께 `ParsingException`이 throw됩니다.

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (ParsingException pex)
    {
        Console.WriteLine(pex.Message);
        foreach (var err in pex.Errors)
        {
            Console.WriteLine(err.PrimaryID);
            Console.WriteLine(err.Message);
        }
    }
    ```

1. `Model`을 검사합니다. 유효성 검사에 성공하면 모델 파서 API를 사용하여 모델을 검사할 수 있습니다. 다음 코드 조각은 구문 분석된 모든 모델을 반복하고 기존 속성을 표시하는 방법을 보여 줍니다.

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>다음 단계

이 문서에서 검토한 모델 파서 API를 사용하면 여러 시나리오에서 DTDL 모델에 종속된 작업을 자동화하거나 유효성을 검사할 수 있습니다. 예를 들어 모델의 정보에서 UI를 동적으로 빌드할 수 있습니다.
