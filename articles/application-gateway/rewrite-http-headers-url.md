---
title: Azure Application Gateway 사용하여 HTTP 헤더 및 URL 다시 작성
description: 이 문서에는 Azure Application Gateway에서 HTTP 헤더 및 URL을 다시 쓰는 방법에 대한 개요가 제공됩니다.
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: c4e4af8fb14c48988a593261365dcfde6c7a0657
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577282"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Application Gateway를 사용하여 HTTP 헤더 및 URL 다시 쓰기

Application Gateway를 사용하면 선택한 요청 및 응답 내용을 다시 쓸 수 있습니다. 이 기능을 사용하면 URL, 쿼리 문자열 매개 변수를 변환하고 요청 및 응답 헤더를 수정할 수 있습니다. 특정 조건이 충족되는 경우에만 URL 또는 지정된 헤더를 다시 쓸 수 있도록 조건을 추가할 수도 있습니다. 이러한 조건은 요청 및 응답 정보를 기반으로 합니다.

>[!NOTE]
>HTTP 헤더 및 URL 다시 쓰기 기능은 [Application Gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md)에서만 사용할 수 있습니다.

## <a name="rewrite-types-supported"></a>지원되는 다시 쓰기 형식

### <a name="request-and-response-headers"></a>요청 및 응답 헤더

HTTP 헤더를 통해 클라이언트와 서버는 요청 또는 응답과 함께 추가 정보를 전달할 수 있습니다. 헤더를 다시 쓰면 HSTS/X-XSS-Protection과 같은 보안 관련 헤더 필드 추가, 중요한 정보를 노출할 수 있는 응답 헤더 필드 제거, X-Forwarded-For 헤더에서 포트 정보 제거와 같은 중요한 작업을 수행할 수 있습니다.

Application Gateway는 요청 및 응답 패킷이 클라이언트와 백 엔드 풀 사이를 이동하는 동안 HTTP 요청 및 응답 헤더를 추가, 제거 또는 업데이트할 수 있습니다.

Azure Portal을 사용하여 Application Gateway로 요청 및 응답 헤더를 다시 쓰는 방법을 알아보려면 [여기](rewrite-url-portal.md)를 참조하세요.

