---
title: Azure AD에서 서비스 앱 등록-FHIR 용 Azure API
description: Azure Active Directory에서 서비스 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아봅니다.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: 3eb942160e45c589377ee3ecd283ea2939a23934
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273403"
---
# <a name="register-a-service-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>fhir 용 Azure API에 대 한 Azure Active Directory에 서비스 클라이언트 응용 프로그램 등록

이 문서에서는 Azure Active Directory에 서비스 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아봅니다. 클라이언트 응용 프로그램 등록은 토큰을 인증 하 고 가져오는 데 사용할 수 있는 응용 프로그램의 Azure Active Directory 표현입니다. 서비스 클라이언트는 응용 프로그램에서 사용자의 대화형 인증 없이 액세스 토큰을 가져오는 데 사용 됩니다. 특정 응용 프로그램 권한을 보유 하 고 액세스 토큰을 가져올 때 응용 프로그램 암호 (암호)를 사용 합니다.

새 서비스 클라이언트를 만들려면 다음 단계를 수행 합니다.

## <a name="app-registrations-in-azure-portal"></a>Azure Portal 앱 등록

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 로 이동 합니다.

2. **앱 등록** 을 선택합니다.

    ![Azure Portal 새 앱 등록.](media/add-azure-active-directory/portal-aad-new-app-registration.png)

3. **새 등록** 을 선택합니다.

4. 서비스 클라이언트에 표시 이름을 지정 합니다. 서비스 클라이언트 응용 프로그램은 일반적으로 회신 URL을 사용 하지 않습니다.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure Portal. 새 서비스 클라이언트 앱 등록.":::

5. **등록** 을 선택합니다.

## <a name="api-permissions"></a>API 사용 권한

FHIR 용 Azure API에 대 한 권한은 RBAC를 통해 관리 됩니다. 자세한 내용은 [FHIR에 대 한 AZURE RBAC 구성](configure-azure-rbac.md)을 참조 하세요.

>[!NOTE]
>Postman과 같은 도구를 사용 하 여 Azure API 용 Azure API에 대 한 액세스 토큰을 사용 하는 경우 client_credentials grant_type을 사용 합니다. 자세한 내용은 [fhir 용 AZURE api에서 FHIR Api 테스트](tutorial-web-app-test-postman.md)를 참조 하세요.

## <a name="application-secret"></a>애플리케이션 비밀

서비스 클라이언트에는 토큰을 얻기 위한 암호 (암호)가 필요 합니다.

1. **인증서 및 비밀** 을 선택합니다.
2. **새 클라이언트 비밀** 을 선택합니다.

    ![Azure Portal 서비스 클라이언트 암호](media/add-azure-active-directory/portal-aad-register-new-app-registration-service-client-secret.png)

3. 비밀의 설명 및 기간 (1 년, 2 년 또는 안 함)을 제공 합니다.

4. 비밀을 생성 한 후에는 포털에서 한 번만 표시 됩니다. 이를 기록해 두고 안전 하 게에 저장 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Active Directory에 서비스 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아보았습니다. 다음으로 Postman을 사용 하 여 FHIR 서버에 대 한 액세스를 테스트 합니다.
 
>[!div class="nextstepaction"]
>[Postman을 사용 하 여 FHIR 서비스에 액세스](./../use-postman.md)
