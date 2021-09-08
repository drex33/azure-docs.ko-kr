---
title: 앱 서비스 약관 및 개인정보처리방침 | Azure
description: Azure AD를 사용하도록 등록된 앱의 서비스 약관 및 개인정보처리방침을 구성하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: ff363550b44286cb7140f533981229565fdf795f
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123032238"
---
# <a name="configure-terms-of-service-and-privacy-statement-for-an-app"></a>앱 서비스 약관 및 개인정보처리방침 구성

Azure AD(Azure Active Directory)와 Microsoft 계정을 통합하는 다중 테넌트 앱을 빌드하고 관리하는 개발자는 앱의 서비스 약관 및 개인정보처리방침에 대한 링크를 포함해야 합니다. 서비스 약관 및 개인정보처리방침은 사용자 동의 환경을 통해 사용자에게 표시됩니다. 서비스 약관 및 개인정보처리방침은 사용자가 앱을 믿고 사용할 수 있게 도와줍니다. 서비스 약관 및 개인정보처리방침은 여러 디렉터리에서 사용되거나 모든 Microsoft 계정에 제공되는 사용자용 다중 테넌트 앱에서 특히 중요합니다.

개발자는 앱의 서비스 약관 및 개인정보처리방침 문서를 만들고 이러한 문서에 대한 URL을 제공할 책임이 있습니다. 다중 테넌트 앱에서 이러한 링크를 제공하지 않는 경우 앱의 사용자 동의 환경에 사용자의 앱 사용 동의를 말리는 경고가 표시됩니다.

> [!NOTE]
> * 서비스 약관 및 개인정보처리방침 링크는 단일 테넌트 앱에는 적용되지 않습니다.
> * 두 링크 중 하나 또는 둘 다 없으면 앱에서 경고를 표시합니다.

## <a name="user-consent-experience"></a>사용자 동의 경험

다음 예제에서는 서비스 약관 및 개인정보처리방침이 구성되고, 해당 링크가 구성되지 않은 경우 다중 테넌트 앱에 대한 사용자 동의 환경을 보여줍니다.

![개인정보처리방침 및 서비스 약관이 제공된 스크린샷과 제공되지 않은 스크린샷](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>서비스 약관 및 개인정보처리방침 문서의 링크 서식 지정

앱의 서비스 약관 및 개인정보처리방침 문서에 링크를 추가하기 전에 URL이 지침을 준수하는지 확인해야 합니다.

| 지침     | 설명                           |
|---------------|---------------------------------------|
| 형식        | 유효한 URL                             |
| 유효한 스키마 | HTTP 및 HTTPS<br/>HTTPS 권장 |
| 최대 길이    | 2048자                       |

예: `https://myapp.com/terms-of-service` 및 `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>서비스 약관 및 개인정보처리방침에 링크 추가

서비스 약관 및 개인정보처리방침이 준비되면 다음 방법 중 하나를 사용하여 앱에 이러한 문서의 링크를 추가할 수 있습니다.

* [Azure Portal을 통해](#azure-portal)
* [앱 개체 JSON을 사용하여](#app-object-json)
* [Microsoft Graph API 사용](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Azure Portal 사용
Azure Portal에서 다음 단계를 따릅니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인하고 올바른 Azure AD 테넌트(B2C 아님)를 선택합니다.
2. **앱 등록** 섹션으로 이동하여 앱을 선택합니다.
3. **관리** 에서 **브랜딩** 을 선택합니다.
4. **서비스 약관 URL** 및 **개인정보처리방침 URL** 필드를 채웁니다.
5. **저장** 을 선택합니다.

    ![앱 속성에는 서비스 약관 및 개인정보처리방침 URL이 포함되어 있습니다.](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>앱 개체 JSON을 사용하여

앱 개체 JSON을 직접 수정하는 것을 선호하는 경우 Azure Portal 또는 애플리케이션 등록 포털에서 매니페스트 편집기를 사용하여 앱의 서비스 약관 및 개인정보처리방침 링크를 포함할 수 있습니다.

1. **앱 등록** 섹션으로 이동하여 앱을 선택합니다.
2. **매니페스트** 창을 엽니다.
3. Ctrl+F, "informationalUrls"를 검색합니다. 정보를 입력합니다.
4. 변경 내용을 저장합니다.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>Microsoft Graph API 사용

프로그래밍 방식으로 모든 앱을 업데이트하려면 Microsoft Graph API를 사용하여 서비스 약관 및 개인정보처리방침 문서 링크를 포함하도록 모든 앱을 업데이트하면 됩니다.

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * `supportUrl`, `marketingUrl` 및 `logoUrl` 필드에 할당된 기존 값을 덮어쓰지 않도록 주의해야 합니다.
> * Microsoft Graph API는 Azure AD 계정으로 로그인할 때만 작동합니다. 개인 Microsoft 계정은 지원되지 않습니다.
