---
title: '빠른 시작: Azure Portal을 사용하여 Azure Static Web Apps를 통해 첫 번째 정적 웹앱 빌드'
description: Azure Portal을 사용하여 정적 사이트를 Azure Static Web Apps에 배포하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.author: cshoe
ms.date: 05/07/2021
ms.topic: quickstart
ms.service: static-web-apps
ms.custom:
- mode-portal
ms.openlocfilehash: 41be62a4e7baed39cc45011ed055a1abfe097002
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124804578"
---
# <a name="quickstart-building-your-first-static-site-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 첫 번째 정적 사이트 빌드

Azure Static Web Apps는 GitHub 리포지토리에서 앱을 빌드하여 프로덕션 환경에 웹 사이트를 게시합니다. 이 빠른 시작에서는 Azure Portal을 사용하여 웹 애플리케이션을 Azure Static 웹앱에 배포합니다.

Azure 구독이 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- [GitHub](https://github.com) 계정
- [Azure](https://portal.azure.com) 계정

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>정적 웹앱 만들기

이제 리포지토리가 생성되었으므로 Azure Portal에서 정적 웹앱을 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **리소스 만들기** 를 선택합니다.
1. **Static Web Apps** 를 검색합니다.
1. **Static Web Apps** 를 선택합니다.
1. **만들기** 를 선택합니다.

_기본 사항_ 섹션에서 새 앱을 구성하고 GitHub 리포지토리에 연결하여 시작합니다.

:::image type="content" source="media/getting-started-portal/quickstart-portal-basics.png" alt-text="기본 사항 섹션":::

1. Azure _구독_ 을 선택합니다.
1. _리소스 그룹_ 옆에 있는 **새로 만들기** 링크를 선택합니다.
1. 텍스트 상자에 **static-web-apps-test** 를 입력합니다.
1. _정적 웹앱 세부 정보_ 아래의 텍스트 상자에 **my-first-static-web-app** 을 입력합니다.
1. _Azure Functions 및 준비 세부 정보_ 에서 사용자에게 가장 가까운 지역을 선택합니다.
1. _배포 세부 정보_ 에서 **GitHub** 를 선택합니다.
1. **GitHub로 로그인** 단추를 선택하고 GitHub로 인증합니다.

GitHub로 로그인한 후 리포지토리 정보를 입력합니다.

:::image type="content" source="media/getting-started-portal/quickstart-portal-source-control.png" alt-text="리포지토리 세부 정보":::

1. 원하는 _조직_ 이름을 선택합니다.
1. _리포지토리_ 드롭다운에서 **my-first-web-static-app** 을 선택합니다.
1. _분기_ 드롭다운에서 **기본** 을 선택합니다.

   > [!NOTE]
   > 리포지토리가 표시되지 않는 경우 GitHub에서 Azure Static Web Apps에 권한을 부여해야 할 수 있습니다. GitHub 리포지토리로 이동하여 **설정 > 애플리케이션 > 권한 부여된 OAuth 앱** 으로 이동하고 **Azure Static Web Apps** 를 선택한 다음 **권한 부여** 를 선택합니다. 조직 리포지토리의 경우 사용 권한을 부여하려면 조직의 소유자여야 합니다.

1. _빌드 세부 정보_ 섹션에서 선호하는 프런트 엔드 프레임워크와 관련된 구성 세부 정보를 추가합니다.

    # <a name="no-framework"></a>[프레임워크 없음](#tab/vanilla-javascript)

    1. _빌드 사전 설정_ 드롭다운에서 **사용자 지정** 을 선택합니다.
    1. _앱 위치_ 상자에 **./src** 를 입력합니다.
    1. _Api 위치_ 상자를 비워 둡니다.
    1. _앱 아티팩트 위치_ 상자에 **./src** 를 입력합니다.

    # <a name="angular"></a>[Angular](#tab/angular)

    1. _빌드 사전 설정_ 드롭다운에서 **Angular** 를 선택합니다.
    1. _앱 위치_ 상자에서 기본값을 유지합니다.
    1. _Api 위치_ 상자를 비워 둡니다.
    1. _앱 아티팩트 위치_ 상자에 **dist/angular-basic** 을 입력합니다.

    # <a name="react"></a>[React](#tab/react)

    1. _빌드 사전 설정_ 드롭다운에서 **React** 를 선택합니다.
    1. _앱 위치_ 상자에서 기본값을 유지합니다.
    1. _Api 위치_ 상자를 비워 둡니다.
    1. _앱 아티팩트 위치_ 상자에 **빌드** 를 입력합니다.

    # <a name="vue"></a>[Vue](#tab/vue)

    1. _빌드 사전 설정_ 드롭다운에서 **Vue.js** 를 선택합니다.
    1. _앱 위치_ 상자에서 기본값을 유지합니다.
    1. _Api 위치_ 상자를 비워 둡니다.
    1. _앱 아티팩트 위치_ 상자에서 기본값을 유지합니다.

    ---

1. **검토 + 만들기** 를 선택합니다.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="만들기 단추 검토":::

    > [!NOTE]
    > 앱을 만든 후 이러한 값을 변경하려면 [워크플로 파일](build-configuration.md)을 편집하면 됩니다.

1. **만들기** 를 선택합니다.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="만들기 단추":::

1. **리소스로 이동** 을 선택합니다.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="리소스로 이동 단추":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않을 경우 다음 단계를 통해 Azure Static Web App 인스턴스를 삭제할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 위쪽 검색 창에서 **my-first-web-static-app** 을 검색합니다.
1. 앱 이름을 선택합니다.
1. **삭제** 단추를 선택합니다.
1. **예** 를 선택하여 삭제 작업을 확인합니다(이 작업은 완료하는 데 몇 분 정도 걸릴 수 있음).

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 추가](add-api.md)
