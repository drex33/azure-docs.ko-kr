---
title: 기존 온-프레미스 프록시 서버 및 Azure Active Directory 작업
description: Azure Active Directory를 사용한 기존 온-프레미스 프록시 서버 작업 방법을 다룹니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 696c0029a238fe08d8792dfb4fb171f6be833d8b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068230"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>기존 온-프레미스 프록시 서버 작업

이 문서에서는 아웃바운드 프록시 서버로 작업하도록 Azure AD(Azure Active Directory) 애플리케이션 프록시 커넥터를 구성하는 방법을 설명합니다. 네트워크 환경에 기존 프록시가 있는 고객을 위한 것입니다.

이러한 주요 배포 시나리오를 살펴보는 것으로 시작합니다.

* 온-프레미스 아웃바운드 프록시를 바이패스하도록 커넥터를 구성합니다.
* 아웃바운드 프록시를 사용하여 Azure AD 애플리케이션 프록시에 액세스하도록 커넥터를 구성합니다.
* 커넥터와 백 엔드 애플리케이션 간에 프록시를 사용하여 구성합니다.

커넥터 작동 방법에 대한 자세한 내용은 [Azure AD 애플리케이션 프록시 커넥터 이해](application-proxy-connectors.md)를 참조하세요.

## <a name="bypass-outbound-proxies"></a>아웃바운드 프록시 바이패스

커넥터에는 아웃바운드 요청을 하는 기본 OS 구성 요소가 있습니다. 이러한 구성 요소는 WPAD(웹 프록시 자동 검색)를 사용하여 자동으로 네트워크에서 프록시 서버를 찾으려고 합니다.

OS 구성 요소는 wpad.domainsuffix에 대한 DNS 조회를 수행하여 프록시 서버를 찾아 봅니다. 조회를 통해 DNS에서 확인되면 wpad.dat에 대한 IP 주소에 HTTP 요청을 수행합니다. 이 요청은 사용자 환경에서 프록시 구성 스크립트가 됩니다. 커넥터는 이 스크립트를 사용하여 아웃바운드 프록시 서버를 선택합니다. 하지만 커넥터 트래픽은 프록시에서 필요한 추가 구성 설정 때문에 여전히 통과하지 못할 수 있습니다.

온-프레미스 프록시를 바이패스하여 Azure 서비스로 직접 연결을 사용하도록 커넥터를 구성할 수 있습니다. 이렇게 하면 유지할 구성이 줄어들기 때문에 네트워크 정책에서 허용하기만 한다면 이 방법을 사용하는 것이 좋습니다.

커넥터에 아웃바운드 프록시 사용을 사용하지 않도록 설정하려면 C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config 파일을 편집하여 다음 코드 샘플에 표시된 *system.net* 섹션을 추가합니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

커넥터 업데이터 서비스도 프록시를 바이패스하도록 하려면 ApplicationProxyConnectorUpdaterService.exe.config 파일을 유사하게 변경합니다. 이 파일은 C:\Program Files\Microsoft AAD App Proxy Connector Updater에 있습니다.

기본 .config 파일로 되돌려야 할 경우를 대비해 원본 파일을 복사해야 합니다.

## <a name="use-the-outbound-proxy-server"></a>아웃바운드 프록시 서버 사용

일부 환경에서는 모든 아웃바운드 트래픽이 예외 없이 아웃바운드 프록시를 통과해야 합니다. 따라서 프록시를 바이패스하는 것은 옵션이 아닙니다.

