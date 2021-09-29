---
title: Logic App 및 Power Automate에 대한 Azure Video Analyzer for Media(이전의 Video Indexer) 커넥터 자습서입니다.
description: 이 자습서에서는 Logic App 및 Power Automate를 사용하여 Azure Video Analyzer for Media(이전의 Video Indexer) 커넥터에서 새로운 경험과 수익 창출 기회를 실현하는 방법을 보여 줍니다.
ms.author: alzam
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 3650d82ec5d35b6b4e9826fd7a9d8579e40bb543
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626155"
---
# <a name="tutorial-use-video-analyzer-for-media-with-logic-app-and-power-automate"></a>자습서: 논리 앱 및 Power Automate에서 Video Analyzer for Media 사용

Video Analyzer for Media(이전의 Video Indexer) [REST API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Video)는 서버-서버 및 클라이언트-서버 통신을 모두 지원하며, Video Analyzer for Media 사용자가 비디오 및 오디오 인사이트를 애플리케이션 논리에 쉽게 통합하여 새로운 경험과 수익 창출 기회를 실현할 수 있도록 합니다.

더 쉽게 통합할 수 있도록 API와 호환되는  [Logic Apps](https://azure.microsoft.com/services/logic-apps/)  및  [Power Automate](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/)  커넥터를 지원합니다. 한 줄의 코드도 작성하지 않고도 커넥터를 사용하여 대량의 비디오 및 오디오 파일에서 인사이트를 효과적으로 인덱싱하고 추출할 수 있는 사용자 지정 워크플로를 설정할 수 있습니다. 또한 통합을 위해 커넥터를 사용하면 워크플로의 상태를 더 효율적으로 파악하고 쉽게 디버그할 수 있습니다.  

Video Analyzer for Media 커넥터를 빠르게 시작할 수 있도록 사용자가 설정할 수 있는 Logic App 및 Power Automate 솔루션의 예를 살펴보겠습니다. 이 자습서에서는 Logic Apps를 사용하여 흐름을 설정하는 방법을 보여 줍니다. 그러나 두 솔루션에서 편집기와 기능이 거의 동일하므로 다이어그램과 설명이 Logic Apps 및 Power Automate 모두에 적용됩니다.

이 자습서에서 다루는 "자동으로 비디오 업로드 및 인덱싱" 시나리오는 함께 작동하는 서로 다른 두 개의 흐름으로 구성됩니다. 
* 첫 번째 흐름은 Azure Storage 계정에서 Blob이 추가되거나 수정될 때 트리거됩니다. 인덱싱 작업이 완료되면 알림을 보내기 위해 콜백 URL을 사용하여 새 파일을 Video Analyzer for Media로 업로드합니다. 
* 두 번째 흐름은 콜백 URL에 따라 트리거되며, 추출된 인사이트를 Azure Storage의 JSON 파일에 다시 저장합니다. 이 두 가지 흐름 방법은 더 큰 파일의 비동기 업로드 및 인덱싱을 효과적으로 지원하는 데 사용됩니다. 

이 자습서에서는 논리 앱을 사용하여 다음을 수행하는 방법을 보여 줍니다.

> [!div class="checklist"]
> * 파일 업로드 흐름 설정
> * JSON 추출 흐름 설정

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* 먼저, [API 키를 통한 API 액세스 권한](video-indexer-use-apis.md)과 함께 Video Analyzer for Media 계정이 필요합니다. 
* Azure Storage 계정도 필요합니다. Storage 계정에 대한 액세스 키를 적어 두세요. 두 개의 컨테이너를 만듭니다. 하나는 비디오를 저장하고, 다른 하나는 Video Analyzer for Media에서 생성한 인사이트를 저장합니다.  
* 다음으로, 사용하는 항목에 따라 Logic Apps 또는 Power Automate에서 별도의 두 흐름을 열어야 합니다. 

## <a name="set-up-the-first-flow---file-upload"></a>첫 번째 흐름 설정 - 파일 업로드   

첫 번째 흐름은 Azure Storage 컨테이너에 Blob이 추가될 때마다 트리거됩니다. 트리거되면 Video Analyzer for Media에서 비디오를 업로드하고 인덱싱하는 데 사용할 수 있는 SAS URI를 만듭니다. 이 섹션에서는 다음과 같은 흐름을 만듭니다. 

![파일 업로드 흐름](./media/logic-apps-connector-tutorial/file-upload-flow.png)

첫 번째 흐름을 설정하려면 Video Analyzer for Media API 키와 Azure Storage 자격 증명을 제공해야 합니다. 

![Azure BLOB 스토리지](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![연결 이름 및 API 키](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> 이전에 Azure Storage 계정 또는 Video Analyzer for Media 계정을 논리 앱에 연결한 경우 연결 정보가 저장되고 자동으로 연결됩니다. <br/>Azure Storage(스토리지 창) 또는 Video Analyzer for Media(플레이어 창) 작업의 맨 아래에 있는 **연결 변경** 을 클릭하여 연결을 편집할 수 있습니다.

Azure Storage 및 Video Analyzer for Media 계정에 연결하고 **Logic Apps Designer** 에서 "Blob이 추가되거나 수정되는 경우" 트리거를 찾아 선택합니다.

비디오 파일을 저장할 컨테이너를 선택합니다. 

![컨테이너를 선택할 수 있는 "Blob이 추가되거나 수정되는 경우" 대화 상자를 보여 주는 스크린샷입니다.](./media/logic-apps-connector-tutorial/container.png)

다음으로, "경로로 SAS URI 만들기" 작업을 찾아 선택합니다. 작업 대화 상자의 동적 콘텐츠 옵션에서 파일 목록 경로를 선택합니다.  

또한 새 "공유 액세스 프로토콜" 매개 변수를 추가합니다. 매개 변수 값에 HttpsOnly를 선택합니다.

![경로로 SAS URI](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Video Analyzer for Media 계정 토큰을 가져오려면 [계정 위치](regions.md) 및 [계정 ID](./video-indexer-use-apis.md#account-id) 를 입력합니다.

![계정 액세스 토큰 가져오기](./media/logic-apps-connector-tutorial/account-access-token.png)

"비디오 업로드 및 인덱싱"에서 필수 매개 변수와 비디오 URL을 입력합니다. "새 매개 변수 추가"를 선택하고, 콜백 URL을 선택합니다. 

![업로드 및 인덱싱](./media/logic-apps-connector-tutorial/upload-and-index.png)

지금은 콜백 URL을 비워 둡니다. 이는 콜백 URL이 만들어지는 두 번째 흐름을 완료한 후에만 추가할 수 있습니다. 

다른 매개 변수에 대한 기본값을 사용하거나 필요에 따라 설정할 수 있습니다. 

**저장** 을 클릭합니다. 업로드와 인덱싱이 완료되면 인사이트를 추출하기 위한 두 번째 흐름을 구성해 보겠습니다. 

## <a name="set-up-the-second-flow---json-extraction"></a>두 번째 흐름 설정 - JSON 추출  

첫 번째 흐름에서 업로드와 인덱싱이 완료되면 올바른 콜백 URL이 포함된 HTTP 요청을 보내 두 번째 흐름을 트리거합니다. 그런 다음, Video Analyzer for Media에서 생성된 인사이트를 검색합니다. 이 예에서는 인덱싱 작업의 출력을 Azure Storage에 저장합니다.  그러나 출력으로 수행할 수 있는 작업은 사용자에게 달려 있습니다.  

두 번째 흐름은 첫 번째 흐름과 별도로 만듭니다. 

![JSON 추출 흐름](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

이 흐름을 설정하려면 Video Analyzer for Media API 키와 Azure Storage 자격 증명을 다시 제공해야 합니다. 첫 번째 흐름과 동일한 매개 변수를 업데이트해야 합니다. 

트리거에서는 HTTP POST URL 필드가 표시됩니다. 흐름을 저장한 후에는 URL이 생성되지 않지만, 결국에는 URL이 필요합니다. 나중에 이를 다시 살펴보겠습니다. 

Video Analyzer for Media 계정 토큰을 가져오려면 [계정 위치](regions.md) 및 [계정 ID](./video-indexer-use-apis.md#account-id) 를 입력합니다.  

"비디오 인덱스 가져오기" 작업으로 이동하여 필수 매개 변수를 입력합니다. [비디오 ID]에 대해 triggerOutputs()['queries']['id'] 식을 입력합니다. 

![Video Analyzer for Media 작업 정보](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

이 식은 트리거의 출력에서 비디오 ID를 가져오도록 커넥터에 지시합니다. 이 경우 트리거의 출력은 첫 번째 트리거에 있는 "비디오 업로드 및 인덱싱"의 출력입니다. 

"Blob 만들기" 작업으로 이동하여 인사이트를 저장할 폴더의 경로를 선택합니다. 만드는 Blob의 이름을 설정합니다. [Blob 콘텐츠]에 대해 body(‘Get_Video_Index’) 식을 입력합니다. 

![Blob 만들기 작업](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

이 식은 이 흐름에서 "비디오 인덱스 가져오기" 작업의 출력을 가져옵니다. 

**흐름 저장** 을 클릭합니다. 

흐름이 저장되면 트리거에서 HTTP POST URL이 만들어집니다. 트리거에서 URL을 복사합니다. 

![URL 트리거 저장](./media/logic-apps-connector-tutorial/save-url-trigger.png)

이제 첫 번째 흐름으로 돌아가서 이 URL을 "비디오 업로드 및 인덱싱" 작업의 콜백 URL 매개 변수로 붙여넣습니다. 

두 흐름이 모두 저장되었는지 확인하고 이동하는 것이 좋습니다. 

비디오를 Azure Blob 컨테이너에 추가하여 새로 만든 Logic App 또는 Power Automate 솔루션을 사용해 보고, 몇 분 후에 다시 이동하여 대상 폴더에 인사이트가 표시되는지 확인합니다. 

## <a name="generate-captions"></a>자막 생성

[Video Analyzer for Media 및 Logic Apps를 사용하여 자막을 생성하는 방법](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198)을 표시하는 단계는 다음 블로그를 참조하세요. 

또한 이 문서에서는 OneDrive에 비디오를 복사하여 자동으로 비디오를 인덱싱하는 방법과 OneDrive에서 Video Analyzer for Media에 의해 생성된 자막을 저장하는 방법을 보여 줍니다.
 
## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료한 후에는 이 Logic App 또는 Power Automate 솔루션을 자유롭게 유지하고 필요한 경우 이를 시작 및 실행할 수 있습니다. 그러나 이 작업을 계속 실행하지 않고 요금이 청구되지 않도록 하려면 Power Automate를 사용하는 경우 두 흐름을 모두 해제합니다. Logic Apps를 사용하는 경우 두 흐름을 모두 사용하지 않도록 설정합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 하나의 Video Analyzer for Media 커넥터 예제만 보여 주었습니다. Video Analyzer for Media에서 제공하는 API 호출에 대해 Video Analyzer for Media 커넥터를 사용할 수 있습니다. 예를 들어 인사이트를 업로드 및 검색하고, 결과를 변환하고, 포함 가능한 위젯을 가져오고, 모델을 사용자 지정합니다. 또한 파일 리포지토리 업데이트 또는 보낸 이메일과 같은 다양한 원본에 따라 이러한 작업을 트리거하도록 선택할 수 있습니다. 그런 다음, 결과를 관련 인프라 또는 애플리케이션에 업데이트하거나 원하는 수의 작업 항목을 생성하도록 선택할 수 있습니다.  

> [!div class="nextstepaction"]
> [Video Analyzer for Media API 사용](video-indexer-use-apis.md)

추가 리소스는 [Video Analyzer for Media](/connectors/videoindexer-v2/)를 참조하세요.