![img](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**지원되는 헤더**

연결 및 업그레이드 헤더를 제외한 요청 및 응답의 모든 헤더를 다시 쓸 수 있습니다. 애플리케이션 게이트웨이를 사용하여 사용자 지정 헤더를 만들고 이를 통해 라우팅되는 요청 및 응답에 추가할 수도 있습니다.

### <a name="url-path-and-query-string"></a>URL 경로 및 쿼리 문자열

Application Gateway의 URL 다시 쓰기 기능을 사용하여 다음을 수행할 수 있습니다.

* 요청 URL의 호스트 이름, 경로 및 쿼리 문자열을 다시 씁니다. 

* 리스너에서 모든 요청의 URL을 다시 쓰거나 설정한 조건 중 하나 이상과 일치하는 요청만 다시 쓰도록 선택합니다. 이러한 조건은 요청 및 응답 속성(요청, 헤더, 응답 헤더, 서버 변수)을 기반으로 합니다.

* 원래 URL 또는 다시 쓴 URL을 기반으로 요청을 라우팅하도록 선택합니다(백 엔드 풀 선택).

Azure Portal을 사용하여 Application Gateway로 URL을 다시 쓰는 방법을 알아보려면 [여기](rewrite-url-portal.md)를 참조하십시오.

![Application Gateway를 사용하여 URL을 다시 쓰는 프로세스를 설명하는 다이어그램입니다.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>다시 쓰기 작업

다시 쓰기 작업을 사용하여 다시 쓰려는 URL, 요청 헤더 또는 응답 헤더와 이러한 항목에 다시 쓰려는 새 값을 지정합니다. URL이나 신규 또는 기존 헤더의 값은 다음과 같은 형식으로 설정할 수 있습니다.

* 텍스트
* 요청 헤더. 요청 헤더를 지정하려면 {http_req_ *headerName*} 구문을 사용해야 합니다.
* 응답 헤더입니다. 응답 헤더를 지정하려면 {http_resp_ *headerName*} 구문을 사용해야 합니다.
* 서버 변수. 서버 변수를 지정하려면 {var_ *serverVariable*} 구문을 사용해야 합니다. 지원되는 서버 변수 목록을 참조하세요.
* 텍스트, 요청 헤더, 응답 헤더 및 서버 변수의 조합 

## <a name="rewrite-conditions"></a>다시 쓰기 조건

다시 쓰기 조건(선택적 구성)을 사용하면 HTTP(S) 요청 및 응답의 내용을 평가하고 하나 이상의 조건이 충족되는 경우에만 다시 쓰기를 수행할 수 있습니다. 애플리케이션 게이트웨이는 다음 유형의 변수를 사용하여 요청 및 응답의 콘텐츠를 평가합니다.

* 요청의 HTTP 헤더
* 응답의 HTTP 헤더
* Application Gateway 서버 변수

조건을 사용하여 지정된 변수가 있는지, 지정된 변수가 특정 값과 일치하는지 또는 지정된 변수가 특정 패턴과 일치하는지 여부를 평가할 수 있습니다. 


### <a name="pattern-matching"></a>패턴 일치 

Application Gateway는 조건에서 패턴 일치에 정규식을 사용합니다. [PCRE(Perl Compatible Regular Expressions) 라이브러리](https://www.pcre.org/)를 사용하여 조건에서 정규식 패턴 일치를 설정할 수 있습니다. 정규식 구문에 대해 알아보려면 [Perl 정규식 기본 페이지](https://perldoc.perl.org/perlre.html)를 참조하세요.

### <a name="capturing"></a>캡처 중

나중에 사용하기 위해 하위 문자열을 캡처하려면 조건 정규식 정의에서 일치하는 하위 패턴을 괄호로 묶습니다. 첫 번째 괄호 쌍은 하위 문자열을 1에 저장하고 두 번째 쌍은 2에 저장하는 식입니다. 괄호는 원하는 만큼 사용할 수 있습니다. Perl은 캡처된 문자열을 표현할 수 있도록 번호가 매겨진 변수를 더 많이 정의하고 있습니다. 다음은 [참조](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm)의 몇 가지 예입니다. 

*  /(\d)(\d)/ # 두 숫자를 일치시켜 그룹 1과 2로 캡처합니다. 

* /(\d+)/ # 하나 이상의 숫자를 일치시켜 모두 그룹 1로 캡처합니다. 

* /(\d)+/ # 숫자를 한 번 이상 일치시켜 마지막 숫자를 그룹 1로 캡처합니다.

캡처한 후에는 다음 형식을 사용하여 작업 세트에서 참조할 수 있습니다.

* 요청 헤더 캡처의 경우 {http_req_headerName_groupNumber}를 사용해야 합니다. 예: {http_req_User-Agent_1} 또는 {http_req_User-Agent_2}
* 응답 헤더 캡처의 경우 {http_resp_headerName_groupNumber}를 사용해야 합니다. 예: {http_resp_Location_1} 또는 {http_resp_Location_2}
* 서버 변수의 경우 {var_serverVariableName_groupNumber}를 사용해야 합니다. 예: {var_uri_path_1} 또는 {var_uri_path_2}

전체 값을 사용하려면 숫자를 언급하지 않아야 합니다. {http_req_headerName} 등의 형식을 groupNumber없이 사용하면 됩니다.

## <a name="server-variables"></a>서버 변수

Application Gateway 서버 변수를 사용하여 서버, 클라이언트와의 연결, 연결에 대한 현재 요청에 관련된 유용한 정보를 저장합니다. 저장된 정보의 예로는 클라이언트의 IP 주소 및 웹 브라우저 유형이 있습니다. 서버 변수는 동적으로(예: 새 페이지가 로드되거나 양식이 게시될 때) 변경됩니다. 이러한 변수를 사용하여 다시 쓰기 조건을 평가하고 헤더를 다시 쓸 수 있습니다. 서버 변수 값을 사용하여 헤더를 다시 쓰려면 {var_ *serverVariableName*} 구문에서 이러한 변수를 지정해야 합니다.

애플리케이션 게이트웨이는 다음 서버 변수를 지원합니다.

|   변수 이름    |                   Description                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | `client_ip` 변수(이 표의 뒷부분에 있는 설명 참조)가 IP1, IP2, IP3 등의 형식으로 추가된 X-Forwarded-For 클라이언트 요청 헤더 필드입니다. X-Forwarded-For 필드가 클라이언트 요청 헤더에 없으면 `add_x_forwarded_for_proxy` 변수는 `$client_ip` 변수와 같습니다.   이 변수는 Application Gateway에서 설정한 X-Forwarded-For 헤더를(헤더에 포트 정보 없이 IP 주소만 포함되도록) 다시 쓰려는 경우 특히 유용합니다. |
| ciphers_supported         | 클라이언트에서 지원하는 암호화 목록입니다.               |
| ciphers_used              | 설정된 TLS 연결에 사용되는 암호화 문자열입니다. |
| client_ip                 | 애플리케이션 게이트웨이가 요청을 받은 클라이언트의 IP 주소입니다. 애플리케이션 게이트웨이와 원래 클라이언트 앞에 역방향 프록시가 있는 경우 `client_ip`는 역방향 프록시의 IP 주소를 반환합니다. |
| client_port               | 클라이언트 포트입니다.                                             |
| client_tcp_rtt            | 클라이언트 TCP 연결에 대한 정보입니다. TCP_INFO 소켓 옵션을 지원하는 시스템에서 사용할 수 있습니다. |
| client_user               | HTTP 인증을 사용하는 경우 인증을 위해 제공된 사용자 이름입니다. |
| 호스트                      | 이 우선 순위대로: 요청 줄의 호스트 이름, 호스트 요청 헤더 필드의 호스트 이름 또는 요청과 일치하는 서버 이름. 예: `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` 요청에서 호스트 값은 `contoso.com`입니다. |
| cookie_ *name*             | *name* 쿠키입니다.                                           |
| http_method               | URL 요청을 만드는 데 사용되는 메서드입니다. 예: GET   또는 POST. |
| http_status               | 세션 상태입니다. 예: 200, 400 또는 403.           |
| http_version              | 요청 프로토콜입니다. 일반적으로 HTTP/1.0, HTTP/1.1 또는 HTTP/2.0입니다. |
| query_string              | 요청된 URL에서 "?" 뒤에 오는 변수/값 쌍 목록입니다. 예: `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` 요청에서, query_string 값은 `id=123&title=fabrikam`입니다. |
| received_bytes            | 요청의 길이(요청 줄, 헤더 및 요청 본문 포함)입니다. |
| request_query             | 요청 줄의 인수입니다.                           |
| request_scheme            | 요청 스키마 http 또는 https입니다.                           |
| request_uri               | 전체 원래 요청 URI(인수 포함)입니다. 예: `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` 요청에서 request_uri 값은 `/article.aspx?id=123&title=fabrikam`입니다. |
| sent_bytes                | 클라이언트에 보낸 바이트 수입니다.                        |
| server_port               | 요청을 수락한 서버의 포트입니다.              |
| ssl_connection_protocol   | 설정된 TLS 연결의 프로토콜입니다.               |
| ssl_enabled               | 연결이 TLS 모드에서 작동하는 경우 "On"입니다. 그렇지 않으면 빈 문자열입니다. |
| uri_path                  | 웹 클라이언트가 액세스하려는 호스트의 특정 리소스를 식별합니다. 인수가 없는 요청 URI의 일부입니다. 예: `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` 요청에서 uri_path 값은 `/article.aspx`입니다. |

### <a name="mutual-authentication-server-variables-preview"></a>상호 인증 서버 변수(미리 보기)

Application Gateway는 상호 인증 시나리오에 대해 다음과 같은 서버 변수를 지원합니다. 이러한 서버 변수는 다른 서버 변수와 함께 위와 동일한 방식으로 사용합니다. 

|   변수 이름    |                   Description                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | 설정된 SSL 연결을 위한 PEM 형식의 클라이언트 인증서입니다. |
| client_certificate_end_date| 클라이언트 인증서의 종료 날짜입니다. |
| client_certificate_fingerprint| 설정된 SSL 연결에 대한 클라이언트 인증서의 SHA1 지문입니다. |
| client_certificate_issuer | 설정된 SSL 연결에 대한 클라이언트 인증서의 "발급자 DN" 문자열입니다. |
| client_certificate_serial | 설정된 SSL 연결에 대한 클라이언트 인증서의 일련 번호입니다.  |
| client_certificate_start_date| 클라이언트 인증서의 시작 날짜입니다. |
| client_certificate_subject| 설정된 SSL 연결에 대한 클라이언트 인증서의 "주체 DN" 문자열입니다. |
| client_certificate_verification| 클라이언트 인증서 확인의 결과: *SUCCESS*, *FAILED:\<reason\>* 또는 인증서가 없는 경우 *NONE* 입니다. | 

## <a name="rewrite-configuration"></a>구성 다시 쓰기

다시 쓰기 규칙을 구성하려면 다시 쓰기 규칙 집합을 만들고 여기에 다시 쓰기 규칙 구성을 추가해야 합니다.

다시 쓰기 규칙 집합에는 다음이 포함됩니다.

* **라우팅 규칙 연결 요청:** 다시 쓰기 구성은 라우팅 규칙을 통해 원본 수신기에 연결됩니다. 기본 라우팅 규칙을 사용하는 경우 다시 쓰기 구성이 원본 수신기와 연결되며 전역 헤더 다시 쓰기입니다. 경로 기반 라우팅 규칙을 사용하는 경우 다시 쓰기 구성이 URL 경로 맵에 정의됩니다. 이 경우 사이트의 특정 경로 영역에만 적용됩니다. 다시 쓰기 집합을 여러 개 만들고 각 다시 쓰기 집합을 여러 수신기에 적용할 수 있습니다. 단, 특정 수신기에는 다시 쓰기 집합을 하나만 적용할 수 있습니다.

* **다시 쓰기 조건**: 선택적 구성입니다. 다시 쓰기 조건은 HTTP(S) 요청 및 응답의 콘텐츠를 평가합니다. HTTP(S) 요청 또는 응답이 다시 쓰기 조건과 일치하면 다시 쓰기 작업이 수행됩니다. 작업에 둘 이상의 조건을 연결할 경우 모든 조건이 충족되는 경우에만 작업이 수행됩니다. 즉, 작업은 논리적 AND 작업입니다.

* **다시 쓰기 유형**: 3가지 유형의 다시 쓰기를 사용할 수 있습니다.
   * 요청 헤더 다시 쓰기 
   * 응답 헤더 다시 쓰기
   * URL 구성 요소 다시 쓰기
      * **URL 경로**: 경로를 다시 쓸 값입니다. 
      * **URL 쿼리 문자열**: 쿼리 문자열을 다시 쓸 값입니다. 
      * **경로 맵 다시 평가**: URL 경로 맵을 다시 평가할지 여부를 결정하는 데 사용됩니다. 선택하지 않은 상태로 유지하면 원래 URL 경로가 URL 경로 맵의 경로 패턴과 일치하는 데 사용됩니다. true로 설정하면 URL 경로 맵이 다시 평가되어 다시 쓴 경로와 일치하는지 확인합니다. 이러한 전환이 가능하도록 설정하면 다시 쓴 후 요청을 다른 백 엔드 풀로 라우팅하는 데 도움이 됩니다.

## <a name="rewrite-configuration-common-pitfalls"></a>다시 쓰기 구성의 일반적인 단점

* 기본 요청 라우팅 규칙에는 '경로 맵 다시 평가'를 사용하도록 설정할 수 없습니다. 기본 라우팅 규칙에 대한 무한 평가 루프를 방지하기 위해서 입니다.

* 경로 기반 라우팅 규칙에 대한 무한 평가 루프를 방지하기 위해서는 경로 기반 라우팅 규칙에 대해 '경로 맵 다시 평가'를 사용하도록 설정하지 않은 다시 쓰기 규칙 또는 조건부 다시 쓰기 규칙이 하나 이상 있어야 합니다.

* 클라이언트 입력을 기반으로 루프가 동적으로 생성되는 경우 들어오는 요청은 500 오류 코드로 종료됩니다. 이러한 시나리오에서 Application Gateway는 성능 저하 없이 계속해서 다른 요청을 처리합니다.

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>Web Application Firewall(WAF_v2 SKU)에서 URL 다시 쓰기 또는 호스트 헤더 다시 쓰기 사용

URL 다시 쓰기 또는 호스트 헤더 다시 쓰기를 구성하는 경우 요청 헤더 또는 URL 매개 변수를 수정한 후(다시 쓴 후) WAF 평가가 수행됩니다. 또한 Application Gateway에서 URL 다시 쓰기 또는 호스트 헤더 다시 쓰기 구성을 제거하면 헤더 다시 쓰기 전(pre-rewrite)에 WAF 평가가 수행됩니다. 이 순서를 통해 백 엔드 풀에서 받은 최종 요청에 WAF 규칙이 적용될 수 있습니다.

예를 들어 헤더 `"Accept" : "text/html"`에 대해 다음과 같은 헤더 다시 쓰기 규칙이 있다고 가정합니다. 헤더 `"Accept"`의 값이 `"text/html"`과 같으면 값을 `"image/png"`로 다시 씁니다.

여기서 헤더 다시 쓰기만 구성하면 `"Accept" : "text/html"`에 대해 WAF 평가가 수행됩니다. 하지만 URL 다시 쓰기 또는 호스트 헤더 다시 쓰기를 구성하면, `"Accept" : "image/png"`에 대해 WAF 평가가 수행됩니다.

>[!NOTE]
> URL 다시 쓰기 작업으로 인해 WAF Application Gateway의 CPU 사용률이 약간 증가할 것으로 예상됩니다. WAF Application Gateway에서 URL 다시 쓰기 규칙을 사용하도록 설정한 후에는 짧은 기간 동안 [CPU 사용률 메트릭](high-traffic-support.md)을 모니터링하는 것이 좋습니다.

### <a name="common-scenarios-for-header-rewrite"></a>헤더 다시 쓰기에 대한 일반적인 시나리오

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>X-Forwarded-For 헤더에서 포트 정보 제거

Application Gateway는 백 엔드에 요청을 전달하기 전에 모든 요청에 X-Forwarded-For 헤더를 삽입합니다. 이 헤더는 IP 포트를 쉼표로 구분한 목록입니다. 백 엔드 서버에는 IP 주소만 포함된 헤더가 필요한 시나리오가 있을 수 있습니다. 헤더 다시 쓰기를 사용하여 X-Forwarded-For 헤더에서 포트 정보를 제거할 수 있습니다. 이 작업을 수행하는 한 가지 방법은 헤더를 add_x_forwarded_for_proxy 서버 변수로 설정하는 것입니다. 또는 client_ip 변수를 사용할 수도 있습니다.

![포트 제거](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>리디렉션 URL 수정

백 엔드 애플리케이션이 리디렉션 응답을 전송할 때 클라이언트를 백 엔드 애플리케이션에서 지정한 것과 다른 URL로 리디렉션할 수 있습니다. 예를 들어, 앱 서비스가 애플리케이션 게이트웨이 뒤에서 호스트되고 클라이언트가 상대 경로에 대한 리디렉션을 수행해야 하는 경우 이 작업을 수행할 수 있습니다. (예를 들어 contoso.azurewebsites.net/path1에서 contoso.azurewebsites.net/path2로 리디렉션)

App Service는 다중 테넌트 서비스이기 때문에, 요청의 호스트 헤더를 사용하여 요청을 올바른 엔드포인트로 라우팅합니다. App Services의 기본 도메인 이름은 \*.azurewebsites.net(예: contoso.azurewebsites.net)이며, 이것은 애플리케이션 게이트웨이의 도메인 이름(예: contoso.com)과 다릅니다. 클라이언트의 원래 요청에는 애플리케이션 게이트웨이의 도메인 이름(contoso.com)이 호스트 이름으로 있으므로 애플리케이션 게이트웨이는 호스트 이름을 contoso.azurewebsites.net으로 변경합니다. App Service가 올바른 엔드포인트로 요청을 라우팅할 수 있도록 이렇게 변경합니다.

앱 서비스가 리디렉션 응답을 보낼 때는 응답의 위치 헤더에 애플리케이션 게이트웨이에서 수신한 요청의 호스트 이름과 동일한 호스트 이름을 사용합니다. 따라서 클라이언트는 애플리케이션 게이트웨이(`contoso.com/path2`)를 통과하지 않고 `contoso.azurewebsites.net/path2`에 직접 요청을 보냅니다. 애플리케이션 게이트웨이를 우회하는 것은 바람직하지 않습니다.

이 문제는 위치 헤더의 호스트 이름을 애플리케이션 게이트웨이의 도메인 이름으로 설정하여 해결할 수 있습니다.

호스트 이름을 바꾸는 단계는 다음과 같습니다.

1. 응답의 위치 헤더에 azurewebsites.net이 포함되어 있는지 평가하는 조건으로 다시 쓰기 규칙을 만듭니다. `(https?):\/\/.*azurewebsites\.net(.*)$` 패턴을 입력합니다.
2. 애플리케이션 게이트웨이의 호스트 이름을 포함하도록 위치 헤더를 다시 쓰는 작업을 수행합니다. 이 작업은 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}`를 헤더 값으로 입력하여 수행합니다. 또는 서버 변수를 `host`를 사용하여 원래 요청과 일치하도록 호스트 이름을 설정할 수도 있습니다.

![위치 헤더 수정](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>취약성을 방지하기 위해 보안 HTTP 헤더 구현

애플리케이션 응답에 필요한 헤더를 구현하여 여러 보안 취약점을 해결할 수 있습니다. 이러한 보안 헤더에는 X-XSS-Protection, Strict-Transport-Security 및 Content-Security-Policy가 포함됩니다. Application Gateway를 사용하여 모든 응답에 대해 이러한 헤더를 설정할 수 있습니다.

![보안 헤더](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>원치 않는 헤더 삭제

HTTP 응답에서 중요한 정보를 나타내는 헤더는 제거하는 것이 좋습니다. 예를 들어 백 엔드 서버 이름, 운영 체제 또는 라이브러리 세부 정보와 같은 정보는 제거하는 것이 좋습니다. 애플리케이션 게이트웨이를 사용하여 이러한 헤더를 제거할 수 있습니다.

![헤더 삭제](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>헤더가 있는지 확인

헤더 또는 서버 변수가 있는지 HTTP 요청 또는 응답 헤더를 평가할 수 있습니다. 이 평가는 특정 헤더가 있는 경우에만 헤더 다시 쓰기를 수행하려는 경우 유용합니다.

![헤더가 있는지 확인하기](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>URL 다시 쓰기에 대한 일반적인 시나리오

#### <a name="parameter-based-path-selection"></a>매개 변수 기반 경로 선택

요청의 헤더 값, URL의 일부 또는 쿼리 문자열을 기반으로 백 엔드 풀을 선택하는 시나리오를 수행하려면 URL 다시 쓰기 기능과 경로 기반 라우팅의 조합을 사용합니다. 예를 들어 쇼핑 웹 사이트가 있고 제품 범주가 URL의 쿼리 문자열로 전달되는 경우 쿼리 문자열을 기반으로 요청을 백 엔드로 라우팅하려면 다음을 수행합니다.

**1단계:** 아래 이미지와 같이 경로 맵을 만듭니다.

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="URL 다시 쓰기 시나리오 1-1.":::

**2단계(a):** 다시 쓰기 규칙 3개를 포함하는 다시 쓰기 집합을 만듭니다. 

* 첫 번째 규칙에는 *category=shoes* 에 대한 *query_string* 변수를 확인하고, URL 경로를 /*listing1* 로 다시 쓰고, **경로 맵 다시 평가** 를 사용하도록 설정된 조건이 있습니다.

* 두 번째 규칙에는 *category=bags* 에 대한 *query_string* 변수를 확인하고, URL 경로를 /*listing2* 로 다시 쓰고, **경로 맵 다시 평가** 를 사용하도록 설정된 조건이 있습니다.

* 세 번째 규칙에는 *category=accessories* 에 대한 *query_string* 변수를 확인하고, URL 경로를 /*listing3* 으로 다시 쓰고, **경로 맵 다시 평가** 를 사용하도록 설정된 조건이 있습니다.

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="URL 다시 쓰기 시나리오 1-2.":::

 

**2단계(b):** 이러한 다시 쓰기 집합을 위의 경로 기반 규칙의 기본 경로와 연결합니다.

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="URL 다시 쓰기 시나리오 1-3.":::

이제 사용자가 *contoso.com/listing?category=any* 를 요청하면 경로 맵(/listing1, /listing2, /listing3)의 경로 패턴이 모두 일치하지 않으므로 기본 경로가 일치됩니다. 위의 다시 쓰기 집합을 이 경로와 연결했으므로 다시 쓰기 집합이 평가됩니다. 쿼리 문자열은 다시 쓰기 집합에 있는 3개 다시 쓰기 규칙 조건 중 무엇과도 일치하지 않으므로 다시 쓰기 작업이 수행되지 않으며 따라서 요청은 기본 경로(*Genericlist*)와 연결된 백 엔드로 변경되지 않고 라우팅됩니다.

사용자가 *contoso.com/listing?category=shoes* 를 요청하면 다시 기본 경로가 일치됩니다. 하지만 이 경우는 첫 번째 규칙의 조건이 일치하므로 해당 조건과 연결된 작업이 실행되어 URL 경로를 /*listing1* 로 다시 쓰고 경로 맵이 다시 평가됩니다. 경로 맵이 다시 평가되면 요청은 패턴 */listing1* 과 연결된 경로와 일치되고 요청은 이 패턴과 연결된 백 엔드인 ShoesListBackendPool로 라우팅됩니다.

>[!NOTE]
>이 시나리오는 정의된 조건에 따라 모든 헤더 또는 쿠키 값, URL 경로, 쿼리 문자열 또는 서버 변수로 확장될 수 있으며, 기본적으로 이러한 조건에 따라 요청을 라우팅할 수 있습니다.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>URL을 기반으로 쿼리 문자열 매개 변수 다시 쓰기

사용자가 볼 수 있는 링크는 간단하고 알기 쉬워야 하지만 백 엔드 서버는 올바른 콘텐츠를 표시하기 위해 쿼리 문자열 매개 변수가 필요한 쇼핑 웹 사이트의 시나리오를 고려해보겠습니다.

이 경우 Application Gateway는 URL에서 매개 변수를 캡처하고 URL에서 쿼리 문자열 키-값 쌍을 추가할 수 있습니다. 예를 들어, 사용자가 `https://www.contoso.com/fashion/shirts`를 `https://www.contoso.com/buy.aspx?category=fashion&product=shirts`로 다시 쓰려는 경우, 다음 URL 다시 쓰기 구성을 통해 수행할 수 있습니다.

**조건** - 서버 변수 `uri_path`가 `/(.+)/(.+)` 패턴과 같은 경우

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="URL 다시 쓰기 시나리오 2-1.":::

**작업** - URL 경로는 `buy.aspx`로, 쿼리 문자열은 `category={var_uri_path_1}&product={var_uri_path_2}`로 설정합니다.

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="URL 다시 쓰기 시나리오 2-2.":::

위에서 설명한 시나리오를 수행하는 단계별 가이드는 [Azure Portal을 사용하여 Application Gateway로 URL 다시 쓰기](rewrite-url-portal.md)를 참조하세요.

### <a name="url-rewrite-vs-url-redirect"></a>URL 다시 쓰기와 URL 리디렉션

URL 다시 쓰기의 경우 요청이 백 엔드로 전송되기 전에 Application Gateway가 URL을 다시 씁니다. 변경 내용이 사용자에게 숨겨져 있기 때문에 브라우저에서 사용자에게 표시되는 내용은 변경되지 않습니다.

URL 리디렉션의 경우 Application Gateway는 새 URL을 사용하여 클라이언트에 리디렉션 응답을 보냅니다. 그러면 클라이언트는 해당 요청을 리디렉션에 제공된 새 URL로 다시 보내야 합니다. 브라우저에서 사용자에게 표시되는 URL이 새 URL로 업데이트됩니다.

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="다시 쓰기 및 리디렉션.":::

## <a name="limitations"></a>제한 사항

- 응답에 이름이 같은 헤더가 둘 이상 있는 경우 이들 헤더 중 하나의 값을 다시 쓰면 응답의 다른 헤더가 삭제됩니다. 이런 경우는 일반적으로 Set-Cookie 헤더에서 발생하는데 응답에 Set-Cookie 헤더가 둘 이상 있을 수 있기 때문입니다. 이러한 시나리오 중 하나는 애플리케이션 게이트웨이와 함께 App Service를 사용하고 애플리케이션 게이트웨이에서 쿠키 기반 세션 선호도를 구성하는 경우입니다. 이 경우 응답에는 두 개의 Set-Cookie 헤더가 포함되며, 그 중 하나는 App Service에서 사용되고(예: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net`)다른 하나는 애플리케이션 게이트웨이 선호도에 사용됩니다(예: `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`). 이 시나리오에서 Set-Cookie 헤더 중 하나를 다시 쓰면 응답에서 다른 Set-Cookie 헤더가 제거될 수 있습니다.
- 애플리케이션 게이트웨이가 요청을 리디렉션하거나 사용자 지정 오류 페이지를 표시하도록 구성된 경우에는 다시 쓰기가 지원되지 않습니다.
- 헤더 이름은 [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)에 정의된 대로 영숫자 문자 및 특정 기호를 포함할 수 있습니다. 현재는 헤더 이름에 밑줄(\_) 특수 문자가 지원되지 않습니다.
- 연결 및 업그레이드 헤더는 다시 쓸 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 Application Gateway로 HTTP 헤더를 다시 쓰는 방법 알아보기](rewrite-http-headers-portal.md)
- [Azure Portal을 사용하여 Application Gateway로 URL을 다시 쓰는 방법 알아보기](rewrite-url-portal.md)
