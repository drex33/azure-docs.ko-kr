---
title: Application Gateway와 통합
description: 이 엔드투엔드 연습에서 애플리케이션 게이트웨이와 ILB App Service Environment의 앱을 통합하는 방법을 알아봅니다.
author: madsd
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 10/12/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: f52f984b1e72d685dae46002e9ae4bac543c068a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447699"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>ILB App Service Environment와 Azure Application Gateway 통합 #

[App Service Environment][AppServiceEnvironmentoverview]는 고객의 Azure 가상 네트워크 서브넷에 Azure App Service를 배포한 환경입니다. 앱 액세스를 위해 외부 또는 내부 엔드포인트를 통해 배포할 수 있습니다. 내부 엔드포인트가 있는 App Service 환경의 배포를 ILB(내부 부하 분산 App Service 환경)라고 합니다.

웹 애플리케이션 방화벽을 통해 SQL 삽입, 사이트 간 스크립팅, 맬웨어 업로드 및 애플리케이션 DDoS와 기타 공격을 차단하기 위해 인바운드 웹 트래픽을 검사하여 웹 애플리케이션을 보호할 수 있습니다. Azure Marketplace WAF 디바이스를 얻거나 [Azure Application Gateway][appgw]사용할 수 있습니다.

Azure Application Gateway는 7계층 부하 분산, TLS/SSL 오프로딩, WAF(웹 애플리케이션 방화벽) 보호를 제공하는 가상 어플라이언스입니다. 공용 IP 주소에서 수신 대기하고, 트래픽을 애플리케이션 엔드포인트로 라우팅할 수 있습니다. 다음 정보는 WAF 구성 애플리케이션 게이트웨이를 ILB App Service 환경의 앱과 통합하는 방법에 대해 설명합니다.  

ILB App Service 환경과 애플리케이션 게이트웨이의 통합은 앱 수준에 있습니다. ILB App Service 환경에서 애플리케이션 게이트웨이를 구성하는 경우 ILB App Service 환경의 특정 앱에 대해 이 작업을 수행합니다. 이 기술을 사용하면 단일 ILB App Service 환경에서 보안 다중테넌트 애플리케이션을 호스팅할 수 있습니다.  

:::image type="content" source="./media/integrate-with-application-gateway/appgw-highlevel.png" alt-text="상위 수준 통합 다이어그램의 스크린샷":::

이 연습에서는 다음을 수행합니다.

* Azure Application Gateway를 만듭니다.
* ILB App Service 환경에서 앱을 가리키도록 애플리케이션 게이트웨이를 구성합니다.
* 애플리케이션 게이트웨이를 가리키는 공용 DNS 호스트 이름을 편집합니다.

## <a name="prerequisites"></a>필수 구성 요소

ILB App Service 환경과 애플리케이션 게이트웨이를 통합하려면 다음이 필요합니다.

* ILB App Service 환경.
* ILB App Service 환경에 대한 프라이빗 DNS 영역입니다.
* ILB App Service 환경에서 실행되는 앱
* 나중에 애플리케이션 게이트웨이를 가리키는 데 사용되는 공용 DNS 이름입니다.
* 애플리케이션 게이트웨이에 TLS/SSL 암호화를 사용해야 하는 경우 애플리케이션 게이트웨이에 바인딩하는 데 사용되는 유효한 공용 인증서가 필요합니다.

### <a name="ilb-app-service-environment"></a>ILB App Service 환경

ILB App Service 환경을 만드는 방법에 대한 자세한 내용은 [Azure Portal ASE 만들기][creation] 및 [ARM을 사용하여 ASE 만들기를 참조하세요.][createfromtemplate]

* ILB ASE를 만든 후 기본 도메인은 `<YourAseName>.appserviceenvironment.net` 입니다.

    :::image type="content" source="./media/integrate-with-application-gateway/ilb-ase.png" alt-text="ILB ASE 개요 스크린샷":::

