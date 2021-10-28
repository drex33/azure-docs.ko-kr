---
title: Azure Active Directory 애플리케이션 프록시 CORS 문제 이해 및 해결
description: Azure Active Directory 애플리케이션 프록시의 CORS를 이해하고 CORS 문제를 식별하고 해결하는 방법을 제공합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/28/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: dc8a48c52c6312c3fcdf9e450a8aa609dc7090d7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988585"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Azure Active Directory 애플리케이션 프록시 CORS 문제 이해 및 해결

[CORS(출처 간 리소스 공유)](https://www.w3.org/TR/cors/)는 Azure Active Directory 애플리케이션 프록시를 통해 게시하는 API와 앱에 대한 문제를 일으키는 경우가 있습니다. 이 문서에서는 Azure AD 애플리케이션 프록시 CORS 문제 및 솔루션에 대해 설명합니다.

브라우저 보안은 일반적으로 웹 페이지에서 다른 도메인으로 AJAX 요청을 수행하지 못하도록 방지합니다. 이 제한은 *동일 출처 정책* 이라고 하며, 악성 사이트에서 다른 사이트의 중요한 데이터를 읽지 못하도록 차단합니다. 그러나 다른 사이트에서 내 웹 API를 호출하도록 하려는 경우도 있습니다. CORS는 서버에서 동일 출처 정책을 완화하고 출처 간 요청을 일부는 허용하고 일부는 거부할 수 있도록 하는 W3C표준입니다.

## <a name="understand-and-identify-cors-issues"></a>CORS 문제 이해 및 식별

두 URL의 체계, 호스트 및 포트가 동일하면 출처가 동일합니다([RFC 6454](https://tools.ietf.org/html/rfc6454)). 예를 들면 아래와 같습니다.

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

다음 URL은 이전 두 URL에 비해 출처가 다릅니다.

-   http:\//contoso.net - 다른 도메인
-   http:\//contoso.com:9000/foo.html - 다른 포트
-   https:\//contoso.com/foo.html - 다른 체계
-   http:\//www.contoso.com/foo.html - 다른 하위 도메인

동일 출처 정책은 올바른 액세스 제어 헤더를 사용하지 않으면 앱이 다른 출처의 리소스에 액세스하지 못하도록 합니다. CORS 헤더가 없거나 올바르지 않으면 출처 간 요청은 실패합니다. 

CORS 문제는 브라우저 디버그 도구를 사용하여 식별할 수 있습니다.

1. 브라우저를 시작하고 웹앱으로 이동합니다.
1. **F12** 키를 눌러 디버그 콘솔을 엽니다.
1. 트랜잭션을 재현하고 콘솔 메시지를 검토합니다. CORS 위반으로 인해 원본에 대한 콘솔 오류가 발생합니다.

다음 스크린샷에서 **Try It** 단추를 선택하면 Access-Control-Allow-Origin 헤더에서 https:\//corswebclient-contoso.msappproxy.net을 찾을 수 없다는 CORS 오류 메시지가 나타납니다.

![CORS 문제](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>애플리케이션 프록시의 CORS 과제

다음 예제는 일반적인 Azure AD 애플리케이션 프록시 CORS 시나리오를 보여줍니다. 내부 서버는 **CORSWebService** 웹 API 컨트롤러 및 **CORSWebService** 를 호출하는 **CORSWebClient** 를 호스트합니다. **CORSWebClient** 에서 **CORSWebService** 로의 AJAX 요청이 있습니다.

![온-프레미스 동일 출처 요청](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient 앱은 온-프레미스에서 호스트하는 경우 작동하지만, Azure AD 애플리케이션 프록시를 통해 게시될 때 로드되지 않거나 오류가 발생합니다. CORSWebClient 및 CORSWebService 앱을 애플리케이션 프록시를 통해 서로 다른 앱으로 별도로 게시한 경우 두 앱은 서로 다른 도메인에서 호스트됩니다. CORSWebClient에서 CORSWebService로의 AJAX 요청은 출처 간 요청이며 실패합니다.

![애플리케이션 프록시 CORS 요청](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>애플리케이션 프록시 CORS 문제에 대한 솔루션

앞의 CORS 문제는 몇 가지 방법 중 하나로 해결할 수 있습니다.

### <a name="option-1-set-up-a-custom-domain"></a>옵션 1: 사용자 지정 도메인 설정

Azure AD 애플리케이션 프록시 [사용자 지정 도메인](./application-proxy-configure-custom-domain.md)을 사용하면 앱 출처, 코드 또는 헤더를 변경하지 않고도 동일한 출처에서 게시할 수 있습니다. 

### <a name="option-2-publish-the-parent-directory"></a>옵션 2: 부모 디렉터리 게시

두 앱의 부모 디렉터리를 게시합니다. 이 솔루션은 웹 서버에 앱이 두 개뿐인 경우 특히 효과적입니다. 각 앱을 별도로 게시하는 대신 공통 상위 디렉터리를 게시하면 출처가 동일해집니다.

다음 예는 CORSWebClient 앱에 대한 포털 Azure AD 애플리케이션 프록시 페이지를 보여줍니다.  **내부 URL** 이 *contoso.com/CORSWebClient* 로 설정되면 앱은 *contoso.com/CORSWebService* 디렉터리에 대한 요청에 성공할 수 없습니다. 출처 간 요청이기 때문입니다. 

![개별적으로 앱 게시](./media/application-proxy-understand-cors-issues/image4.png)

대신 *CORSWebClient* 및 *CORSWebService* 디렉터리를 모두 포함하는 부모 디렉터리를 게시하도록 **내부 URL** 을 설정합니다.

![부모 디렉터리 게시](./media/application-proxy-understand-cors-issues/image5.png)

결과 앱 URL은 CORS 문제를 효과적으로 해결합니다.

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>옵션 3: HTTP 헤더 업데이트

웹 서비스에 사용자 지정 HTTP 응답 헤더를 추가하여 출처 요청과 매칭합니다. IIS(인터넷 정보 서비스)에서 실행되는 웹 사이트의 경우 IIS 관리자를 사용하여 헤더를 수정합니다.

![IIS 관리자에서 사용자 지정 응답 헤더 추가](./media/application-proxy-understand-cors-issues/image6.png)

이 수정 작업에는 코드 변경이 필요하지 않습니다. Fiddler 추적에서 확인할 수 있습니다.

**헤더 추가 게시**\
HTTP/1.1 200 OK\
Cache-Control: no-cache\
Pragma: no-cache\
Content-Type: text/plain; charset=utf-8\
Expires: -1\
Vary: Accept-Encoding\
Server: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin: https\://corswebclient-contoso.msappproxy.net**\
X-AspNet-Version: 4.0.30319\
X-Powered-By: ASP.NET\
Content-Length: 17

### <a name="option-4-modify-the-app"></a>옵션 4: 앱 수정

Access-Control-Allow-Origin 헤더를 적절한 값과 함께 추가하여 CORS를 지원하도록 앱을 변경할 수 있습니다. 헤더를 추가하는 방법은 앱의 코드 언어에 따라 달라집니다. 코드를 변경하는 것은 가장 많은 노력이 필요하기 때문에 가장 권장되지 않는 옵션입니다.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>옵션 5: 액세스 토큰의 수명 연장

앱이 인증을 위해 *login.microsoftonline.com* 으로 리디렉션되고 액세스 토큰이 만료된 경우와 같은 일부 CORS 문제는 해결할 수 없습니다. 그러면 CORS 호출이 실패합니다. 이 시나리오에 대한 해결 방법은 사용자 세션 중에 만료되지 않도록 액세스 토큰의 수명을 연장하는 것입니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Azure AD에서 구성 가능한 토큰 수명](../develop/active-directory-configurable-token-lifetimes.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
- [자습서: Azure Active Directory에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](../app-proxy/application-proxy-add-on-premises-application.md) 
- [Azure AD 애플리케이션 프록시 배포 계획](application-proxy-deployment-plan.md) 
- [Azure Active Directory 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 원격 액세스](application-proxy.md)