다음 다이어그램에 표시된 대로 커넥터 트래픽이 아웃바운드 프록시를 통과하도록 구성할 수 있습니다.

 ![Azure AD 애플리케이션 프록시에 대한 아웃바운드 프록시를 통과하도록 커넥터 트래픽 구성](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

그 결과 아웃바운드 트래픽만 포함되므로 방화벽을 통과하는 인바운드 액세스를 구성할 필요가 없습니다.

> [!NOTE]
> 애플리케이션 프록시는 다른 프록시에 대한 인증을 지원하지 않습니다. connector/updater 네트워크 서비스 계정은 인증하는 데 어려움 없이 프록시에 연결할 수 있어야 합니다.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>1단계: 아웃바운드 프록시를 통과하도록 커넥터 및 관련 서비스 구성

환경에 WPAD가 사용되고 적절히 구성된 경우 커넥터는 아웃바운드 프록시 서버를 자동으로 검색하고 사용하려고 합니다. 하지만 트래픽이 아웃바운드 프록시를 통과하도록 명시적으로 커넥터를 구성할 수 있습니다.

이렇게 하려면 C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config 파일을 편집하여 다음 코드 샘플에 표시된 *system.net* 섹션을 추가합니다. 로컬 프록시 서버 이름 또는 IP 주소와 수신 대기 중인 포트를 반영하도록 *proxyserver:8080* 을 변경합니다. IP 주소를 사용하는 경우에도 값에는 http:// 접두사가 있어야 합니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

그런 다음 C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config 파일을 유사하게 변경하여 프록시를 사용하도록 커넥터 업데이터 서비스를 구성합니다.

> [!NOTE]
> Connector 서비스는 **defaultProxy** 가 ApplicationProxyConnectorService.exe.config에서 기본적으로 구성되지 않은 경우 `%SystemRoot%\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config`에서 사용할 **defaultProxy** 구성을 평가합니다. Connector Updater 서비스(ApplicationProxyConnectorUpdaterService.exe.config)에도 동일하게 적용됩니다. 

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>2단계: 커넥터 및 관련 서비스에서 트래픽이 통과하여 흐를 수 있도록 프록시 구성

아웃바운드 프록시에서는 다음 4가지 측면을 고려해야 합니다.

* 프록시 아웃바운드 규칙
* 프록시 인증
* 프록시 포트
* TLS 조사

#### <a name="proxy-outbound-rules"></a>프록시 아웃바운드 규칙

다음 URL에 대한 액세스를 허용합니다.

| URL | 포트 | 사용 방법 |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | 커넥터와 애플리케이션 프록시 클라우드 서비스 간의 통신 |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP | 커넥터는 이러한 URL을 사용하여 인증서를 확인합니다. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;.microsoftonline-p.com<br>&ast;.msauth.net<br>&ast;.msauthimages.net<br>&ast;.msecnd.net<br>&ast;.msftauth.net<br>&ast;.msftauthimages.net<br>&ast;.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com | 443/HTTPS | 커넥터는 등록 프로세스 동안 다음과 같은 URL을 사용합니다. |
| ctldl.windowsupdate.com | 80/HTTP | 커넥터는 등록 프로세스 동안 이 URL을 사용합니다. |

방화벽이나 프록시에서 DNS 허용 목록을 허용하면 \*.msappproxy.net 및 \*.servicebus.windows.net에 대한 연결을 허용할 수 있습니다.

FQDN으로 연결을 허용할 수 없고 그 대신 IP 범위를 지정해야 하는 경우 다음 옵션을 사용합니다.

* 모든 대상에 대한 커넥터 아웃바운드 액세스 허용
* 모든 Azure 데이터 센터 IP 범위에 대한 커넥터 아웃바운드 액세스 허용. Azure 데이터 센터 IP 범위 목록의 사용과 관련된 문제는 매주 업데이트됩니다. 액세스 규칙을 적절하게 업데이트하도록 프로세스를 실행해야 합니다. IP 주소 서브넷만 사용하면 구성이 중단될 수 있습니다. 최신 Azure 데이터 센터 IP 범위를 다운로드하려면 [https://download.microsoft.com](https://download.microsoft.com)으로 이동하여 "Azure IP 범위 및 서비스 태그"를 검색합니다. 관련 클라우드를 선택해야 합니다. 예를 들어 퍼블릭 클라우드 IP 범위는 "Azure IP 범위 및 서비스 태그 - 퍼블릭 클라우드"를 통해 찾을 수 있습니다. 미국 정부 클라우드는 "Azure IP 범위 및 서비스 태그 - US 정부 클라우드"를 검색하여 찾을 수 있습니다.

#### <a name="proxy-authentication"></a>프록시 인증

프록시 인증은 현재 지원되지 않습니다. 현재 권장 사항은 인터넷 대상에 대한 커넥터 익명 액세스를 허용하는 것입니다.

#### <a name="proxy-ports"></a>프록시 포트

커넥터는 CONNECT 메서드를 사용하여 아웃바운드 TLS 기반 연결을 만듭니다. 이 메서드는 기본적으로 아웃바운드 프록시를 통해 터널을 설정합니다. 비표준 SSL 포트 443 및 80으로 터널링을 허용하도록 프록시 서버를 구성합니다.

> [!NOTE]
> Service Bus가 HTTPS를 초과하면 포트 443을 사용합니다. 하지만 기본적으로 Service Bus는 직접 TCP 연결을 시도하며 직접 연결이 실패할 때만 HTTPS를 대체합니다.

#### <a name="tls-inspection"></a>TLS 조사

커넥터 트래픽에 대한 문제를 발생시키기 때문에 커넥터 트래픽에 대한 TLS 검사를 사용하지 않습니다. 커넥터는 인증서를 사용하여 애플리케이션 프록시 서비스를 인증하며 TLS 검사 중에 인증서가 손실될 수 있습니다.

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>커넥터와 백 엔드 애플리케이션 간에 프록시를 사용하여 구성
일부 환경에서는 백 엔드 애플리케이션에 대한 통신에 전달 프록시를 사용해야 할 수 있습니다.
이를 사용하도록 설정하려면 다음 단계를 수행하세요.

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>1단계: 서버에 필요한 레지스트리 값 추가
1. 기본 프록시를 사용하도록 설정하려면 다음 레지스트리 값(DWORD) `UseDefaultProxyForBackendRequests = 1`을 "HKEY_LOCAL_MACHINE \Software\Microsoft\Microsoft AAD App Proxy Connector"에 있는 커넥터 구성 레지스트리 키에 추가합니다.

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>2단계: netsh 명령을 사용하여 수동으로 프록시 서버 구성
1.  ‘컴퓨터 단위로 프록시 설정 만들기’ 그룹 정책을 사용하도록 설정합니다. 이 정책 위치는 컴퓨터 구성\정책\관리 템플릿\Windows 구성 요소\Internet Explorer입니다. 이 정책은 사용자 단위가 아니라 컴퓨터 단위로 설정해야 합니다.
2.  서버에서 `gpupdate /force`를 실행하거나 서버를 다시 부팅하여 업데이트된 그룹 정책 설정을 사용하도록 합니다.
3.  관리자 권한으로 명령 프롬프트를 시작하고 `control inetcpl.cpl`을 입력합니다.
4.  필요한 프록시 설정을 구성합니다. 

이러한 설정은 커넥터에서 Azure 및 백 엔드 애플리케이션에 대한 통신에서 동일한 전달 프록시를 사용하도록 합니다. Azure와 커넥터 간 통신에 정방향 프록시가 필요하지 않거나 다른 정방향 프록시가 필요한 경우 아웃바운드 프록시 무시 또는 아웃바운드 프록시 서버 사용 섹션에 설명된 대로 ApplicationProxyConnectorService.exe.config 파일을 수정하여 설정할 수 있습니다.

> [!NOTE]
> 운영 체제에서 인터넷 프록시를 구성하는 방법에는 여러 가지가 있습니다. NETSH WINHTTP를 통해 구성된 프록시 설정(확인하려면 `NETSH WINHTTP SHOW PROXY` 실행)은 2단계에서 구성한 프록시 설정을 재정의합니다. 

커넥터 업데이트 프로그램 서비스는 컴퓨터 프록시도 사용합니다. ApplicationProxyConnectorUpdaterService.exe.config 파일을 수정하여 이 동작을 변경할 수 있습니다.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>커넥터 프록시 및 서비스 연결 문제 해결

이제 프록시를 통과하여 흐르는 모든 트래픽이 표시됩니다. 문제가 있는 경우 다음 문제 해결 정보가 도움이 됩니다.

커넥터 연결 문제를 식별하고 해결하는 좋은 방법은 커넥터 서비스를 시작하는 동안 네트워크 캡처를 사용하는 것입니다. 다음은 네트워크 추적을 캡처 및 필터링하는 몇 가지 간단한 팁입니다.

원하는 모니터링 도구를 사용할 수 있습니다. 이 문서에서는 Microsoft 메시지 분석기를 사용했습니다.

> [!NOTE]
> 2019년 11월 25일에 [MMA(Microsoft 메시지 분석기)가 사용 중지](/openspecs/blog/ms-winintbloglp/dd98b93c-0a75-4eb0-b92e-e760c502394f)되었으며 해당 다운로드 패키지가 microsoft.com 사이트에서 제거되었습니다.  현재 개발 중인 Microsoft Message Analyzer에 대한 Microsoft 대체 항목은 없습니다.  유사한 기능을 사용하려면 Wireshark와 같은 타사 네트워크 프로토콜 분석기 도구를 사용하는 것이 좋습니다.

다음 예는 메시지 분석기에 대한 내용이지만 원칙은 모든 분석 도구에 적용할 수 있습니다.

### <a name="take-a-capture-of-connector-traffic"></a>커넥터 트래픽 캡처

초기 문제 해결의 경우 다음 단계를 수행합니다.

1. services.msc에서 Azure AD 애플리케이션 프록시 커넥터 서비스를 중지합니다.

   ![services.msc의 Azure AD 애플리케이션 프록시 커넥터 서비스](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. 관리자 권한으로 메시지 분석기를 실행합니다.
1. **로컬 추적 시작** 을 선택합니다.
1. Azure AD 애플리케이션 프록시 커넥터 서비스를 시작합니다.
1. 네트워크 캡처를 중지합니다.

   ![네트워크 캡처 중지 단추를 보여 주는 스크린샷](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>커넥터 트래픽이 아웃바운드 프록시를 바이패스하는지 확인

프록시 서버를 바이패스하고 애플리케이션 프록시 서비스에 직접 연결하도록 애플리케이션 프록시 커넥터를 구성한 경우 실패한 TCP 연결 시도에 대한 네트워크 캡처를 확인하려고 합니다.

메시지 분석기 필터를 사용하여 이러한 시도를 식별합니다. 필터 상자에 `property.TCPSynRetransmit`을 입력하고 **적용** 을 선택합니다.

SYN 패킷은 TCP 연결을 설정하기 위해 전송된 첫 번째 패킷입니다. 이 패킷이 응답을 반환하지 않으면 SYN이 다시 시도됩니다. 이전 필터를 사용하여 재전송된 SYN을 확인할 수 있습니다. 그런 다음 이러한 SYN이 커넥터와 관련된 트래픽에 해당하는지를 확인할 수 있습니다.

커넥터를 Azure 서비스에 직접 연결하려는 경우 포트 443에서 SynRetransmit 응답은 네트워크 또는 방화벽 문제가 있음을 나타냅니다.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>커넥터 트래픽이 아웃바운드 프록시를 사용하는지 확인

프록시 서버를 통과하도록 애플리케이션 프록시 커넥터 트래픽을 구성한 경우 프록시에 대해 실패한 https 연결을 찾으려고 합니다.

이러한 연결 시도에 대한 네트워크 캡처를 필터링하려면 메시지 분석기 필터에 `(https.Request or https.Response) and tcp.port==8080`을 입력하고 8080을 프록시 서비스 포트로 바꿉니다. **적용** 을 선택하여 필터 결과를 확인합니다.

이전 필터는 프록시 포트로 보내거나 받는 HTTPs 요청 및 응답만 보여 줍니다. 프록시 서버와 통신을 보여 주는 CONNECT 요청을 찾는 중입니다. 성공하면 HTTP OK(200) 응답을 받게 됩니다.

다른 응답 코드(예: 407 또는 502)가 표시되면 프록시에서 인증을 요청 중이고 다른 이유로 트래픽을 허용하지 않음을 나타냅니다. 이 시점에는 프록시 서버 지원 팀이 관여합니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD 애플리케이션 프록시 커넥터 이해](application-proxy-connectors.md)
* 커넥터 연결 문제가 있는 경우 [Azure Active Directory 에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-active-directory.html)에서 질문하거나 지원 팀을 사용하여 티켓을 만드세요.
