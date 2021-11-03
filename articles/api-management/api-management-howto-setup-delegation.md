---
title: 사용자 등록 및 제품 구독을 위임하는 방법
description: Azure API Management에서 사용자 등록 및 제품 구독을 타사에 위임하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2021
ms.author: danlep
ms.openlocfilehash: dbb948e45473bf091cb2d69e82ad2adf5fcfe8a2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058638"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>사용자 등록 및 제품 구독을 위임하는 방법

위임을 사용하면 웹 사이트에서 사용자 데이터를 소유하고 사용자 지정 유효성 검사를 수행할 수 있습니다. 위임을 사용하면 개발자 포털의 기본 제공 기능이 아니라 기존 웹 사이트를 사용하여 개발자 로그인/가입 및 제품 구독을 처리할 수 있습니다. 

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>개발자 로그인 및 가입 위임

개발자를 위임하고 기존 웹 사이트에 로그인하여 가입하려면 사이트에서 특별한 위임 엔드포인트를 만듭니다. 이 특수 위임은 API Management 개발자 포털에서 시작된 로그인/가입 요청에 대한 진입점 역할을 합니다.

최종 워크플로는 다음과 같습니다.

1. 개발자가 API Management 개발자 포털에서 로그인 또는 가입 링크를 클릭합니다.
2. 브라우저가 위임 엔드포인트로 리디렉션됩니다.
3. 위임 엔드포인트는 대신 사용자를 로그인/가입으로 리디렉션하거나 제공합니다. 
4. 로그인/가입에 성공하면 사용자가 떠난 위치의 API Management 개발자 포털로 다시 리디렉션됩니다.

### <a name="set-up-api-management-to-route-requests-via-delegation-endpoint"></a>위임 엔드포인트를 통해 요청을 라우팅하도록 API Management 설정

1. Azure Portal의 API Management 리소스에서 **개발자 포털** 을 검색합니다.
2. **위임** 항목을 클릭합니다. 
3. **로그인 및 가입 위임** 확인란을 클릭하여 사용하도록 설정합니다.

:::image type="content" source="media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png" alt-text="위임 개요":::

4. 특수 위임 엔드포인트의 URL을 결정하고 **위임 엔드포인트 URL** 필드에 이 URL을 입력합니다. 
5. **위임 유효성 검사 키** 필드에 다음을 입력합니다.
    * 요청이 API Management에서 시작되는지 확인하기 위해 제공된 서명을 계산하는 데 사용하는 비밀을 입력합니다. 
    * API Management의 **생성** 단추를 클릭하여 임의 키를 생성합니다.
6. **저장** 을 클릭합니다.

### <a name="create-your-delegation-endpoint"></a>위임 엔드포인트 만들기

>[!NOTE]
> 다음 프로시저에서는 **로그인** 작업의 예를 제공하지만, 아래 단계에 따라 사용 가능한 작업을 사용하여 계정 관리를 수행할 수 있습니다. 

사이트에서 구현할 새 위임 엔드포인트를 만드는 권장 단계는 다음과 같습니다.

1. 다음 형식의 요청을 받습니다.
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   
    로그인/가입 사례에 대한 쿼리 매개 변수는 다음과 같습니다.

   | 매개 변수 | Description |
   | --------- | ----------- |
   | **operation** | 위임 요청 유형을 식별합니다. 사용 가능한 작업: **SignIn**, **ChangePassword**, **ChangeProfile**, **CloseAccount**, **SignOut**. |
   | **returnUrl** | 사용자가 로그인 또는 가입 링크를 클릭하는 위치의 URL입니다. |
   | **salt** | 보안 해시를 계산하는 데 사용되는 특수 salt 문자열입니다. |
   | **sig** | 자신의 계산된 해시와 비교하는 데 사용되는 계산된 보안 해시입니다. |
   
3. 요청이 Azure API Management에서 들어오는지 확인합니다(선택 사항이지만 보안을 위해 상당히 권장됨).
   
   * **returnUrl** 및 **salt** 쿼리 매개 변수에 따라 문자열의 HMAC-SHA512 해시를 컴퓨팅합니다. 자세한 내용은 [예제 코드]를 확인하세요.
     
     ```
         HMAC(salt + '\n' + returnUrl)
     ```
   * 위의 계산된 해시와 **sig** 쿼리 매개 변수 값을 비교합니다. 두 해시가 일치하면 다음 단계를 진행합니다. 일치하지 않으면 요청을 거부합니다.
4. 로그인/가입 요청을 받았는지 확인합니다.
    * **작업** 쿼리 매개 변수는 “**SignIn**”으로 설정됩니다.
5. 사용자에게 로그인/가입을 위한 UI를 제공합니다.
    * 사용자가 가입하는 경우 API Management 해당 계정을 만듭니다. 
      * API Management REST API를 사용하여 [사용자를 만듭니다]. 
      * 사용자 ID를 사용자 저장소의 동일한 값 또는 쉽게 추적할 수 있는 새 ID로 설정합니다.
6. 사용자가 인증되면
   
   * API Management REST API를 통해 [공유 액세스 토큰을 요청]합니다.
   * 위의 API 호출에서 받은 SSO URL에 **returnUrl** 쿼리 매개 변수를 추가합니다. 예를 들면 다음과 같습니다.
     
     > `https://contoso.developer.azure-api.net/signin-sso?token=<URL-encoded token>&returnUrl=%2Freturn%2Furl` 
     
   * 사용자를 위에서 생성한 URL로 리디렉션합니다.

