---
title: 빠른 시작 - Azure Portal의 App Service에서 서비스 연결 만들기
description: Azure Portal의 App Service에서 서비스 연결을 만드는 방법을 보여 주는 빠른 시작
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fcab4ce8d1f0d4d6f2dd7d29b1de8f9c7519c9f1
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851879"
---
# <a name="quickstart-create-a-service-connection-in-app-service-from-azure-portal"></a>빠른 시작: Azure Portal의 App Service에서 서비스 연결 만들기

이 빠른 시작에서는 Azure Portal의 App Service에서 서비스 커넥터를 사용하여 새 서비스 연결을 만드는 방법을 보여 줍니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

이 빠른 시작에서는 적어도 Azure에서 이미 실행 중인 App Service가 있다고 가정합니다. App Service가 없는 경우 [새로 만듭니다](../app-service/quickstart-dotnetcore.md).

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [https://portal.azure.com/](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.

## <a name="create-a-new-service-connection-in-app-service"></a>App Service에서 새 서비스 연결 만들기

서비스 커넥터를 사용하여 App Service에서 새 서비스 연결을 만듭니다.

1. Azure Portal 왼쪽에 있는 **모든 리소스** 단추를 선택합니다. 필터에 **App Service** 를 입력하고 목록에서 사용할 App Service 이름을 클릭합니다.
2. 왼쪽 목차에서 **서비스 커넥터(미리 보기)** 를 선택합니다. 그런 다음 **만들기** 를 선택합니다.
3. 다음 설정을 선택하거나 입력합니다.

    | 설정      | 제안 값  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **구독** | 구독 중 하나 | 대상 서비스(연결할 서비스)가 있는 구독입니다. 기본값은 이 App Service가 있는 구독입니다. |
    | **서비스 유형** | Blob Storage | 대상 서비스 유형입니다. Storage Blob 컨테이너가 없는 경우 [하나를 만들거나](../storage/blobs/storage-quickstart-blobs-portal.md) 다른 서비스 유형을 사용할 수 있습니다. |
    | **연결 이름** | 생성된 고유 이름 | App Service와 대상 서비스 간의 연결을 식별하는 연결 이름  |
    | **스토리지 계정** | 스토리지 계정 | 연결하려는 대상 스토리지 계정입니다. 다른 서비스 유형을 선택하는 경우 해당하는 대상 서비스 인스턴스를 선택합니다. |
    | **클라이언트 유형** | 이 App Service의 동일한 앱 스택 | 선택한 대상 서비스에서 작동하는 애플리케이션 스택입니다. 기본값은 App Service 런타임 스택에서 가져옵니다. |

4. **다음: 인증** 을 선택하여 인증 형식을 선택합니다. 그런 다음 **연결 문자열** 을 선택하여 액세스 키를 사용하여 Blob 스토리지 계정을 연결합니다.

5. 그런 다음을 **다음: 검토 + 만들기** 를 선택하여 제공된 정보를 검토합니다. 그런 다음 **만들기** 를 선택하여 서비스 연결을 만듭니다. 작업을 완료하는 데 1분 정도 걸릴 수 있습니다.

## <a name="view-service-connections-in-app-service"></a>App Service의 서비스 연결 보기

1. **서비스 커넥터(미리 보기)** 에서 대상 서비스에 대한 App Service 연결을 확인할 수 있습니다.

1. **>** 단추를 클릭하여 목록을 확장하면 애플리케이션 코드에 필요한 환경 변수를 확인할 수 있습니다.

1. **...** 단추를 클릭하고 **유효성 검사** 를 선택하면 오른쪽의 팝업 블레이드에서 연결 유효성 검사 세부 정보를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

아래 나열된 자습서를 따라 서비스 커넥터로 사용자만의 애플리케이션을 빌드할 수 있습니다.

> [!div class="nextstepaction"]
> [자습서: WebApp + Azure CLI를 사용하는 스토리지](./tutorial-csharp-webapp-storage-cli.md)

> [!div class="nextstepaction"]
> [자습서: WebApp + Azure CLI를 사용하는 PostgreSQL](./tutorial-django-webapp-postgres-cli.md)
