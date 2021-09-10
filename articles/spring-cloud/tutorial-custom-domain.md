---
title: '자습서: Azure Spring Cloud에 기존 사용자 지정 도메인 매핑'
description: 기존 사용자 지정 DNS(Distributed Name Service) 이름을 Azure Spring Cloud에 매핑하는 방법
author: karlerickson
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: bbead4e25df9646c08ec605694cfa7514b011522
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015405"
---
# <a name="tutorial-map-an-existing-custom-domain-to-azure-spring-cloud"></a>자습서: Azure Spring Cloud에 기존 사용자 지정 도메인 매핑

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

DNS(Domain Name Service)는 네트워크를 통해 네트워크 노드 이름을 저장하는 기술입니다. 이 자습서에서는 CNAME 레코드를 사용하여 www.contoso.com 같은 도메인을 매핑합니다. 이 기술은 인증서를 사용하여 사용자 지정 도메인을 보호하며 SSL(Secure Sockets Layer)이라고도 하는 TLS(전송 계층 보안)를 적용하는 방법을 보여줍니다.

인증서는 웹 트래픽을 암호화합니다. 이러한 TLS/SSL 인증서는 Azure Key Vault에 저장할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Spring Cloud에 배포된 애플리케이션. [빠른 시작: Azure Portal을 사용하여 기존 Azure Spring Cloud 애플리케이션 시작](./quickstart.md)을 참조하거나 기존 앱을 사용하세요.
* GoDaddy 같은 도메인 공급자의 DNS 레지스트리에 대한 액세스 권한이 있는 도메인
* 타사 공급자의 프라이빗 인증서(즉, 자체 서명된 인증서) 인증서는 도메인과 일치해야 합니다.
* 배포된 [Azure Key Vault](../key-vault/general/overview.md) 인스턴스

## <a name="keyvault-private-link-considerations"></a>Keyvault Private Link 고려 사항

Azure Spring Cloud 관리 IP는 아직 Azure Trusted Microsoft 서비스의 일부가 아닙니다. 따라서 Azure Spring Cloud가 프라이빗 엔드포인트 연결로 보호되는 Key Vault에서 인증서를 로드할 수 있도록 하려면 다음 IP를 Azure Key Vault Firewall에 추가해야 합니다. `20.53.123.160 52.143.241.210 40.65.234.114 52.142.20.14 20.54.40.121 40.80.210.49 52.253.84.152 20.49.137.168 40.74.8.134 51.143.48.243`

## <a name="import-certificate"></a>인증서 가져오기

### <a name="prepare-your-certificate-file-in-pfx-optional"></a>PFX에서 인증서 파일 준비(선택 사항)

