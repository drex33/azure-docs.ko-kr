---
title: 인스턴스를 다른 Azure 지역으로 이동
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 인스턴스를 한 Azure 지역에서 다른 Azure 자역으로 이동하는 방법을 살펴봅니다.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 9617fc3cc7668d8449a9ec3b46e6c82228783e9d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447152"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Azure Digital Twins 인스턴스를 다른 Azure 지역으로 이동

Azure Digital Twines 인스턴스를 한 지역에서 다른 지역으로 이동해야 하는 경우 현재 프로세스는 새 지역에서 리소스를 다시 만든 다음, 원래 리소스를 삭제하는 것입니다. 이 프로세스가 끝나면 업데이트된 위치를 제외하고 처음과 동일한 새 Azure Digital Twins 인스턴스를 사용하게 됩니다.

이 문서에서는 새 인스턴스를 원본과 일치시키는 데 필요한 모든 항목을 완전히 이동하고 복사하는 방법에 대한 지침을 제공합니다.

이 프로세스에는 다음 단계가 포함됩니다.

1. 준비: 원본 모델, 트윈 및 그래프를 다운로드합니다.
1. 이동: 새 Azure Digital Twins 인스턴스를 새 지역에 만듭니다.
1. 이동: 새 Azure Digital Twins 인스턴스를 다시 채웁니다.
    - 원래 모델, 트윈 및 그래프를 업로드합니다.
    - 엔드포인트 및 경로를 다시 만듭니다.
    - 연결된 리소스를 다시 연결합니다.
1. 원본 리소스 정리: 원본 인스턴스를 삭제합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Digital Twins 인스턴스를 다시 만들기 전에 원래 인스턴스의 구성 요소를 검토하여 다시 만들어야 하는 모든 부분을 명확히 파악합니다.

고려해야 할 질문은 다음과 같습니다.

* 내 인스턴스에 업로드된 *모델* 은 무엇인가요? 몇 개인가요?
* 내 인스턴스의 *트윈* 은 무엇인가요? 몇 개인가요?
* 내 인스턴스에서 *그래프* 의 일반적인 모양은 무엇인가요? 얼마나 많은 관계가 있나요?
* 내 인스턴스에 있는 *엔드포인트* 는 무엇인가요?
* 내 인스턴스에 있는 *경로* 는 무엇인가요? 필터가 있나요?
* 내 인스턴스가 *다른 Azure 서비스에 연결* 되는 위치는 어떻게 되나요? 몇 가지 일반적인 통합 지점은 다음과 같습니다.

    - Azure Event Grid, Azure Event Hubs 또는 Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Azure IoT Hub Device Provisioning Service
* 내 인스턴스에 연결할 수 있는 다른 *개인 또는 회사 앱* 은 무엇인가요?

