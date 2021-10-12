---
title: 빠른 시작 - Azure Digital Twins Explorer 시작
titleSuffix: Azure Digital Twins
description: 빠른 시작 - Azure Digital Twins Explorer 샘플을 사용하여 미리 작성된 시나리오를 시각화하고 살펴봅니다.
author: baanders
ms.author: baanders
ms.date: 4/27/2021
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 92b1eeb5317f5a7535406d1b3781a4b0b66cad45
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272456"
---
# <a name="quickstart---get-started-with-a-sample-scenario-in-azure-digital-twins-explorer"></a>빠른 시작 - Azure Digital Twins Explorer에서 샘플 시나리오 시작

Azure Digital Twins를 사용하면 실제 환경의 라이브 모델을 만들어 이와 상호 작용할 수 있습니다. 먼저 개별 요소를 **디지털 트윈** 으로 모델링합니다. 그런 다음, 라이브 이벤트에 응답하고 정보를 쿼리할 수 있는 기술 자료 **그래프** 에 연결합니다.

이 빠른 시작에서는 [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md)를 사용하여 미리 빌드된 Azure Digital Twins 그래프를 살펴봅니다. 이 도구는 Azure Portal 내에서 Azure Digital Twins 데이터를 시각화하고 상호 작용할 수 있는 도구입니다. 

다음 단계를 완료하게 됩니다.

1. Azure Digital Twins 인스턴스를 만들어서 Azure Digital Twins Explorer에서 연결합니다.
1. 미리 작성된 모델 및 그래프 데이터를 업로드하여 샘플 시나리오 생성
1. 만들어진 시나리오 그래프 살펴보기
1. 그래프 변경
1. 경험을 통해 학습한 내용을 검토합니다.

작업할 샘플 그래프는 두 개의 층과 두 개의 방이 있는 건물을 나타냅니다. Floor0에는 Room0이 포함되고 Floor1에는 Room1이 포함됩니다. 그래프는 다음 이미지와 비슷합니다.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Azure Digital Twins Explorer에서 화살표로 연결된 4개의 원형 노드로 구성된 그래프의 스크린샷":::

