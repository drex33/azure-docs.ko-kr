---
title: 쌍 그래프 및 관계 관리
titleSuffix: Azure Digital Twins
description: 관계에 디지털 트윈을 연결하여 그래프를 관리하는 방법을 확인합니다.
author: baanders
ms.author: baanders
ms.date: 9/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5c02ad72ace0568f7be1062993adaf154bb31cbf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624314"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>관계를 사용하여 디지털 트윈의 그래프 관리

Azure Digital Twins의 핵심은 전체 환경을 나타내는 [트윈 그래프](concepts-twins-graph.md)입니다. 트윈 그래프는 **관계** 를 통해 연결되는 개별 디지털 트윈으로 구성됩니다. 

작동하는 [Azure Digital Twins 인스턴스](how-to-set-up-instance-portal.md)가 있고 클라이언트 앱에서 [인증](how-to-authenticate-client.md) 코드를 설정한 후에는 Azure Digital Twins 인스턴스에서 디지털 트윈 및 그 관계를 만들고, 수정하고, 삭제할 수 있습니다.

이 문서에서는 관계와 그래프를 한꺼번에 관리하는 데 초점을 맞춥니다. 개별 디지털 트윈 작업은 [디지털 트윈 관리](how-to-manage-twin.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="샘플 모델 및 트윈을 보여주는 Azure Digital Twins Explorer의 스크린샷." lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::


## <a name="create-relationships"></a>관계 만들기

관계는 다양한 디지털 트윈이 서로 연결된 방식을 설명하며 트윈 그래프의 기초가 됩니다.

관계는 `CreateOrReplaceRelationshipAsync()` 호출을 사용하여 만듭니다. 

관계를 만들려면 다음을 지정해야 합니다.
* 원본 트윈 ID(아래 코드 샘플의 `srcId`): 관계가 시작되는 트윈의 ID입니다.
* 대상 트윈 ID(아래 코드 샘플의 `targetId`): 관계가 도착하는 트윈의 ID입니다.
* 관계 이름(아래 코드 샘플의 `relName`): 관계의 제네릭 형식입니다(예: _포함_).
* 관계 ID(아래 코드 샘플의 `relId`): 이 관계의 특정 이름입니다(예: _Relationship1_).

관계 ID는 주어진 원본 트윈 안에서 고유해야 합니다. 전역적으로 고유할 필요는 없습니다.
예를 들어 트윈 Foo의 경우 각 특정 관계 ID가 고유해야 합니다. 그러나 다른 트윈인 Bar에는 Foo 관계의 동일한 ID와 일치하는 발신 관계가 있을 수 있습니다.

다음 코드 샘플에서는 Azure Digital Twins 인스턴스에서 관계를 만드는 방법을 보여 줍니다. 여기서는 더 큰 프로그램의 컨텍스트에 나타날 수 있는 사용자 지정 메서드 안에서 SDK 호출(강조 표시)을 사용합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

이제 이 사용자 지정 함수를 호출하여 다음 방법으로 _포함_ 관계를 만들 수 있습니다. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

여러 관계를 만들려는 경우 동일한 메서드에 대한 호출을 반복하여 다양한 관계 형식을 인수에 전달할 수 있습니다. 

도우미 클래스 `BasicRelationship`에 대한 자세한 내용은 [Azure Digital Twins API 및 SDK](concepts-apis-sdks.md#serialization-helpers)를 참조하세요.

### <a name="create-multiple-relationships-between-twins"></a>트윈 간에 여러 관계 만들기

관계는 다음 중 하나로 분류할 수 있습니다. 

* 발신 관계: 다른 트윈에 연결하기 위해 바깥쪽을 향하는 이 트윈에 속한 관계입니다. `GetRelationshipsAsync()` 메서드는 트윈의 발신 관계를 얻는 데 사용됩니다.
* 수신 관계: “들어오는” 링크를 만들기 위해 이 트윈 쪽을 가리키는 다른 트윈에 속한 관계입니다. `GetIncomingRelationshipsAsync()` 메서드는 트윈의 수신 관계를 얻는 데 사용됩니다.

두 트윈 간에 가질 수 있는 관계의 수에는 제한이 없고 원하는 만큼 트윈 간에 관계를 만들 수 있습니다. 

즉, 한 번에 두 트윈 사이에 여러 다른 형식의 관계를 표현할 수 있습니다. 예를 들어 트윈 A는 트윈 B와 *저장됨* 관계와 *제조됨* 관계를 모두 가질 수 있습니다.

원하는 경우 동일한 두 트윈 간에 같은 형식의 관계 인스턴스를 여러 개 만들 수도 있습니다. 이 예제에서 트윈 A는 트윈 B와 관계 ID가 서로 다른 두 개의 *저장됨* 관계를 가질 수 있습니다.

## <a name="list-relationships"></a>관계 목록

### <a name="list-properties-of-a-single-relationship"></a>단일 관계의 속성 나열

언제든지 관계 데이터를 선택한 형식으로 역직렬화할 수 있습니다. 관계에 대한 기본 액세스를 위해 `BasicRelationship` 형식을 사용합니다. `BasicRelationship` 도우미 클래스는 `IDictionary<string, object>`를 통해 관계에 정의된 속성에 액세스할 수 있습니다. 속성을 나열하려면 다음을 사용할 수 있습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

### <a name="list-outgoing-relationships-from-a-digital-twin"></a>디지털 트윈에서 나가는 관계 나열

그래프의 특정 트윈에 대한 **발신** 관계 목록에 액세스하기 위해 다음과 같이 `GetRelationships()` 메서드를 사용할 수 있습니다. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

이 메서드는 호출의 동기 또는 비동기 버전 사용 여부에 따라 `Azure.Pageable<T>` 또는 `Azure.AsyncPageable<T>`을 반환합니다.

다음은 관계 목록을 검색하는 예입니다. 여기서는 더 큰 프로그램의 컨텍스트에 나타날 수 있는 사용자 지정 메서드 안에서 SDK 호출(강조 표시)을 사용합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

이제 이 사용자 지정 메서드를 호출하여 다음과 같이 트윈의 발신 관계를 확인할 수 있습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

검색된 관계를 통해 반환된 관계에서 `target` 필드를 읽고 이를 `GetDigitalTwin()`에 대한 다음 호출의 ID로 사용하여 그래프의 다른 트윈으로 이동할 수 있습니다.

### <a name="list-incoming-relationships-to-a-digital-twin"></a>디지털 트윈의 수신 관계 나열

Azure Digital Twins에는 주어진 트윈의 모든 **수신** 관계를 찾는 SDK 호출도 있습니다. 이 SDK는 종종 역방향 탐색이나 트윈을 삭제할 때 유용합니다.

>[!NOTE]
> `IncomingRelationship` 호출은 관계의 전체 본문을 반환하지 않습니다. `IncomingRelationship` 클래스에 대한 자세한 내용은 [참조 문서](/dotnet/api/azure.digitaltwins.core.incomingrelationship?view=azure-dotnet&preserve-view=true)를 확인하세요.

이전 섹션의 코드 샘플에서는 트윈으로부터의 발신 관계에 초점을 맞추었습니다. 다음 예제는 구성이 유사하나 트윈으로의 *수신* 관계를 찾습니다. 이 예제에서도 더 큰 프로그램의 컨텍스트에 나타날 수 있는 사용자 지정 메서드 안에서 SDK 호출(강조 표시)을 사용합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

이제 이 사용자 지정 메서드를 호출하여 다음과 같이 트윈의 수신 관계를 확인할 수 있습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>모든 트윈 속성 및 관계 나열

위의 메서드를 사용하여 트윈에 대한 발신 및 수신 관계를 나열하면 트윈의 속성, 두 관계 유형 등을 비롯하여 전체 트윈 정보를 출력하는 메서드를 만들 수 있습니다. 다음은 이 목적으로 위의 사용자 지정 메서드를 결합하는 방법을 보여 주는 사용자 지정 메서드의 예입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

이제 다음과 같이 이 사용자 지정 함수를 호출할 수 있습니다. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>관계 업데이트

관계는 `UpdateRelationship` 메서드를 사용하여 업데이트합니다. 

>[!NOTE]
>이 메서드는 관계의 **속성** 을 업데이트하기 위한 것입니다. 관계의 원본 트윈이나 대상 트윈을 변경해야 할 경우 [관계를 삭제](#delete-relationships)하고 새 트윈을 사용하여 [다시 만들어야](#create-relationships) 합니다.

클라이언트 호출에 필요한 매개 변수는 다음과 같습니다.
- 원본 트윈(관계가 시작된 트윈)의 ID입니다.
- 업데이트할 관계의 ID입니다.
- 업데이트하려는 속성과 새 값이 포함된 [JSON 패치](http://jsonpatch.com/) 문서.

다음은 이 방법을 사용하는 방법을 보여 주는 샘플 코드 조각입니다. 이 예제에서는 더 큰 프로그램의 컨텍스트에 나타날 수 있는 사용자 지정 메서드 안에서 SDK 호출(강조 표시)을 사용합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

다음은 이 사용자 지정 메서드에 대한 호출의 예로, 속성을 업데이트하기 위한 정보가 담긴 JSON 패치 문서를 전달합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>관계 삭제

첫 번째 매개 변수는 원본 트윈(관계가 시작되는 트윈)을 지정합니다. 다른 매개 변수는 관계 ID입니다. 관계 ID는 트윈 범위 내에서만 고유하므로 트윈 ID와 관계 ID가 모두 필요합니다.

이 메서드의 사용 방법을 보여 주는 샘플 코드는 다음과 같습니다. 이 예제에서는 더 큰 프로그램의 컨텍스트에 나타날 수 있는 사용자 지정 메서드 안에서 SDK 호출(강조 표시)을 사용합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

이제 이 사용자 지정 메서드를 호출하여 다음과 같은 관계를 삭제할 수 있습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="create-graph-from-a-csv-file"></a>CSV 파일에서 그래프 만들기

실제 사용 사례에서 트윈 계층 구조는 종종 다른 데이터베이스, 또는 스프레드시트나 CSV 파일에 저장된 데이터로부터 만들어집니다. 이 섹션에서는 CSV 파일에서 데이터를 읽어 트윈 그래프를 만드는 방법을 보여 줍니다.

디지털 트윈 및 관계 집합을 설명하는 다음 데이터 테이블을 고려합니다.

|  모델 ID    | 트윈 ID(고유해야 함) | 관계 이름  | 대상 트윈 ID  | 트윈 init 데이터 |
| --- | --- | --- | --- | --- |
| dtmi:example:Floor;1    | Floor1 | 포함 | Room1 | |
| dtmi:example:Floor;1    | Floor0 | 포함 | Room0 | |
| dtmi:example:Room;1    | Room1 | | | {"Temperature": 80} |
| dtmi:example:Room;1    | Room0 | | | {"Temperature": 70} |

이 데이터를 Azure Digital Twins로 가져오는 한 가지 방법은 테이블을 CSV 파일로 변환하는 것입니다. 테이블이 변환되면 파일을 명령으로 해석하여 트윈과 관계를 만드는 코드를 작성할 수 있습니다. 다음 코드 샘플은 CSV 파일에서 데이터를 읽고 Azure Digital Twins에서 트윈 그래프를 만드는 방법을 보여 줍니다.

아래 코드에서는 CSV 파일을 *data.csv* 라고 하며, Azure Digital Twins 인스턴스의 **호스트 이름** 을 나타내는 자리 표시자가 있습니다. 이 샘플에서는 이 프로세스를 지원하기 위해 프로젝트에 추가할 수 있는 몇 가지 패키지도 사용합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="runnable-twin-graph-sample"></a>실행 가능한 트윈 그래프 샘플

다음의 실행 가능한 코드 조각은 이 문서의 관계 작업을 사용하여 디지털 트윈과 관계로부터 트윈 그래프를 만듭니다.

### <a name="set-up-sample-project-files"></a>샘플 프로젝트 파일 설정

이 조각은 [Room.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-samples/master/AdtSampleApp/SampleClientApp/Models/Room.json) 및 [Floor.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-samples/master/AdtSampleApp/SampleClientApp/Models/Floor.json)이라는 두 가지 샘플 모델 정의를 사용합니다. 코드에서 사용할 수 있도록 **모델 파일을 다운로드** 하려면 다음 링크를 사용하여 GitHub의 파일로 직접 이동합니다. 그런 다음, 화면의 아무 곳이나 마우스 오른쪽 단추로 클릭하고 브라우저의 오른쪽 클릭 메뉴에서 **다른 이름으로 저장** 을 선택한 다음, 다른 이름으로 저장 창을 사용하여 파일을 **Room.json** 및 **Floor.json** 으로 저장합니다.

다음으로, Visual Studio 또는 원하는 편집기에서 **새 콘솔 앱 프로젝트** 를 만듭니다.

그런 다음, 프로젝트에 실행 가능한 샘플의 **다음 코드를 복사합니다**.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

### <a name="configure-project"></a>프로젝트 구성

다음으로, 다음 단계를 완료하여 프로젝트 코드를 구성합니다.
1. 이전에 다운로드한 **Room.json** 및 **Floor.json** 파일을 프로젝트에 추가하고 코드의 `<path-to>` 자리 표시자를 대체하여 프로그램에서 찾을 위치를 알려줍니다.
1. `<your-instance-hostname>` 자리 표시자를 Azure Digital Twins 인스턴스의 호스트 이름으로 바꿉니다.
1. Azure Digital Twins와 함께 작동하는 데 필요한 두 개의 종속성을 프로젝트에 추가합니다. 첫 번째는 [.NET용 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)의 패키지이고, 두 번째는 Azure에 대한 인증을 지원하는 도구를 제공합니다.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

또한 샘플을 직접 실행하려면 로컬 자격 증명을 설정해야 합니다. 다음 섹션에서는 이 과정을 안내합니다.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>샘플 실행

이제 설치를 완료했으므로 샘플 코드 프로젝트를 실행할 수 있습니다.

다음은 프로그램의 콘솔 출력입니다. 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="트윈의 수신 및 발신 관계를 포함하는 트윈 세부 정보를 보여주는 콘솔 출력의 스크린샷." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> 트윈 그래프는 트윈 간의 관계를 만드는 개념입니다. 트윈 그래프의 시각적 표시를 보려면 이 문서의 [시각화](how-to-manage-graph.md#visualization) 섹션을 참조하세요. 

## <a name="next-steps"></a>다음 단계

Azure Digital Twins 트윈 그래프를 쿼리하는 방법을 살펴봅니다.
* [쿼리 언어](concepts-query-language.md)
* [쌍 그래프 쿼리](how-to-query-graph.md)