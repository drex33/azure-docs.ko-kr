---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/08/2021
ms.openlocfilehash: 1cd52fc7930407317d426b3e2615202dce37e860
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751165"
---
## <a name="get-the-client-url-with-a-temp-access-token"></a>임시 액세스 토큰을 사용하여 클라이언트 URL 가져오기

Azure Portal은 빠른 테스트/유효성 검사 목적으로 임시 URL을 생성하는 단순 클라이언트 URL 생성기를 제공합니다. 이 생성기를 사용하여 임시 클라이언트 액세스 URL을 가져오고 인스턴스에 연결해 보겠습니다.

- Azure Portal로 이동하여 Azure Web PubSub 인스턴스를 확인합니다.
- `Key` 블레이드의 `Client URL Generator`로 이동합니다. 
- 적절하게 `Roles` 설정: **그룹에 보내기** 및 **그룹 가입/탈퇴**
- `Client Access URL`을 생성하고 복사합니다. 

:::image type="content" source="../media/quickstart-live-demo/generate-client-url.png" alt-text="클라이언트 URL 생성의 스크린샷.":::

## <a name="try-the-instance-with-an-online-demo"></a>온라인 데모에서 인스턴스를 사용해 보세요.

이 라이브 데모를 사용하여 그룹에 가입하거나 그룹에서 탈퇴하고 그룹 멤버에게 메시지를 쉽게 보낼 수 있습니다.

> [!div class="nextstepaction"]
> [데모 열기](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)

> [!NOTE]
>  **클라이언트 액세스 URL** 은 시작 환경을 단순화하기 위해 포털에서 제공되는 편리한 도구로, 이 클라이언트 액세스 URL을 사용하여 빠른 연결 테스트를 수행할 수도 있습니다. 사용자 고유의 애플리케이션을 작성할 수 있도록 SDK를 4개 언어로 제공하여 URL을 생성하는 데 도움을 줍니다. 

- 가입할 다른 그룹과 메시지를 보낼 다른 그룹을 시도하고 수신되는 메시지를 확인합니다. 예를 들어:
    - 두 클라이언트를 동일한 그룹에 조인합니다. 메시지가 그룹 구성원에게 브로드캐스트될 수 있음을 알 수 있습니다. 
    - 두 클라이언트를 서로 다른 그룹에 조인합니다. 그룹 멤버가 아닌 경우에는 클라이언트가 메시지를 수신할 수 없습니다. 
- `Client Access URL`을 생성할 때 `Roles`를 선택 취소하여 그룹에 조인하거나 그룹에 메시지를 보낼 때 어떤 일이 발생하는지 확인할 수도 있습니다. 예를 들어:
    - `Send to Groups` 권한을 선택 취소합니다. 클라이언트가 그룹에 메시지를 보낼 수 없는 것을 볼 수 있습니다. 
    - `Join/Leave Groups` 권한을 선택 취소합니다. 클라이언트는 그룹에 조인할 수 없습니다. 
