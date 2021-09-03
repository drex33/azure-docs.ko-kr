---
title: 앱의 게시자 도메인 구성 | Azure
titleSuffix: Microsoft identity platform
description: 애플리케이션의 게시자 도메인을 구성하여 사용자가 자신의 정보가 전송되는 위치를 알 수 있도록 하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/23/2021
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: contperf-fy21q4, aaddev
ms.openlocfilehash: 43fef26f3006120cb62676bfd5675c1e79b849d4
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112579975"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>방법: 애플리케이션의 게시자 도메인 구성

[애플리케이션의 동의 프롬프트](application-consent-experience.md)에서 자신의 정보가 전송되는 위치를 알려주는 애플리케이션의 게시자 도메인이 사용자에게 표시됩니다. 2019년 5월 21일 이후에 등록된 다중 테넌트 애플리케이션 중 게시자 도메인이 없을 경우 **확인되지 않음** 으로 표시됩니다. 다중 테넌트 애플리케이션은 단일 조직 디렉터리 외부의 계정을 지원하는 애플리케이션입니다. 예를 들어 모든 Azure AD 계정을 지원하거나 모든 Azure AD 계정 및 개인 Microsoft 계정을 지원합니다.

## <a name="new-applications"></a>새 애플리케이션

새 앱을 등록하는 경우 앱의 게시자 도메인을 기본값으로 설정할 수 있습니다. 이 값은 앱이 등록된 위치, 특히 앱이 테넌트에 등록되었는지 여부 및 테넌트에 테넌트 확인 도메인이 있는지 여부에 따라 달라집니다.

테넌트 확인 도메인이 있는 경우 앱의 게시자 도메인은 기본적으로 테넌트의 확인된 주 도메인으로 변환됩니다. 테넌트 확인 도메인이 없는 경우(애플리케이션이 테넌트에 등록되지 않은 경우) 앱의 게시자 도메인은 null로 설정됩니다.

다음 표에서는 게시자 도메인 값의 기본 동작을 요약하여 보여줍니다.  

| 테넌트 확인 도메인 | 게시자 도메인의 기본값 |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (주) | domain2.com |

다중 테넌트 애플리케이션의 게시자 도메인이 설정되지 않았거나 .onmicrosoft.com으로 끝나는 도메인으로 설정된 경우 앱의 동의 프롬프트에는 게시자 도메인 대신 **확인되지 않음** 으로 표시됩니다.

## <a name="grandfathered-applications"></a>Grandfathered 애플리케이션

앱이 2019년 5월 21일 이전에 등록된 경우 게시자 도메인을 설정하지 않았다면 애플리케이션의 동의 프롬프트에는 **확인되지 않음** 이 표시되지 않습니다. 사용자가 앱의 동의 프롬프트에서 이 정보를 볼 수 있도록 게시자 도메인 값을 설정하는 것이 좋습니다.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Azure Portal을 이용하여 게시자 도메인 구성

앱의 게시자 도메인을 설정하려면 다음 단계를 수행합니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 앱이 등록된 테넌트를 선택합니다.
1. [Azure Active Directory > 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908)으로 이동하여 구성하고자 하는 앱을 선택합니다.

   앱을 선택하면 앱의 **개요** 페이지가 나타납니다.
1. **관리** 에서 **브랜딩** 을 선택합니다.
1. **게시자 도메인** 필드를 찾아 다음 옵션 중 하나를 선택합니다.

   - 도메인을 아직 구성하지 않은 경우 **도메인 구성** 을 선택합니다.
   - 도메인이 이미 구성되어 있으면 **도메인 업데이트** 를 선택합니다.

앱이 테넌트에 등록된 경우 두 개의 탭, 즉 **확인된 도메인 선택** 및 **새 도메인 확인** 이 표시됩니다.

앱이 테넌트에 등록되지 않은 경우 애플리케이션에 대한 새 도메인을 확인하는 옵션만 표시됩니다.

### <a name="to-verify-a-new-domain-for-your-app"></a>앱에 대한 새 도메인 확인

