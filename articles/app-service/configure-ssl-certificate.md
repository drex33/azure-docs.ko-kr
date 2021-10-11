---
title: TLS/SSL 인증서 추가 및 관리
description: Azure App Service에서 무료 인증서를 만들거나, App Service 인증서를 가져오거나, Key Vault 인증서를 가져오거나, App Service 인증서를 구입합니다.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 05/13/2021
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: cf15fd428c7e487b82823586be6edfd21f6cab48
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057401"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Azure App Service에서 TLS/SSL 인증서 추가

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 문서에서는 프라이빗 인증서 또는 공용 인증서를 만들거나, 업로드하거나, App Service로 가져오는 방법을 보여줍니다. 

인증서가 App Service 앱 또는 [함수 앱](../azure-functions/index.yml)에 추가되면 [인증서로 사용자 지정 DNS 이름을 보호](configure-ssl-bindings.md)하거나 [애플리케이션 코드에서 인증서를 사용](configure-ssl-certificate-in-code.md)할 수 있습니다.

> [!NOTE]
> 앱에 업로드된 인증서는 App Service 요금제의 리소스 그룹 및 지역 조합에 바인딩된 배포 단위(내부적으로 ‘웹 공간’이라고 함)에 저장됩니다. 이렇게 하면 동일한 리소스 그룹 및 지역 조합에 있는 다른 앱에서 인증서에 액세스할 수 있습니다. 

다음 표에는 App Service에서 인증서를 추가할 수 있는 옵션이 나열되어 있습니다.