* 내부 부하 분산기는 인바운드 액세스를 위해 프로비전됩니다. ASE 설정 IP 주소에서 인바운드 주소를 확인할 수 있습니다. 나중에 이 IP 주소에 매핑된 프라이빗 DNS 영역을 만들 수 있습니다.

    :::image type="content" source="./media/integrate-with-application-gateway/ip-addresses.png" alt-text="ILB ASE IP 주소 설정에서 인바운드 주소를 얻는 스크린샷.":::

### <a name="a-private-dns-zone"></a>프라이빗 DNS 영역

내부 이름 확인에 [프라이빗 DNS 영역이][privatednszone] 필요합니다. 다음 표에 표시된 레코드 집합을 사용하여 ASE 이름을 사용하여 만듭니다(지침은 [빠른 시작 - Azure Portal 사용하여 Azure 프라이빗 DNS 영역 만들기][createprivatednszone]참조).

| Name  | Type | 값               |
| ----- | ---- | ------------------- |
| *     | A    | ASE 인바운드 주소 |
| @     | A    | ASE 인바운드 주소 |
| @     | SOA  | ASE DNS 이름        |
| *.scm | A    | ASE 인바운드 주소 |

### <a name="app-service-on-ilb-ase"></a>ILB ASE에서 App Service

ILB ASE에서 App Service 계획 및 앱을 만들어야 합니다. 포털에서 앱을 만들 때 지역 으로 ILB **ASE를** 선택합니다.

### <a name="a-public-dns-name-to-the-application-gateway"></a>애플리케이션 게이트웨이에 대한 공용 DNS 이름

