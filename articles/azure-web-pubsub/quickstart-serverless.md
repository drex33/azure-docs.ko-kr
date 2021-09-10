---
title: 자습서 - Azure Web PubSub 서비스 및 Azure Functions를 사용하여 서버리스 채팅 만들기
description: Azure Web PubSub 서비스 및 Azure Functions를 사용하여 서버리스 애플리케이션을 빌드하는 방법을 안내하는 자습서입니다.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 03/11/2021
ms.openlocfilehash: 466da06b470774c67fb93eca5cc027edb16bcbc7
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446169"
---
# <a name="tutorial-create-a-serverless-chat-with-azure-functions-and-azure-web-pubsub-service"></a>자습서: Azure Functions 및 Azure Web PubSub 서비스를 사용하여 서버리스 채팅 만들기

Azure Web PubSub 서비스를 사용하면 WebSocket 및 게시-구독 패턴을 사용하여 실시간 메시징 웹 애플리케이션을 쉽게 빌드할 수 있습니다. Azure Functions는 인프라를 관리하지 않고 코드를 실행할 수 있는 서버리스 플랫폼입니다. 이 자습서에서는 Azure Web PubSub 서비스 및 Azure Functions를 사용하여 실시간 메시징과 게시-구독 패턴으로 서버리스 애플리케이션을 빌드하는 방법에 대해 알아봅니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Web PubSub 서비스 인스턴스 만들기
> * 로컬에서 샘플 함수 실행
> * 애플리케이션에 이벤트 및 메시지를 라우팅하도록 Web PubSub 이벤트 처리기 구성

