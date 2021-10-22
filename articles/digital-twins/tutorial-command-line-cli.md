---
title: '자습서: Azure Digital Twins에서 그래프 만들기(CLI)'
titleSuffix: Azure Digital Twins
description: Azure CLI를 사용하여 Azure Digital Twins 시나리오를 빌드하는 자습서입니다.
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 8df2c2a1eb7a16df4d35a8b6530aec82a1632722
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996288"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>자습서: Azure CLI를 사용하여 Azure Digital Twins 그래프 만들기

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

이 자습서에서는 모델, 트윈 및 관계를 사용하여 Azure Digital Twins에서 그래프를 작성합니다. 이 자습서의 도구는 [Azure CLI에 대한 Azure Digital Twines 명령 집합](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)입니다.

CLI 명령을 사용하여 모델 업로드, 트윈 만들기/수정 및 관계 만들기와 같은 필수 Azure Digital Twins 작업을 수행할 수 있습니다. [az dt 명령 세트에 대한 참조 설명서](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)를 참조하여 CLI 명령의 전체 세트를 확인할 수도 있습니다.

이 자습서에서는 다음을 수행합니다.
> [!div class="checklist"]
> * 환경 모델링
> * 디지털 트윈 만들기
> * 관계를 추가하여 그래프 형성
> * 그래프를 쿼리하여 질문에 답변

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 단계를 완료하려면 먼저 다음 필수 구성 요소를 완료해야 합니다.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

### <a name="download-the-sample-models"></a>샘플 모델 다운로드