>[!NOTE]
>이 빠른 시작은 Azure Digital Twins 데이터를 나타내는 방법을 해석하기 위해 미리 정의된 그래프를 탐색하기 위한 것입니다. 간단히 하기 위해 빠른 시작에서는 IoT Hub 디바이스와 해당 그래프 표현 간의 연결 설정을 다루지 않습니다. 그래프에 연결된 엔드투엔드 흐름을 설정하려면 자습서: [엔드투엔드 연결 솔루션](tutorial-end-to-end.md)으로 이동합니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 Azure 구독이 필요합니다. 아직 없는 경우 지금 [체험 구독을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

빠른 시작에서 사용되는 샘플 그래프의 자료를 다운로드해야 합니다. 아래 링크와 지침을 사용하여 필요한 파일 3개를 [digital-twins-explorer GitHub repository](https://github.com/Azure-Samples/digital-twins-explorer)에서 다운로드하세요.
* [Room.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-explorer/main/client/examples/Room.json): 링크로 이동하여 화면에서 아무 곳이나 마우스 오른쪽 단추로 클릭하고 브라우저의 오른쪽 클릭 메뉴에서 **다른 이름으로 저장** 을 선택합니다. 다른 이름으로 저장 창을 사용하여 컴퓨터의 어딘가에 **Room.json** 이라는 이름으로 파일을 저장합니다.
* [Floor.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-explorer/main/client/examples/Floor.json): 링크로 이동하여 화면에서 아무 곳이나 마우스 오른쪽 단추로 클릭하고 브라우저의 오른쪽 클릭 메뉴에서 **다른 이름으로 저장** 을 선택합니다. 다른 이름으로 저장 창을 사용하여 **Room.json** 과 동일한 위치에 **Floor.json** 이라는 이름으로 파일을 저장합니다.
* [buildingScenario.xlsx](https://github.com/Azure-Samples/digital-twins-explorer/blob/main/client/examples/buildingScenario.xlsx): 링크로 이동하여 **다운로드** 단추를 선택합니다. 파일이 기본 다운로드 위치에 다운로드됩니다.

    :::image type="content" source="media/quickstart-azure-digital-twins-explorer/download-building-scenario.png" alt-text="GitHub의 digital-twins-explorer/client/examples/buildingScenario.xlsx 파일 스크린샷. 다운로드 단추에 강조 표시가 있습니다." lightbox="media/quickstart-azure-digital-twins-explorer/download-building-scenario.png":::

## <a name="set-up-azure-digital-twins"></a>Azure Digital Twins 설정

Azure Digital Twins를 사용하는 첫 번째 단계는 Azure Digital Twins 인스턴스를 만드는 것입니다. 서비스 인스턴스를 만든 후 빠른 시작 전체에서 인스턴스 작업에 사용하게 되는 Azure Digital Twins Explorer에서 인스턴스에 연결할 수 있습니다.

이 섹션의 나머지 부분에서는 이러한 단계를 안내합니다.

### <a name="create-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 만들기

[!INCLUDE [digital-twins-setup-portal.md](../../includes/digital-twins-setup-portal.md)]

3. 설정의 **기본 사항** 탭에서 새 인스턴스의 구독, 리소스 그룹, 위치, 리소스 이름 등을 포함한 필드를 입력합니다. **Azure Digital Twins 데이터 소유자 역할 할당** 상자를 선택하여 인스턴스에서 데이터를 관리할 권한을 자신에게 부여합니다.

    >[!NOTE]
    > Azure Digital Twins 데이터 소유자 역할 할당 상자가 회색으로 표시되면 Azure 구독에서 리소스에 대한 사용자 액세스를 관리할 권한이 없는 것입니다. 이 섹션에서 인스턴스를 계속 만들 수 있습니다. 이 경우 빠른 시작의 나머지 부분을 완료하기 전에 필요한 권한이 있는 누군가가 [인스턴스에서 사용자에게 이 역할을 할당](how-to-set-up-instance-portal.md#assign-the-role-using-azure-identity-management-iam)해야 합니다.
    >
    > 이 요구 사항을 충족하는 일반적인 역할은 **소유자**, **계정 관리자** 또는 **사용자 액세스 관리자** 와 **기여자** 의 조합입니다.  

4. **검토 + 만들기** 를 선택하여 인스턴스 만들기를 완료합니다.

    :::image type="content" source= "media/quickstart-azure-digital-twins-explorer/create-azure-digital-twins-basics.png" alt-text="Azure Portal의 Azure Digital Twins에 대한 리소스 만들기 프로세스의 스크린샷. 설명된 값이 채워져 있습니다.":::
    
5. 입력한 세부 정보를 보여 주는 요약 페이지가 표시됩니다. **만들기** 를 선택하여 인스턴스를 확인하고 만듭니다.

그러면 인스턴스의 배포 상태를 추적하는 개요 페이지로 이동됩니다.

### <a name="open-instance-in-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer에서 인스턴스 열기

인스턴스 배포가 완료되면 **리소스로 이동** 단추를 사용하여 포털에서 인스턴스의 개요 페이지로 이동합니다.

:::image type="content" source= "media/quickstart-azure-digital-twins-explorer/deployment-complete.png" alt-text="Azure Portal에서 Azure Digital Twins 배포 페이지의 스크린샷. 이 페이지는 배포가 완료되었음을 나타냄":::

그런 다음, **Azure Digital Twins Explorer 열기(미리 보기)** 단추를 선택합니다.

:::image type="content" source="media/includes/azure-digital-twins-explorer-portal-access.png" alt-text="Azure Digital Twins 인스턴스에 대한 개요 페이지를 보여주는 Azure Portal 스크린샷. Azure Digital Twins Explorer 열기(미리 보기) 단추 주변에 강조 표시가 있습니다." lightbox="media/includes/azure-digital-twins-explorer-portal-access.png":::

그러면 인스턴스에 연결된 Azure Digital Twins Explorer 창이 열립니다.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="인터넷 브라우저의 Azure Digital Twins Explorer 스크린샷" lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

## <a name="add-the-sample-data"></a>샘플 데이터 추가

다음으로, 샘플 시나리오와 그래프를 Azure Digital Twins Explorer로 가져옵니다. 샘플 시나리오는 [필수 조건](#prerequisites) 섹션에서 다운로드한 **digital-twins-explorer-main** 폴더에 있습니다.

### <a name="models"></a>모델

Azure Digital Twins 솔루션의 첫 번째 단계는 환경의 어휘를 정의하는 것입니다. 환경에 존재하는 엔터티 형식을 설명하는 사용자 지정 [모델](concepts-models.md)을 만들겠습니다.

각 모델은 DTDL(디지털 트윈 정의 언어)이라고 하는 JSON-LD와 같은 언어로 작성됩니다. 각 모델은 **속성**, **원격 분석**, **관계** 및  **구성 요소** 의 측면에서 단일 엔터티 형식을 설명합니다. 나중에 이러한 형식의 특정 인스턴스를 나타내는 디지털 트윈의 기반으로 이러한 모델을 사용합니다.

일반적으로 모델을 만들 때 다음 세 가지 단계를 완료하게 됩니다.

1. 모델 정의를 작성합니다. 이 단계는 빠른 시작에서 이미 샘플 솔루션의 일부로 수행되었습니다.
1. 유효성을 검사하여 구문이 정확한지 확인합니다. 이 단계는 빠른 시작에서 이미 샘플 솔루션의 일부로 수행되었습니다.
1. Azure Digital Twins 인스턴스에 업로드합니다.
 
이 빠른 시작에서는 모델 파일이 이미 작성되어 유효성 검사를 마쳤습니다. 여러분이 다운로드한 솔루션에 포함되어 있습니다. 이 섹션에서는 미리 작성된 두 가지 모델을 인스턴스에 업로드하여 건물 환경의 다음 구성 요소를 정의합니다.

* Floor
* 공간

#### <a name="upload-models"></a>모델 업로드

다음 단계에 따라 모델을 업로드합니다.

1. **모델** 패널에서 **모델 업로드** 아이콘(구름을 가리키는 화살표)을 선택합니다.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/upload-model.png" alt-text="모델 패널과 그 안의 '모델 업로드' 아이콘이 강조 표시된 Azure Digital Twins Explorer 스크린샷." lightbox="media/quickstart-azure-digital-twins-explorer/upload-model.png":::
 
1. 열기 창이 나타나면 이전에 다운로드한 **Room.json** 및 **Floor.json** 파일이 포함된 폴더로 이동합니다.
1. **Room.json** 및 **Floor.json** 을 선택하고 **열기** 를 선택하여 둘 다 업로드합니다. 

Azure Digital Twins Explorer는 모델 파일을 Azure Digital Twins 인스턴스에 업로드합니다. 해당 파일이 **모델** 패널에 표시되며, 식별 이름과 전체 모델 ID가 표시됩니다. **모델 보기** 정보 아이콘을 선택하면 숨겨져 있는 DTDL 코드를 볼 수 있습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/model-info.png" alt-text="모델 패널 안에 모델 정의 두 개(Floor 및 Room)가 나열되어 있는 Azure Digital Twins Explorer 스크린샷" lightbox="media/quickstart-azure-digital-twins-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>쌍 및 쌍 그래프

이제 일부 모델이 Azure Digital Twins 인스턴스에 업로드되었으므로 모델 정의를 따르는 [디지털 쌍](concepts-twins-graph.md)을 추가할 수 있습니다.

디지털 트윈은 비즈니스 환경 내의 실제 엔터티를 나타냅니다. 팜의 센서, 자동차의 조명, 이 빠른 시작에 나오는 건물의 방을 예로 들 수 있습니다. 모두 Room 모델을 사용하는 여러 채팅방처럼 지정된 모델 형식의 여러 트윈을 만들 수 있습니다. 전체 환경을 나타내는 **트윈 그래프** 에 관계를 연결합니다.

이 섹션에서는 미리 만들어진 그래프에 연결된 미리 만들어진 트윈을 업로드합니다. 그래프에는 다음 레이아웃으로 연결된 두 개의 층과 두 개의 방이 있습니다.

* Floor0
    - Room0 포함
* Floor1
    - Room1 포함

#### <a name="import-the-graph"></a>그래프 가져오기

다음 단계에 따라 그래프를 가져옵니다.

1. **트윈 그래프** 패널에서 **그래프 가져오기** 아이콘(구름을 가리키는 화살표)을 선택합니다.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-graph.png" alt-text="'그래프 가져오기' 아이콘이 강조 표시된 그래프 보기 패널을 보여주는 Azure Digital Twins Explorer 스크린샷" lightbox="media/quickstart-azure-digital-twins-explorer/import-graph.png":::

2. 열기 창에서 이전에 다운로드한 **buildingScenario.xlsx** 파일로 이동합니다. 이 파일에는 샘플 그래프에 대한 설명이 포함되어 있습니다. **열기** 를 선택합니다.

   몇 초 후 Azure Digital Twins Explorer에서 **가져오기** 보기가 열리고 로드할 그래프의 미리 보기가 표시됩니다.

3. 그래프 업로드를 확인하려면 그래프 미리 보기 패널의 오른쪽 위 모서리에서 **저장** 아이콘을 선택합니다.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png" alt-text="그래프 미리 보기 창에 저장 아이콘이 강조 표시되어 있는 Azure Digital Twins Explorer 스크린샷" lightbox="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Azure Digital Twins Explorer는 업로드된 파일을 사용하여 요청된 트윈 및 이들 트윈 간의 관계를 만듭니다. 완료되면 대화 상자가 나타납니다. **닫기** 를 선택합니다.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-success.png" alt-text="그래프 가져오기 성공을 나타내는 대화 상자를 보여주는 Azure Digital Twins Explorer 스크린샷" lightbox="media/quickstart-azure-digital-twins-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. 이제 그래프가 Azure Digital Twins Explorer에 업로드되었습니다. **트윈 그래프** 패널로 다시 전환합니다.
 
   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png" alt-text="트윈 그래프 탭이 강조 표시된 Azure Digital Twins Explorer 스크린샷" lightbox="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png"::: 
 
6. 그래프를 보려면 Azure Digital Twins Explorer 창 상단 근처에 있는 **쿼리 탐색기** 패널에서 **쿼리 실행** 단추를 선택합니다.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/run-query.png" alt-text="창의 오른쪽 위 모서리에 있는 '쿼리 실행' 단추가 강조 표시된 Azure Digital Twins Explorer 스크린샷" lightbox="media/quickstart-azure-digital-twins-explorer/run-query.png":::

이 작업은 모든 디지털 트윈을 선택하고 표시하는 기본 쿼리를 실행합니다. Azure Digital Twins Explorer는 서비스의 모든 트윈과 관계를 검색합니다. 그리고 그러한 관계를 통해 정의된 그래프를 **트윈 그래프** 패널에 그립니다.

## <a name="explore-the-graph"></a>그래프 살펴보기

이제 샘플 시나리오의 업로드된 그래프가 표시됩니다.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="내부에 트윈 그래프가 있는 그래프 보기 패널을 보여주는 Azure Digital Twins Explorer 스크린샷":::

원(그래프 "노드")은 디지털 트윈을 나타냅니다. 선은 관계를 나타냅니다. Floor0 트윈에는 Room0이 포함되고, Floor1 트윈에는 Room1이 포함됩니다.

마우스를 사용하는 경우 그래프 조각을 끌어서 이동할 수 있습니다.

### <a name="view-twin-properties"></a>쌍 속성 보기

트윈을 선택하여 **속성** 패널에서 속성과 해당 값의 목록을 볼 수 있습니다.

Room0의 속성은 다음과 같습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room0.png" alt-text="Room0의 $dtId, Temperature 및 Humidity 속성을 보여주는 속성 패널이 강조 표시된 Azure Digital Twins Explorer 스크린샷" lightbox="media/quickstart-azure-digital-twins-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0의 온도는 70입니다.

Room1의 속성은 다음과 같습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room1.png" alt-text="Room1의 $dtId, Temperature 및 Humidity 속성을 보여주는 속성 패널이 강조 표시된 Azure Digital Twins Explorer 스크린샷" lightbox="media/quickstart-azure-digital-twins-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1의 온도는 80입니다.

### <a name="query-the-graph"></a>그래프 쿼리

Azure Digital Twins의 주요 기능은 환경에 대한 질문에 답하도록 쉽고 효율적으로 트윈 그래프를 [쿼리](concepts-query-language.md)하는 기능입니다.

그래프에서 쌍을 쿼리하는 한 가지 방법은 **속성** 을 사용하는 것입니다. 속성 기반 쿼리를 통해 다양한 질문에 대답할 수 있습니다. 예를 들어 사용자 환경에서 주의가 필요할 수 있는 이상값을 찾을 수 있습니다.

이 섹션에서는 환경에서 온도가 75보다 높은 트윈이 몇 개인지 묻는 질문에 대답하는 쿼리를 실행합니다.

대답을 확인하려면 **쿼리 탐색기** 패널에서 다음 쿼리를 실행합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

이전의 트윈 속성을 살펴보면 Room0의 온도가 70이고 Room1의 온도가 80입니다. 따라서 Room1만 결과에 표시됩니다.
    
:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png" alt-text="속성 쿼리 결과(Room1만 표시됨)를 보여주는 Azure Digital Twins Explorer 스크린샷" lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png":::

>[!TIP]
> 다른 비교 연산자(<,>, = 또는 !=)도 위의 쿼리 내에서 지원됩니다. 이러한 연산자, 다른 값 또는 다른 트윈 속성을 쿼리에 연결하여 사용자 고유의 질문에 대답해 볼 수 있습니다.

## <a name="edit-data-in-the-graph"></a>그래프에서 데이터 편집

Azure Digital Twins Explorer를 사용하여 그래프에 표시된 쌍의 속성을 편집할 수 있습니다. 이 섹션에서는 Room0의 온도를 76으로 높이겠습니다.

시작 하려면 다음 쿼리를 다시 실행하여 모든 디지털 트윈을 선택합니다. 그러면 **트윈 그래프** 패널에 전체 그래프가 한 번 더 표시됩니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

**Room0** 을 선택하여 **속성** 패널에 속성 목록을 표시합니다.

이 목록의 속성은 편집할 수 있습니다. 새 값을 입력하도록 설정하려면 온도 값으로 **70** 을 선택합니다. **76** 을 입력하고, **저장** 아이콘을 선택하여 온도를 **76** 으로 업데이트합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png" alt-text="Room0에 대해 편집할 수 있는 속성을 보여주는, 속성 패널이 강조 표시된 Azure Digital Twins Explorer 스크린샷" lightbox="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

이제 업데이트하기 위해 Azure Digital Twins [API](concepts-apis-sdks.md)를 사용하여 백그라운드에서 사용된 패치 코드를 표시하는 **패치 정보** 창이 표시됩니다. **닫기** 를 선택합니다.

### <a name="query-to-see-the-result"></a>결과를 확인하는 쿼리

그래프가 Room0의 온도 업데이트를 성공적으로 등록했는지 확인하려면 이전 쿼리를 다시 실행하여 환경에서 온도가 75를 초과하는 모든 트윈을 가져옵니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

이제 Room0의 온도가 70에서 76으로 변경되었으므로 두 쌍이 모두 결과에 표시됩니다.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png" alt-text="속성 쿼리 결과(Room0과 Room1이 둘 다 표시됨)를 보여주는 Azure Digital Twins Explorer 스크린샷" lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>학습 검토 및 컨텍스트화

이 빠른 시작에서는 Azure Digital Twins 인스턴스를 만들고, Azure Digital Twins Explorer를 사용하여 인스턴스를 샘플 시나리오로 채웠습니다.

그런 다음, 다음을 수행하여 그래프를 살펴보았습니다.

* 쿼리를 사용하여 시나리오에 대한 질문에 대답합니다.
* 디지털 쌍의 속성을 편집합니다.
* 쿼리를 다시 실행하여 업데이트의 결과로 대답이 변경되는 상황을 확인합니다.

이 연습의 목적은 환경이 계속 변경되는 경우에도 Azure Digital Twins 그래프를 사용하여 환경에 대한 질문에 대답하는 방법을 보여 주기 위한 것입니다.

이 빠른 시작에서는 온도를 수동으로 업데이트했습니다. Azure Digital Twins에서는 디지털 트윈을 실제 IoT 디바이스에 연결하여 원격 분석 데이터를 기반으로 업데이트를 자동으로 받는 것이 일반적입니다. 이 방법을 통해 항상 환경의 실제 상태를 반영하는 라이브 그래프를 작성할 수 있습니다. 쿼리를 사용하여 환경에서 발생하는 상황에 대한 정보를 실시간으로 가져올 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

빠른 시작 후에 정리하려면 다음에 수행할 작업을 기반으로 제거하려는 리소스를 선택합니다.

* **Azure Digital Twins 자습서를 계속 진행할 생각이라면** 이 빠른 시작의 인스턴스를 해당 문서에 다시 사용할 수 있으므로 제거할 필요가 없습니다.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

로컬 머신에서 샘플 프로젝트 폴더를 삭제해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

다음으로, Azure Digital Twins 자습서로 계속 진행하여 사용자 고유의 Azure Digital Twins 시나리오와 상호 작용 도구를 빌드합니다.

> [!div class="nextstepaction"]
> [클라이언트 앱 코딩](tutorial-code.md)
