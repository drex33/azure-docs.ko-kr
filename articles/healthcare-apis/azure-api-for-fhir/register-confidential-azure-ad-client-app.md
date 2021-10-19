---
title: Azure AD에서 기밀 클라이언트 앱 등록 - Azure API for FHIR
description: 사용자를 대신하여 인증하고 리소스 애플리케이션에 대한 액세스를 요청하는 기밀 클라이언트 애플리케이션을 Azure Active Directory 등록합니다.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: 2851f230a19bfbafb514e9fb22fd3f37de1f610b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272550"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>Azure Active Directory 기밀 클라이언트 애플리케이션을 등록하여 Azure API for FHIR

이 자습서에서는 Azure AD(Azure Active Directory)에서 기밀 클라이언트 애플리케이션을 등록하는 방법을 알아봅니다.  

클라이언트 애플리케이션 등록은 사용자를 대신하여 인증하고 리소스 애플리케이션에 대한 액세스를 요청하는 데 사용할 수 있는 애플리케이션의 Azure AD [표현입니다.](register-resource-azure-ad-client-app.md) 기밀 클라이언트 애플리케이션은 액세스를 요청할 때 비밀을 보관하고 해당 비밀을 표시하도록 신뢰할 수 있는 애플리케이션입니다. 기밀 애플리케이션의 예로는 서버 쪽 애플리케이션이 있습니다. 

새 기밀 클라이언트 애플리케이션을 등록하려면 아래 단계를 참조하세요. 

## <a name="register-a-new-application"></a>새 애플리케이션 등록

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 선택합니다.

1. **앱 등록** 을 선택합니다. 

    :::image type="content" source="media/add-azure-active-directory/portal-aad-new-app-registration.png" alt-text="Azure Portal. 새 앱 등록.":::

1. **새 등록** 을 선택합니다.

1. 애플리케이션에 사용자용 표시 이름을 지정합니다.

1. **지원되는 계정 유형에** 대해 애플리케이션을 사용하거나 API에 액세스할 수 있는 사람을 선택합니다.

1. (선택 사항) 리디렉션 **URI 를** 제공합니다. 이러한 세부 정보는 나중에 변경할 수 있지만 애플리케이션의 회신 URL을 알고 있는 경우 지금 입력합니다.

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client.png" alt-text="새 기밀 클라이언트 앱 등록.":::

1. **등록** 을 선택합니다.

## <a name="api-permissions"></a>API 사용 권한

Azure API for FHIR 대한 권한은 RBAC를 통해 관리됩니다. 자세한 내용은 [Azure RBAC for FHIR 구성을](configure-azure-rbac.md)방문하세요.

>[!NOTE]
>Postman과 같은 도구를 사용하여 Azure API for FHIR 대한 액세스 토큰을 획득하려고 할 때 client_credentials grant_type 사용합니다. 자세한 내용은 [Azure API for FHIR FHIR API 테스트를](tutorial-web-app-test-postman.md)방문하세요.


## <a name="application-secret"></a>애플리케이션 비밀

1. **인증서 및 암호** 를 선택하고 **새 클라이언트 암호** 를 선택합니다. 

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client-secret.png" alt-text="기밀 클라이언트. 애플리케이션 암호.":::

1. 클라이언트 암호에 대한 **설명** 을 입력합니다. **만료** 드롭다운 메뉴를 선택하여 만료 시간 프레임을 선택한 다음, **추가를** 클릭합니다.

   :::image type="content" source="media/add-azure-active-directory/add-a-client-secret.png" alt-text="클라이언트 비밀을 추가합니다.":::

1. 클라이언트 비밀 문자열을 만든 후 **해당 값** 및 **ID** 를 복사하여 선택한 안전한 위치에 저장합니다.

   :::image type="content" source="media/add-azure-active-directory/client-secret-string-password.png" alt-text="클라이언트 비밀 문자열입니다."::: 

> [!NOTE]
>클라이언트 비밀 문자열은 Azure Portal 한 번만 표시됩니다. 인증서 & 비밀 웹 페이지에서 이동한 다음 다시 돌아가면 값 문자열이 마스킹됩니다. 클라이언트 비밀 문자열을 생성한 직후에 복사하는 것이 중요합니다. 클라이언트 비밀의 백업 복사본이 없는 경우 위의 단계를 반복하여 다시 생성해야 합니다.
 
## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD에서 기밀 클라이언트 애플리케이션을 등록하는 방법의 단계를 안내했습니다. 또한 Azure Healthcare API에 API 권한을 추가하는 방법의 단계를 안내했습니다. 마지막으로, 애플리케이션 비밀을 만드는 방법을 보여 받았습니다. 또한 Postman을 사용하여 FHIR 서버에 액세스하는 방법을 알아볼 수 있습니다.
 
>[!div class="nextstepaction"]
>[Postman을 사용하여 FHIR 서비스에 액세스](./../use-postman.md)
