---
title: Azure Application Gateway에서 상호 인증 문제 해결
description: Application Gateway에서 상호 인증 문제 해결 방법 알아보기
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 679038f91ea33448aff0210f97df70d5d60fca13
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133517474"
---
# <a name="troubleshooting-mutual-authentication-errors-in-application-gateway-preview"></a>Application Gateway(미리 보기)에서 상호 인증 오류 문제 해결

Application Gateway를 사용하는 경우 상호 인증 문제를 해결하는 방법을 알아봅니다. 

## <a name="overview"></a>개요 

Application Gateway에서 상호 인증을 구성한 후 상호 인증을 사용하려고 할 때 표시되는 여러 가지 오류가 있을 수 있습니다. 오류가 발생하는 몇 가지 일반적인 원인은 다음과 같습니다.

* 루트 CA 인증서가 없는 인증서 또는 인증서 체인을 업로드함
* 여러 루트 CA 인증서를 사용하여 인증서 체인을 업로드함
* CA 인증서가 없는 리프 인증서만 포함된 인증서 체인을 업로드함 
* 발급자 DN 불일치로 인해 유효성 검사 오류가 발생함  

발생할 수 있는 다양한 시나리오와 이러한 시나리오를 해결하는 방법을 살펴보겠습니다. 그런 다음, 오류 코드를 해결하고 상호 인증에 표시될 수 있는 특정 오류 코드에 대한 가능한 원인을 설명합니다. 

## <a name="scenario-troubleshooting---configuration-problems"></a>시나리오 문제 해결 - 구성 문제
상호 인증을 구성하려고 할 때 직면하는 몇 가지 시나리오가 있습니다. 가장 일반적인 문제 중 일부를 해결하는 방법을 살펴보겠습니다. 

### <a name="self-signed-certificate"></a>자체 서명된 인증서

#### <a name="problem"></a>문제 

업로드한 클라이언트 인증서는 자체 서명된 인증서이며 이로 인해 오류 코드 ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate가 발생합니다.

#### <a name="solution"></a>해결 방법 

사용 중인 자체 서명된 인증서에 *BasicConstraintsOid* = "2.5.29.19" 확장이 있는지 다시 확인합니다. 이는 주체가 CA 역할을 할 수 있음을 나타냅니다. 이렇게 하면 사용되는 인증서가 CA 인증서임을 확인할 수 있습니다. 자체 서명된 클라이언트 인증서를 생성하는 방법에 대한 자세한 내용은 [신뢰할 수 있는 클라이언트 인증서](./mutual-authentication-certificate-management.md)를 참조하세요.

## <a name="scenario-troubleshooting---connectivity-problems"></a>시나리오 문제 해결 - 연결 문제

문제 없이 상호 인증을 구성했을 수 있지만 Application Gateway에 요청을 보낼 때 문제가 발생하고 있습니다. 다음 섹션에서 몇 가지 일반적인 문제 및 해결 방법을 설명합니다. Application Gateway의 액세스 로그에서 *sslClientVerify* 속성을 찾을 수 있습니다. 

### <a name="sslclientverify-is-none"></a>SslClientVerify가 NONE입니다.

#### <a name="problem"></a>문제 

*sslClientVerify* 속성은 액세스 로그에 "NONE"으로 표시됩니다. 

#### <a name="solution"></a>해결 방법 

클라이언트는 Application Gateway에 요청을 보낼 때 클라이언트 인증서를 보내지 않는 경우에 표시됩니다. Application Gateway에 요청을 보내는 클라이언트가 클라이언트 인증서를 사용하도록 올바르게 구성되지 않은 경우 이 문제가 발생할 수 있습니다. Application Gateway에서 클라이언트 인증 설정이 예상대로 작동하는지 확인하는 한 가지 방법은 다음 OpenSSL 명령을 사용하는 것입니다.

```
openssl s_client -connect <hostname:port> -cert <path-to-certificate> -key <client-private-key-file> 
```

`-cert` 플래그는 리프 인증서이며, `-key` 플래그는 클라이언트 프라이빗 키 파일입니다. 

