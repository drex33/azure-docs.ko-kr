---
title: 디지털 트윈에 태그 추가
titleSuffix: Azure Digital Twins
description: 디지털 트윈에서 태그를 구현하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 8/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8059178543bde38cbb1429f98f2f0205fb07c347
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771178"
---
# <a name="add-tags-to-digital-twins"></a>디지털 트윈에 태그 추가 

태그 개념을 사용하여 디지털 트윈을 추가로 식별하고 분류할 수 있습니다. 특히 사용자는 해당 Azure Digital Twins 인스턴스 내에서 [Haystack Tags](https://project-haystack.org/doc/appendix/tags)와 같은 기존 시스템의 태그를 복제할 수 있습니다. 

이 문서에서는 디지털 트윈에서 태그를 구현하는 데 사용할 수 있는 패턴에 대해 설명합니다.

태그는 먼저 디지털 트윈을 설명하는 [모델](concepts-models.md) 내에 속성으로 추가됩니다. 그런 다음, 해당 속성이 모델을 기반으로 생성될 때 트윈에서 설정됩니다. 이후 [쿼리](concepts-query-language.md)에서 태그를 사용하여 트윈을 식별하고 필터링할 수 있습니다.

## <a name="marker-tags"></a>표식 태그 

**표식 태그** 는 "blue" 또는 "red"와 같이 디지털 트윈을 표시하거나 분류하는 데 사용되는 간단한 문자열입니다. 이 문자열은 태그의 이름이고, 표식 태그에는 의미 있는 값이 없습니다. 즉, 태그는 존재(또는 부재)로만 의미가 있습니다. 

### <a name="add-marker-tags-to-model"></a>모델에 표식 태그 추가 

표식 태그는 `string`에서 `boolean`로 [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 맵으로 모델링됩니다. 태그의 존재는 모두 중요하므로 부울 `mapValue`은 무시됩니다. 

다음은 표식 태그를 속성으로 구현하는 트윈 모델에서 발췌한 것입니다.

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>디지털 트윈에 표식 태그 추가

`tags` 속성이 디지털 트윈 모델의 일부이면 이 속성의 값을 설정하여 디지털 트윈의 표식 태그를 설정할 수 있습니다. 

다음은 [.NET SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)를 사용하여 트윈에 대한 `tags` 표식을 설정하는 방법에 대한 코드 예제입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesCsharp":::

위의 예제에 따라 만든, 태그 속성이 있는 트윈은 다음과 같이 표시됩니다.

```JSON
{
  "$dtId": "myTwinID",
  "$etag": "W/\"e7429259-6833-46b4-b443-200a77a468c2\"",
  "$metadata": {
    "$model": "dtmi:example:Room;1",
    "Temperature": {
      "lastUpdateTime": "2021-08-03T14:24:42.0850614Z"
    },
    "tags": {
      "lastUpdateTime": "2021-08-03T14:24:42.0850614Z"
    }
  },
  "Temperature": 75,
  "tags": {
    "VIP": true,
    "oceanview": true
  }
}
```

>[!TIP]
> CLI 또는 API를 사용하여 [쿼리](how-to-query-graph.md)하면 트윈의 JSON 표현을 확인할 수 있습니다.

### <a name="query-with-marker-tags"></a>표식 태그가 있는 쿼리

태그가 디지털 트윈에 추가되면 태그를 사용하여 쿼리에서 트윈을 필터링할 수 있습니다. 

다음은 "red"로 태그가 지정된 모든 트윈을 가져오는 쿼리입니다. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryMarkerTags1":::

더 복잡한 쿼리에 대한 태그를 결합할 수도 있습니다. 다음은 red가 아니라 round인 모든 트윈을 가져오는 쿼리입니다. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>값 태그 

**값 태그** 는 `"color": "blue"` 또는 `"color": "red"`와 같이 각 태그를 값으로 제공하는 데 사용되는 키-값 쌍입니다. 값 태그를 만들면 태그의 값을 무시하여 표식 태그로 사용할 수도 있습니다. 

### <a name="add-value-tags-to-model"></a>모델에 값 태그 추가 

값 태그는 `string`에서 `string`으로 [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 맵으로 모델링 됩니다. `mapKey`와 `mapValue` 모두 중요합니다. 

다음은 값 태그를 속성으로 구현하는 트윈 모델에서 발췌한 것입니다.

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>디지털 트윈에 값 태그 추가

표식 태그와 마찬가지로 모델에서 이 `tags` 속성의 값을 설정하여 디지털 트윈에서 값 태그를 설정할 수 있습니다. 값 태그를 표식 태그로 사용하려면 `tagValue` 필드를 빈 문자열 값(`""`)으로 설정하면 됩니다. 

아래에는 크기를 나타내는 값 태그가 있는 두 트윈의 JSON 본문이 나와 있습니다. 예제의 트윈에는 표식 태그로 사용되는 “red” 또는 “purple”의 값 태그도 있습니다.

large 크기의 값 태그와 “red”의 표식 태그가 있는 예제 Twin1:

```JSON
{
  "$dtId": "Twin1",
  "$etag": "W/\"d3997593-cc5f-4d8a-8683-957becc2bcdd\"",
  "$metadata": {
    "$model": "dtmi:example:ValueTags;1",
    "tags": {
      "lastUpdateTime": "2021-08-03T14:43:02.3150852Z"
    }
  },
  "tags": {
    "red": "",
    "size": "large"
  }
}
```

small 크기의 값 태그와 “purple”의 표식 태그가 있는 예제 Twin2:
```JSON
{
  "$dtId": "Twin2",
  "$etag": "W/\"e215e586-b14a-4234-8ddb-be69ebfef878\"",
  "$metadata": {
    "$model": "dtmi:example:ValueTags;1",
    "tags": {
      "lastUpdateTime": "2021-08-03T14:43:53.1517123Z"
    }
  },
  "tags": {
    "purple": "",
    "size": "small"
  }
}
```

### <a name="query-with-value-tags"></a>값 태그가 있는 쿼리

표식 태그와 마찬가지로 값 태그를 사용하여 쿼리에서 트윈을 필터링할 수 있습니다. 값 태그와 표식 태그를 함께 사용할 수도 있습니다.

위 예에서는 `red`가 표식 태그로 사용됩니다. 이는 "red"로 태그가 지정된 모든 트윈을 가져오는 쿼리입니다. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryMarkerTags1":::

다음은 red가 아니라 small(값 태그)인 모든 엔터티를 가져오는 쿼리입니다. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>다음 단계

디지털 트윈 모델 디자인 및 관리에 대해 자세히 알아보기
* [DTDL 모델 관리](how-to-manage-model.md)

트윈 그래프 쿼리에 대해 자세히 알아보기
* [쌍 그래프 쿼리](how-to-query-graph.md)
