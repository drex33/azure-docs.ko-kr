---
title: Azure Search .NET SDK 버전 9로 업그레이드
titleSuffix: Azure Cognitive Search
description: 이전 버전에서 Azure Search .NET SDK 버전 9로 코드를 마이그레이션합니다. 새로운 기능과 필요한 코드 변경 내용을 알아봅니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/16/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 71a7c60d1665f3b9267ef02c3ed3d7a71653de2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553225"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Azure Search .NET SDK 버전 9로 업그레이드

버전 7.0-preview 또는 이전 버전의 [Azure Search .NET SDK](/dotnet/api/overview/azure/search)를 사용하는 경우 이 문서를 통해 버전 9로 애플리케이션을 업그레이드할 수 있습니다.

> [!NOTE]
> 버전 8.0-preview를 사용하여 일반적으로 사용할 수 없는 기능을 평가하려는 경우 이 문서의 지침에 따라 이전 버전에서 8.0-preview로 업그레이드할 수도 있습니다.

예제를 비롯하여 SDK에 대한 보다 일반적인 연습은 [.NET 애플리케이션에서 Azure Search를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

Azure Search .NET SDK 버전 5에는 이전 버전에서 변경된 사항이 일부 포함되어 있습니다. 이러한 변경 내용 중 일부는 호환성이 손상되는 변경이지만 코드를 비교적 약간만 변경해야 합니다. 새 SDK 버전을 사용하는 코드를 변경하는 방법에 대한 지침은 [업그레이드 단계](#UpgradeSteps) 를 참조하세요.

> [!NOTE]
> 4\.0-preview 또는 이전 버전을 사용하는 경우, 먼저 버전 5로 업그레이드한 후 버전 9로 업그레이드해야 합니다. 설명은 [Azure Search .NET SDK 버전 5로 업그레이드](search-dotnet-sdk-migration-version-5.md)를 참조하세요.
>
> Azure Search 서비스 인스턴스는 최신 버전을 포함한 여러 REST API 버전을 지원합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 코드를 마이그레이션하여 최신 버전을 사용하는 것이 좋습니다. REST API를 사용하는 경우 api-version 매개 변수를 통해 모든 요청에 API 버전을 지정해야 합니다. .NET SDK를 사용하는 경우 사용 중인 SDK의 버전에 따라 해당하는 REST API 버전이 결정됩니다. 이전 버전의 SDK를 사용하는 경우 최신 API 버전을 지원하기 위해 서비스가 업그레이드된 경우에도 변경 내용 없이 해당 코드를 계속 실행할 수 있습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>버전 9의 새로운 기능
Azure Search .NET SDK 버전 9는 다음 기능을 포함하는 Azure Search REST API의 2019-05-06 버전을 대상으로 합니다.

* [AI 보강](cognitive-search-concept-intro.md)은 이미지, blob 및 기타 비정형 데이터 소스에서 텍스트를 추출하는 기능으로, 콘텐츠를 Azure Search 인덱스에서 보다 검색 가능하도록 강화합니다.
* [복합 형식](search-howto-complex-data-types.md)을 지원하면 Azure Search 인덱스에서 거의 모든 중첩된 JSON 구조를 모델링할 수 있습니다.
* [자동 완성](search-add-autocomplete-suggestions.md)은 증분 검색 동작을 구현하기 위한 **API 제안** 에 대한 대안을 제공합니다. 자동 완성은 사용자가 현재 입력하고 있는 단어 또는 구를 "완성"합니다.
* Blob 인덱싱의 일부인 [JsonLines 구문 분석 모드](search-howto-index-json-blobs.md)는 JSON 엔터티 마다 하나의 검색 문서가 작성되고, 이 문서는 새로운 행으로 구분됩니다.

### <a name="new-preview-features-in-version-80-preview"></a>버전 8.0-preview의 새로운 미리 보기 기능
Azure Search .NET SDK의 버전 8.0-preview는 API 버전 2017-11-11-Preview를 대상으로 합니다. 이 버전에는 버전 9의 모든 기능이 포함되어 있고, 이에 더하여:

* 서비스 측면 데이터 암호화에 대한 [고객 관리 암호화 키는](search-security-manage-encryption-keys.md) 새로운 preview 기능입니다. Microsoft에서 관리하는 기본 제공 데이터 암호화 외에도 키의 유일한 소유자가 되는 추가 암호화 계층을 적용할 수 있습니다.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계
먼저, NuGet 패키지 관리자 콘솔을 사용하거나 Visual Studio에서 프로젝트 참조를 마우스 오른쪽 단추로 클릭하고 "NuGet 패키지 관리..."를 선택하여 `Microsoft.Azure.Search` 에 대한 NuGet 참조를 업데이트합니다.

NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 코드가 구조화된 방식에 따라 다시 빌드할 수 있습니다. 다시 빌드할 수 있으면 배포할 준비가 된 것입니다.

빌드에 실패하는 경우 각 빌드 오류를 수정해야 합니다. 잠재적 빌드 오류를 해결하는 방법에 대한 자세한 내용은 [버전 9의 주요 변경 내용](#ListOfChanges)을 참조하세요.

사용되지 않은 메서드 또는 속성과 관련된 추가 빌드 경고가 표시될 수 있습니다. 경고에는 사용되지 않는 기능 대신 사용해야 하는 항목에 대한 지침이 포함됩니다. 예를 들어 애플리케이션이 `DataSourceType.DocumentDb` 속성을 사용하는 경우 다음과 같은 경고가 표시됩니다. “이 멤버는 더 이상 사용되지 않습니다. 대신 CosmosDb를 사용하십시오.”

모든 빌드 오류 또는 경고가 수정되면 원하는 경우 새로운 기능을 활용하도록 애플리케이션을 변경할 수 있습니다. SDK의 새로운 기능에 대한 내용은 [버전 9의 새로운 기능](#WhatsNew)에 자세히 나와 있습니다.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>버전 9의 주요 변경 내용

애플리케이션을 다시 빌드하는 것 외에도, 버전 9에는 코드 변경이 필요할 수 있는 몇 가지 주요 변경 내용이 있습니다.

> [!NOTE]
> 아래 변경 사항 목록은 전부가 아닙니다. 일부 변경 내용으로 인해 빌드 오류가 발생하지는 않지만, 이전 버전의 Azure Search.NET SDK 어셈블리에 종속된 어셈블리와의 이진 호환성이 단절되기 때문에 기술적으로 중단됩니다. 이러한 변경 사항은 아래에 나열되어 있지 않습니다. 이진 호환성 문제를 방지하려면 버전 9로 업그레이드할 때 애플리케이션을 다시 빌드하세요.

### <a name="immutable-properties"></a>변경할 수 없는 속성

이제 여러 모델 클래스의 공용 속성을 변경할 수 없습니다. 테스트를 위한 이 클래스의 사용자 지정 인스턴스를 만들어야 하는 경우, 새 매개 변수화된 생성자를 사용할 수 있습니다.

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>필드에 대한 변경

`Field`클래스가 복합 필드를 나타낼 수도 있으므로 이제 변경되었습니다.

이제 다음 `bool`의 속성을 null로 설정할 수 있습니다.

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

이러한 속성은 이제 `null`복잡한 필드의 경우에 필요하기 때문입니다. 이러한 속성을 읽는 코드가 있는 경우, 이를 처리하도록 준비해야 합니다`null`. `Field`의 다른 모든 속성은 항상 null일 수 있으며, 이러한 속성 중 일부는 복잡한 필드의 경우에도 `null`이(가) 됩니다. -- 특히 다음과 같습니다.

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

`Field`의 매개 변수 없는 생성자가 생성되었습니다`internal`. 지금부터의 모든 `Field`에는 생성 시 명시적인 이름 및 데이터 형식이 필요합니다.

### <a name="simplified-batch-and-results-types"></a>단순화된 batch 및 결과 형식

7\.0-preview 및 이전 버전에서는 문서 그룹을 캡슐화하는 다양한 클래스가 병렬 클래스 계층 구조로 구성되었습니다.

  -  `DocumentSearchResultBase`에서 상속된 `DocumentSearchResult`과 `DocumentSearchResult<T>`
  -  `DocumentSuggestResult`에서 상속된 `DocumentSuggestResult<T>`과 `DocumentSuggestResultBase`
  -  `IndexAction`에서 상속된 `IndexAction<T>`과 `IndexActionBase`
  -  `IndexBatch`에서 상속된 `IndexBatch<T>`과 `IndexBatchBase`
  -  `SearchResult`에서 상속된 `SearchResult<T>`과 `SearchResultBase`
  -  `SuggestResult`에서 상속된 `SuggestResult<T>`과 `SuggestResultBase`

제네릭 형식 매개 변수 없이 파생된 형식은 "동적으로 형식화된" 시나리오에서 사용되며 `Document`형식의 사용을 가정합니다.

8\.0-preview 버전부터 기본 클래스 및 제네릭이 아닌 파생 클래스가 모두 제거되었습니다. 동적으로 형식화된 시나리오의 경우, `IndexBatch<Document>`, `DocumentSearchResult<Document>` 등을 사용할 수 있습니다.
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum이 제거됨

`ExtensibleEnum` 기본 클래스가 제거되었습니다. 예를 들어, 여기에서 파생된 모든 클래스는 이제 `AnalyzerName`, `DataType` 및 `DataSourceType`과(와) 같은 구조입니다. 해당 `Create` 메서드도 제거되었습니다. `Create`애 대한 호출은 문자열에서 암시적으로 변환할 수 있기 때문에 제거할 수 있습니다. 이로 인해 컴파일러 오류가 발생하는 경우, 캐스트를 통해 변환 연산자를 명시적으로 호출하여 형식을 분명히 구분할 수 있습니다. 예를 들어 다음과 같은 코드를 변경할 수 있습니다.

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

아래와 같이 변환합니다.

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

이러한 형식의 옵션 값을 보유하는 속성은 이제 null 허용으로 명시적으로 형식화되므로 선택적으로 계속 사용할 수 있습니다.

### <a name="removed-facetresults-and-hithighlights"></a>FacetResults 및 HitHighlights 제거됨

`FacetResults` 및 `HitHighlights` 클래스의 이름이 변경되었습니다. 패싯 결과는 이제 `IDictionary<string, IList<FacetResult>>`로 형식화되며 `IDictionary<string, IList<string>>`로 강조 표시됩니다. 이러한 변경으로 인해 발생하는 빌드 오류를 신속하게 해결하는 방법은 `using` 제거된 형식을 사용하는 각 파일의 맨 위에 별칭을 추가하는 것입니다. 예를 들면 다음과 같습니다.

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>SynonymMap으로 변경 

`SynonymMap` 생성자에는 더 이상 `SynonymMapFormat`에 대한 `enum` 매개 변수가 없습니다. 이 열거형에는 하나의 값만 있으므로 중복되었습니다. 이로 인해 빌드 오류가 표시되면 `SynonymMapFormat` 매개 변수에 대한 참조만 제거하면 됩니다.

### <a name="miscellaneous-model-class-changes"></a>기타 모델 클래스 변경 내용

`AutocompleteParameters`의 `AutocompleteMode` 속성은 더 이상 null을 허용하지 않습니다. 이 속성을 `null`에 할당하는 코드가 있는 경우, 해당 속성을 제거하기만 하면 속성이 자동으로 기본값으로 초기화됩니다.

`IndexAction` 생성자에 대한 매개 변수 순서가 변경되어 이제 이 생성자가 자동으로 생성되었습니다. 생성자를 사용하는 대신 팩터리 메서드 `IndexAction.Upload`, `IndexAction.Merge` 등을 사용하는 것이 좋습니다.

### <a name="removed-preview-features"></a>제거된 미리 보기 기능

버전 8.0-preview에서 버전 9로 업그레이드하는 경우, 이 기능은 아직 미리 보기 상태이므로 고객 관리 키로 설정한 암호화가 제거된 것을 알고 있어야 합니다. 특히 `Index`와 `SynonymMap`의 `EncryptionKey` 속성이 제거되었습니다.

애플리케이션이 이러한 기능에 강하게 종속될 경우 Azure Search .NET SDK 버전 9로 업그레이드하지 못할 수 있습니다. 버전 8.0-preview는 계속 사용할 수 있습니다. 하지만 **프로덕션 애플리케이션에서는 미리 보기 SDK를 사용하지 않는 것이 좋습니다**. 미리 보기 기능은 평가용일 뿐이며 변경될 수 있습니다.

> [!NOTE]
> SDK의 버전 8.0-preview를 사용하여 암호화된 인덱스나 동의어 맵을 만든 경우에도 해당 암호화 상태에 부정적인 영향을 주지 않고 SDK 버전 9를 사용하여 이를 사용하고 해당 정의를 수정할 수 있습니다. SDK 버전 9는 속성을 REST API로 속성 `encryptionKey`을 전송하지 않으며, 그 결과 REST API는 리소스의 암호화 상태를 변경하지 않습니다. 

### <a name="behavioral-change-in-data-retrieval"></a>데이터 검색의 동작 변경

`Document` 유형의 인스턴스를 반환하는 "동적인 유형" `Search`, `Suggest` 또는 `Get` API를 사용하는 경우, 이제 `string[]`이(가) 아닌 `object[]`(으)로 빈 JSON 배열을 역직렬화합니다.

## <a name="conclusion"></a>결론
Azure Search .NET SDK 사용에 대한 자세한 내용은 [.NET 방법](search-howto-dotnet-sdk.md)을 참조하세요.

SDK에 대한 귀하의 피드백을 환영합니다! 만약 문제가 발생하면 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search)에 대해 자유롭게 도움을 요청해 주십시오. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. 문제 제목에 "[Azure Search]"라는 접두사를 지정해야 합니다.

Azure Search를 이용해 주셔서 감사합니다!