OpenSSL `s_client` 명령을 사용하는 방법에 대한 자세한 내용은 [설명서 페이지](https://www.openssl.org/docs/manmaster/man1/openssl-s_client.html)를 참조하세요.

### <a name="sslclientverify-is-failed"></a>SslClientVerify가 FAILED입니다.

#### <a name="problem"></a>문제

*sslClientVerify* 속성은 액세스 로그에 "FAILED"로 표시됩니다. 

#### <a name="solution"></a>해결 방법

액세스 로그에 오류가 발생할 수 있는 잠재적인 원인에는 여러 가지가 있습니다. 다음은 실패에 대한 일반적인 원인 목록입니다.
* **발급자 인증서를 가져올 수 없음:** 클라이언트 인증서의 발급자 인증서를 찾을 수 없습니다. 이는 일반적으로 Application Gateway에서 신뢰할 수 있는 클라이언트 CA 인증서 체인이 완전하지 않음을 의미합니다. Application Gateway에서 업로드된 신뢰할 수 있는 클라이언트 CA 인증서 체인이 완료되었는지 확인합니다.  
* **로컬 발급자 인증서를 가져올 수 없음:** 발급자 인증서를 가져올 수 없는 것처럼 클라이언트 인증서의 발급자 인증서를 찾을 수 없습니다. 이는 일반적으로 Application Gateway에서 신뢰할 수 있는 클라이언트 CA 인증서 체인이 완전하지 않음을 의미합니다. Application Gateway에서 업로드된 신뢰할 수 있는 클라이언트 CA 인증서 체인이 완료되었는지 확인합니다.
* **첫 번째 인증서를 확인할 수 없음:** 클라이언트 인증서를 확인할 수 없습니다. 이 오류는 클라이언트가 발급자를 신뢰하지 않는 리프 인증서만 제공하는 경우에 특히 발생합니다. Application Gateway에서 업로드된 신뢰할 수 있는 클라이언트 CA 인증서 체인이 완료되었는지 확인합니다.
* **클라이언트 인증서 발급자를 확인할 수 없음:** 이 오류는 구성 *VerifyClientCertIssuerDN* 이 true로 설정된 경우에 발생합니다. 이는 일반적으로 클라이언트 인증서의 발급자 DN이 고객이 업로드한 신뢰할 수 있는 클라이언트 CA 인증서 체인에서 추출된 *ClientCertificateIssuerDN* 과 일치하지 않는 경우에 발생합니다. Application Gateway에서 *ClientCertificateIssuerDN* 을 추출하는 방법에 대한 자세한 내용은 [Application Gateway 발급자 DN 추출](./mutual-authentication-overview.md#verify-client-certificate-dn)을 확인하세요. 모범 사례로, 파일당 인증서 체인 하나를 Application Gateway에 업로드하고 있는지 확인합니다. 

Application Gateway에 업로드할 신뢰할 수 있는 전체 클라이언트 CA 인증서 체인을 추출하는 방법에 대한 자세한 내용은 [신뢰할 수 있는 클라이언트 CA 인증서 체인을 추출하는 방법](./mutual-authentication-certificate-management.md)을 참조하세요.

## <a name="error-code-troubleshooting"></a>오류 코드 문제 해결
다음 오류 코드가 표시되는 경우 발생할 수 있는 문제를 해결하는 데 도움이 되는 몇 가지 권장 솔루션이 있습니다. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustspecifydata"></a>오류 코드: ApplicationGatewayTrustedClientCertificateMustSpecifyData

#### <a name="cause"></a>원인

누락된 인증서 데이터가 있습니다. 업로드된 인증서는 인증서 데이터가 없는 빈 파일일 수 있습니다. 

#### <a name="solution"></a>해결 방법

업로드한 인증서 파일에 누락된 데이터가 없는지 확인합니다. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustnothaveprivatekey"></a>오류 코드: ApplicationGatewayTrustedClientCertificateMustNotHavePrivateKey

#### <a name="cause"></a>원인

인증서 체인에 프라이빗 키가 있습니다. 인증서 체인에 프라이빗 키가 있어서는 안 됩니다. 

#### <a name="solution"></a>해결 방법

업로드된 인증서 체인을 다시 확인하고 체인에 포함된 프라이빗 키를 제거합니다. 프라이빗 키 없이 체인을 다시 업로드합니다. 

### <a name="error-code-applicationgatewaytrustedclientcertificateinvaliddata"></a>오류 코드: ApplicationGatewayTrustedClientCertificateInvalidData

#### <a name="cause"></a>원인

이 오류 코드에는 두 가지 잠재적 원인이 있을 수 있습니다.
1. 체인이 올바른 형식으로 제공되지 않아 구문 분석에 실패했습니다. Application Gateway는 인증서 체인이 PEM 형식일 것으로 예상하며 개별 인증서 데이터도 구분될 것으로 예상합니다. 
2. 파서가 구문 분석할 대상을 찾지 못했습니다. 업로드된 파일에는 구분 기호만 있지만 인증서 데이터는 없을 수 있습니다. 

#### <a name="solution"></a>해결 방법

이 오류의 원인에 따라 두 가지 잠재적인 솔루션이 있습니다. 
* 업로드한 인증서 체인이 올바른 형식(PEM)인지와 인증서 데이터가 제대로 분리되었는지 확인합니다. 
* 업로드한 인증서 파일에 구분 기호와 함께 인증서 데이터가 포함되어 있는지 확인합니다. 

### <a name="error-code-applicationgatewaytrustedclientcertificatedoesnotcontainanycacertificate"></a>오류 코드: ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate

#### <a name="cause"></a>원인

업로드된 인증서에는 CA 인증서가 없는 리프 인증서만 포함되어 있습니다. 인증서 체인을 CA 인증서와 리프 인증서로 업로드하는 것은 리프 인증서를 무시해도 되지만 인증서에는 CA가 있어야 하기 때문에 허용됩니다. 

#### <a name="solution"></a>해결 방법 

업로드된 인증서 체인에 리프 인증서만 포함되어 있지 않은지 다시 확인합니다. *BasicConstraintsOid* = "2.5.29.19" 확장이 있어야 하고 주체가 CA 역할을 할 수 있음을 나타냅니다.

### <a name="error-code-applicationgatewayonlyonerootcaallowedintrustedclientcertificate"></a>오류 코드: ApplicationGatewayOnlyOneRootCAAllowedInTrustedClientCertificate

#### <a name="cause"></a>원인

인증서 체인에 루트 CA 인증서가 여러 개 포함되어 *있거나* 루트 CA 인증서가 0개 포함되어 있습니다. 

#### <a name="solution"></a>해결 방법

업로드된 인증서는 정확히 하나의 루트 CA 인증서를 포함해야 하며 필요한 경우 많은 중간 CA 인증서를 포함해야 합니다.