이 정보는 [Azure Portal](https://portal.azure.com), [Azure Digital Twins API 및 SDK](concepts-apis-sdks.md), [Azure Digital Twins CLI 명령](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) 또는 [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md)를 사용하여 수집할 수 있습니다.

## <a name="prepare"></a>준비

이 섹션에서는 원래 인스턴스에서 원래 모델, 트윈 및 그래프를 다운로드하여 인스턴스를 다시 만들 준비를 합니다. 이 문서에서는 [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md)를 이 작업에 사용합니다.

>[!NOTE]
>인스턴스에 모델 또는 그래프가 포함된 파일이 이미 있을 수 있습니다. 이 경우 모든 항목을 다운로드하는 것이 아니라 누락된 항목 또는 이러한 파일을 처음 업로드한 이후 변경되었을 수 있는 항목만 다시 다운로드하면 됩니다. 예를 들어 새 데이터로 업데이트된 트윈이 있을 수 있습니다.

### <a name="download-models-twins-and-graph-with-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer를 사용하여 모델, 트윈 및 그래프 다운로드

먼저 [Azure Portal](https://portal.azure.com)에서 Azure Digital Twins 인스턴스에 대한 **Azure Digital Twins Explorer** 를 엽니다. 이렇게 하려면 포털 검색 창에서 이름을 검색하여 포털에서 Azure Digital Twins 인스턴스로 이동합니다. 그런 다음, **탐색기로 이동(미리 보기)** 단추를 선택합니다. 

:::image type="content" source="media/includes/azure-digital-twins-explorer-portal-access.png" alt-text="Azure Digital Twins 인스턴스에 대한 개요 페이지를 보여주는 Azure Portal 스크린샷. 탐색기로 이동(미리 보기) 단추 주변에 강조 표시가 있습니다." lightbox="media/includes/azure-digital-twins-explorer-portal-access.png":::

그러면 이 인스턴스에 연결된 Azure Digital Twins Explorer 창이 열립니다.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="인터넷 브라우저의 Azure Portal 스크린샷. 포털에 데이터가 없는 Azure Digital Twins Explorer가 표시됩니다." lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

Azure Digital Twins Explorer 지침에 따라 [그래프 및 모델을 내보냅니다](how-to-use-azure-digital-twins-explorer.md#export-graph-and-models). 그러면 모델, 트윈 및 관계(현재 그래프에서 사용되지 않는 모델 포함)에 대한 코드가 포함된 JSON 파일이 머신에 다운로드됩니다.

## <a name="move"></a>이동

다음으로, 새 인스턴스를 대상 지역에 만들어 인스턴스의 "이동"을 완료합니다. 그런 다음, 원래 인스턴스의 데이터 및 구성 요소로 채웁니다.

### <a name="create-a-new-instance"></a>새 인스턴스 만들기

먼저, 새 Azure Digital Twines 인스턴스를 대상 지역에 만듭니다. [인스턴스 및 인증 설정](how-to-set-up-instance-portal.md)의 단계를 따릅니다. 다음 사항에 유의하세요.

* 다른 리소스 그룹에 있는 *경우* 새 인스턴스에 대해 동일한 이름을 유지할 수 있습니다. 원래 인스턴스가 포함된 리소스 그룹을 사용해야 하는 경우 새 인스턴스에는 고유한 이름이 필요합니다.
* 위치를 묻는 메시지가 표시되면 새 대상 지역을 입력합니다.

이 단계가 완료된 후 데이터를 사용하여 계속 설정하려면 새 인스턴스의 호스트 이름이 필요합니다. 설치 중에 호스트 이름을 적어 두지 않은 경우 지금 [이러한 지침](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)에 따라 Azure Portal에서 가져옵니다.

다음으로, 새 인스턴스의 데이터가 원래 인스턴스의 복사본이 되도록 설정합니다.

#### <a name="upload-models-twins-and-graph-with-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer를 사용하여 모델, 트윈 및 그래프 업로드

이 섹션에서는 모델, 트윈 및 그래프를 새 인스턴스에 다시 업로드할 수 있습니다. 모델, 트윈 또는 그래프가 원래 인스턴스에 없거나 새 인스턴스로 이동하지 않으려면 [다음 섹션](#re-create-endpoints-and-routes)으로 건너뛸 수 있습니다.

먼저 **Azure Portal** 에서 새 인스턴스에 대한 [Azure Digital Twins Explorer](https://portal.azure.com)로 이동합니다. 

[Azure Digital Twins Explorer로 파일 가져오기](how-to-use-azure-digital-twins-explorer.md#import-file-to-azure-digital-twins-explorer)에 대한 Azure Digital Twins Explorer 지침의 단계에 따라 이 문서의 앞부분에서 [다운로드한 JSON 파일](#download-models-twins-and-graph-with-azure-digital-twins-explorer)을 새 인스턴스로 가져옵니다. 그러면 원래 인스턴스의 모든 모델, 트윈 및 관계가 새 인스턴스로 업로드됩니다.

모든 항목이 성공적으로 업로드되었는지 확인하려면 **Twin Graph** 탭으로 다시 전환하고 **쿼리 탐색기** 패널에서 **쿼리 실행** 단추를 선택하여 그래프에 모든 쌍과 관계를 표시하는 기본 쿼리를 실행합니다. 이 작업은 **모델** 패널의 모델 목록도 새로 고칩니다.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="창의 오른쪽 위 모서리에 있는 '쿼리 실행' 단추가 강조 표시된 Azure Digital Twins Explorer의 스크린샷." lightbox="media/how-to-move-regions/run-query.png":::

**Twin Graph** 패널의 모든 트윈과 관계를 보여주는 그래프가 표시되어야 합니다. **모델** 패널에 나열된 모델도 표시되어야 합니다.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="모델 상자에 강조 표시된 두 가지 모델과 Twin Graph 상자에 강조 표시된 그래프를 보여주는 Azure Digital Twins Explorer의 스크린샷." lightbox="media/how-to-move-regions/post-upload.png":::

이러한 보기는 모델, 트윈 및 그래프가 대상 지역의 새 인스턴스로 다시 업로드되었음을 확인합니다.

#### <a name="re-create-endpoints-and-routes"></a>엔드포인트 및 경로 다시 만들기

원래 인스턴스의 엔드포인트 또는 경로가 있는 경우 새 인스턴스에서 이를 다시 만들어야 합니다. 원래 인스턴스의 엔드포인트 또는 경로가 없거나 새 인스턴스로 이동하지 않으려면 [다음 섹션](#relink-connected-resources)으로 건너뛸 수 있습니다.

그렇지 않으면 새 인스턴스를 사용하여 [엔드포인트 및 경로 관리](how-to-manage-routes.md)의 단계를 따릅니다. 다음 사항에 유의하세요.

* 엔드포인트에 사용하는 Event Grid, Event Hubs 또는 Service Bus 리소스를 다시 만들 필요가 *없습니다*. 자세한 내용은 엔드포인트 지침의 "필수 구성 요소" 섹션을 참조하세요. Azure Digital Twins 인스턴스에서 엔드포인트를 다시 만들기만 하면 됩니다.
* 범위가 다른 인스턴스로 지정되므로 엔드포인트 및 경로 이름은 다시 사용할 수 있습니다.
* 필요한 필터를 만든 경로에 추가해야 합니다.

#### <a name="relink-connected-resources"></a>연결된 리소스 다시 연결

원래 Azure Digital Twins 인스턴스에 연결된 다른 앱 또는 Azure 리소스가 있는 경우 새 인스턴스에 대신 연결되도록 연결을 편집해야 합니다. 이러한 리소스에는 Azure Digital Twins에서 작동하도록 설정한 다른 Azure 서비스 또는 개인/회사 앱이 포함될 수 있습니다.

원래 인스턴스의 연결된 다른 리소스가 없거나 새 인스턴스로 이동하지 않으려면 [다음 섹션](#verify)으로 건너뛸 수 있습니다.

그렇지 않으면 시나리오에서 연결된 리소스를 고려합니다. 연결된 리소스를 삭제하고 다시 만들 필요가 없습니다. 대신 호스트 이름을 통해 Azure Digital Twins 인스턴스에 연결되는 지점을 편집하기만 하면 됩니다. 그런 다음, 원래 인스턴스 대신 새 인스턴스의 호스트 이름을 사용하도록 이러한 지점을 업데이트합니다.

편집해야 하는 정확한 리소스는 시나리오에 따라 다르지만, 몇 가지 일반적인 통합 지점은 다음과 같습니다.

* Azure Functions. 원래 인스턴스의 호스트 이름이 포함된 코드의 Azure 함수가 있는 경우 이 값을 새 인스턴스의 호스트 이름으로 업데이트하고 함수를 다시 게시해야 합니다.
* Event Grid, Event Hubs 또는 Service Bus
* Logic Apps입니다.
* Time Series Insights
* Azure Maps
* IoT Hub Device Provisioning Service
* 인스턴스에 연결하고 Azure Digital Twins API를 호출하는 Azure 외부의 개인 또는 회사 앱(예: [클라이언트 앱 코딩](tutorial-code.md)에서 만든 클라이언트 앱).
* Azure AD 앱 등록을 다시 만들 필요가 *없습니다*. [앱 등록](./how-to-create-app-registration-portal.md)을 사용하여 Azure Digital Twins API에 연결하는 경우 동일한 앱 등록을 새 인스턴스에 다시 사용할 수 있습니다.

이 단계가 완료되면 대상 지역의 새 인스턴스가 원래 인스턴스의 복사본이 됩니다.

## <a name="verify"></a>확인

새 인스턴스가 올바르게 설정되었는지 확인하려면 다음 도구를 사용합니다.

* [Azure Portal](https://portal.azure.com). 포털은 새 인스턴스가 있고 올바른 대상 지역에 있는지 확인하는 데 유용합니다. 또한 엔드포인트, 경로 및 다른 Azure 서비스에 대한 연결을 확인하는 데도 유용합니다.
* [Azure Digital Twins CLI 명령](/cli/azure/dt?view=azure-cli-latest&preserve-view=true). 이러한 명령은 새 인스턴스가 있고 올바른 대상 지역에 있는지 확인하는 데 유용합니다. 또한 인스턴스 데이터를 확인하는 데 사용할 수도 있습니다.
* [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Azure Digital Twines Explorer는 모델, 트윈 및 그래프와 같은 인스턴스 데이터를 확인하는 데 유용합니다.
* [Azure Digital Twins API 및 SDK](concepts-apis-sdks.md). 이러한 리소스는 모델, 트윈 및 그래프와 같은 인스턴스 데이터를 확인하는 데 유용합니다. 또한 엔드포인트 및 경로를 확인하는 데도 유용합니다.

또한 원래 인스턴스에서 실행했던 사용자 지정 앱 또는 엔드투엔드 흐름을 실행하여 새 인스턴스에서 제대로 작동하는지 확인할 수 있습니다.

## <a name="clean-up-source-resources"></a>원본 리소스 정리

이제 새 인스턴스가 원래 인스턴스의 데이터 및 연결 복사본을 사용하여 대상 지역에 설정되었으므로 원래 인스턴스를 삭제할 수 있습니다.

[Azure Portal](https://portal.azure.com), [Azure CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) 또는 [컨트롤 플레인 API](concepts-apis-sdks.md#overview-control-plane-apis)를 사용할 수 있습니다.

Azure Portal을 사용하여 인스턴스를 삭제하려면 브라우저 창에서 [포털을 열고](https://portal.azure.com), 포털 검색 창에서 이름을 검색하여 원래 Azure Digital Twins 인스턴스로 이동합니다.

**삭제** 단추를 선택하고, 화면의 지시에 따라 삭제를 완료합니다.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Azure Portal의 개요 탭에 있는 Azure Digital Twines 인스턴스 세부 정보의 스크린샷. 삭제 단추가 강조 표시되어 있습니다.":::