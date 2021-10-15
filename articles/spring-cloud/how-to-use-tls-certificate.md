---
title: Azure 스프링 클라우드의 응용 프로그램에서 TLS/SSL 인증서 사용
titleSuffix: Azure Spring Cloud
description: 응용 프로그램에서 TLS/SSL 인증서를 사용 합니다.
author: karlerickson
ms.author: jieshe
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/08/2021
ms.custom: devx-track-java
ms.openlocfilehash: 44ea16782bb3bfdd51e89fd140f123f34fe4b44d
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067889"
---
# <a name="use-a-tlsssl-certificate-in-your-application-in-azure-spring-cloud"></a>Azure 스프링 클라우드의 응용 프로그램에서 TLS/SSL 인증서 사용

이 문서에서는 응용 프로그램에 대해 Azure 스프링 클라우드에서 공용 인증서를 사용 하는 방법을 보여 줍니다. 앱이 클라이언트 역할을 하 고 인증서 인증을 필요로 하는 외부 서비스에 액세스 하거나 암호화 작업을 수행 해야 할 수 있습니다.  

Azure 스프링 클라우드가 TLS/SSL 인증서를 관리 하도록 허용 하면 인증서와 응용 프로그램 코드를 별도로 유지 관리 하 여 중요 한 데이터를 보호할 수 있습니다. 앱 코드는 Azure 스프링 클라우드 인스턴스에 추가 하는 공용 인증서에 액세스할 수 있습니다.

> [!NOTE]
> 이 문서에는 Azure CLI 및 Terraform 지원 및 샘플이 곧 제공 될 예정입니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 스프링 클라우드에 배포 된 응용 프로그램입니다. [빠른 시작: Azure 스프링 클라우드의 첫 번째 응용 프로그램 배포](./quickstart.md)또는 기존 앱 사용을 참조 하세요.
- 확장명이 *.crt*, *.cer*, *pem* 또는 *.* n a n 인 인증서 파일 이거나, 개인 인증서를 사용 하는 Azure Key Vault의 배포 된 인스턴스입니다.

## <a name="import-a-certificate"></a>인증서 가져오기

Key Vault에서 Azure 스프링 클라우드 인스턴스로 인증서를 가져오도록 선택 하거나 로컬 인증서 파일을 사용할 수 있습니다.

### <a name="import-a-certificate-from-key-vault"></a>Key Vault에서 인증서 가져오기

다음 단계를 사용 하 여 인증서를 가져오기 전에 키 자격 증명 모음에 대 한 Azure 스프링 클라우드 액세스 권한을 부여 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **키 자격 증명 모음** 을 선택 하 고 인증서를 가져올 Key Vault를 선택 합니다.
1. 왼쪽 탐색 창에서 **액세스 정책** 을 선택 하 고 **+ 액세스 정책 추가** 를 선택 합니다.
1. **인증서 사용 권한** 을 선택한 다음, **가져오기** 및 **목록** 을 선택합니다.
1. 보안 **주체 선택** 에서 선택 **안 함** 을 선택한 다음 보안 주체를 선택 합니다.
1. **선택** 을 선택한 다음 **추가** 를 선택 합니다.

:::image type="content" source="media/use-tls-certificates/grant-key-vault-permission.png" alt-text="인증서를 가져오기 전에 키 자격 증명 모음에 대 한 액세스 권한을 부여 하는 스크린샷" lightbox="media/use-tls-certificates/grant-key-vault-permission.png":::

키 자격 증명 모음에 대 한 액세스 권한을 부여 하면 다음 단계를 사용 하 여 인증서를 가져올 수 있습니다.

1. 서비스 인스턴스로 이동합니다.
1. 인스턴스의 왼쪽 탐색 창에서 **TLS/SSL 설정** 을 선택 합니다.
1. **공개 키 인증서** 섹션에서 **Key Vault 인증서 가져오기** 를 선택 합니다.
1. **키 자격 증명 모음** 에서 Key Vault를 선택한 다음 **인증서** 에서 인증서를 선택 하 고 **적용** **을 선택 합니다** .
1. 인증서를 성공적으로 가져오면 공개 키 인증서의 목록에 표시 됩니다.

### <a name="import-a-local-certificate-file"></a>로컬 인증서 파일 가져오기

다음 단계를 사용 하 여 로컬에 저장 된 인증서 파일을 가져올 수 있습니다.

1. 서비스 인스턴스로 이동합니다.
1. 인스턴스의 왼쪽 탐색 창에서 **TLS/SSL 설정** 을 선택 합니다.
1. **공개 키 인증서** 섹션에서 **업로드 공용 인증서** 를 선택 합니다.
1. 인증서를 성공적으로 가져오면 공개 키 인증서의 목록에 표시 됩니다.

## <a name="load-a-certificate"></a>인증서 로드

Azure 스프링 클라우드의 응용 프로그램에 인증서를 로드 하려면 다음 단계를 시작 합니다.

1. 응용 프로그램 인스턴스로 이동 합니다.
1. 앱의 왼쪽 탐색 창에서 **인증서 관리** 를 선택 합니다.
1. **인증서 추가** 를 선택 하 여 앱에 액세스할 수 있는 인증서를 선택 합니다.

:::image type="content" source="media/use-tls-certificates/load-certificate.png" alt-text="응용 프로그램에 인증서를 로드 하는 위치 스크린샷" lightbox="media/use-tls-certificates/load-certificate.png":::

### <a name="load-a-certificate-from-code"></a>코드에서 인증서 로드

로드 된 인증서는 */etc/azure-spring-cloud/certs/public* 폴더에서 사용할 수 있습니다. 다음 Java 코드를 사용 하 여 Azure 스프링 클라우드의 응용 프로그램에서 공용 인증서를 로드 합니다.

```java
CertificateFactory factory = CertificateFactory.getInstance("X509");
FileInputStream is = new FileInputStream("/etc/azure-spring-cloud/certs/public/<certificate name>");
X509Certificate cert = (X509Certificate) factory.generateCertificate(is);

// use the loaded certificate
```

### <a name="load-a-certificate-into-the-trust-store"></a>인증서를 신뢰 저장소로 로드 합니다.

Java 응용 프로그램의 경우 선택한 인증서의 **신뢰 저장소에 로드** 를 선택할 수 있습니다. SSL 인증에서 서버를 인증 하기 위해 인증서가 Java 기본 TrustStores에 자동으로 추가 됩니다.

앱의 다음 로그는 인증서가 성공적으로 로드 되었음을 보여 줍니다.

```
Load certificate from specific path. alias = <certificate alias>, thumbprint = <certificate thumbprint>, file = <certificate name>
```

## <a name="next-steps"></a>다음 단계

* [엔드투엔드 전송 계층 보안 사용](./how-to-enable-end-to-end-tls.md)
* [Config Server 및 서비스 레지스트리 액세스](./how-to-access-data-plane-azure-ad-rbac.md)