1. `microsoft-identity-association.json`라는 파일을 만들고 다음과 같은 JSON 코드 조각을 붙여 넣습니다.

   ```json
   {
      "associatedApplications": [
         {
            "applicationId": "{YOUR-APP-ID-HERE}"
         },
         {
            "applicationId": "{YOUR-OTHER-APP-ID-HERE}"
         }
      ]
    }
   ```

1. 자리 표시자 *{YOUR-APP-ID-HERE}* 를 앱에 해당하는 애플리케이션(클라이언트) ID로 바꿉니다.
1. `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json` 위치에서 파일을 호스트합니다. 확인된 도메인과 일치하도록 자리 표시자 *{YOUR-DOMAIN-HERE}* 를 변경합니다.
1. **도메인 확인 및 저장** 단추를 클릭합니다.

도메인이 확인된 후에는 확인에 사용되는 리소스를 유지 관리하지 않아도 됩니다. 확인이 완료되면 호스트된 파일을 제거할 수 있습니다.

### <a name="to-select-a-verified-domain"></a>확인된 도메인 선택

테넌트가 도메인을 확인한 경우 **확인된 도메인 선택** 드롭다운에 있는 도메인 중 하나를 선택합니다.

> [!NOTE]
> 반환되어야 하는 예상 `Content-Type` 헤더는 `application/json`입니다. `application/json; charset=utf-8`과 같은 다른 항목을 사용하는 경우 오류가 발생할 수 있습니다.
>
> `Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value.`
>

## <a name="implications-on-the-app-consent-prompt"></a>앱 동의 프롬프트에 대한 영향

게시자 도메인을 구성하면 앱 동의 프롬프트에서 사용자에게 표시되는 항목에 영향을 줍니다. 동의 프롬프트의 구성 요소를 완전히 이해하려면 [애플리케이션 동의 환경 이해](application-consent-experience.md)를 참조하세요.

다음 표에서는 2019년 5월 21일 이전에 만든 애플리케이션의 동작에 대해 설명합니다.

![2019년 5월 21일 이전에 만든 앱에 대한 동의 프롬프트](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

2019년 5월 21일 이후에 만든 애플리케이션에 대한 동작은 게시자 도메인과 애플리케이션 유형에 따라 달라집니다. 다음 표에서는 다양한 구성의 조합에 따라 변경될 수 있는 사항을 설명합니다.

![2019년 5월 21일 이후에 만든 앱에 대한 동의 프롬프트](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>리디렉션 URI에 대한 영향

회사/학교 계정 또는 개인 Microsoft 계정(다중 테넌트)으로 로그인하는 애플리케이션은 리디렉션 URI를 지정할 때 몇 가지 제한 사항이 적용됩니다.

### <a name="single-root-domain-restriction"></a>단일 루트 도메인 제한

다중 테넌트 앱에 대한 게시자 도메인 값이 null로 설정되면 앱은 리디렉션 URI에 대해 단일 루트 도메인을 공유하도록 제한됩니다. 예를 들어 다음과 같은 값은 조합하여 사용할 수 없는데, 이는 루트 도메인 contoso.com이 fabrikam.com과 일치하지 않기 때문입니다.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>하위 도메인 제한

하위 도메인은 허용되지만 루트 도메인을 명시적으로 등록해야 합니다. 예를 들어 다음 URI는 단일 루트 도메인을 공유하지만 조합은 허용되지 않습니다.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

그러나 개발자가 루트 도메인을 명시적으로 추가하는 경우 조합이 허용됩니다.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>예외

다음 경우에는 단일 루트 도메인 제한이 적용되지 않습니다.

- 단일 테넌트 앱 또는 단일 디렉터리의 계정을 대상으로 하는 앱
- Localhost를 리디렉션 URI로 사용
- 사용자 지정 스키마를 사용한 리디렉션 URI(non-HTTP 또는 HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>프로그래밍 방식의 게시자 도메인 구성

현재는 게시자 도메인을 프로그래밍 방식으로 구성하는 REST API 또는 PowerShell을 지원하지 않습니다.