>[!NOTE]
> 계정 관리 작업(**ChangePassword**, **ChangeProfile**, **CloseAccount**)의 경우 다음 쿼리 매개 변수를 전달합니다.
> 
>    | 매개 변수 | Description |
>    | --------- | ----------- |
>    | **operation** | 위임 요청 유형을 식별합니다. |
>    | **userId** | 관리할 계정의 사용자 ID입니다. |
>    | **salt** | 보안 해시를 계산하는 데 사용되는 특수 salt 문자열입니다. |
>    | **sig** | 자신의 계산된 해시와 비교하는 데 사용되는 계산된 보안 해시입니다. |

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>제품 구독 위임

제품 구독 위임은 사용자 로그인/가입 위임과 비슷하게 작동합니다. 최종 워크플로는 다음과 같습니다.

1. 개발자가 API Management 개발자 포털에서 제품을 선택하고 **구독** 단추를 클릭합니다.
2. 브라우저가 위임 엔드포인트로 리디렉션됩니다.
3. 위임 엔드포인트는 사용자가 디자인한 필수 제품 구독 단계를 수행합니다. 여기에는 다음이 포함될 수 있습니다. 
   * 청구 정보를 요청하기 위해 다른 페이지로 리디렉션.
   * 추가 질문하기.
   * 정보를 저장하고 사용자 작업이 필요하지 않음.

### <a name="enable-the-api-management-functionality"></a>API Management 기능 사용

**위임** 페이지에서 **제품 구독 위임** 을 클릭합니다.

### <a name="create-your-delegation-endpoint"></a>위임 엔드포인트 만들기

사이트에서 구현할 새 위임 엔드포인트를 만드는 권장 단계는 다음과 같습니다.

1. 다음 형식의 요청을 받습니다.
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    제품 구독 케이스에 대한 쿼리 매개 변수:

   | 매개 변수 | Description |
   | --------- | ----------- |
   | **operation** | 위임 요청 유형을 식별합니다. 유효한 제품 구독 요청 옵션은 다음과 같습니다. <ul><li>**Subscribe**: 사용자가 제공된 ID를 사용하여 지정된 제품을 구독하도록 하는 요청입니다(아래 참조).</li><li>**Unsubscribe**: 제품에 대한 사용자 구독을 취소하는 요청입니다.</li><li>**Renew**: 구독을 갱신하는 요청입니다(예: 만료일이 다가오는 경우).</li></ul> |
   | **productId** | *Subscribe* 에서는 사용자가 구독을 요청한 제품 ID입니다. |
   | **subscriptionId** | *Unsubscribe* 와 *Renew* 에서는 제품 구독입니다. |
   | **userId** | *Subscribe* 에서는 요청하는 사용자의 ID입니다. |
   | **salt** | 보안 해시를 계산하는 데 사용되는 특수 salt 문자열입니다. |
   | **sig** | 자신의 계산된 해시와 비교하는 데 사용되는 계산된 보안 해시입니다. |

2. 요청이 Azure API Management에서 들어오는지 확인합니다(선택 사항이지만 보안을 위해 상당히 권장됨).
   
   * **productId**, **userId** 및 **salt** 쿼리 매개 변수를 기반으로 하는 문자열의 HMAC-SHA512를 계산합니다.
     ```
     HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     ```
   * 위의 계산된 해시와 **sig** 쿼리 매개 변수 값을 비교합니다. 두 해시가 일치하면 다음 단계를 진행합니다. 일치하지 않으면 요청을 거부합니다.
3. **operation** 에서 요청한 작업 유형(예: 청구, 추가 질문 등)에 따라 제품 구독을 처리합니다.
4. 사용자의 제품 구독이 성공적으로 완료되면 [구독에 대한 REST API를 호출]하여 사용자가 API Management 제품도 구독하도록 처리합니다.

## <a name="example-code"></a><a name="delegate-example-code"> </a> 예제 코드

이러한 코드 샘플에서는 다음을 수행하는 방법을 보여 줍니다.

* Azure Portal의 **위임** 화면에서 설정 된 *위임 유효성 검사 키* 를 사용 합니다.
* HMAC를 만든 다음, 이를 통해 서명의 유효성을 검사하여 전달된 returnUrl의 유효성을 증명합니다.

약간만 수정하면 동일한 코드를 **productId** 와 **userId** 에도 사용할 수 있습니다.

### <a name="c-code-to-generate-hash-of-returnurl"></a>returnUrl의 해시를 생성하는 C# 코드

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

### <a name="nodejs-code-to-generate-hash-of-returnurl"></a>returnUrl의 해시를 생성하는 NodeJS 코드

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> 위임 변경 내용을 적용하려면 [개발자 포털을 다시 게시](api-management-howto-developer-portal-customize.md#publish)해야 합니다.

## <a name="next-steps"></a>다음 단계
- [다음 문서를 통해 개발자 포털에 관해 자세히 알아보세요.](api-management-howto-developer-portal.md)
- [Azure AD B2C](api-management-howto-aad-b2c.md) 또는 [Azure AD를 사용하여 인증](api-management-howto-aad.md)합니다.
- 개발자 포털에 대한 질문이 더 있나요? [FAQ에서 답변 찾기](developer-portal-faq.md).

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[공유 액세스 토큰을 요청]: /rest/api/apimanagement/2020-12-01/user/get-shared-access-token
[사용자를 만듭니다]: /rest/api/apimanagement/2020-12-01/user/create-or-update
[구독에 대한 REST API 호출]: /rest/api/apimanagement/2020-12-01/subscription/create-or-update
[Next steps]: #next-steps
[예제 코드]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
