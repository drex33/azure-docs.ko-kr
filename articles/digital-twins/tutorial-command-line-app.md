---
title: '자습서: Azure Digital Twins에서 그래프 만들기(클라이언트 앱)'
titleSuffix: Azure Digital Twins
description: 샘플 명령줄 애플리케이션을 사용하여 Azure Digital Twins 시나리오를 작성하는 자습서
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 4d75685ce62258c18d3c501ae08acd2678e177a6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658377"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>자습서: 샘플 클라이언트 앱을 사용하여 Azure Digital Twins 그래프 만들기

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

이 자습서에서는 모델, 트윈 및 관계를 사용하여 Azure Digital Twins에서 그래프를 작성합니다. 이 자습서의 도구는 Azure Digital Twins 인스턴스와 상호 작용하기 위한 **샘플 명령줄 클라이언트 애플리케이션** 입니다. 클라이언트 앱은 [클라이언트 앱 코딩](tutorial-code.md)에서 작성한 것과 비슷합니다.

이 샘플을 사용하여 모델 업로드, 트윈 생성 및 수정, 관계 생성과 같은 필수 Azure Digital Twins 작업을 수행할 수 있습니다. 또한 [샘플 코드](https://github.com/Azure-Samples/digital-twins-samples/tree/master/)를 보고 Azure Digital Twins API에 대해 배우고 원하는 대로 샘플 프로젝트를 수정하여 명령을 직접 구현해 볼 수 있습니다.

이 자습서에서는 다음을 수행합니다.
> [!div class="checklist"]
> * 환경 모델링
> * 디지털 트윈 만들기
> * 관계를 추가하여 그래프 형성
> * 그래프를 쿼리하여 질문에 답변

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>샘플 프로젝트 실행

이제 앱과 인증이 설정되었으므로 툴바의 이 단추로 프로젝트를 실행합니다.

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Visual Studio 시작 단추(SampleClientApp 프로젝트) 스크린샷." lightbox="media/tutorial-command-line/app/start-button-sample.png":::

콘솔 창이 열리고 인증을 수행하고 명령을 기다립니다. 

프로젝트 콘솔의 스크린샷은 다음과 같습니다.

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="명령줄 앱에서 시작 메시지의 스크린샷." lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> 이 프로젝트에서 사용할 수 있는 모든 명령 목록을 보려면 프로젝트 콘솔에 `help`를 입력하고 Return 키를 누릅니다.

앱이 성공적으로 실행되고 있는지 확인했으면 콘솔 창을 닫아 앱 실행을 중지합니다. 자습서 뒷부분에서 다시 실행하게 됩니다.

## <a name="model-a-physical-environment-with-dtdl"></a>DTDL로 물리적 환경 모델링

Azure Digital Twins 인스턴스와 샘플 앱이 설정되었으므로 이제 시나리오의 그래프를 만들 수 있습니다. 

Azure Digital Twins 솔루션을 만드는 첫 번째 단계는 사용자 환경에 대한 트윈 [모델](concepts-models.md)을 정의하는 것입니다. 

모델은 개체 지향 프로그래밍 언어의 클래스와 유사하며, 나중에 [디지털 트윈](concepts-twins-graph.md)이 따르고 인스턴스화할 수 있도록 사용자 정의 템플릿을 제공합니다. 모델은 **DTDL(Digital Twins 정의 언어)** 이라는 JSON과 같은 언어로 작성되며 트윈의 *속성*, *원격 분석*, *관계* 및 *구성 요소* 를 정의할 수 있습니다.

> [!NOTE]
> 또한 DTDL은 디지털 트윈에 *명령* 정의를 허용합니다. 그러나 Azure Digital Twins 서비스에서는 현재 명령이 지원되지 않습니다.

_**AdtE2ESample**_ 프로젝트가 열려 있는 Visual Studio 창에서 *솔루션 탐색기* 창을 사용하여 *AdtSampleApp\SampleClientApp\Models 폴더* 로 이동합니다. 이 폴더에는 샘플 모델이 있습니다.

*Room.json* 을 선택하여 편집 창에서 열고 다음과 같은 방법으로 변경합니다.

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Azure Digital Twins에 모델 업로드

모델을 설계한 후에는 Azure Digital Twins 인스턴스에 업로드해야 합니다. 이렇게 하면 고유의 사용자 지정 도메인 어휘를 사용하여 Azure Digital Twins 서비스 인스턴스가 구성됩니다. 모델이 업로드되면 이를 사용하는 트윈 인스턴스를 만들 수 있습니다.

1. 이전 섹션에서 파일의 Room.json 파일을 편집한 후 콘솔 앱 실행을 다시 시작합니다.

1. 프로젝트 콘솔 창에서 다음 명령을 실행하여 다음 섹션에서도 사용할 Floor 모델과 함께 업데이트된 Room 모델을 업로드하여 여러 유형의 트윈을 만듭니다.

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    출력에 모델이 성공적으로 만들어졌다고 표시되어야 합니다.

1. 명령 `GetModels true`를 실행하여 모델이 만들어졌는지 확인합니다. 이 명령은 Azure Digital Twins 인스턴스에 업로드된 모든 모델에 대한 전체 정보를 출력합니다. 결과에서 편집된 Room 모델을 찾습니다.

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="업데이트된 Room 모델을 보여주는 GetModels의 결과 스크린샷." lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>오류

샘플 애플리케이션은 서비스의 오류도 처리합니다. 

`CreateModels` 명령을 다시 실행하여 업로드한 동일한 모델 중 하나를 다시 업로드해 봅니다.

```cmd/sh
CreateModels Room
```

모델을 덮어쓸 수 없으므로 이 명령은 이제 서비스 오류를 반환합니다.
기존 모델을 삭제하는 방법에 대한 자세한 내용은 [DTDL 모델 관리](how-to-manage-model.md)를 참조하세요.
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

## <a name="create-digital-twins"></a>디지털 트윈 만들기

일부 모델이 Azure Digital Twins 인스턴스에 업로드되었으므로 모델 정의를 기반으로 [디지털 트윈](concepts-twins-graph.md)을 만들 수 있습니다. 디지털 트윈은 농장의 센서, 건물의 방 또는 자동차의 조명과 같은 비즈니스 환경 내의 엔터티를 나타냅니다. 

디지털 트윈을 만들려면 `CreateDigitalTwin` 명령을 사용합니다. 트윈이 기반으로 하는 모델을 참조해야 하며, 필요에 따라 모델의 속성에 대한 초기 값을 정의할 수 있습니다. 이 단계에서는 관계 정보를 전달할 필요가 없습니다.

1. 실행 중인 프로젝트 콘솔에서 이 코드를 실행하여 이전에 업데이트한 Room 모델과 또 다른 모델인 Floor를 기반으로 여러 개의 트윈을 만듭니다. Room에는 세 가지 속성이 있으므로 이러한 속성에 대한 초기 값을 인수에 제공할 수 있습니다. (속성 값 초기화는 일반적으로 선택 사항이지만 이 자습서에는 필요합니다.)

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    이러한 명령의 출력에 트윈이 성공적으로 만들어졌다고 표시되어야 합니다. 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="floor0, floor1, room0 및 room1을 포함하는 CreateDigitalTwin 명령의 결과에서 발췌한 스크린샷." lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. `Query` 명령을 실행하여 트윈이 만들어졌는지 확인할 수 있습니다. 이 명령은 Azure Digital Twins 인스턴스에 포함된 모든 디지털 트윈을 쿼리합니다. 결과에서 room0, room1, floor0, floor1 트윈을 찾습니다.

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

### <a name="modify-a-digital-twin"></a>디지털 트윈 수정

사용자가 만든 트윈의 속성을 수정할 수도 있습니다. 

> [!NOTE]
> 기본 REST API는 [JSON 패치](http://jsonpatch.com/) 형식을 사용하여 트윈에 대한 업데이트를 정의합니다. 명령줄 앱도 이 형식을 사용하여 기본 API가 기대하는 바를 보다 정확하게 경험할 수 있습니다.

1. 이 명령을 실행하여 room0의 RoomName을 "Room0"에서 "PresidentialSuite"로 변경합니다.
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    출력에 트윈이 성공적으로 업데이트되었다고 표시되어야 합니다.

1. 이 명령을 실행하여 *room0* 의 정보를 확인하여 업데이트 성공 여부를 확인할 수 있습니다.

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    출력에 업데이트된 이름이 반영되어야 합니다.


## <a name="create-a-graph-by-adding-relationships"></a>관계를 추가하여 그래프 만들기

다음으로, 이러한 트윈 간의 몇 가지 **관계** 를 만들어 [트윈 그래프](concepts-twins-graph.md)로 연결할 수 있습니다. 트윈 그래프는 전체 환경을 나타내는 데 사용됩니다. 

한 트윈에서 다른 트윈으로 작성할 수 있는 관계 유형은 이전에 업로드한 [모델](#model-a-physical-environment-with-dtdl) 내에서 정의됩니다. [Floor에 대한 모델 정의](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)는 층에 *contains* 라는 관계 유형이 있을 수 있음을 지정하여 각 Floor 트윈에서 포함된 해당 방에 대한 *contains* 유형 관계를 만들 수 있습니다.

관계를 추가하려면 `CreateRelationship` 명령을 사용합니다. 관계가 시작되는 트윈, 관계 유형 및 관계가 연결되는 트윈을 지정합니다. 마지막으로 관계에 고유한 ID를 지정합니다.

1. 다음 코드를 실행하여 앞에서 만든 각 Floor 트윈의 "포함" 관계를 해당 Room 트윈에 추가합니다. 관계 이름은 relationship0 및 relationship1입니다.

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >또한 [Floor 모델](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)의 *포함* 관계는 두 문자열 속성(`ownershipUser` 및 `ownershipDepartment`)으로도 정의되었기 때문에 관계를 만들 때 이에 대한 초기값으로 인수를 제공할 수도 있습니다.
    > 다음은 이러한 속성에 대한 초기값도 지정하는 relationship0을 만드는 위 명령의 대체 버전입니다.
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    이러한 명령의 출력은 관계가 성공적으로 만들어졌는지 확인합니다.
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="CreateRelationship 명령의 결과에서 발췌한 스크린샷으로, relationship0 및 relationship1이 포함됩니다." lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. Azure Digital Twins 인스턴스의 관계를 출력하는 다음 명령 중 하나를 사용하여 관계를 확인할 수 있습니다.
    * 각 floor에서 시작되는 모든 관계를 보려면 다음 명령을 실행합니다(한 쪽에서 관계 보기).
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * 각 room에 도착하는 모든 관계를 보려면 다음 명령을 실행합니다("다른" 쪽에서 관계 보기).
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * 이러한 관계를 ID별로 개별적으로 찾으려면 다음 명령을 실행합니다.
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

이 자습서에서 설정한 트윈과 관계는 다음과 같은 개념적 그래프를 만듭니다.

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="개념 그래프를 보여 주는 다이어그램. floor0은 relationship0을 통해 room0에 연결되고, floor1은 relationship1을 통해 room1에 연결되어 있습니다." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>트윈 그래프를 쿼리하여 환경 질문에 대답

Azure Digital Twins의 주요 기능은 환경에 대한 질문에 답하도록 쉽고 효율적으로 트윈 그래프를 [쿼리](concepts-query-language.md)하는 기능입니다. 

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

실행 중인 프로젝트 콘솔에서 다음 명령어를 실행하여 샘플 환경에 대한 몇 가지 질문에 답변합니다.

1. **Azure Digital Twins에 표시되는 내 환경의 모든 엔터티는 무엇인가요?** (모두 쿼리)

    ```cmd/sh
    Query
    ```

    이 명령을 사용하면 환경을 한눈에 파악하고 모든 것이 Azure Digital Twins 내에 원하는 대로 표시되는지 확인할 수 있습니다. 이 명령의 결과는 해당 세부 정보와 함께 각 디지털 트윈이 포함된 출력입니다. 발췌한 출력은 다음과 같습니다.

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="room0 및 floor1을 포함하여 쌍 쿼리의 부분적인 결과를 보여주는 스크린샷.":::

    >[!TIP]
    >샘플 프로젝트에서 추가 인수가 없는 명령 `Query`는 `Query SELECT * FROM DIGITALTWINS`와 같습니다. [쿼리 API](/rest/api/digital-twins/dataplane/query) 또는 [CLI 명령](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)을 사용하여 인스턴스의 모든 쌍을 쿼리하려면 더 긴(전체) 쿼리를 사용합니다.

1. **내 환경의 모든 room은 무엇인가요?** (모델로 쿼리)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    특정 유형의 트윈으로 쿼리를 제한하여 표시되는 항목에 대한 보다 구체적인 정보를 얻을 수 있습니다. 이 쿼리의 결과는 room0 및 room1을 표시하지만 floor0 또는 floor1을 표시하지 **않습니다**(방이 아니라 층이므로).
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="room0 및 room1만 보여주는 모델 쿼리의 결과 스크린샷.":::

1. ***floor0* 의 모든 room은 무엇인가요?** (관계로 쿼리)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    그래프의 관계를 기반으로 쿼리하여 트윈 연결 방식에 대한 정보를 얻거나 쿼리를 특정 영역으로 제한할 수 있습니다. room0만 floor0에 있으므로 이것이 결과의 유일한 room입니다.

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="room0을 보여주는 관계 쿼리의 결과 스크린샷.":::

1. **온도가 75도가 넘는 내 환경의 모든 트윈은 무엇인가요?** (속성으로 쿼리)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    속성을 기준으로 그래프를 쿼리하여 주의가 필요할 수 있는 환경의 이상값 찾기를 포함하여 다양한 질문에 대답할 수 있습니다. 다른 비교 연산자( *<* , *>* , *=* 또는 *!=* )도 지원됩니다. room1이 온도가 80도이기 때문에 다음 결과에 표시됩니다.

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="room1만 보여주는 속성 쿼리의 결과 스크린샷.":::

1. **온도가 75도가 넘는 *floor0* 의 모든 room은 무엇인가요?** (복합 쿼리)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    `AND`, `OR`, `NOT`과 같은 조합 연산자를 사용하여 SQL에서와 같이 이전 쿼리를 결합할 수도 있습니다. 이 쿼리는 `AND`를 사용하여 트윈 온도에 대한 이전 쿼리를 더 구체화합니다. 이제 결과에는 floor0에서 온도가 75도가 넘는 room만 포함됩니다(이 경우에는 없음). 결과 집합이 비어 있습니다.

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="결과 없음을 보여주는 복합 쿼리의 결과 스크린샷." lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료한 후 다음에 수행하려는 작업에 따라 제거할 리소스를 선택할 수 있습니다.

* **다음 자습서로 계속 진행할 계획인 경우** 여기에서 설정한 리소스를 유지하여 이 Azure Digital Twins 인스턴스와 구성된 샘플 앱을 다음 자습서에서 계속 사용할 수 있습니다.

* **Azure Digital Twins 인스턴스를 계속 사용하지만 해당 모델, 트윈 및 관계를 모두 지우려는 경우** 샘플 앱의 `DeleteAllTwins` 및 `DeleteAllModels` 명령을 사용하여 각각 인스턴스의 트윈과 모델을 지울 수 있습니다.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

로컬 머신에서 프로젝트 폴더를 삭제해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계 

이 자습서에서는 샘플 클라이언트 애플리케이션을 사용하여 인스턴스에서 그래프를 만들어 Azure Digital Twins를 시작했습니다. 모델, 디지털 트윈 및 관계를 만들어 그래프를 구성했습니다. 또한 그래프에서 몇 가지 쿼리를 실행하여 Azure Digital Twins가 환경에 대해 어떤 종류의 질문에 답할 수 있는지 알아보았습니다.

다음 자습서에서 Azure Digital Twins와 다른 Azure 서비스를 결합하여 데이터 기반의 엔드투엔드 시나리오를 완성하세요.
> [!div class="nextstepaction"]
> [엔드투엔드 솔루션 연결](tutorial-end-to-end.md)