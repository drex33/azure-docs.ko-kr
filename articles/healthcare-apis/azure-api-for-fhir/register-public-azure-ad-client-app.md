---
title: Azure AD에서 공용 클라이언트 앱 등록 - Azure API for FHIR
description: 이 문서에서는 Azure에서 FHIR API 배포를 준비하기 위해 Azure Active Directory 공용 클라이언트 애플리케이션을 등록하는 방법을 설명합니다.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: e89d577385e41fc1e2dcd0b58afafc8ccf5371c8
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278313"
---
# <a name="register-a-public-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>Azure API for FHIR Azure Active Directory 공용 클라이언트 애플리케이션 등록

이 문서에서는 Azure Active Directory 공용 애플리케이션을 등록하는 방법을 알아봅니다.  

클라이언트 애플리케이션 등록은 사용자를 대신하여 인증하고 API 권한을 요청할 수 있는 애플리케이션의 Azure Active Directory 표현입니다. 공용 클라이언트는 비밀을 기밀로 유지할 수 없는 모바일 애플리케이션 및 단일 페이지 JavaScript 애플리케이션과 같은 애플리케이션입니다. 이 절차는 [기밀 클라이언트를 등록하는](register-confidential-azure-ad-client-app.md)것과 유사하지만 퍼블릭 클라이언트가 애플리케이션 비밀을 보유하도록 신뢰할 수 없으므로 추가할 필요가 없습니다.

빠른 시작에서는 [Microsoft ID 플랫폼 애플리케이션을 등록하는](../../active-directory/develop/quickstart-register-app.md)방법에 대한 일반 정보를 제공합니다.

## <a name="app-registrations-in-azure-portal"></a>Azure Portal 앱 등록

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Azure Active Directory** 를 클릭합니다.

2. **Azure Active Directory** 블레이드에서 **앱 등록** 클릭합니다.

    ![Azure Portal 새 앱 등록.](media/add-azure-active-directory/portal-aad-new-app-registration.png)

3. 새 **등록** 을 클릭합니다.

## <a name="application-registration-overview"></a>애플리케이션 등록 개요

1. 애플리케이션에 표시 이름을 지정합니다.

2. 회신 URL을 제공합니다. 회신 URL은 인증 코드가 클라이언트 애플리케이션에 반환되는 위치입니다. 회신 URL을 더 추가하고 나중에 기존 URL을 편집할 수 있습니다.

    ![Azure Portal 새 공용 앱 등록.](media/add-azure-active-directory/portal-aad-register-new-app-registration-pub-client-name.png)


[데스크톱,](../../active-directory/develop/scenario-desktop-app-registration.md) [모바일](../../active-directory/develop/scenario-mobile-app-registration.md) 또는 [단일 페이지 애플리케이션을](../../active-directory/develop/scenario-spa-app-registration.md) 공용 애플리케이션으로 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com) **앱 등록** 에서 앱을 선택한 다음, **인증을** 선택합니다.

2. **고급 설정** 기본  >  **클라이언트 유형** 을 선택합니다. **애플리케이션을 공용 클라이언트로 처리에서** **예를** 선택합니다.

3. 단일 페이지 애플리케이션의 경우 **액세스 토큰 및** ID **토큰을** 선택하여 암시적 흐름을 사용하도록 설정합니다.

   - 애플리케이션에서 사용자를 로그인하는 경우 **ID 토큰** 을 선택합니다.
   - 애플리케이션에서 보호된 웹 API를 호출해야 하는 경우에는 **액세스 토큰** 을 선택합니다.

## <a name="api-permissions"></a>API 사용 권한

Azure API for FHIR 대한 권한은 RBAC를 통해 관리됩니다. 자세한 내용은 [Azure RBAC for FHIR 구성을](configure-azure-rbac.md)방문하세요.

>[!NOTE]
>Postman과 같은 도구를 사용하여 Azure API for FHIR 대한 액세스 토큰을 획득하려고 할 때 client_credentials grant_type 사용합니다. 자세한 내용은 [Azure API for FHIR FHIR API 테스트를](tutorial-web-app-test-postman.md)방문하세요.

## <a name="validate-fhir-server-authority"></a>FHIR 서버 기관 유효성 검사
이 문서에서 등록한 애플리케이션과 FHIR 서버가 동일한 Azure AD 테넌트인 경우 다음 단계를 진행하는 것이 좋습니다.

FHIR 서버의 다른 Azure AD 테넌트에서 클라이언트 애플리케이션을 구성하는 경우 **기관** 을 업데이트해야 합니다. Azure API for FHIR --> 인증을 설정 인증을 설정합니다. 권한을 '' '로 https://login.microsoftonline.com/ \<TENANT-ID> 설정합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Active Directory 공용 클라이언트 애플리케이션을 등록하는 방법을 배웠습니다. 다음으로 Postman을 사용하여 FHIR 서버에 대한 액세스를 테스트합니다.
 
>[!div class="nextstepaction"]
>[Postman을 사용하여 FHIR 서비스에 액세스](./../use-postman.md)
