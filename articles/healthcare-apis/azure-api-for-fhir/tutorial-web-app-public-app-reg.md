---
title: 웹 앱 자습서-클라이언트 응용 프로그램 설정-FHIR 용 Azure API
description: 이 자습서에서는 웹 애플리케이션을 배포할 준비를 위해 퍼블릭 애플리케이션을 등록하는 단계를 안내합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 438ed15df54aefe299cbda0c45fe168f5857c93c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787076"
---
# <a name="client-application-registration-for-azure-api-for-fhir"></a>FHIR 용 Azure API에 대 한 클라이언트 응용 프로그램 등록
이전 자습서에서는 Azure API for FHIR을 배포하고 설정했습니다. 이제 Azure API for FHIR을 설정했으므로 퍼블릭 클라이언트 애플리케이션을 등록합니다. 자세한 내용 또는 문제 해결에 대한 전체 [퍼블릭 클라이언트 앱 등록](register-public-azure-ad-client-app.md) 방법 가이드를 참조할 수 있지만 아래 이 자습서에 대한 주요 단계를 소개했습니다.

1. Azure Active Directory로 이동
1. **앱 등록** --> **새 등록** 선택
1. 애플리케이션의 이름을 지정합니다.
1. **퍼블릭 클라이언트/네이티브(모바일 및 데스크톱)** 를 선택하고 리디렉션 URI를 `https://www.getpostman.com/oauth2/callback` 으로 설정합니다.

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="애플리케이션 등록 창의 스크린샷 및 애플리케이션 이름과 리디렉션 URL의 예입니다.":::

## <a name="client-application-settings"></a>클라이언트 애플리케이션 설정

클라이언트 애플리케이션이 등록되면 개요 페이지에서 애플리케이션(클라이언트) ID와 테넌트 ID를 복사합니다. 나중에 클라이언트에 액세스할 때 이 두 값이 필요합니다.

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="애플리케이션 및 디렉터리 ID가 강조 표시된 클라이언트 애플리케이션 설정 창의 스크린샷.":::

### <a name="connect-with-web-app"></a>웹앱으로 연결

Azure API for FHIR에 연결하기 위해 [웹앱을 작성](tutorial-web-app-write-web-app.md)한 경우 올바른 인증 옵션도 설정해야 합니다. 

1. 왼쪽 메뉴의 **관리** 아래에서 **인증** 을 선택합니다. 

1. 새 플랫폼 구성을 추가하려면 **웹** 을 선택합니다.

1. 이 자습서의 네 번째 부분에서 웹 애플리케이션을 만들 때를 준비하는 과정에서 리디렉션 URI를 설정합니다. 이렇게 하려면 리디렉션 URI 목록에 `https://\<WEB-APP-NAME>.azurewebsites.net`을 추가합니다. [웹앱을 작성](tutorial-web-app-write-web-app.md)하는 단계에서 다른 이름을 선택하는 경우 돌아가서 이를 업데이트해야 합니다.

1. **액세스 토큰** 및 **ID 토큰** 확인란을 선택합니다.

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="플랫폼 추가 단계가 강조 표시된 인증 설정 블레이드의 스크린샷.":::

## <a name="add-api-permissions"></a>API 사용 권한 추가

이제 올바른 인증을 설정했으므로 API 사용 권한을 설정합니다.

1. **API 사용 권한** 을 선택하고 **사용 권한 추가** 를 클릭합니다.
1. **내 조직에서 사용하는 API** 에서 Azure Healthcare API를 검색합니다.
1. **user_impersonation** 을 선택하고 **사용 권한 추가** 를 클릭합니다.

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="API 사용 권한을 추가하는 단계가 강조 표시된 API 사용 권한 추가 블레이드의 스크린샷.":::

## <a name="next-steps"></a>다음 단계
이제 퍼블릭 클라이언트 애플리케이션이 있습니다. 다음 자습서에서는 Postman을 통해 이 애플리케이션을 테스트하고 액세스 권한을 얻는 방법을 살펴봅니다.

>[!div class="nextstepaction"]
>[Postman에서 클라이언트 애플리케이션 테스트](tutorial-web-app-test-postman.md)
