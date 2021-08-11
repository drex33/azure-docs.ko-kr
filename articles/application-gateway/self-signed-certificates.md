---
title: 사용자 지정 루트 CA를 사용하여 자체 서명된 인증서 생성
titleSuffix: Azure Application Gateway
description: 사용자 지정 루트 CA를 사용하여 Azure Application Gateway 자체 서명된 인증서를 생성하는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/23/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 602f766429045d91748c0f0873744fe1d35b621d
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110467016"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>사용자 지정 루트 CA를 사용하여 Azure Application Gateway 자체 서명된 인증서 생성

Application Gateway v2 SKU는 백 엔드 서버를 허용하기 위해 신뢰할 수 있는 루트 인증서를 사용하는 방식을 도입했습니다. 따라서 v1 SKU에 필요한 인증 인증서가 제거되었습니다. *루트 인증서* 는 백 엔드 인증서 서버의 Base-64로 인코딩된 X.509(.CER) 형식 루트 인증서입니다. 이 인증서는 서버 인증서를 발급한 루트 CA(인증 기관)를 식별하며 TLS/SSL 통신에 서버 인증서가 사용됩니다.

Application Gateway는 잘 알려진 CA(예: GoDaddy 또는 DigiCert)에 의해 서명된 경우 기본적으로 웹 사이트의 인증서를 신뢰합니다. 이 경우 루트 인증서를 명시적으로 업로드하지 않아도 됩니다. 자세한 내용은 [Application Gateway를 사용한 TLS 종료 및 엔드투엔드 TLS 개요](ssl-overview.md)를 참조하세요. 그러나 개발/테스트 환경이 있고 확인된 CA 서명 인증서를 구매하지 않으려는 경우 사용자 고유의 사용자 지정 CA를 만들고 이를 사용하여 자체 서명된 인증서를 만들 수 있습니다. 

> [!NOTE]
> 자체 서명된 인증서는 기본적으로 신뢰할 수 없으며 관리하기 어려울 수 있습니다. 또한 강력하지 않을 수 있는 오래된 해시 및 암호 그룹을 사용할 수 있습니다. 보안 향상을 위해 잘 알려진 인증 기관에서 서명한 인증서를 구매합니다.

이 아티클에서는 다음 방법을 설명합니다.

- 사용자 고유의 사용자 지정 인증 기관 만들기
- 사용자 지정 CA에서 서명한 자체 서명된 인증서 만들기
- Application Gateway에 자체 서명된 루트 인증서를 업로드하여 백 엔드 서버 인증

## <a name="prerequisites"></a>사전 요구 사항