|옵션|설명|
|-|-|
| 무료 App Service 관리형 인증서 만들기 | App Service에서 [사용자 지정 도메인](app-service-web-tutorial-custom-domain.md)을 보호하기만 하면 되는 경우 무료이며 간편하게 사용할 수 있는 프라이빗 인증서입니다. |
| App Service 인증서 구매 | Azure에서 관리하는 프라이빗 인증서입니다. 간편한 자동 인증서 관리의와 유연한 갱신 및 내보내기 옵션이 결합되었습니다. |
| Key Vault에서 인증서 가져오기 | [Azure Key Vault](../key-vault/index.yml)를 사용하여 [PKCS12 인증서](https://wikipedia.org/wiki/PKCS_12)를 관리하는 경우에 유용합니다. [프라이빗 인증서 요구 사항](#private-certificate-requirements)을 참조하세요. |
| 프라이빗 인증서 업로드 | 타사 공급자의 프라이빗 인증서가 이미 있는 경우 해당 인증서를 업로드할 수 있습니다. [프라이빗 인증서 요구 사항](#private-certificate-requirements)을 참조하세요. |
| 공용 인증서 업로드 | 공용 인증서는 사용자 지정 도메인을 보호하는 데 사용되지 않지만, 원격 리소스에 액세스할 때 필요한 경우 공용 인증서를 코드에 로드할 수 있습니다. |

## <a name="prerequisites"></a>필수 구성 요소

- [App Service 앱을 만듭니다](./index.yml).
- 프라이빗 인증서의 경우 [App Service의 모든 요구 사항](#private-certificate-requirements)을 충족하는지 확인합니다.
- **무료 인증서 전용**:
    - 인증서를 사용할 도메인을 App Service에 매핑합니다. 자세한 내응은 [자습서: Azure App Service에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)을 참조하세요.
    - 루트 도메인(예: contoso.com)의 경우 구성된 [IP 제한](app-service-ip-restrictions.md)이 없는지 확인합니다. 인증서 생성과 루트 도메인에 대한 정기 갱신은 모두 인터넷에서 연결할 수 있는 앱에 따라 달라집니다.

## <a name="private-certificate-requirements"></a>프라이빗 인증서 요구 사항

[무료 App Service 관리형 인증서](#create-a-free-managed-certificate) 및 [App Service 인증서](#import-an-app-service-certificate)는 이미 App Service 요구 사항을 충족합니다. 프라이빗 인증서를 App Service에 업로드하거나 가져오기로 선택하는 경우 인증서가 다음 요구 사항을 충족해야 합니다.

* 삼중 DES를 사용하여 암호화된 [암호로 보호된 PFX 파일](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)로 내보냅니다.
* 길이가 2048비트 이상인 프라이빗 키 포함
* 인증서 체인의 모든 중간 인증서와 루트 인증서를 포함합니다.

TLS 바인딩에서 사용자 지정 도메인을 보호하려면 인증서가 다음과 같은 추가 요구 사항을 충족해야 합니다.

* 서버 인증용 [확장 키 사용](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) 포함(OID = 1.3.6.1.5.5.7.3.1)
* 신뢰할 수 있는 인증 기관에서 서명됨

> [!NOTE]
> **ECC(타원 곡선 암호화) 인증서** 는 App Service에서 사용할 수 있지만 이 문서에서는 다루지 않습니다. ECC 인증서를 만드는 정확한 단계에서 인증 기관을 사용하세요.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-managed-certificate"></a>무료 관리형 인증서 만들기

> [!NOTE]
> 무료 관리형 인증서를 만들기 전에 앱의 [필수 구성 요소를 충족](#prerequisites)하는지 확인합니다.

무료 App Service 관리형 인증서는 App Service에서 사용자 지정 DNS 이름을 보호하는 데 사용되는 턴키 솔루션입니다. 이 인증서는 App Service에서 완전히 관리되는 TLS/SSL 서버 인증서이며 만료되기 45일 전에 6개월 단위로 지속적으로 자동으로 갱신됩니다. 인증서를 만들고 사용자 지정 도메인에 바인딩하면 App Service가 나머지 작업을 수행합니다.

무료 인증서에는 다음과 같은 제한이 있습니다.

- 와일드카드 인증서를 지원하지 않습니다.
- 인증서 지문을 클라이언트 인증서로 사용하도록 지원하지 않습니다(인증서 지문 제거 예정).
- 내보낼 수 없습니다.
- 공개적으로 액세스할 수 없는 App Service에서는 지원되지 않습니다.
- ASE(App Service Environment)에서 지원되지 않습니다.
- Traffic Manager와 통합된 루트 도메인에서 지원되지 않습니다.
- 인증서가 CNAME 매핑된 도메인용인 경우 CNAME을 `<app-name>.azurewebsites.net`에 직접 매핑해야 합니다.

> [!NOTE]
> 무료 인증서는 DigiCert에서 발급됩니다. 일부 도메인의 경우 `0 issue digicert.com` 값으로 [CAA 도메인 레코드](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization)를 만들어 DigiCert를 인증서 발급자로 명시적으로 허용해야 합니다.
> 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>의 왼쪽 메뉴에서 **App Services** >  **\<app-name>** 를 선택합니다.

앱의 왼쪽 탐색 영역에서 **TLS/SSL 설정** > **프라이빗 키 인증서(.pfx)**  > **App Service Managed Certificate 만들기** 를 선택합니다.

![App Service에서 무료 인증서 만들기](./media/configure-ssl-certificate/create-free-cert.png)

무료 인증서를 만들 사용자 지정 도메인을 선택하고 **만들기** 를 선택합니다. 지원되는 사용자 지정 도메인마다 인증서를 하나씩만 만들 수 있습니다.

작업이 완료되면 **프라이빗 키 인증서** 목록에 인증서가 표시됩니다.

![무료 인증서 만들기 완료](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> 이 인증서를 사용하여 사용자 지정 도메인을 보호하려면 여전히 인증서 바인딩을 만들어야 합니다. [바인딩 만들기](configure-ssl-bindings.md#create-binding)의 단계를 따릅니다.
>

## <a name="import-an-app-service-certificate"></a>App Service Certificate 가져오기

Azure에서 App Service Certificate를 구매하는 경우 Azure에서 다음 작업을 관리합니다.

- GoDaddy의 구매 프로세스를 담당합니다.
- 인증서의 도메인 확인을 수행합니다.
- [Azure Key Vault](../key-vault/general/overview.md)에 인증서를 유지합니다.
- 인증서 갱신을 관리합니다([인증서 갱신](#renew-an-app-service-certificate) 참조).
- App Service 앱으로 가져온 복사본과 인증서를 자동으로 동기화합니다.

App Service 인증서를 구매하려면 [인증서 주문 시작](#start-certificate-order)으로 이동합니다.

작동 중인 App Service 인증서가 이미 있는 경우 다음을 수행할 수 있습니다.

- [인증서를 App Service로 가져옵니다](#import-certificate-into-app-service).
- 갱신, 키 다시 입력, 내보내기 등 [인증서를 관리합니다](#manage-app-service-certificates).
> [!NOTE]
> App Service 인증서는 현재 Azure 국가별 클라우드에서 지원되지 않습니다.

### <a name="start-certificate-order"></a>인증서 주문 시작

<a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service Certificate 만들기 페이지</a>에서 App Service Certificate 주문을 시작합니다.

![App Service 인증서 구매 시작](./media/configure-ssl-certificate/purchase-app-service-cert.png)

다음 표를 사용하여 인증서를 구성할 수 있습니다. 작업을 마쳤으면 **만들기** 를 클릭합니다.

| 설정 | Description |
|-|-|
| Name | App Service Certificate에 대한 식별 이름입니다. |
| Naked 도메인 호스트 이름 | 여기서 루트 도메인을 지정합니다. 발급된 인증서는 루트 도메인과 `www` 하위 도메인을 *모두* 보호합니다. 발급된 인증서의 일반 이름 필드에는 루트 도메인이 포함되고, 주체 대체 이름 필드에는 `www` 도메인이 포함됩니다. 하위 도메인만 보호하려면 여기에 하위 도메인의 정규화된 도메인 이름을 지정합니다(예: `mysubdomain.contoso.com`).|
| Subscription | 인증서를 포함할 구독입니다. |
| Resource group | 인증서를 포함할 리소스 그룹입니다. 예를 들어, 새로운 리소스 그룹을 사용하거나 App Service 앱과 동일한 리소스 그룹을 선택할 수 있습니다. |
| 인증서 SKU | 표준 인증서 또는 [와일드 카드 인증서](https://wikipedia.org/wiki/Wildcard_certificate) 여부에 관계없이 만들려는 인증서 유형을 결정합니다. |
| 약관 | 약관에 동의하는지 확인하려면 클릭합니다. GoDaddy에서 인증서가 옵니다. |

> [!NOTE]
> Azure에서 구매한 App Service 인증서는 GoDaddy에 의해 발급됩니다. 일부 도메인의 경우 `0 issue godaddy.com` 값으로 [CAA 도메인 레코드](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization)를 만들어 GoDaddy를 인증서 발급자로 명시적으로 허용해야 합니다.
> 

### <a name="store-in-azure-key-vault"></a>Azure Key Vault에 저장

인증서 구매 프로세스가 완료되고 나면 이 인증서를 사용하기 전에 완료해야 할 단계가 몇 가지 더 있습니다. 

[App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) 페이지에서 인증서를 선택한 다음, **인증서 구성** > **1단계: 저장** 을 클릭합니다.

![App Service 인증서의 Key Vault 스토리지 구성](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](../key-vault/general/overview.md)는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 되는 Azure 서비스입니다. App Service Certificate에 대해 선택한 스토리지입니다.

**Key Vault 상태** 페이지에서 **Key Vault 리포지토리** 를 클릭하여 새 자격 증명 모음을 만들거나 기존 자격 증명 모음을 선택합니다. 새 자격 증명 모음을 만들려면 다음 표를 사용하여 자격 증명 모음을 구성하고 만들기를 클릭합니다. App Service 앱과 동일한 구독 및 리소스 그룹 내에 새 Key Vault를 만듭니다.

| 설정 | Description |
|-|-|
| Name | 영숫자와 대시로 구성된 고유한 이름입니다. |
| Resource group | 권장 사항으로, App Service Certificate과 동일한 리소스 그룹을 선택합니다. |
| 위치 | App Service 앱과 동일한 위치를 선택합니다. |
| 가격 책정 계층 | 자세한 내용은 [Azure Key Vault 가격 책정 정보](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요. |
| 액세스 정책| 애플리케이션 및 자격 증명 모음 리소스에 허용된 액세스를 정의합니다. 나중에 [Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md)의 단계에 따라 구성할 수 있습니다. |
| Virtual Network 액세스 | 특정 Azure Virtual Network에 대한 자격 증명 모음 액세스 액세스를 제한합니다. 나중에 [Azure Key Vault 방화벽 및 Virtual Network 구성](../key-vault/general/network-security.md)의 단계에 따라 구성할 수 있습니다. |

자격 증명 모음을 선택한 후에는 **Key Vault 리포지토리** 페이지를 닫습니다. **1단계: 저장소** 옵션에는 성공을 나타내는 녹색 확인 표시가 나타납니다. 다음 단계를 위해 페이지를 열어둡니다.

> [!NOTE]
> 현재 App Service Certificate는 Key Vault 액세스 정책만 지원하지만 RBAC 모델은 지원하지 않습니다.
>

### <a name="verify-domain-ownership"></a>도메인 소유권 확인

마지막 단계에서 사용한 것과 동일한 **인증서 구성** 페이지에서 **2단계: 확인** 을 클릭합니다.

![App Service 인증서의 도메인 확인](./media/configure-ssl-certificate/verify-domain.png)

**App Service 확인** 을 선택합니다. 도메인을 웹앱에 이미 매핑했기 때문에([필수 구성 요소](#prerequisites) 참조), 이미 확인되었습니다. 간단히 **확인** 을 클릭하여 단계를 마칩니다. **인증서 도메인이 확인됨** 메시지가 나타날 때까지 **Refresh** 단추를 누릅니다.

> [!NOTE]
> 네 가지 유형의 도메인 확인 방법이 지원됩니다. 
> 
> - **App Service**  - 도메인이 동일한 구독의 App Service 앱에 이미 매핑된 경우 가장 편리한 옵션입니다. App Service 앱이 도메인 소유권을 이미 확인했다는 사실을 활용합니다.
> - **도메인** - [Azure에서 구매한 App Service 도메인](manage-custom-dns-buy-domain.md)을 확인합니다. Azure는 사용자에게 자동으로 확인 TXT 레코드를 추가하고 프로세스를 완료합니다.
> - **이메일** - 도메인 관리자에게 이메일을 보내서 도메인을 확인합니다. 옵션을 선택하면 지침이 제공됩니다.
> - **수동** - HTML 페이지(**표준** 인증서만) 또는 DNS TXT 레코드를 사용하여 도메인을 확인합니다. 옵션을 선택하면 지침이 제공됩니다.

### <a name="import-certificate-into-app-service"></a>App Service로 인증서 가져오기

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>의 왼쪽 메뉴에서 **App Services** >  **\<app-name>** 를 선택합니다.

앱의 왼쪽 탐색 영역에서 **TLS/SSL 설정** > **프라이빗 키 인증서(.pfx)**  > **App Service Certificate 가져오기** 를 선택합니다.

![App Service에서 App Service 인증서 가져오기](./media/configure-ssl-certificate/import-app-service-cert.png)

방금 구매한 인증서를 선택하고 **확인** 을 선택합니다.

작업이 완료되면 **프라이빗 키 인증서** 목록에 인증서가 표시됩니다.

![App Service 인증서 가져오기 완료](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> 이 인증서를 사용하여 사용자 지정 도메인을 보호하려면 여전히 인증서 바인딩을 만들어야 합니다. [바인딩 만들기](configure-ssl-bindings.md#create-binding)의 단계를 따릅니다.
>

## <a name="import-a-certificate-from-key-vault"></a>Key Vault에서 인증서 가져오기

Azure Key Vault를 사용하여 인증서를 관리하는 경우 Key Vault의 [요구 사항을 충족](#private-certificate-requirements)하는 PKCS12 인증서를 App Service으로 가져올 수 있습니다.

### <a name="authorize-app-service-to-read-from-the-vault"></a>자격 증명 모음에서 읽을 App Service 권한 부여
기본적으로 App Service 리소스 공급자는 Key Vault에 대한 액세스 권한이 없습니다. 인증서 배포에 Key Vault를 사용하려면 [KeyVault에 대한 읽기 액세스 권한을 부여](../key-vault/general/assign-access-policy-cli.md)해야 합니다. 

`abfa0a7c-a6b6-4736-8310-5855508787cd`는 App Service의 리소스 공급자 서비스 주체 이름으로, 모든 Azure 구독에 대해 동일합니다. Azure Government 클라우드 환경의 경우 리소스 공급자 서비스 주체 이름으로 대신 `6a02c803-dafd-4136-b4c3-5a6f318b4714`를 사용합니다.

> [!NOTE]
> 현재 Key Vault 인증서는 Key Vault 액세스 정책만 지원하지만 RBAC 모델은 지원하지 않습니다.
> 

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>자격 증명 모음에서 앱으로 인증서 가져오기

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>의 왼쪽 메뉴에서 **App Services** >  **\<app-name>** 를 선택합니다.

앱의 왼쪽 탐색 영역에서 **TLS/SSL 설정** > **프라이빗 키 인증서(.pfx)**  > **Key Vault 인증서 가져오기** 를 선택합니다.

![App Service에서 Key Vault 인증서 가져오기](./media/configure-ssl-certificate/import-key-vault-cert.png)

다음 표를 사용하여 인증서를 선택합니다.

| 설정 | Description |
|-|-|
| Subscription | Key Vault가 속한 구독입니다. |
| Key Vault | 가져오려는 인증서가 포함된 자격 증명 모음입니다. |
| 인증서 | 자격 증명 모음의 PKCS12 인증서 목록에서 선택합니다. 자격 증명 모음의 모든 PKCS12 인증서가 해당 지문과 함께 나열되지만, 모두 App Service에서 지원되지는 않습니다. |

작업이 완료되면 **프라이빗 키 인증서** 목록에 인증서가 표시됩니다. 오류가 발생하여 가져오기에 실패하는 경우 인증서가 [App Service에 대한 요구 사항](#private-certificate-requirements)을 충족하지 않는 것입니다.

![Key Vault 인증서 가져오기 완료](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> Key Vault의 인증서를 새 인증서로 업데이트하면 App Service는 24시간 이내에 인증서를 자동으로 동기화합니다.

> [!IMPORTANT] 
> 이 인증서를 사용하여 사용자 지정 도메인을 보호하려면 여전히 인증서 바인딩을 만들어야 합니다. [바인딩 만들기](configure-ssl-bindings.md#create-binding)의 단계를 따릅니다.
>

## <a name="upload-a-private-certificate"></a>프라이빗 인증서 업로드

인증서 공급자로부터 인증서를 가져온 후 이 섹션의 단계를 수행하여 App Service에 적합하게 준비합니다.

### <a name="merge-intermediate-certificates"></a>중간 인증서 병합

인증 기관에서 여러 인증서를 인증서 체인에 제공하면 인증서를 순서대로 병합해야 합니다.

이렇게 하려면 받은 각 인증서를 텍스트 편집기에서 엽니다.

_mergedcertificate.crt_ 라는 병합된 인증서의 파일을 만듭니다. 텍스트 편집기에서 각 인증서의 내용을 이 파일에 복사합니다. 사용자 인증서의 순서는 사용자의 인증서로 시작하고 루트 인증서로 끝나는 인증서 체인의 순서에 따라야 합니다. 다음 예제와 유사합니다.

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>PFX로 인증서 내보내기

인증서 요청 생성에 사용된 프라이빗 키로 병합된 TLS/SSL 인증서를 내보냅니다.

OpenSSL을 사용하여 인증서 요청을 생성한 경우 프라이빗 키 파일을 만든 것입니다. 인증서를 PFX로 내보내려면 다음 명령을 실행합니다. 자리 표시자 _&lt;private-key-file&gt;_ 및 _&lt;merged-certificate-file&gt;_ 을 사용자의 프라이빗 키 및 병합된 인증서 파일에 대한 경로로 바꿉니다.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

메시지가 표시되면 내보내기 암호를 정의합니다. 나중에 TLS/SSL 인증서를 App Service에 업로드할 때 이 암호를 사용합니다.

IIS 또는 _Certreq.exe_ 를 사용하여 인증서 요청을 생성한 경우 인증서를 로컬 컴퓨터에 설치한 다음 [해당 인증서를 PFX로 내보냅니다](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="upload-certificate-to-app-service"></a>App Service에 인증서 업로드

이제 인증서를 App Service에 업로드할 준비가 완료되었습니다.

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>의 왼쪽 메뉴에서 **App Services** >  **\<app-name>** 를 선택합니다.

앱의 왼쪽 탐색 영역에서 **TLS/SSL 설정** > **프라이빗 키 인증서(.pfx)**  > **인증서 업로드** 를 선택합니다.

![App Service에서 프라이빗 인증서 업로드](./media/configure-ssl-certificate/upload-private-cert.png)

**PFX 인증서 파일** 에서 PFX 파일을 선택합니다. **인증서 암호** 에서 PFX 파일을 내보낼 때 만든 암호를 입력합니다. 여기까지 마쳤으면 **업로드** 를 클릭합니다. 

작업이 완료되면 **프라이빗 키 인증서** 목록에 인증서가 표시됩니다.

![인증서 업로드 완료](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> 이 인증서를 사용하여 사용자 지정 도메인을 보호하려면 여전히 인증서 바인딩을 만들어야 합니다. [바인딩 만들기](configure-ssl-bindings.md#create-binding)의 단계를 따릅니다.

## <a name="upload-a-public-certificate"></a>공용 인증서 업로드

공용 인증서는 *.cer* 형식으로 지원됩니다. 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>의 왼쪽 메뉴에서 **App Services** >  **\<app-name>** 를 선택합니다.

앱의 왼쪽 탐색 영역에서 **TLS/SSL 설정** > **공용 인증서(.cer)**  > **공개 키 인증서 업로드** 를 선택합니다.

**이름** 에 인증서 이름을 입력합니다. **CER 인증서 파일** 에서 CER 파일을 선택합니다.

**업로드** 를 클릭합니다.

![App Service에서 공용 인증서 업로드](./media/configure-ssl-certificate/upload-public-cert.png)

인증서가 업로드되면 인증서 지문을 복사하고 [인증서에 액세스할 수 있도록 설정](configure-ssl-certificate-in-code.md#make-the-certificate-accessible)을 참조합니다.

## <a name="renew-an-expiring-certificate"></a>만료되는 인증서 갱신

인증서가 만료되기 전에 갱신된 인증서를 App Service에 추가하고 모든 [TLS/SSL 바인딩](configure-ssl-certificate.md)을 업데이트해야 합니다. 프로세스는 인증서 유형에 따라 다릅니다. 예를 들어 [App Service 인증서](#import-an-app-service-certificate)를 포함하여 [Key Vault에서 가져온 인증서](#import-a-certificate-from-key-vault)는 24시간마다 App Service에 자동으로 동기화되며 인증서를 갱신할 때 TLS/SSL 바인딩을 업데이트합니다. [업로드된 인증서](#upload-a-private-certificate)의 경우 자동 바인딩 업데이트가 없습니다. 시나리오에 따라 다음 섹션 중 하나를 참조하세요.

- [업로드된 인증서 갱신](#renew-an-uploaded-certificate)
- [App Service 인증서 갱신](#renew-an-app-service-certificate)
- [Key Vault에서 가져온 인증서 갱신](#renew-a-certificate-imported-from-key-vault)

### <a name="renew-an-uploaded-certificate"></a>업로드된 인증서 갱신

만료되는 인증서를 교체하기 위해 새 인증서로 인증서 바인딩을 업데이트하는 방법은 사용자 환경에 부정적인 영향을 줄 수 있습니다. 예를 들어 바인딩이 IP 기반이라고 해도 바인딩을 삭제하면 인바운드 IP 주소가 변경될 수 있습니다. 이것은 IP 기반 바인딩에 이미 있는 인증서를 갱신할 때 특히 중요합니다. 앱의 IP 주소가 변경되지 않도록 하고 HTTPS 오류로 인한 앱의 가동 중지 시간을 방지하려면 다음 단계를 순서대로 수행합니다.

1. [새 인증서를 업로드합니다](#upload-a-private-certificate).
2. 만료되는 기존 인증서를 삭제하지 않고 [동일한 사용자 지정 도메인에 새 인증서를 바인딩합니다](configure-ssl-bindings.md). 이 작업은 기존 인증서 바인딩을 제거하는 대신 바인딩을 교체합니다.
3. 기존 인증서를 삭제합니다.

### <a name="renew-an-app-service-certificate"></a>App Service 인증서 갱신

> [!NOTE]
> 2021년 9월 23일부터 App Service 인증서는 395일마다 도메인 유효성 검사를 수행해야 합니다. App Service Managed Certificate와 달리 App Service Certificate에 대한 도메인 다시 유효성 검사는 자동화되지 않습니다.

> [!NOTE]
> 갱신 프로세스를 수행하려면 [App Service의 잘 알려진 서비스 주체에게 키 자격 증명 모음에 대한 필수 권한이 있어야 합니다](deploy-resource-manager-template.md#deploy-web-app-certificate-from-key-vault). 이 권한은 포털을 통해 App Service Certificate를 가져올 때 구성되며 키 자격 증명 모음에서 제거하면 안 됩니다.

언제든 App Service 인증서의 자동 갱신 설정을 전환하려면 [App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) 페이지에서 인증서를 선택한 다음, 왼쪽 탐색 영역에서 **자동 갱신 설정** 을 클릭합니다. 기본적으로 App Service 인증서의 유효 기간은 1년입니다.

**켜기** 또는 **끄기** 를 선택하고 **저장** 을 클릭합니다. 자동 갱신을 켜 놓으면 인증서가 만료 31일 전에 자동으로 갱신됩니다.

![App Service 인증서 자동 갱신](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

인증서를 수동으로 갱신하려면 **수동 갱신** 을 클릭합니다. 만료 60일 전에 인증서를 자동으로 갱신하도록 요청할 수 있습니다.

갱신 작업이 완료되면 **동기화** 를 클릭합니다. 동기화 작업에서는 앱 가동 중지 시간 없이 App Service의 인증서에 대한 호스트 이름 바인딩을 자동으로 업데이트합니다.

> [!NOTE]
> **동기화** 를 클릭하지 않으면 App Service는 24시간 이내에 인증서를 자동으로 동기화합니다.

### <a name="renew-a-certificate-imported-from-key-vault"></a>Key Vault에서 가져온 인증서 갱신

Key Vault에서 App Service로 가져온 인증서를 갱신하려면 [Azure Key Vault 인증서 갱신](../key-vault/certificates/overview-renew-certificate.md)을 참조하세요.

키 자격 증명 모음에서 인증서가 갱신되면 App Service는 새 인증서를 자동으로 동기화하고 24시간 이내에 적용 가능한 TLS/SSL 바인딩을 업데이트합니다. 수동으로 동기화하려면 다음을 수행합니다.

1. 앱의 **TLS/SSL 설정** 페이지로 이동합니다.
1. **프라이빗 키 인증서** 에서 가져온 인증서를 선택합니다.
1. **동기화** 를 클릭합니다. 

## <a name="manage-app-service-certificates"></a>Azure App 인증서 관리

이 섹션에서는 [구매한 App Service 인증서](#import-an-app-service-certificate)를 관리하는 방법을 보여줍니다.

- [인증서 키 다시 입력](#rekey-certificate)
- [인증서 내보내기](#export-certificate)
- [인증서 삭제](#delete-certificate)

또한 [App Service 인증서 갱신](#renew-an-app-service-certificate)을 참조하세요.

### <a name="rekey-certificate"></a>인증서 키 다시 입력

인증서의 프라이빗 키가 손상되었다고 생각되는 경우 인증서 키를 다시 입력할 수 있습니다. [App Service 인증서](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) 페이지에서 인증서를 선택한 다음, 왼쪽 탐색 영역에서 **키 다시 입력 및 동기화** 를 선택합니다.

**키 다시 입력** 을 클릭하여 프로세스를 시작합니다. 이 프로세스는 완료하는 데 1-10분 정도 걸릴 수 있습니다.

![App Service 인증서 키 다시 입력](./media/configure-ssl-certificate/rekey-app-service-cert.png)

인증서 키를 다시 생성하면 인증서가 인증 기관에서 발급한 새 인증서로 롤링됩니다.

키 다시 입력 작업이 완료되면 **동기화** 를 클릭합니다. 동기화 작업에서는 앱 가동 중지 시간 없이 App Service의 인증서에 대한 호스트 이름 바인딩을 자동으로 업데이트합니다.

> [!NOTE]
> **동기화** 를 클릭하지 않으면 App Service는 24시간 이내에 인증서를 자동으로 동기화합니다.

### <a name="export-certificate"></a>인증서 내보내기

App Service 인증서는 [Key Vault 비밀](../key-vault/general/about-keys-secrets-certificates.md)이므로, PFX 복사본을 내보내서 다른 Azure 서비스에 또는 Azure 외부에서 사용할 수 있습니다.

App Service 인증서를 PFX 파일로 내보내려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다. [Azure CLI를 설치한 경우](/cli/azure/install-azure-cli) 로컬로 실행할 수도 있습니다. 자리 표시자를 [App Service 인증서를 만들 때](#start-certificate-order) 사용한 이름으로 바꿉니다.

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

다운로드한 *appservicecertificate.pfx* 파일은 공용 인증서와 프라이빗 인증서를 모두 포함하는 원시 PKCS12 파일입니다. 각 프롬프트에서 가져오기 암호 및 PEM 전달 구에 빈 문자열을 사용합니다.

### <a name="delete-certificate"></a>인증서 삭제 

App Service 인증서를 삭제하면 다시 되돌릴 수 없습니다. App Service Certificate 리소스를 삭제하면 인증서가 해지됩니다. 이 인증서와 App Service의 모든 바인딩이 유효하지 않게 됩니다. 실수로 인한 삭제를 방지하기 위해 Azure에서 인증서를 잠급니다. App Service 인증서를 삭제하려면 먼저 인증서에 대한 삭제 잠금을 제거해야 합니다.

[App Service 인증서](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) 페이지에서 인증서를 선택한 다음, 왼쪽 탐색 영역에서 **잠금** 을 선택합니다.

잠금 유형 **삭제** 를 사용하여 인증서에 대한 잠금을 찾습니다. 인증서 오른쪽에서 **삭제** 를 선택합니다.

![App Service 인증서에 대한 삭제 잠금](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

이제 App Service 인증서를 삭제할 수 있습니다. 왼쪽 탐색 영역에서 **개요** > **삭제** 를 선택합니다. 확인 대화 상자에서 인증서 이름을 입력하고 **확인** 을 선택합니다.

## <a name="automate-with-scripts&quot;></a>스크립트를 사용하여 자동화

### <a name=&quot;azure-cli&quot;></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 &quot;Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>추가 리소스

* [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)
* [HTTPS 적용](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 적용](configure-ssl-bindings.md#enforce-tls-versions)
* [Azure App Service의 코드에서 TLS/SSL 인증서 사용](configure-ssl-certificate-in-code.md)
* [FAQ: App Service Certificate](./faq-configuration-and-management.yml)
