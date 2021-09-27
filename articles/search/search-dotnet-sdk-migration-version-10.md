---
title: .NET SDK 버전 10으로 업그레이드
titleSuffix: Azure Cognitive Search
description: 이전 버전에서 Azure Cognitive Search .NET SDK 버전 10으로 코드를 마이그레이션합니다. 새로운 기능과 필요한 코드 변경 내용을 알아봅니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/16/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ff3398d326de07ea97b5e854efab1478ef4bad4e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665020"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Azure Cognitive Search .NET SDK 버전 10으로 업그레이드

버전 9.0 또는 이전 버전의 [.NET SDK](/dotnet/api/overview/azure/search)를 사용하는 경우 이 문서를 통해 버전 10으로 애플리케이션을 업그레이드할 수 있습니다.

Azure Search는 버전 10에서 Azure Cognitive Search로 이름이 변경되지만 네임스페이스와 패키지 이름은 변경되지 않습니다. 이전 버전의 SDK(9.0 및 이전 버전)는 계속해서 이전 이름을 사용합니다. 예제를 포함하여 SDK를 사용하는 방법에 대한 자세한 내용은 [.NET 애플리케이션에서 Azure Cognitive Search를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

버전 10은 여러 기능과 버그 수정을 추가하여 REST API 버전 `2019-05-06`과 동일한 기능 수준으로 구현됩니다. 변경 내용으로 인해 기존 코드가 중단되는 경우 [문제를 해결하는 데 필요한 단계](#UpgradeSteps)가 제공됩니다.

> [!NOTE]
> 8\.0-preview 또는 이전 버전을 사용하는 경우 먼저 버전 9로 업그레이드한 후 버전 10으로 업그레이드해야 합니다. 지침은 [Azure Search .NET SDK 버전 9로 업그레이드](search-dotnet-sdk-migration-version-9.md)를 참조하세요.
>
> 검색 서비스 인스턴스는 최신 버전을 포함한 여러 REST API 버전을 지원합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 코드를 마이그레이션하여 최신 버전을 사용하는 것이 좋습니다. REST API를 사용하는 경우 api-version 매개 변수를 통해 모든 요청에 API 버전을 지정해야 합니다. .NET SDK를 사용하는 경우 사용 중인 SDK의 버전에 따라 해당하는 REST API 버전이 결정됩니다. 이전 버전의 SDK를 사용하는 경우 최신 API 버전을 지원하기 위해 서비스가 업그레이드된 경우에도 변경 내용 없이 해당 코드를 계속 실행할 수 있습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>버전 10의 새로운 기능
Azure Cognitive Search .NET SDK의 버전 10은 다음 업데이트가 적용된 REST API `2019-05-06`을 대상으로 합니다.

* 두 가지 새로운 기술인 [조건부 기술](cognitive-search-skill-conditional.md) 및 [텍스트 번역 기술](cognitive-search-skill-text-translation.md) 도입
* [쉐이퍼 기술](cognitive-search-skill-shaper.md) 입력은 중첩된 컨텍스트의 통합을 수용할 수 있도록 재구성되었습니다. 자세한 내용은 [예제 JSON 정의](./cognitive-search-skill-shaper.md#scenario-3-input-consolidation-from-nested-contexts)를 참조하세요.
* 다음 2개의 새 [필드 매핑 함수](search-indexer-field-mappings.md) 추가:
    - [urlEncode](./search-indexer-field-mappings.md#urlencode-function)
    - [urlDecode](./search-indexer-field-mappings.md#urldecode-function)
* 경우에 따라 [인덱서 실행 상태](/rest/api/searchservice/get-indexer-status)에 표시되는 오류 및 경고에는 디버깅에 도움이 되는 추가 세부 정보가 있을 수 있습니다. `IndexerExecutionResult`가 이 동작을 반영하도록 업데이트되었습니다.
* [기술 세트](cognitive-search-defining-skillset.md) 내에 정의된 개별 기술은 `name` 속성을 지정하여 선택적으로 식별할 수 있습니다.
* `ServiceLimits`는 [복합 형식](./search-howto-complex-data-types.md)에 대한 제한을 표시하고 `IndexerExecutionInfo`는 관련 인덱서 제한/할당량을 표시합니다.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계

1. NuGet 패키지 관리자 콘솔을 사용하거나 Visual Studio에서 프로젝트 참조를 마우스 오른쪽 단추로 클릭하고 “NuGet 패키지 관리...”를 선택하여 `Microsoft.Azure.Search`에 대한 NuGet 참조를 업데이트합니다.

2. NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 

3. 빌드에 실패 하는 경우 각 빌드 오류를 수정해야 합니다. 잠재적 빌드 오류를 해결하는 방법에 대한 자세한 내용은 [버전 10의 주요 변경 내용](#ListOfChanges)을 참조하세요.

4. 모든 빌드 오류 또는 경고가 수정되면 원하는 경우 새로운 기능을 활용하도록 애플리케이션을 변경할 수 있습니다. SDK의 새로운 기능에 대한 자세한 내용은 [버전 10의 새로운 기능](#WhatsNew)에 나와 있습니다.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>버전 10의 주요 변경 내용

애플리케이션을 다시 빌드하는 것 외에도, 버전 10에는 코드 변경이 필요할 수 있는 몇 가지 주요 변경 내용이 있습니다.

> [!NOTE]
> 아래 변경 사항 목록은 전부가 아닙니다. 일부 변경 내용으로 인해 빌드 오류가 발생하지는 않지만, 이전 버전의 Azure Cognitive Search.NET SDK 어셈블리에 종속된 어셈블리와의 이진 호환성이 단절되기 때문에 기술적으로 중단됩니다. 이 범주에 속하는 중요한 변경 내용도 권장 사항과 함께 나열됩니다. 이진 호환성 문제를 방지하려면 버전 10으로 업그레이드할 때 애플리케이션을 다시 빌드하세요.

### <a name="custom-web-api-skill-definition"></a>사용자 지정 Web API 기술 정의

[사용자 지정 Web API 기술](cognitive-search-custom-skill-web-api.md) 정의가 버전 9 이상에서 잘못 지정되었습니다. 

`WebApiSkill`에 대한 모델은 `HttpHeaders`를 사전을 _포함_ 하는 개체 속성으로 지정했습니다. 이 방식으로 생성된 `WebApiSkill`을 사용하여 기술 세트를 만들면 REST API는 요청을 잘못된 형식으로 간주하기 때문에 예외가 발생합니다. 이 문제는 `WebApiSkill` 모델 자체에 대해 `HttpHeaders`를 **최상위 사전 속성** 으로 만들어 해결되었습니다(REST API의 유효한 요청으로 간주됨).

예를 들어, 이전에 다음과 같이 `WebApiSkill`을 인스턴스화하려고 시도한 경우

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

REST API의 유효성 검사 오류를 방지하려면 다음과 같이 변경합니다.

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>쉐이퍼 기술은 중첩된 컨텍스트 통합을 허용합니다.

쉐이퍼 기술은 이제 중첩된 컨텍스트의 입력 통합을 허용할 수 있습니다. 이 변경을 사용하도록 설정하기 위해 `Source` 속성만 지정하거나 `SourceContext` 및 `Inputs` 속성을 모두 지정하여 인스턴스화할 수 있도록 `InputFieldMappingEntry`를 수정했습니다.

거의 대부분의 경우에서 코드를 변경할 필요는 없지만 이러한 두 조합 중 하나만 사용할 수 있습니다. 다시 말해,

- `Source`만 초기화되는 `InputFieldMappingEntry`를 만드는 것이 적절합니다.
- `SourceContext` 및 `Inputs`만 초기화되는 `InputFieldMappingEntry`를 만드는 것이 적절합니다.
- 이러한 세 가지 속성을 포함하는 다른 모든 조합은 유효하지 않습니다.

이 새로운 기능을 사용하려면 해당 변경 내용을 롤아웃하기 전에 먼저 버전 10을 사용하도록 모든 클라이언트를 업데이트해야 합니다. 그러지 않으면 이전 버전의 SDK를 사용하여 클라이언트에서 쉐이퍼 기술에 업데이트하는 경우 유효성 검사 오류가 발생할 수 있습니다.

> [!NOTE]
> 기본 `InputFieldMappingEntry` 모델이 중첩된 컨텍스트의 통합을 허용하도록 수정된 경우에도 쉐이퍼 기술 정의 내에서만 사용하는 것이 적절합니다. 다른 기술에서 이 기능을 사용하는 경우 컴파일 타임에는 유효하지만 런타임에는 유효성 검사 오류가 발생합니다.

## <a name="skills-can-be-identified-by-a-name"></a>기술을 이름으로 식별할 수 있음

이제 기술 세트 내의 각 기술에는 기술 식별을 위해 코드에서 초기화할 수 있는 새 속성인 `Name`이 있습니다. 이 옵션은 선택 사항입니다. 지정하지 않으면(명시적 코드가 변경되지 않은 경우 기본값임) 기술 세트의 기술에 대해 1부터 시작하는 인덱스를 사용하고 '#' 문자를 접두사로 지정하여 기본 이름이 할당됩니다. 예를 들어, 다음 기술 세트 정의에서는 다음과 같은 사항이 적용됩니다(간단히 하기 위해 대부분의 초기화는 생략됨).

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill`에는 이름 `#1`이, `WebApiSkill`에는 `#2`가, `ShaperSkill`에는 `#3` 등과 같이 할당됩니다.

사용자 지정 이름으로 기술을 식별하도록 선택하는 경우 먼저 클라이언트의 모든 인스턴스를 SDK 버전 10으로 업데이트해야 합니다. 그러지 않으면 이전 버전의 SDK를 사용하는 클라이언트가 기술에 대한 `Name` 속성을 `null`로 처리하므로 클라이언트가 기본 이름 지정 체계로 대체됩니다.

## <a name="details-about-errors-and-warnings"></a>오류 및 경고에 대한 세부 정보

인덱서 실행 중에 발생하는 오류 및 경고에 대한 세부 정보를 각각 캡슐화하는 `ItemError` 및 `ItemWarning` 모델은 인덱서 디버깅에 도움을 주기 위해 세 개의 새로운 속성을 포함하도록 수정되었습니다. 이러한 속성은 다음과 같습니다.

- `Name`: 오류가 발생한 원본의 이름입니다. 예를 들어, 연결된 기술 세트의 특정 기술을 나타낼 수 있습니다.
- `Details`: 오류 또는 경고에 대한 추가 세부 정보입니다.
- `DocumentationLink`: 특정 오류 또는 경고에 대한 문제 해결 가이드로 연결되는 링크입니다.

> [!NOTE]
> 가능한 경우에 이러한 유용한 세부 정보를 포함하도록 오류 및 경고를 구조화하기 시작 했습니다. 모든 오류 및 경고에 대해 이러한 세부 정보가 표시될 수 있게 하려고 하지만 이 작업은 아직 진행 중이며, 이러한 추가 세부 정보가 항상 제공되지는 못할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 쉐이퍼 기술 변경 내용이 새 코드나 기존 코드에 영향을 미칠 수 있습니다. 다음 단계로, 입력 구조를 보여 주는 [쉐이퍼 기술 JSON 정의 예제](cognitive-search-skill-shaper.md)를 다시 확인해 보세요.
- [AI 보강 개요](cognitive-search-concept-intro.md)를 참조하세요.
- SDK에 대한 귀하의 피드백을 환영합니다! 만약 문제가 발생하면 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search)에 대해 자유롭게 도움을 요청해 주십시오. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. 이슈 제목에 "[Azure Cognitive Search]"라는 접두사를 지정해야 합니다.