자습서에서는 Azure Digital Twins용 C# [엔드투엔드 샘플 프로젝트](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)의 일부인 두 개의 미리 작성된 모델을 사용합니다. 모델 파일은 다음 위치에 있습니다. 
* [Room.json](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [Floor.json](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

컴퓨터에서 파일을 가져오려면 위의 탐색 링크를 사용하고, 파일 본문을 동일한 이름(*Room.json* 및 *Floor.json*)으로 컴퓨터의 로컬 파일에 복사합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell 세션 설정
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 준비하기

이 문서에서 Azure Digital Twins로 작업하려면 먼저 **Azure Digital Twins 인스턴스를 설정** 하고 이를 사용하는 데 필요한 권한이 필요합니다. 이전 작업에서 이미 Azure Digital Twins 인스턴스가 설정된 경우 해당 인스턴스를 사용할 수 있습니다.

그렇지 않으면 [인스턴스 및 인증 설정](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)의 지침을 따릅니다. 지침에는 각 단계를 성공적으로 완료하고 새 인스턴스를 사용할 준비가 되었는지 확인하는 단계도 포함되어 있습니다.

Azure Digital Twins 인스턴스를 설정한 후 나중에 인스턴스에 연결하는 데 필요한 다음 값을 기록해 둡니다.
* 인스턴스의 **_호스트 이름_**
* 인스턴스를 만드는 데 사용한 **Azure 구독** 

인스턴스에 대한 이러한 값은 모두 다음 Azure CLI 명령의 출력에서 가져올 수 있습니다. 

```azurecli-interactive
az dt show --dt-name <Azure-Digital-Twins-instance-name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="az dt show 명령의 출력을 보여 주는 Cloud Shell 브라우저 창의 스크린샷. hostName 필드와 구독 ID가 강조 표시되어 있습니다.":::

## <a name="model-a-physical-environment-with-dtdl"></a>DTDL로 물리적 환경 모델링

이제 CLI 및 Azure Digital Twins 인스턴스가 설정되었으므로 시나리오의 그래프 빌드를 시작할 수 있습니다. 

Azure Digital Twins 솔루션을 만드는 첫 번째 단계는 사용자 환경에 대한 트윈 [모델](concepts-models.md)을 정의하는 것입니다. 

모델은 개체 지향 프로그래밍 언어의 클래스와 유사하며, 나중에 [디지털 트윈](concepts-twins-graph.md)이 따르고 인스턴스화할 수 있도록 사용자 정의 템플릿을 제공합니다. 모델은 **DTDL(Digital Twins 정의 언어)** 이라는 JSON과 같은 언어로 작성되며 트윈의 *속성*, *원격 분석*, *관계* 및 *구성 요소* 를 정의할 수 있습니다.

> [!NOTE]
> 또한 DTDL은 디지털 트윈에 *명령* 정의를 허용합니다. 그러나 Azure Digital Twins 서비스에서는 현재 명령이 지원되지 않습니다.

컴퓨터에서 [필수 구성 요소](#prerequisites) 섹션에서 만든 *Room.json*  파일로 이동합니다. 코드 편집기에서 열고, 다음과 같은 방법으로 변경합니다.

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Azure Digital Twins에 모델 업로드

모델을 설계한 후에는 Azure Digital Twins 인스턴스에 업로드해야 합니다. 그러면 고유의 사용자 지정 도메인 어휘로 Azure Digital Twins 서비스 인스턴스가 구성됩니다. 모델을 업로드한 후에는 이를 사용하는 트윈 인스턴스를 만들 수 있습니다.

1. Cloud Shell을 사용하여 모델을 추가하려면 사용하는 Cloud Shell 명령을 실행할 때 모델 파일을 사용할 수 있도록 해당 파일을 Cloud Shell의 스토리지에 업로드해야 합니다. 이렇게 하려면 "파일 업로드/다운로드" 아이콘을 선택하고, "업로드"를 선택합니다.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="업로드 아이콘의 선택을 보여 주는 Cloud Shell 브라우저 창의 스크린샷":::
    
    컴퓨터에서 *Room.json* 파일로 이동하여 "열기"를 선택합니다. 그런 다음, *Floor.json* 에 대해 이 단계를 반복합니다.

1. 다음으로 [az dt model create](/cli/azure/dt/model?view=azure-cli-latest&preserve-view=true#az_dt_model_create) 명령을 아래와 같이 사용하여 업데이트된 Room 모델을 Azure Digital Twins 인스턴스에 업로드합니다. 두 번째 명령은 다른 모델인 Floor를 업로드합니다. 이 모델은 다음 섹션에서도 다른 유형의 트윈을 만드는 데 사용합니다.

    ```azurecli-interactive
    az dt model create --dt-name <Azure-Digital-Twins-instance-name> --models Room.json
    az dt model create --dt-name <Azure-Digital-Twins-instance-name> --models Floor.json
    ```
    
    각 명령의 출력에는 성공적으로 업로드된 모델에 대한 정보가 표시됩니다.

    >[!TIP]
    >`--from-directory` 옵션을 model create 명령에 사용하여 디렉터리 내의 모든 모델을 동시에 업로드할 수도 있습니다. 자세한 내용은 [az dt model create에 대한 선택적 매개 변수](/cli/azure/dt/model?view=azure-cli-latest&preserve-view=true#az_dt_model_create-optional-parameters)를 참조하세요.

1. [azdt model list](/cli/azure/dt/model?view=azure-cli-latest&preserve-view=true#az_dt_model_list) 명령을 아래와 같이 사용하여 모델이 만들어졌는지 확인합니다. 그러면 Azure Digital Twins 인스턴스에 업로드된 모든 모델의 목록이 전체 정보와 함께 출력됩니다. 

    ```azurecli-interactive
    az dt model list --dt-name <Azure-Digital-Twins-instance-name> --definition
    ```
    
    결과에서 편집된 Room 모델을 찾습니다.
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="업데이트된 Room 모델이 포함된 model list 명령의 결과를 보여 주는 Cloud Shell의 스크린샷" lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>오류

CLI는 서비스의 오류도 처리합니다. 

`az dt model create` 명령을 다시 실행하여 방금 업로드한 동일한 모델 중 하나를 다시 업로드해 봅니다.

```azurecli-interactive
az dt model create --dt-name <Azure-Digital-Twins-instance-name> --models Room.json
```

모델을 덮어쓸 수 없으므로 이제 `ModelIdAlreadyExists` 오류 코드가 반환됩니다.

## <a name="create-digital-twins"></a>디지털 트윈 만들기

일부 모델이 Azure Digital Twins 인스턴스에 업로드되었으므로 모델 정의를 기반으로 [디지털 트윈](concepts-twins-graph.md)을 만들 수 있습니다. 디지털 트윈은 농장의 센서, 건물의 방 또는 자동차의 조명과 같은 비즈니스 환경 내의 엔터티를 나타냅니다. 

디지털 트윈을 만들려면 [az dt twin create](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_create) 명령을 사용합니다. 트윈이 기반으로 하는 모델을 참조해야 하며, 필요에 따라 모델의 속성에 대한 초기 값을 정의할 수 있습니다. 이 단계에서는 관계 정보를 전달할 필요가 없습니다.

1. Cloud Shell에서 이 코드를 실행하여 이전에 업데이트한 Room 모델 및 또 다른 모델인 Floor를 기반으로 하여 여러 개의 트윈을 만듭니다. Room에는 세 가지 속성이 있으므로 인수에 이들 속성에 대한 초기 값을 제공할 수 있습니다. (속성 값 초기화는 일반적으로 선택 사항이지만 이 자습서에는 필요합니다.)

    ```azurecli-interactive
    az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":80, "HumidityLevel":60}'
    az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > PowerShell 환경에서 Cloud Shell을 사용하는 경우 `--properties` JSON 값을 올바르게 구문 분석하려면 따옴표 문자를 이스케이프해야 할 수 있습니다. 이 편집을 통해 방 트윈을 만드는 명령은 다음과 같습니다.
    >
    > ```azurecli-interactive
    > az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > 이는 아래의 스크린샷에 반영되어 있습니다.
    
    각 명령의 출력에는 성공적으로 만들어진 트윈에 대한 정보(초기화된 방 트윈에 대한 속성 포함)가 표시됩니다.

1. [azd twin query](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query) 명령을 아래와 같이 사용하여 트윈이 만들어졌는지 확인할 수 있습니다. 표시된 쿼리는 Azure Digital Twins 인스턴스에서 모든 디지털 트윈을 찾습니다.
    
    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT * FROM DIGITALTWINS"
    ```
    
    결과에서 room0, room1, floor0, floor1 트윈을 찾습니다. 이 쿼리의 결과 일부를 보여 주는 발췌 부분은 다음과 같습니다.
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="room0 및 room1이 포함된 트윈 쿼리의 부분 결과를 보여 주는 Cloud Shell 스크린샷" lightbox="media/tutorial-command-line/cli/output-query-all.png":::

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

### <a name="modify-a-digital-twin"></a>디지털 트윈 수정

사용자가 만든 트윈의 속성을 수정할 수도 있습니다. 

1. 이 [az dt twin update](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_update) 명령을 실행하여 *room0* 의 RoomName을 *Room0* 에서 *PresidentialSuite* 로 변경합니다.

    ```azurecli-interactive
    az dt twin update --dt-name <Azure-Digital-Twins-instance-name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > PowerShell 환경에서 Cloud Shell을 사용하는 경우 `--json-patch` JSON 값을 올바르게 구문 분석하려면 따옴표 문자를 이스케이프해야 할 수 있습니다. 이 편집을 통해 트윈을 업데이트하는 명령은 다음과 같습니다.
    >
    > ```azurecli-interactive
    > az dt twin update --dt-name <Azure-Digital-Twins-instance-name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > 이는 아래의 스크린샷에 반영되어 있습니다.
    
    이 명령의 출력에는 트윈의 현재 정보가 표시되며, 결과에서 `RoomName`에 대한 새 값이 표시됩니다.

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="PresidentialSuite의 RoomName이 포함된 update 명령의 결과를 보여 주는 Cloud Shell의 스크린샷" lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. [az dt twin show](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) 명령을 실행하여 room0의 정보를 확인하면 업데이트가 성공했는지 확인할 수 있습니다.

    ```azurecli-interactive
    az dt twin show --dt-name <Azure-Digital-Twins-instance-name> --twin-id room0
    ```
    
    출력에 업데이트된 이름이 반영되어야 합니다.

## <a name="create-a-graph-by-adding-relationships"></a>관계를 추가하여 그래프 만들기

다음으로, 이러한 트윈 간의 몇 가지 **관계** 를 만들어 [트윈 그래프](concepts-twins-graph.md)로 연결할 수 있습니다. 트윈 그래프는 전체 환경을 나타내는 데 사용됩니다. 

한 트윈에서 다른 트윈으로 작성할 수 있는 관계 유형은 이전에 업로드한 [모델](#model-a-physical-environment-with-dtdl) 내에서 정의됩니다. [Floor에 대한 모델 정의](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)는 *포함* 이라는 관계 유형이 있는 floor를 지정합니다. 이를 통해 각 Floor 트윈에서 포함된 해당 room으로의 *포함* 유형 관계를 만들 수 있습니다.

관계를 추가하려면 [az dt twin relationship create](/cli/azure/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#az_dt_twin_relationship_create) 명령을 사용합니다. 관계가 시작되는 트윈, 관계 유형 및 관계가 연결되는 트윈을 지정합니다. 마지막으로 관계에 고유한 ID를 지정합니다. 관계가 속성으로 정의된 경우 이 명령에서도 관계 속성을 초기화할 수 있습니다.

1. 다음 코드를 실행하여 이전에 만든 각 Floor 트윈의 *contains* 형식 관계를 해당 Room 트윈에 추가합니다. 관계 이름은 relationship0 및 relationship1입니다.

    ```azurecli-interactive
    az dt twin relationship create --dt-name <Azure-Digital-Twins-instance-name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create --dt-name <Azure-Digital-Twins-instance-name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >[Floor 모델](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)의 *contains* 관계도 `ownershipUser` 및 `ownershipDepartment`의 두 가지 속성으로 정의되었으므로 관계를 만들 때 인수를 이러한 관계에 대한 초기 값으로 제공할 수도 있습니다.
    > 이러한 속성이 초기화된 관계를 만들려면 다음과 같이 `--properties` 옵션을 위의 명령 중 하나에 추가합니다.
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > PowerShell 환경에서 Cloud Shell을 사용하는 경우 `--properties` JSON 값을 올바르게 구문 분석하려면 따옴표 문자를 이스케이프해야 할 수 있습니다.
    
    각 명령의 출력에는 성공적으로 만들어진 관계에 대한 정보가 표시됩니다.

1. Azure Digital Twins 인스턴스의 관계를 인쇄하는 다음 명령 중 하나를 사용하여 관계를 확인할 수 있습니다.
    * 각 floor에서 시작되는 모든 관계를 보려면 다음 명령을 실행합니다(한 쪽에서 관계 보기).
        ```azurecli-interactive
        az dt twin relationship list --dt-name <Azure-Digital-Twins-instance-name> --twin-id floor0
        az dt twin relationship list --dt-name <Azure-Digital-Twins-instance-name> --twin-id floor1
        ```
    * 각 room에 도착하는 모든 관계를 보려면 다음 명령을 실행합니다("다른" 쪽에서 관계 보기).
        ```azurecli-interactive
        az dt twin relationship list --dt-name <Azure-Digital-Twins-instance-name> --twin-id room0 --incoming
        az dt twin relationship list --dt-name <Azure-Digital-Twins-instance-name> --twin-id room1 --incoming
        ```
    * 이러한 관계를 ID별로 개별적으로 찾으려면 다음 명령을 실행합니다.
        ```azurecli-interactive
        az dt twin relationship show --dt-name <Azure-Digital-Twins-instance-name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show --dt-name <Azure-Digital-Twins-instance-name> --twin-id floor1 --relationship-id relationship1
        ```

이 자습서에서 설정한 트윈과 관계는 다음과 같은 개념적 그래프를 만듭니다.

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="개념 그래프를 보여 주는 다이어그램. floor0은 relationship0을 통해 room0에 연결되고, floor1은 relationship1을 통해 room1에 연결되어 있습니다." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>트윈 그래프를 쿼리하여 환경 질문에 대답

Azure Digital Twins의 주요 기능은 환경에 대한 질문에 답하도록 쉽고 효율적으로 트윈 그래프를 [쿼리](concepts-query-language.md)하는 기능입니다. Azure CLI에서 이 작업은 [az dt twin query](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query) 명령을 사용하여 수행됩니다.

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

Cloud Shell에서 다음 쿼리를 실행하여 샘플 환경에 대한 몇 가지 질문에 답변합니다.

1. **Azure Digital Twins에 표시되는 내 환경의 모든 엔터티는 무엇인가요?** (모두 쿼리)

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT * FROM DIGITALTWINS"
    ```

    이를 통해 환경을 한눈에 파악하고 모든 것이 Azure Digital Twins 내에 원하는 대로 표시되는지 확인할 수 있습니다. 이 쿼리의 결과는 각 디지털 트윈에 세부 정보가 포함된 출력입니다. 발췌 내용은 다음과 같습니다.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="room0 및 room1이 포함된 트윈 쿼리의 부분 결과를 보여 주는 Cloud Shell 스크린샷" lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >이는 이전의 [디지털 트윈 만들기](#create-digital-twins) 섹션에서 사용한 명령과 동일하여 인스턴스의 모든 Azure Digital Twins를 찾을 수 있습니다.

1. **내 환경의 모든 room은 무엇인가요?** (모델로 쿼리)

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    특정 유형의 트윈으로 쿼리를 제한하여 표시되는 항목에 대한 보다 구체적인 정보를 얻을 수 있습니다. 이 쿼리의 결과는 room0과 room1을 표시하지만 floor0이나 floor1을 표시하지 **않습니다**(room이 아닌 floor이기 때문에).
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="room0 및 room1만 포함된 모델 쿼리의 결과를 보여 주는 Cloud Shell의 스크린샷" lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. **floor0의 모든 room은 무엇인가요?** (관계로 쿼리)

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    그래프의 관계를 기반으로 쿼리하여 트윈 연결 방식에 대한 정보를 얻거나 쿼리를 특정 영역으로 제한할 수 있습니다. room0만 floor0에 있으므로 이것이 결과의 유일한 room입니다.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="room0이 포함된 관계 쿼리의 결과를 보여 주는 Cloud Shell의 스크린샷" lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > 트윈의 ID(예: 위 쿼리의 floor0)는 메타데이터 필드 `$dtId`를 사용하여 쿼리됩니다. 
    >
    >Cloud Shell에서 `$`로 시작하는 것과 같은 메타데이터 필드를 사용하여 쿼리를 실행하는 경우, Cloud Shell에서 이 필드가 변수가 아니고 쿼리 텍스트에서 리터럴로 사용되어야 함을 인식할 수 있도록 `$`를 백틱으로 이스케이프해야 합니다. 이는 위의 스크린샷에 반영되어 있습니다.

1. **온도가 75도가 넘는 내 환경의 모든 트윈은 무엇인가요?** (속성으로 쿼리)

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    속성을 기반으로 그래프를 쿼리하여 환경에서 주의가 필요한 이상값을 찾는 등 다양한 질문에 답할 수 있습니다. 다른 비교 연산자( *<* , *>* , *=* 또는 *!=* )도 지원됩니다. room1이 온도가 80도이기 때문에 다음 결과에 표시됩니다.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="room1만 포함된 속성 쿼리의 결과를 보여 주는 Cloud Shell의 스크린샷" lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. **온도가 75도가 넘는 *floor0* 의 모든 room은 무엇인가요?** (복합 쿼리)

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    `AND`, `OR`, `NOT`과 같은 조합 연산자를 사용하여 SQL에서와 같이 이전 쿼리를 결합할 수도 있습니다. 이 쿼리는 `AND`를 사용하여 트윈 온도에 대한 이전 쿼리를 더 구체화합니다. 이제 결과에는 floor0에서 온도가 75도가 넘는 room만 포함됩니다(이 경우에는 없음). 결과 집합이 비어 있습니다.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="항목이 포함되지 않은 복합 쿼리의 결과를 보여 주는 Cloud Shell의 스크린샷" lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료한 후 다음에 수행하려는 작업에 따라 제거할 리소스를 선택할 수 있습니다.

* **다음 자습서로 계속 진행하려는 경우** 여기서 설정한 리소스를 유지하고 중간에 아무것도 지우지 않고 Azure Digital Twines 인스턴스를 다시 사용할 수 있습니다.

* **Azure Digital Twins 인스턴스를 계속 사용하지만 해당 모델, 트윈 및 관계를 모두 지우려는 경우** [az dt twin relationship delete](/cli/azure/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#az_dt_twin_relationship_delete), [az dt twin delete](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_delete) 및 [az dt model delete](/cli/azure/dt/model?view=azure-cli-latest&preserve-view=true#az_dt_model_delete) 명령을 사용하여 인스턴스의 관계, 트윈 및 모델을 각각 지울 수 있습니다.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

로컬 컴퓨터에서 만든 모델 파일을 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계 

이 자습서에서는 Azure CLI를 사용하여 인스턴스에서 그래프를 빌드함으로써 Azure Digital Twins를 시작했습니다. 모델, 디지털 트윈 및 관계를 만들어 그래프를 구성했습니다. 또한 그래프에서 몇 가지 쿼리를 실행하여 Azure Digital Twins가 환경에 대해 어떤 종류의 질문에 답할 수 있는지 알아보았습니다.

다음 자습서에서 Azure Digital Twins와 다른 Azure 서비스를 결합하여 데이터 기반의 엔드투엔드 시나리오를 완성하세요.
> [!div class="nextstepaction"]
> [엔드투엔드 솔루션 연결](tutorial-end-to-end.md)