---
title: Purview 데이터 평면에 REST API를 사용하는 방법
description: 이 자습서에서는 Azure Purview REST API를 사용하여 Purview의 콘텐츠에 액세스하는 방법을 설명합니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/17/2021
ms.openlocfilehash: 5074e0017a5811b9418771aeef3b0883e10891c1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212820"
---
# <a name="tutorial-use-the-rest-apis"></a>자습서: REST API 사용

이 자습서에서는 Azure Purview REST API를 사용하는 방법을 알아봅니다. 데이터를 Azure Purview에 제출하거나, Purview를 자동화된 프로세스의 일부로 포함하거나, Purview에서 고유한 사용자 환경을 구축하려는 사용자는 REST API를 사용하여 이를 수행할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* 시작하려면 기존 Azure Purview 계정이 있어야 합니다. 카탈로그가 없는 경우 [Azure Purview 계정 만들기 빠른 시작](create-catalog-portal.md)을 참조하세요.

## <a name="create-a-service-principal-application"></a>서비스 주체(애플리케이션) 만들기

REST API 클라이언트가 카탈로그에 액세스하려면 카탈로그에서 인식하고 신뢰하도록 구성된 ID와 서비스 주체(애플리케이션)가 클라이언트에 있어야 합니다. 카탈로그에 대한 REST API 호출을 수행하면 해당 호출에 서비스 주체의 ID가 사용됩니다.

기존 서비스 주체(애플리케이션 ID)를 사용하는 고객은 실패율이 높습니다. 따라서 API 호출에 사용할 새 서비스 주체를 만드는 것이 좋습니다.

새 서비스 주체를 만들려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털에서 **Azure Active Directory** 를 검색하여 선택합니다.
1. **Azure Active Directory** 페이지의 왼쪽 창에서 **앱 등록** 을 선택합니다.
1. **새 등록** 을 선택합니다.
1. **애플리케이션 등록** 페이지에서 다음을 수행합니다.
    1. 애플리케이션 **이름**(서비스 주체 이름)을 입력합니다.
    1. **이 조직 디렉터리의 계정만 해당( _&lt;테넌트 이름만&gt;_ - 단일 테넌트)** 을 선택합니다.
    1. **리디렉션 URI(선택 사항)** 로 **웹** 을 선택하고 값을 입력합니다. 이 값은 유효한 URI가 아니어도 됩니다.
    1. **등록** 을 선택합니다.
1. 나중에 사용할 수 있도록 새 서비스 주체 페이지에서 **표시 이름** 및 **애플리케이션(클라이언트) ID** 값을 복사하여 저장합니다.

   애플리케이션 ID는 샘플 코드의 `client_id` 값입니다.

서비스 주체(애플리케이션)를 사용하려면 해당 암호를 가져와야 합니다. 방법은 다음과 같습니다.

1. Azure Portal에서 **Azure Active Directory** 를 검색하여 선택한 다음, 왼쪽 창에서 **앱 등록** 을 선택합니다.
1. 목록에서 서비스 주체(애플리케이션)를 선택합니다.
1. 왼쪽 창에서 **인증서 및 비밀** 을 선택합니다.
1. **새 클라이언트 비밀** 을 선택합니다.
1. **클라이언트 암호 추가** 페이지에서 **설명** 을 입력하고 **만료** 에서 만료 시간을 선택한 다음, **추가** 를 선택합니다.

   **클라이언트 암호** 페이지에서 새 비밀의 **값** 열에 있는 문자열이 암호입니다. 이 값을 저장합니다.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="클라이언트 암호를 보여주는 스크린샷":::

## <a name="set-up-authentication-using-service-principal"></a>서비스 주체를 사용하여 인증 설정

서비스 주체가 만들어지면 purview 계정의 데이터 평면 역할을 위에서 만든 서비스 주체에 할당해야 합니다. 아래 단계에 따라 역할을 할당하여 서비스 주체와 purview 계정 간에 신뢰를 설정해야 합니다.

1. [Purview Studio](https://web.purview.azure.com/resource/)로 이동합니다.
1. 왼쪽 메뉴에서 데이터 맵을 선택합니다.
1. 컬렉션을 선택합니다.
1. 컬렉션 메뉴에서 루트 컬렉션을 선택합니다. 이 컬렉션은 목록에서 최상위 컬렉션이 되며 Purview 계정과 동일한 이름을 갖습니다.
1. 역할 할당 탭을 선택합니다.
1. Purview의 다양한 데이터 평면에 액세스하려면 다음 역할을 위에서 만든 서비스 주체에 할당합니다.
    1. 카탈로그 데이터 평면에 액세스할 수 있는 '데이터 큐레이터' 역할
    1. 검사 데이터 평면에 액세스할 수 있는 '데이터 원본 관리자' 역할 
    1. 계정 데이터 평면에 액세스할 수 있는 '컬렉션 관리자' 역할
    1. 메타데이터 정책 데이터 평면에 액세스할 수 있는 '컬렉션 관리자' 역할

> [!Note]
> '컬렉션 관리자'만 Purview에서 데이터 평면 역할을 할당할 수 있습니다. [Azure Purview의 액세스 제어](./catalog-permissions.md)

## <a name="get-token"></a>토큰 가져오기
POST 요청을 다음 URL에 보내 액세스 토큰을 얻을 수 있습니다.

https://login.microsoftonline.com/{your-tenant-id}/oauth2/token

다음 매개변수를 위의 URL에 전달해야 합니다.

- **client_id**: Azure Active Directory에 등록된 애플리케이션의 클라이언트 ID이며, Purview 계정에 대한 데이터 평면 역할에 할당됩니다.
- **client_secret**: 위의 애플리케이션에 대해 만든 클라이언트 암호입니다.
- **grant_type**: 'client_credentials'여야 합니다.
- **resource**: 'https://purview.azure.net '이어야 합니다.
 
샘플 응답 토큰:

```json
    {
        "token_type": "Bearer",
        "expires_in": "86399",
        "ext_expires_in": "86399",
        "expires_on": "1621038348",
        "not_before": "1620951648",
        "resource": "https://purview.azure.net",
        "access_token": "<<access token>>"
    }
```

위의 액세스 토큰을 사용하여 데이터 평면 API를 호출합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데이터 원본 관리](manage-data-sources.md)
> [Purview 데이터 평면 REST API](/rest/api/purview/)