인터넷에서 애플리케이션 게이트웨이에 연결하려면 라우팅 가능한 도메인 이름이 필요합니다. 이 경우 라우팅 가능한 도메인 이름을 사용하고 이 도메인 이름이 `asabuludemo.com` 인 App Service 연결하려고 계획했습니다. `app.asabuludemo.com` 이 앱 도메인 이름에 매핑된 IP 주소는 애플리케이션 게이트웨이를 만든 후 공용 IP로 설정해야 합니다.
애플리케이션 게이트웨이에 매핑된 공용 도메인을 사용하면 App Service 사용자 지정 도메인을 구성할 필요가 없습니다. App Service 도메인 을 통해 사용자 지정 도메인 이름을 구입할 수 [있습니다.](../manage-custom-dns-buy-domain.md#buy-an-app-service-domain) 

### <a name="a-valid-public-certificate"></a>유효한 공용 인증서

보안 향상을 위해 세션 암호화를 위해 TLS/SSL 인증서를 바인딩하는 것이 좋습니다. TLS/SSL 인증서를 애플리케이션 게이트웨이에 바인딩하려면 다음 정보가 있는 유효한 공용 인증서가 필요합니다. [App Service 인증서를](../configure-ssl-certificate.md#start-certificate-order)사용하면 TLS/SSL 인증서를 구입하고 .pfx 형식으로 내보낼 수 있습니다.

| Name  | 값               | Description|
| ----- | ------------------- |------------|
| **일반 이름** |`<yourappname>.<yourdomainname>`, 예: `app.asabuludemo.com`  <br/> 또는 `*.<yourdomainname>` 입니다. 예를 들면 다음과 같습니다. `*.asabuludemo.com` | 애플리케이션 게이트웨이에 대한 표준 인증서 또는 [와일드카드 인증서](https://wikipedia.org/wiki/Wildcard_certificate)|
| **주체 대체 이름** | `<yourappname>.scm.<yourdomainname>`, 예: `app.scm.asabuludemo.com`  <br/>또는 `*.scm.<yourdomainname>` 입니다. 예를 들면 다음과 같습니다. `*.scm.asabuludemo.com` |App Service kudu 서비스에 연결할 수 있는 SAN입니다. App Service kudu 서비스를 인터넷에 게시하지 않으려면 선택적 설정입니다.|

인증서 파일에는 프라이빗 키가 있어야 하고 .pfx 형식으로 저장해야 합니다. 나중에 애플리케이션 게이트웨이로 가져옵니다.

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

기본 애플리케이션 게이트웨이 만들기는 [자습서: Azure Portal 사용하여 Web Application Firewall 사용하여 애플리케이션 게이트웨이 만들기를][Tutorial: Create an application gateway with a Web Application Firewall using the Azure portal]참조하세요.

이 자습서에서는 Azure Portal 사용하여 ILB App Service 환경에서 애플리케이션 게이트웨이를 만듭니다.

Azure Portal **새** 네트워크 Application Gateway 선택하여  >    >   애플리케이션 게이트웨이를 만듭니다.

1. 기본 사항 설정

    **계층** 드롭다운 목록에서 표준 V2 또는 **WAF V2를** 선택하여 애플리케이션 게이트웨이에서 **WAF** 기능을 사용하도록 설정할 수 있습니다.  

2. 프런트 엔드 설정

    프런트 엔드 IP 주소 유형을 **공용**, **프라이빗** 또는 **둘 다로** 선택합니다. **를 프라이빗** 또는 **둘 다로** 설정한 경우 애플리케이션 게이트웨이 서브넷 범위에서 고정 IP 주소를 할당해야 합니다. 이 경우 공용 엔드포인트에 대해서만 공용 IP로 설정합니다.
    
    * 공용 IP 주소 - 애플리케이션 게이트웨이 공용 액세스에 대한 공용 IP 주소를 연결해야 합니다. 이 IP 주소를 기록합니다. 나중에 DNS 서비스에 레코드를 추가해야 합니다.
    
        :::image type="content" source="./media/integrate-with-application-gateway/frontends.png" alt-text="애플리케이션 게이트웨이 프런트 엔드 설정에서 공용 IP를 얻는 스크린샷.":::

3. 백 엔드 설정

    백 엔드 풀 이름을 입력하고 대상 유형 에서 **App Services** **또는 IP 주소 또는 FQDN을** 선택합니다.  이 경우 **를 App Services로** 설정하고 대상 드롭다운 목록에서 App Service 이름을 선택합니다.
    
    :::image type="content" source="./media/integrate-with-application-gateway/add-backend-pool.png" alt-text="백 엔드 설정에 백 엔드 풀 이름을 추가하는 스크린샷.":::

4. 구성 설정

    **구성** 설정에서 라우팅 규칙 추가 아이콘을 클릭하여 **라우팅 규칙을 추가해야** 합니다.
    
    :::image type="content" source="./media/integrate-with-application-gateway/configuration.png" alt-text="구성 설정에서 라우팅 규칙을 추가하는 스크린샷.":::
    
    라우팅 규칙에서 **수신기** 및  **백 엔드 대상을** 구성해야 합니다. 개념 증명 배포를 위해 HTTP 수신기를 추가하거나 보안 향상을 위해 HTTPS 수신기를 추가할 수 있습니다.
    
    * HTTP 프로토콜을 사용하여 애플리케이션 게이트웨이에 연결하려면 다음 설정을 사용하여 수신기를 만들 수 있습니다.
    
        | 매개 변수      | 값                             | Description                                                  |
        | -------------- | --------------------------------- | ------------------------------------------------------------ |
        | 규칙 이름      | 예: `http-routingrule`    | 라우팅 이름                                                 |
        | 수신기 이름  | 예: `http-listener`       | 수신기 이름                                                |
        | 프런트 엔드 IP    | 공용                            | 인터넷 액세스의 경우를 공용으로 설정 합니다.                           |
        | 프로토콜       | HTTP                             | TLS/SSL 암호화 사용 안 함                                       |
        | 포트           | 80                               | 기본 HTTP 포트                                           |
        | 수신기 유형  | 다중 사이트                        | 응용 프로그램 게이트웨이에서 다중 사이트를 수신 대기 하도록 허용           |
        | 호스트 유형      | 여러/와일드 카드                 | 수신기 형식이 다중 사이트로 설정 된 경우 여러 또는 와일드 카드 웹 사이트 이름으로 설정 합니다. |
        | 호스트 이름      | 예: `app.asabuludemo.com` | App Service에 대 한 라우팅 가능한 도메인 이름으로 설정              |
        
        :::image type="content" source="./media/integrate-with-application-gateway/http-routing-rule.png" alt-text="Application gateway 라우팅 규칙의 HTTP 수신기 스크린샷":::
    
    * TLS/SSL 암호화를 사용 하 여 응용 프로그램 게이트웨이에 연결 하려면 다음 설정을 사용 하 여 수신기를 만들 수 있습니다.
    
        | 매개 변수      | 값                             | Description                                                  |
        | -------------- | --------------------------------- | ------------------------------------------------------------ |
        | 규칙 이름      | 예: `https-routingrule`    | 라우팅 이름                                                 |
        | 수신기 이름  | 예: `https-listener`       | 수신기 이름                                                |
        | 프런트 엔드 IP    | 공용                            | 인터넷 액세스의 경우를 공용으로 설정 합니다.                           |
        | 프로토콜       | HTTPS                             | TLS/SSL 암호화 사용                                       |
        | 포트           | 443                               | 기본 HTTPS 포트                                           |
        | Https 설정 | 인증서 업로드              | 인증서에는 CN과 .pfx 형식의 개인 키가 포함 업로드 합니다. |
        | 수신기 유형  | 다중 사이트                        | 응용 프로그램 게이트웨이에서 다중 사이트를 수신 대기 하도록 허용           |
        | 호스트 유형      | 여러/와일드 카드                 | 수신기 형식이 다중 사이트로 설정 된 경우 여러 또는 와일드 카드 웹 사이트 이름으로 설정 합니다. |
        | 호스트 이름      | 예: `app.asabuludemo.com` | App Service에 대 한 라우팅 가능한 도메인 이름으로 설정              |
        
        :::image type="content" source="./media/integrate-with-application-gateway/https-routing-rule.png" alt-text="Application gateway 라우팅 규칙의 HTTPS 수신기입니다.":::
    
    * 백 엔드 **대상** 에서 **백 엔드 풀** 및 **HTTP 설정을** 구성 해야 합니다. 백 엔드 풀은 이전 단계에서 구성 되었습니다. **새 링크 추가** 를 클릭 하 여 HTTP 설정을 추가 합니다.
    
        :::image type="content" source="./media/integrate-with-application-gateway/add-new-http-setting.png" alt-text="Http 설정을 추가 하기 위한 새 링크 추가의 스크린샷":::
    
    * HTTP 설정은 아래와 같이 나열 됩니다.
    
        | 매개 변수                     | 값                                                        | Description                                                  |
        | ----------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
        | HTTP 설정 이름             | 예: `https-setting`                                   | HTTP 설정 이름                                            |
        | 백 엔드 프로토콜              | HTTPS                                                        | TLS/SSL 암호화 사용                                       |
        | 백 엔드 포트                  | 443                                                          | 기본 HTTPS 포트                                           |
        | 잘 알려진 CA 인증서 사용 | 예                                                          | ILB ASE의 기본 도메인 이름은 **appserviceenvironment.net** 입니다 .이 도메인의 인증서는 신뢰할 수 있는 공용 루트 기관에서 발급 됩니다. 신뢰할 수 있는 루트 인증서 설정에서 **잘 알려진 CA 신뢰할 수 있는 루트 인증서** 를 사용 하도록 설정할 수 있습니다. |
        | 새 호스트 이름으로 재정의   | 예                                                          | ILB ASE에서 앱에 연결 하는 경우 호스트 이름 헤더가 덮어 만들어집니다. |
        | 호스트 이름 재정의            | 백 엔드 대상에서 호스트 이름 선택 | 백 엔드 풀을 App Service로 설정 하는 경우 백 엔드 대상에서 호스트를 선택할 수 있습니다. |
        | 사용자 지정 프로브 만들기 | 아니요 | 기본 상태 프로브 사용|
        
        :::image type="content" source="./media/integrate-with-application-gateway/https-setting.png" alt-text="HTTP 설정 세부 정보를 추가 하는 스크린샷":::


## <a name="configure-an-application-gateway-integration-with-ilb-ase"></a>ILB ASE를 사용 하 여 application gateway 통합 구성

Application gateway에서 ILB ASE에 액세스 하려면 개인 DNS 영역에 대 한 가상 네트워크가 연결 되어 있는지 확인 해야 합니다. 응용 프로그램 게이트웨이의 VNet에 연결 된 가상 네트워크가 없으면 다음 단계에 따라 가상 네트워크 링크를 추가 합니다.

### <a name="configure-virtual-network-links-with-a-private-dns-zone"></a>개인 DNS 영역을 사용 하 여 가상 네트워크 링크 구성

* 개인 DNS 영역을 사용 하 여 가상 네트워크 링크를 구성 하려면 개인 DNS 영역 구성 평면으로 이동 합니다. **가상 네트워크 링크**  >  **추가** 를 선택 합니다. 

:::image type="content" source="./media/integrate-with-application-gateway/add-vnet-link.png" alt-text="개인 DNS 영역에 가상 네트워크 링크를 추가 합니다.":::

* **링크 이름을** 입력 하 고 응용 프로그램 게이트웨이가 있는 해당 구독 및 가상 네트워크를 선택 합니다.

:::image type="content" source="./media/integrate-with-application-gateway/vnet-link.png" alt-text="개인 DNS 영역에 있는 가상 네트워크 링크에 대 한 입력 링크 이름 세부 정보의 스크린샷":::

* 응용 프로그램 게이트웨이 평면의 **백 엔드 상태** 에서 백 엔드 상태를 확인할 수 있습니다.

:::image type="content" source="./media/integrate-with-application-gateway/backend-health.png" alt-text="백 엔드 상태에서 백 엔드 상태를 확인 하는 스크린샷":::

### <a name="add-a-public-dns-record"></a>공용 DNS 레코드 추가

인터넷에서 애플리케이션 게이트웨이에 액세스할 때 적절한 DNS 매핑을 구성해야 합니다.

* 애플리케이션 게이트웨이의 공용 IP 주소는 애플리케이션 게이트웨이 평면의 **프런트 엔드 IP 구성에서** 찾을 수 있습니다.

:::image type="content" source="./media/integrate-with-application-gateway/frontend-ip.png" alt-text="애플리케이션 게이트웨이 프런트 엔드 IP 주소는 프런트 엔드 IP 구성에서 찾을 수 있습니다.":::

* 예를 들어 Azure DNS 서비스를 사용하여 앱 도메인 이름을 애플리케이션 게이트웨이의 공용 IP 주소에 매핑하는 레코드 집합을 추가할 수 있습니다.

:::image type="content" source="./media/integrate-with-application-gateway/dns-service.png" alt-text="앱 도메인 이름을 애플리케이션 게이트웨이의 공용 IP 주소에 매핑하는 레코드 집합을 추가하는 스크린샷.":::

### <a name="validate-connection"></a>연결 유효성 검사

* 인터넷에서 머신에 액세스하는 경우 애플리케이션 게이트웨이 공용 IP 주소에 대한 앱 도메인 이름의 이름 확인을 확인할 수 있습니다.

:::image type="content" source="./media/integrate-with-application-gateway/name-resolution.png" alt-text="명령 프롬프트에서 이름 확인의 유효성을 검사합니다.":::

* 인터넷에서 머신에 액세스하는 경우 브라우저에서 웹 액세스를 테스트합니다.

:::image type="content" source="./media/integrate-with-application-gateway/access-web.png" alt-text="브라우저를 열고 웹에 액세스하는 스크린샷.":::

<!--LINKS-->
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[creation]: ./creation.md
[createfromtemplate]: ./create-from-template.md
[createprivatednszone]: ../../dns/private-dns-getstarted-portal.md
[AppServiceEnvironmentoverview]: ./overview.md
[privatednszone]: ../../dns/private-dns-overview.md
[Tutorial: Create an application gateway with a Web Application Firewall using the Azure portal]: ../../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md