Azure Key Vault는 PEM 및 PFX 형식의 프라이빗 인증서 가져오기를 지원합니다. 인증서 공급자로부터 가져온 PEM 파일이 아래 섹션에서 작동하지 않으면 다음을 수행합니다. [Key Vault에 인증서를 저장](#save-certificate-in-key-vault)하고 여기에 있는 단계에 따라 Azure Key Vault에 대한 PFX를 생성합니다.

#### <a name="merge-intermediate-certificates"></a>중간 인증서 병합

인증 기관에서 여러 인증서를 인증서 체인에 제공하면 인증서를 순서대로 병합해야 합니다.

이렇게 하려면 받은 각 인증서를 텍스트 편집기에서 엽니다.

_mergedcertificate.crt_ 라는 병합된 인증서의 파일을 만듭니다. 텍스트 편집기에서 각 인증서의 내용을 이 파일에 복사합니다. 사용자 인증서의 순서는 사용자의 인증서로 시작하고 루트 인증서로 끝나는 인증서 체인의 순서에 따라야 합니다. 다음 예제와 유사합니다.

```crt
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

#### <a name="export-certificate-to-pfx"></a>PFX로 인증서 내보내기

인증서 요청 생성에 사용된 프라이빗 키로 병합된 TLS/SSL 인증서를 내보냅니다.

OpenSSL을 사용하여 인증서 요청을 생성한 경우 프라이빗 키 파일을 만든 것입니다. 인증서를 PFX로 내보내려면 다음 명령을 실행합니다. 자리 표시자 _&lt;private-key-file&gt;_ 및 _&lt;merged-certificate-file&gt;_ 을 사용자의 프라이빗 키 및 병합된 인증서 파일에 대한 경로로 바꿉니다.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

메시지가 표시되면 내보내기 암호를 정의합니다. 나중에 TLS/SSL 인증서를 Azure Key Vault에 업로드할 때 이 암호를 사용합니다.

IIS 또는 _Certreq.exe_ 를 사용하여 인증서 요청을 생성한 경우 인증서를 로컬 컴퓨터에 설치한 다음 [해당 인증서를 PFX로 내보냅니다](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Key Vault에 인증서 저장

인증서를 가져오는 절차를 수행하려면 PEM 또는 PFX로 인코딩된 파일이 디스크에 있어야 하며 프라이빗 키가 필요합니다.

#### <a name="portal"></a>[포털](#tab/Azure-portal)
인증서를 주요 자격 증명 모음에 업로드하려면 다음을 수행합니다.
1. 키 자격 증명 모음 인스턴스로 이동합니다.
1. 왼쪽 탐색 창에서 **인증서** 를 선택합니다.
1. 상단 메뉴에서 **생성/가져오기** 를 선택합니다.
1. **인증서 만들기** 대화 상자의 **인증서를 만드는 방법** 에서 `Import`를 선택합니다.
1. **인증서 파일 업로드** 에서 인증서 위치로 이동하여 선택합니다.
1. **암호** 아래에서 암호로 보호된 인증서 파일을 업로드하는 경우 여기에 해당 암호를 입력합니다. 그렇지 않으면 비워 둡니다. 인증서 파일을 성공적으로 가져오면 키 자격 증명 모음에서 해당 암호를 제거합니다.
1. **만들기** 를 선택합니다.

    ![인증서 가져오기 1](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```

---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>키 자격 증명 모음에 Azure Spring Cloud 액세스 권한 부여

인증서를 가져오기 전에 키 자격 증명 모음에 Azure Spring Cloud 액세스 권한을 부여해야 합니다.

#### <a name="portal"></a>[포털](#tab/Azure-portal)
1. 키 자격 증명 모음 인스턴스로 이동합니다.
1. 왼쪽 탐색 창에서 **액세스 정책** 을 선택합니다.
1. 상단 메뉴에서 **액세스 정책 추가** 를 선택합니다.
1. 정보를 입력하고, **추가** 단추를 선택한 다음, 액세스 정책을 **저장** 합니다.

| 비밀 권한 | 인증서 권한 | 보안 주체 선택 |
|--|--|--|
| 가져오기, 목록 | 가져오기, 목록 | Azure Spring Cloud 도메인-관리 |

![인증서 가져오기 2](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

키 자격 증명 모음에 Azure Spring Cloud 읽기 액세스 권한을 부여하고, 다음 명령에서 *\<key vault resource group>* 및 *\<key vault name>* 을 바꿉니다.

```azurecli
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
```

---

### <a name="import-certificate-to-azure-spring-cloud"></a>Azure Spring Cloud로 인증서 가져오기

#### <a name="portal"></a>[포털](#tab/Azure-portal)
1. 서비스 인스턴스로 이동합니다.
1. 앱의 왼쪽 탐색 창에서 **TLS/SSL 설정** 을 선택합니다.
1. 그런 다음 **Key Vault 인증서 가져오기** 를 선택합니다.

    ![인증서 가져오기](./media/custom-dns-tutorial/import-certificate.png)

1. 인증서를 성공적으로 가져왔다면 **프라이빗 키 인증서** 목록에 표시됩니다.

    ![프라이빗 키 인증서](./media/custom-dns-tutorial/key-certificates.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```azurecli
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

가져온 인증서 목록을 표시하려면 다음을 수행합니다.

```azurecli
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```

---

> [!IMPORTANT]
> 이 인증서로 사용자 지정 도메인을 보호하려면 여전히 인증서를 특정 도메인에 바인딩해야 합니다. 다음 섹션의 단계를 따릅니다. [SSL 바인딩을 추가](#add-ssl-binding)합니다.

## <a name="add-custom-domain"></a>사용자 지정 도메인 추가
CNAME 레코드를 사용하여 사용자 지정 DNS 이름을 Azure Spring Cloud에 매핑할 수 있습니다.

> [!NOTE]
> A 레코드는 지원되지 않습니다.

### <a name="create-the-cname-record"></a>CNAME 레코드 만들기

DNS 공급자로 이동한 후 CNAME 레코드를 추가하여 도메인을 <service_name>.azuremicroservices.io에 매핑합니다. 여기서 <service_name>은 Azure Spring Cloud 인스턴스의 이름입니다. 와일드카드 도메인 및 하위 도메인을 지원합니다.
CNAME을 추가한 후 DNS 레코드 페이지는 다음 예제와 비슷합니다.

![DNS 레코드 페이지](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Azure Spring Cloud 앱에 사용자 지정 도메인 매핑
Azure Spring Cloud에 아직 애플리케이션이 없으면 [빠른 시작: Azure Portal을 사용하여 기존 Azure Spring Cloud 애플리케이션 시작](./quickstart.md)의 지침을 따릅니다.

#### <a name="portal"></a>[포털](#tab/Azure-portal)
애플리케이션 페이지로 이동합니다.

1. **사용자 지정 도메인** 을 선택합니다.
2. **사용자 지정 도메인 추가** 를 선택합니다.

    ![사용자 지정 도메인](./media/custom-dns-tutorial/custom-domain.png)

3. CNAME 레코드를 추가한 정규화된 도메인 이름을 입력합니다(예: www.contoso.com). 호스트 이름 레코드 형식이 CNAME으로 설정되었는지 확인합니다(<service_name>.azuremicroservices.io).
4. **유효성 검사** 를 선택하여 **추가** 단추를 사용하도록 설정합니다.
5. **추가** 를 선택합니다.

    ![사용자 지정 도메인 추가](./media/custom-dns-tutorial/add-custom-domain.png)

하나의 앱이 여러 도메인을 포함할 수 있지만, 하나의 도메인은 하나의 앱에만 매핑할 수 있습니다. 사용자 지정 도메인을 앱에 성공적으로 매핑했다면 사용자 지정 도메인 테이블에 표시됩니다.

![사용자 지정 도메인 테이블](./media/custom-dns-tutorial/custom-domain-table.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```azurecli
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

사용자 지정 도메인 목록을 표시하려면 다음을 수행합니다.

```azurecli
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```

---

> [!NOTE]
> 사용자 지정 도메인에 **안전하지 않음** 이라는 레이블이 있으면 아직 SSL 인증서에 바인딩되지 않았다는 뜻입니다. 브라우저에서 사용자 지정 도메인으로 전송되는 HTTPS 요청은 오류 또는 경고를 수신합니다.

## <a name="add-ssl-binding"></a>SSL 바인딩 추가

#### <a name="portal"></a>[포털](#tab/Azure-portal)
위의 그림처럼 사용자 지정 도메인 테이블에서 **SSL 바인딩 추가** 를 선택합니다.
1. **인증서** 를 선택하거나 가져옵니다.
1. **저장** 을 선택합니다.

    ![SSL 바인딩 추가 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```azurecli
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```

---

SSL 바인딩을 성공적으로 추가한 후에는 도메인 상태가 안전함을 나타내는 **정상** 으로 표시됩니다.

![SSL 바인딩 추가 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>HTTPS 적용

기본적으로 여전히 누구나 HTTP를 사용하여 앱에 액세스할 수 있지만, 모든 HTTP 요청을 HTTPS 포트로 리디렉션할 수 있습니다.
#### <a name="portal"></a>[포털](#tab/Azure-portal)
앱 페이지의 왼쪽 탐색 영역에서 **사용자 지정 도메인** 을 선택합니다. **HTTPS만 사용** 을 *True* 로 설정합니다.

![SSL 바인딩 추가 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```azurecli
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```

---

작업이 완료되면 앱을 가리키는 HTTPS URL 중 하나로 이동합니다. HTTP URL은 작동하지 않습니다.

## <a name="see-also"></a>참고 항목

* [Azure Key Vault란?](../key-vault/general/overview.md)
* [인증서 가져오기](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Azure CLI를 사용하여 Spring Cloud 앱 시작](./quickstart.md)