- **Windows 또는 Linux를 실행하는 컴퓨터의 [OpenSSL](https://www.openssl.org/)** 

   인증서 관리에 사용할 수 있는 다른 도구가 있을 수 있지만 이 자습서에서는 OpenSSL을 사용합니다. Ubuntu와 같은 많은 Linux 배포판에 번들로 제공되는 OpenSSL을 찾을 수 있습니다.
- **웹 서버**

   예를 들어, Apache, IIS 또는 NGINX를 통해 인증서를 테스트할 수 있습니다.

- **Application Gateway v2 SKU**
   
  기존 Application Gateway가 없는 경우 다음을 참조하세요. [빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - Azure Portal](quick-create-portal.md)

## <a name="create-a-root-ca-certificate"></a>루트 CA 인증서 만들기

OpenSSL을 사용하여 루트 CA 인증서를 만듭니다.

### <a name="create-the-root-key"></a>루트 키 만들기

1. OpenSSL이 설치된 컴퓨터에 로그인하고 다음 명령을 실행합니다. 암호화된 키를 생성합니다.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
   
### <a name="create-a-root-certificate-and-self-sign-it"></a>루트 인증서 만들기 및 자체 서명

1. 다음 명령을 사용하여 csr 및 인증서를 생성합니다.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr
   ```
   
   ```
   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   이전 명령은 루트 인증서를 만듭니다. 서버 인증서에 서명하는 데 이 인증서를 사용합니다.

1. 메시지가 표시되면 루트 키에 대한 암호, 사용자 지정 CA에 대한 정보[국가/지역, 시/도, 조직, OU 및 정규화된 도메인 이름(발급자의 도메인)]를 입력합니다.

   ![루트 인증서 만들기](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>서버 인증서 만들기

다음으로, OpenSSL을 사용하여 서버 인증서를 만듭니다.

### <a name="create-the-certificates-key"></a>인증서의 키 만들기

다음 명령을 사용하여 서버 인증서에 대한 키를 생성합니다.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>CSR(인증서 서명 요청) 만들기

CSR은 인증서를 요청할 때 CA에 제공되는 퍼블릭 키입니다. CA는 이 특정 요청에 대해 인증서를 발급합니다.

> [!NOTE]
> 서버 인증서의 CN(일반 이름)은 발급자의 도메인과 달라야 합니다. 예를 들어 이 경우 발급자의 CN은 `www.contoso.com`이고 서버 인증서의 CN은 `www.fabrikam.com`입니다.


1. 다음 명령을 사용하여 CSR을 생성합니다.

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. 메시지가 표시되면 루트 키에 대한 암호, 사용자 지정 CA에 대한 정보(국가/지역, 시/도, 조직, OU 및 정규화된 도메인 이름)를 입력합니다. 이것은 웹 사이트의 도메인이며 발급자와 달라야 합니다.

   ![서버 인증서](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>CSR 및 키를 사용하여 인증서를 생성하고 CA의 루트 키로 서명

1. 다음 명령을 사용하여 인증서를 만듭니다.

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>새로 만든 인증서 확인

1. 다음 명령을 사용하여 CRT 파일의 출력을 인쇄하고 해당 내용을 확인합니다.

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![인증서 확인](media/self-signed-certificates/verify-cert.png)

1. 디렉터리의 파일을 확인하고 다음 파일이 있는지 확인합니다.

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>웹 서버의 TLS 설정에서 인증서 구성

웹 서버에서 fabrikam.crt 및 fabrikam.key 파일을 사용하여 TLS를 구성합니다. 웹 서버에서 두 개의 파일을 가져올 수 없는 경우 OpenSSL 명령을 사용하여 단일 .pem 또는 .pfx 파일로 결합할 수 있습니다.

### <a name="iis"></a>IIS

인증서를 가져오고 IIS에서 서버 인증서로 업로드하는 방법에 대한 지침은 [방법: Windows Server 2003에서 웹 서버에 가져온 인증서 설치](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)를 참조하세요.

TLS 바인딩 지침은 [IIS 7에서 SSL을 설정하는 방법](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)을 참조하세요.

### <a name="apache"></a>Apache

다음 구성은 [SSL용으로 구성된 가상 호스트](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) 예제입니다.

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

다음 구성은 TLS 구성을 사용하는 [NGINX 서버 블록](https://nginx.org/docs/http/configuring_https_servers.html) 예제입니다.

![TLS를 사용하는 NGINX](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>서버에 액세스하여 구성 확인

1. 루트 인증서를 컴퓨터의 신뢰할 수 있는 루트 저장소에 추가합니다. 웹 사이트에 액세스하는 경우 전체 인증서 체인이 브라우저에 표시되는지 확인합니다.

   ![신뢰할 수 있는 루트 인증서](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > DNS가 웹 서버 이름(이 예제에서는 www.fabrikam.com)을 웹 서버의 IP 주소로 가리키도록 구성되었다고 가정합니다. 그렇지 않으면 [hosts 파일](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d)을 편집하여 이름을 확인할 수 있습니다.
1. 웹 사이트로 이동하고 브라우저의 주소 상자에서 자물쇠 아이콘을 클릭하여 사이트 및 인증서 정보를 확인합니다.

## <a name="verify-the-configuration-with-openssl"></a>OpenSSL을 사용하여 구성 확인

또는 OpenSSL을 사용하여 인증서를 확인할 수 있습니다.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![OpenSSL 인증서 확인](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Application Gateway의 HTTP 설정에 루트 인증서 업로드

Application Gateway에서 인증서를 업로드하려면 .crt 인증서를 Base-64로 인코딩된 .cer 형식으로 내보내야 합니다. .crt에는 base-64로 인코딩된 형식의 퍼블릭 키가 이미 포함되어 있으므로 .crt에서 .cer로 파일 확장명을 바꿉니다. 

### <a name="azure-portal"></a>Azure portal

포털에서 신뢰할 수 있는 루트 인증서를 업로드하려면 **HTTP 설정** 을 선택하고 **HTTPS** 프로토콜을 선택합니다.

![포털을 사용하여 인증서 추가](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

또는 Azure CLI 또는 Azure PowerShell을 사용하여 루트 인증서를 업로드할 수 있습니다. 다음 코드는 Azure PowerShell 샘플입니다.

> [!NOTE]
> 다음 샘플에서는 Application Gateway에 신뢰할 수 있는 루트 인증서를 추가하고, 새 HTTP 설정을 만들고, 새 규칙을 추가하며, 백 엔드 풀 및 수신기가 이미 존재한다고 가정합니다.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```

### <a name="verify-the-application-gateway-backend-health"></a>Application Gateway 백 엔드 상태 확인

1. Application Gateway의 **백 엔드 상태** 보기를 클릭하여 프로브가 정상 상태인지 확인합니다.
1. HTTPS 프로브이 상태가 **정상** 으로 표시되어야 합니다.

![HTTPS 프로브](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>다음 단계

Application Gateway의 SSL\TLS에 대한 자세한 내용은 [Application Gateway를 사용한 TLS 종료 및 종단간 TLS 개요](ssl-overview.md)를 참조하세요.
