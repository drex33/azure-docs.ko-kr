---
title: 모델 구문 분석 및 유효성 검사
titleSuffix: Azure Digital Twins
description: 파서 라이브러리를 사용하여 DTDL 모델을 구문 분석하는 방법을 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 9/2/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: e77fc64277cd0210a4741bc6828b5f1f72cf0e73
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831010"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>DTDL 파서 라이브러리를 사용하여 모델 구문 분석 및 유효성 검사

Azure Digital Twins의 [모델](concepts-models.md)은 JSON-LD 기반 DTDL(디지털 트윈 정의 언어)을 사용하여 정의됩니다. **Azure Digital Twins 인스턴스에 업로드하기 전에 오프라인으로 모델의 유효성을 검사하는 것이 좋습니다.**

모델의 유효성을 검사하는 데 도움이 되는 .NET 클라이언트 쪽 DTDL 구문 분석 라이브러리는 NuGet [Microsoft.Azure.DigitalTwins.Parser에](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)제공됩니다. 

파서 라이브러리를 C# 코드에서 직접 사용하거나, 파서 라이브러리: [DTDL 유효성 검사기 샘플](/samples/azure-samples/dtdl-validator/dtdl-validator)에서 빌드된 언어 중립적 코드 샘플 프로젝트를 사용할 수 있습니다.

## <a name="use-the-dtdl-validator-sample"></a>DTDL 유효성 검사기 샘플 사용

[DTDL 유효성 검사기](/samples/azure-samples/dtdl-validator/dtdl-validator)는 DTDL이 유효한지 확인하도록 모델 문서의 유효성을 검사할 수 있는 샘플 프로젝트입니다. .NET 파서 라이브러리를 기반으로 하며 언어에 구애받지 않습니다. 샘플 링크의 *ZIP 다운로드* 단추를 사용하여 가져올 수 있습니다.

소스 코드는 파서 라이브러리를 사용하는 방법에 대한 예제를 보여줍니다. 유효성 검사기 샘플을 명령줄 유틸리티로 사용하여 DTDL 파일의 디렉터리 트리 유효성을 검사할 수 있습니다. 또한 대화형 모드를 제공합니다.

DTDL 유효성 검사기 샘플 폴더에서 *readme.md* 파일을 참조하여 샘플을 자체적으로 포함된 실행 파일로 패키징하는 방법을 알아보세요.

자체적으로 포함된 패키지를 빌드하고 경로에 실행 파일을 추가한 후에는 컴퓨터의 콘솔에서 다음 명령을 사용하여 유효성 검사기를 실행할 수 있습니다.

```cmd/sh
DTDLValidator
```

기본 옵션을 사용하여 샘플은 현재 디렉터리 및 모든 하위 디렉터리에서 .json 파일을 검색합니다. 다음 옵션을 추가하여 샘플에서 .dtdl 파일 이름 확장명을 가진 파일을 표시된 디렉터리와 모든 하위 디렉터리에서 검색하도록 할 수 있습니다.

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

`-i` 옵션을 추가하면 샘플이 대화형 모드로 시작됩니다.

```cmd/sh
DTDLValidator -i
```

이 샘플에 대한 자세한 내용을 알아보려면 소스 코드를 참조하거나 `DTDLValidator --help`를 실행하세요.

## <a name="use-the-net-parser-library"></a>.NET 파서 라이브러리 사용 

[Microsoft.Azure.DigitalTwins.Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) 라이브러리는 DTDL 정의에 대한 모델 액세스를 제공하며, 기본적으로 DTDL에 대한 C# 리플렉션과 동등한 역할을 합니다. 이 라이브러리는 특히 시각적 개체 또는 텍스트 편집기에서 DTDL 유효성 검사를 위해 [Azure Digital Twins SDK](concepts-apis-sdks.md)와 별개로 사용할 수 있습니다. 서비스에 업로드하기 전에 모델 정의 파일이 유효한지 확인하는 데 유용합니다.

파서 라이브러리 사용을 위해 DTDL 문서 집합을 제공합니다. 일반적으로 서비스에서 이러한 모델 문서를 검색하지만 클라이언트에서 먼저 서비스에 업로드해야 하는 경우 로컬에서 검색이 가능하도록 할 수 있습니다. 

파서 사용에 대한 일반적인 워크플로는 다음과 같습니다.
1. 서비스에서 일부 또는 모든 DTDL 문서를 검색합니다.
2. 반환된 메모리 내 DTDL 문서를 파서에 전달합니다.
3. 파서에서 전달된 문서 집합의 유효성을 검사하고, 자세한 오류 정보를 반환합니다. 이 기능은 편집기 시나리오에서 유용합니다.
4. 파서 API를 사용하여 문서 집합에 포함된 모델을 계속 분석할 수 있습니다. 

파서 기능은 다음과 같습니다.
* 모든 구현된 모델 인터페이스(인터페이스의 `extends` 섹션의 콘텐츠)를 가져옵니다.
* 모델에 선언된 모든 속성, 원격 분석, 명령, 구성 요소 및 관계를 가져옵니다. 이 명령은 또한 이러한 정의에 포함된 메타데이터를 모두 가져오고, 상속(`extends` 섹션)을 고려합니다.
* 모든 복합 모델 정의를 가져옵니다.
* 모델을 다른 모델에서 할당할 수 있는지 여부를 확인합니다.

> [!NOTE]
> [IoT 플러그 앤 플레이](../iot-develop/overview-iot-plug-and-play.md) 디바이스는 작은 구문 변형을 사용하여 기능을 설명합니다. 이 구문 변형은 Azure Digital Twins에서 사용되는 DTDL의 의미 체계적으로 호환되는 하위 집합입니다. 파서 라이브러리를 사용하는 경우 디지털 쌍에 대한 DTDL을 만드는 데 사용된 구문 변형을 알 필요가 없습니다. 파서는 기본적으로 항상 IoT 플러그 앤 플레이와 Azure Digital Twins 구문 모두에 대해 동일한 모델을 반환합니다.

### <a name="code-with-the-parser-library"></a>파서 라이브러리를 사용하는 코드

애플리케이션에서 모델의 유효성을 검사하거나 동적인 모델 기반 UI, 대시보드 및 보고서를 생성하는 등의 작업을 수행하기 위해 파서 라이브러리를 직접 사용할 수 있습니다.

아래의 파서 코드 예제를 지원하려면 Azure Digital Twins 인스턴스에 정의된 몇 가지 모델을 고려하세요.

:::code language="json" source="~/digital-twins-docs-samples/models/coffeeMaker-coffeeMakerInterface-coffeeBar.json":::

다음 코드에서는 파서 라이브러리를 사용하여 C#에서 이러한 정의를 반영하는 방법의 예를 보여줍니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/parseModels.cs":::

## <a name="next-steps"></a>다음 단계

모델 작성이 완료되면 DigitalTwinsModels API를 사용하여 모델을 업로드하고 다른 관리 작업을 수행하는 방법을 참조하세요.
* [DTDL 모델 관리](how-to-manage-model.md)