## <a name="prerequisites"></a>사전 요구 사항

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[Visual Studio Code](https://code.visualstudio.com/)와 같은 코드 편집기를 설치하고 [Node.js](https://nodejs.org/en/download/) 라이브러리 10.x 버전도 설치합니다.

   > [!NOTE]
   > 지원되는 Node.js 버전에 대한 자세한 내용은 [Azure Functions 런타임 버전 설명서](../azure-functions/functions-versions.md#languages)를 참조하세요.

Azure Function 앱을 로컬로 실행하려면 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)(버전 2.7.1505 이상)를 설치하세요.

# <a name="c"></a>[C#](#tab/csharp)

[Visual Studio Code](https://code.visualstudio.com/)와 같은 코드 편집기를 설치합니다.

Azure Function 앱을 로컬로 실행하려면 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)(버전 3 이상)를 설치하세요.

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

서비스가 배포되는 동안 코드로 작업을 전환해 보겠습니다. 첫 번째 단계로 [GitHub의 샘플 앱](https://github.com/Azure/azure-webpubsub/tree/main/samples/functions/js/simplechat)을 복제합니다.

1. Git 터미널 창을 엽니다. 샘플 프로젝트를 복제할 폴더로 이동합니다.

1. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure/azure-webpubsub.git
    ```

## <a name="configure-and-run-the-azure-function-app"></a>Azure 함수 앱을 구성하고 실행합니다.

- 브라우저에서 **Azure Portal** 을 열고 이전에 배포한 Web PubSub 서비스 인스턴스가 성공적으로 만들어졌는지 확인합니다. 인스턴스로 이동합니다.
- **키** 를 선택하고 연결 문자열을 복사합니다.

:::image type="content" source="media/quickstart-serverless/copy-connection-string.png" alt-text="Web PubSub 연결 문자열을 복사하는 스크린샷.":::

# <a name="javascript"></a>[JavaScript](#tab/javascript)

- 함수 구성을 업데이트합니다.

  복제된 리포지토리에서 */samples/functions/js/simplechat* 폴더를 엽니다. *local.settings.json* 을 편집하여 서비스 연결 문자열을 추가합니다.
  *local.settings.json* 에서 이러한 변경을 수행한 후 파일을 저장해야 합니다.
    - `<connection-string>` 자리표시자를 **Azure Portal** 에서 복사한 **`WebPubSubConnectionString`** 설정의 실제 값으로 바꿉니다. 
    - **`AzureWebJobsStorage`** 설정은 [Azure Functions를 사용하려면 Azure Storage 계정이 필요](../azure-functions/storage-considerations.md)하기 때문에 필요합니다.
        - [Azure Storage Emulator](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409)가 로컬에서 실행되는 경우 원래 설정 “UseDevelopmentStorage=true”를 유지합니다.
        - Azure Storage 연결 문자열이 있으면 값을 해당 문자열로 바꿉니다.
 
- JavaScript 함수는 폴더로 구성됩니다. 각 폴더에는 두 개의 파일이 있습니다. `function.json`은 함수에서 사용되는 바인딩을 정의하고 `index.js`는 함수의 본문입니다. 이 함수 앱에는 여러 트리거 함수가 있습니다.

    - **login** - 이 함수는 HTTP 트리거 함수입니다. *webPubSubConnection* 입력 바인딩을 사용하여 유효한 서비스 연결 정보를 생성하고 반환합니다.
    - **messages** - 이 함수는 `WebPubSubTrigger` 트리거 함수입니다. 요청 본문에서 대화 메시지를 수신하고 `WebPubSub` 출력 바인딩을 사용하여 모든 연결된 클라이언트 애플리케이션으로 메시지를 브로드캐스트합니다.
    - **connect** 및 **connected** - 이러한 함수는 `WebPubSubTrigger` 트리거 함수입니다. connect 및 connected 이벤트를 처리합니다.

- 터미널에서 */samples/functions/js/simplechat* 폴더에 있는지 확인합니다. 확장을 설치하고 함수 앱을 실행합니다.

    ```bash
    func extensions install

    func start
    ```

# <a name="c"></a>[C#](#tab/csharp)

- 함수 구성을 업데이트합니다.

  복제된 리포지토리에서 */samples/functions/csharp/simplechat* 폴더를 엽니다. *local.settings.json* 을 편집하여 서비스 연결 문자열을 추가합니다.
  *local.settings.json* 에서 이러한 변경을 수행한 후 파일을 저장해야 합니다.
    - `<connection-string>` 자리표시자를 **Azure Portal** 에서 복사한 **`WebPubSubConnectionString`** 설정의 실제 값으로 바꿉니다. 
    - **`AzureWebJobsStorage`** 설정은 [Azure Functions를 사용하려면 Azure Storage 계정이 필요](../azure-functions/storage-considerations.md)하기 때문에 필요합니다.
        - [Azure Storage Emulator](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409)가 로컬에서 실행되는 경우 원래 설정 “UseDevelopmentStorage=true”를 유지합니다.
        - Azure Storage 연결 문자열이 있으면 값을 해당 문자열로 바꿉니다.

- C# 함수는 Functions.cs 파일로 구성됩니다. 이 함수 앱에는 여러 트리거 함수가 있습니다.

    - **login** - 이 함수는 HTTP 트리거 함수입니다. *webPubSubConnection* 입력 바인딩을 사용하여 유효한 서비스 연결 정보를 생성하고 반환합니다.
    - **connected** - 이 함수는 `WebPubSubTrigger` 트리거 함수입니다. 요청 본문에서 대화 메시지를 수신하고 여러 작업으로 연결된 클라이언트 애플리케이션으로 메시지를 브로드캐스트합니다.
    - **broadcast** - 이 함수는 `WebPubSubTrigger` 트리거 함수입니다. 요청 본문에서 대화 메시지를 수신하고 단일 작업으로 연결된 클라이언트 애플리케이션으로 메시지를 브로드캐스트합니다.
    - **connect** 및 **disconnect** - 이러한 함수는 `WebPubSubTrigger` 트리거 함수입니다. connect 및 disconnect 이벤트를 처리합니다.

- 터미널에서 */samples/functions/csharp/simplechat* 폴더에 있는지 확인합니다. 확장을 설치하고 함수 앱을 실행합니다.

    ```bash
    func extensions install

    func start
    ```

---

- 로컬 함수는 `local.settings.json` 파일에 정의된 포트를 사용합니다. 이를 공용 네트워크에서 사용할 수 있도록 하려면 [ngrok](https://ngrok.com)를 사용하여 이 엔드포인트를 노출해야 합니다. 아래 명령을 실행하면 전달 엔드포인트(예: http://{ngrok-id}.ngrok.io -> http://localhost:7071 )를 가져올 수 있습니다.

    ```bash
    ngrok http 7071
    ``` 

- Azure Web PubSub 서비스에 `Event Handler`를 설정합니다. **Azure Portal** -> Web PubSub 리소스 찾기 -> **설정** 으로 이동합니다. 아래와 같이 사용 중인 하나의 함수에 새 허브 설정 매핑을 추가합니다. {ngrok-id}를 실제 ID로 바꿉니다.

   - 허브 이름: `simplechat`
   - URL 템플릿: **http://{ngrok-id}.ngrok.io/runtime/webhooks/webpubsub**
   - 사용자 이벤트 패턴: *
   - 시스템 이벤트: connect, connected, disconnected.

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="이벤트 처리기를 설정하는 스크린샷.":::

## <a name="run-the-web-application"></a>웹 애플리케이션 실행

1. 클라이언트 테스트를 간소화하려면 브라우저에서 샘플 [단일 페이지 웹 애플리케이션](http://jialinxin.github.io/webpubsub/)을 엽니다. 

1. 로컬 함수 앱 기준 URL을 입력합니다(`http://localhost:7071`).

1. 사용자 이름을 입력합니다.

1. 웹 애플리케이션이 함수 앱에서 *login* 함수를 호출하여 Azure Web PubSub 서비스에 연결할 연결 정보를 검색합니다. `Client websocket opened.`가 표시되는 경우 연결이 설정된 것입니다. 

1. 메시지를 입력하고 Enter 키를 누릅니다. 애플리케이션이 Azure Function 앱에서 이 메시지를 *messages* 함수로 보낸 다음, Web PubSub 출력 바인딩을 사용하여 연결된 클라이언트 모두에 이 메시지를 브로드캐스트합니다. 모든 기능이 올바르게 작동하는 경우 이 메시지가 애플리케이션에 표시됩니다.

1. 다른 브라우저 창에서 웹 애플리케이션의 다른 인스턴스를 엽니다. 애플리케이션의 모든 인스턴스에 전송된 메시지가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 앱을 계속 사용하지 않으려면 다음 단계에 따라 이 문서에서 만든 리소스를 모두 삭제하세요. 요금은 발생되지 않습니다.

1. Azure Portal에서 맨 왼쪽에 있는 **리소스 그룹** 을 선택한 다음, 만든 리소스 그룹을 선택합니다. 대신 검색 상자를 사용하여 이름으로 리소스 그룹을 찾을 수 있습니다.

1. 열린 창에서 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 를 선택합니다.

1. 새 창에서 삭제할 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 서버리스 채팅 애플리케이션을 실행하는 방법을 알아보았습니다. 이제 자체 애플리케이션을 빌드할 수 있습니다. 

> [!div class="nextstepaction"]
> [빠른 시작: Azure Web PubSub를 사용하여 간단한 채팅방 만들기](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Azure Functions에 대한 Azure Web PubSub 바인딩](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [Azure Web PubSub 샘플 자세히 살펴보기](https://github.com/Azure/azure-webpubsub/tree/main/samples)