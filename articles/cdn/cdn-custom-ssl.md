---
title: '자습서: Azure CDN 사용자 지정 도메인에서 HTTPS 구성'
description: 이 자습서에서는 Azure CDN 엔드포인트 사용자 지정 도메인에서 HTTPS를 활성화하거나 비활성화하는 방법을 알아봅니다.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: duau
ms.custom: mvc
ms.openlocfilehash: d6a56aecaf8110118081e8c79e86bae5c277d992
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458687"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>자습서: Azure CDN 사용자 지정 도메인에서 HTTPS 구성

이 자습서에서는 Azure CDN 엔드포인트에 연결된 사용자 지정 도메인에 HTTPS 프로토콜을 사용하도록 설정하는 방법을 보여줍니다. 

사용자 지정 도메인의 HTTPS 프로토콜(예: https:\//www.contoso.com)은 중요한 데이터가 TLS/SSL을 통해 안전하게 전송되도록 보장합니다. 웹 브라우저가 HTTPS를 통해 연결되면 브라우저는 웹 사이트의 인증서를 확인합니다. 브라우저는 인증서가 합법적인 인증 기관에서 발급되었는지 확인합니다. 이 프로세스는 보안을 제공하며 공격으로부터 웹 애플리케이션을 보호합니다.

Azure CDN은 기본적으로 CDN 엔드포인트에서 HTTPS를 지원합니다. 예를 들어 CDN 엔드포인트를 만드는 경우(예: https:\//contoso.azureedge.net) HTTPS가 자동으로 사용하도록 설정됩니다.  

사용자 지정 HTTPS 기능의 몇 가지 주요 특성은 다음과 같습니다.

- 추가 비용 없음: 인증서 얻기 또는 갱신에 대해 비용이 없으며 HTTPS 트래픽에 대한 추가 비용이 없습니다. CDN에서 송신되는 양(GB)에 대한 비용만 지불합니다.

- 간단한 사용: [Azure Portal](https://portal.azure.com)에서 원클릭 프로비저닝을 사용할 수 있습니다. REST API 또는 기타 개발자 도구를 사용하여 기능을 활성화할 수도 있습니다.

- 완전한 인증서 관리: 
    * 사용자를 위해 모든 인증서 조달 및 관리가 처리됩니다. 
    * 인증서는 만료되기 전에 자동으로 프로비저닝 및 갱신됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - 사용자 지정 도메인에서 HTTPS 프로토콜을 사용하도록 설정
> - CDN 관리되는 인증서 사용 
> - 사용자 고유의 인증서 사용
> - 도메인의 유효성 검사
> - 사용자 지정 도메인에서 HTTPS 프로토콜을 사용하지 않도록 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

이 자습서의 단계를 완료하려면 먼저 CDN 프로필과 하나 이상의 CDN 엔드포인트를 만들어야 합니다. 자세한 내용은 [빠른 시작: Azure CDN 프로필 및 엔드포인트 만들기](cdn-create-new-endpoint.md)를 참조하세요.

CDN 엔드포인트에서 Azure CDN 사용자 지정 도메인을 연결합니다. 자세한 내용은 [자습서: Azure CDN 엔드포인트에 사용자 지정 도메인 추가](cdn-map-content-to-custom-domain.md)를 참조하세요.

> [!IMPORTANT]
> CDN 관리 인증서는 루트 또는 정점 도메인에서 제공되지 않습니다. Azure CDN 사용자 지정 도메인이 루트 또는 정점 도메인인 경우 자체 인증서 가져오기 기능을 사용해야 합니다. 
>

---

## <a name="tlsssl-certificates"></a>TLS/SSL 인증서

Azure CDN 사용자 지정 도메인에서 HTTPS를 사용하도록 설정하려면 TLS/SSL 인증서를 사용합니다. Azure CDN에서 관리되는 인증서를 사용하거나 사용자의 인증서를 사용하도록 선택합니다.

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[옵션 1(기본값): CDN 관리되는 인증서를 사용하여 HTTPS 활성화](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Azure CDN은 조달 및 갱신과 같은 인증서 관리 작업을 처리합니다. 기능을 활성화하면 프로세스는 즉시 시작됩니다. 

사용자 지정 도메인이 CDN 엔드포인트에 이미 매핑된 경우 추가적인 조치가 필요하지 않습니다. Azure CDN은 단계를 처리하고 자동으로 요청을 완료합니다.

사용자 지정 도메인이 다른 곳에 매핑된 경우 이메일을 사용하여 도메인 소유권의 유효성을 검사합니다.

사용자 지정 도메인에서 HTTPS를 활성화하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com)로 이동하여 Azure CDN에서 관리하는 인증서를 찾습니다. **CDN 프로필** 을 검색하고 선택합니다. 

2. 프로필 선택:
    * **Microsoft의 Azure CDN 표준**
    * **Akamai의 Azure CDN 표준**
    * **Verizon의 Azure CDN 표준**
    * **Verizon의 Azure CDN Premium**

3. CDN 엔드포인트 목록에서 사용자 지정 도메인을 포함하고 있는 엔드포인트를 선택합니다.

    ![엔드포인트 목록](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    **엔드포인트** 페이지가 열립니다.

4. 사용자 지정 도메인 목록에서 HTTPS를 사용하도록 설정할 사용자 지정 도메인을 선택합니다.

    ![스크린샷은 내 인증서 사용 옵션이 있는 사용자 지정 도메인 페이지를 보여줍니다.](./media/cdn-custom-ssl/cdn-custom-domain.png)

    **사용자 지정 도메인** 페이지가 나타납니다.

5. 인증서 관리 유형에서 **CDN 관리** 를 선택합니다.

6. **켜기** 를 선택하여 HTTPS를 사용하도록 설정합니다.

    ![사용자 지정 도메인 HTTPS 상태](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. 계속해서 [도메인의 유효성 검사](#validate-the-domain)를 진행합니다.


# <a name="option-2-enable-https-with-your-own-certificate"></a>[옵션 2: 사용자 고유의 인증서를 사용하여 HTTPS 활성화](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> 이 옵션은 **Microsoft의 Azure CDN** 및 **Verizon의 Azure CDN** 프로필에서만 사용할 수 있습니다. 
>
 
사용자 고유의 인증서를 사용하여 HTTPS 기능을 활성화합니다. 이 프로세스는 인증서를 안전하게 저장할 수 있도록 하는 Azure Key Vault와의 통합을 통해 수행됩니다. Azure Front Door는 이 보안 메커니즘을 사용하여 인증서를 가져오며, 몇 가지 추가 단계를 수행해야 합니다. TLS/SSL 인증서를 만들 때 [Microsoft 신뢰할 수 있는 CA 목록](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)의 일부인 허용된 CA(인증 기관)를 사용하여 전체 인증서 체인을 만들어야 합니다. 허용되지 않는 CA를 사용하는 경우 요청이 거부됩니다.  전체 체인이 없는 인증서가 표시되면 해당 인증서와 관련된 요청이 예상대로 작동하지 않을 수 있습니다. Verizon에서 Azure CDN의 경우 유효한 모든 CA가 허용됩니다.

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Azure 키 자격 증명 모음 계정 및 인증서 준비
 
1. Azure Key Vault: 사용자 지정 HTTPS를 활성화하려는 Azure CDN 프로필 및 CDN 엔드포인트와 동일한 구독에서 Azure Key Vault 계정을 실행해야 합니다. 아직 Azure Key Vault 계정이 없는 경우 새로 하나 만듭니다.
 
2. Azure Key Vault 인증서: 인증서가 있는 경우 Azure Key Vault 계정에 직접 업로드합니다. 인증서가 없는 경우 Azure Key Vault를 통해 직접 새 인증서를 만듭니다.

> [!NOTE]
> 인증서에는 리프 및 중간 인증서가 있는 완전한 인증서 체인이 있어야 하며 루트 CA는 [Microsoft 신뢰할 수 있는 CA 목록](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)의 일부여야 합니다.

### <a name="register-azure-cdn"></a>Azure CDN 등록

PowerShell을 통해 Azure Active Directory에서 앱으로 Azure CDN을 등록합니다.

1. 필요한 경우 [Azure PowerShell](/powershell/azure/install-az-ps)을 로컬 머신에 설치합니다.

2. PowerShell에서 다음 명령을 실행합니다.

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** 은 **Microsoft.AzureFrontDoor-Cdn** 의 서비스 주체입니다.

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>키 자격 증명 모음에 Azure CDN 액세스 권한 부여
 
Azure Key Vault 계정에서 인증서(비밀)에 액세스하려면 Azure CDN 사용 권한을 부여합니다.

1. 키 자격 증명 모음의 **설정** 섹션에서 **액세스 정책** 을 선택합니다. 오른쪽 창에서 **+ 액세스 정책 추가** 를 선택합니다.

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="CDN에 대한 키 자격 증명 모음 액세스 정책 만들기" border="true":::

2. **액세스 정책 추가** 페이지에서 **주체 선택 **옆에 있는** 선택하지 않음** 을 선택합니다. **주체** 페이지에서 **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** 을 입력합니다. **Microsoft.AzureFrontdoor-Cdn** 을 선택합니다.  **선택** 을 선택합니다.

3. **보안 주체 선택** 에서 **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** 을 검색하고, **Microsoft.AzureFrontDoor-Cdn** 을 선택합니다. **선택** 을 선택합니다.

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="Azure CDN의 서비스 주체를 선택합니다." border="true":::
    
4. **인증서 사용 권한** 을 선택합니다. **가져오기** 및 **나열** 확인란을 선택하여 CDN 권한으로 인증서를 가져오고 나열할 수 있도록 합니다.

5. **비밀 권한** 을 선택합니다. **가져오기** 및 **나열** 확인란을 선택하여 CDN 권한으로 비밀을 가져오고 나열할 수 있도록 합니다.

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="keyvault에 대한 CDN 권한 선택" border="true":::

6. **추가** 를 선택합니다. 

> [!NOTE]
> Azure CDN은 이제 이 키 자격 증명 모음에 저장된 이 키 자격 증명 모음 및 인증서(비밀)에 액세스할 수 있습니다. 이 구독에서 만든 모든 CDN 인스턴스는 이 키 자격 증명 모음의 인증서에 액세스할 수 있습니다.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>배포할 Azure CDN에 대한 인증서 선택
 
1. Azure CDN 포털로 돌아와서 사용자 지정 HTTPS를 활성화하려는 프로필 및 CDN 엔드포인트를 선택합니다. 

2. 사용자 지정 도메인 목록에서 HTTPS를 사용하도록 설정할 사용자 지정 도메인을 선택합니다.

    **사용자 지정 도메인** 페이지가 나타납니다.

3. 인증서 관리 유형에서 **내 인증서 사용** 을 선택합니다. 

    :::image type="content" source="./media/cdn-custom-ssl/cdn-configure-your-certificate.png" alt-text="cdn 엔드포인트에 대한 인증서를 구성하는 방법의 스크린샷.":::

4. 키 자격 증명 모음, 인증서/비밀 및 인증서/비밀 버전을 선택합니다.

    Azure CDN은 다음 정보를 나열합니다. 
    - 구독 ID에 대한 키 자격 증명 모음 계정 
    - 선택한 키 자격 증명 모음 아래의 인증서/비밀입니다. 
    - 사용 가능한 인증서/비밀 버전입니다.
 
    > [!NOTE]
    > Key Vault에서 최신 버전의 인증서를 사용할 수 있을 때 인증서를 최신 버전으로 자동 회전하려면 인증서/비밀 버전을 '최신'으로 설정하세요. 특정 버전을 선택한 경우 인증서 회전을 위해 새 버전을 수동으로 다시 선택해야 합니다. 새 버전의 인증서/비밀을 배포하는 데 최대 24시간이 걸립니다. 
   
5. **켜기** 를 선택하여 HTTPS를 사용하도록 설정합니다.
  
6. 사용자의 인증서를 사용할 경우 도메인 유효성 검사가 필요하지 않습니다. 계속해서 [전파 대기](#wait-for-propagation)를 진행합니다.

---

## <a name="validate-the-domain"></a>도메인의 유효성 검사

사용 중인 사용자 지정 도메인이 CNAME 레코드가 있는 사용자 지정 엔드포인트에 매핑되어 있거나 사용자 고유의 인증서를 사용하는 경우, [CDN 엔드포인트에 매핑된 사용자 지정 도메인](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record)을 진행합니다. 

그렇지 않고 엔드포인트의 CNAME 레코드 항목이 더 이상 존재하지 않거나 cdnverify 하위 도메인을 포함하는 경우, [CDN 엔드포인트에 매핑되지 않은 사용자 지정 도메인](#custom-domain-isnt-mapped-to-your-cdn-endpoint)을 진행합니다.

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>CNAME 레코드를 통해 사용자 지정 도메인이 CDN 엔드포인트에 매핑됨

엔드포인트에 사용자 지정 도메인을 추가할 때 CDN 엔드포인트 호스트 이름에 매핑된 DNS 도메인 등록 기관에서 CNAME 레코드를 만들었습니다. 

해당 CNAME 레코드가 여전히 있고 cdnverify 하위 도메인을 포함하지 않는 경우 DigiCert CA는 이 레코드를 사용하여 사용자 지정 도메인의 소유권을 자동으로 확인합니다. 

사용자 고유의 인증서를 사용 중인 경우 도메인 유효성 검사가 필요하지 않습니다.

CNAME 레코드는 다음과 같은 형식이어야 합니다.

* *이름* 은 사용자 지정 도메인 이름입니다.
* *값* 은 CDN 엔드포인트 호스트 이름입니다.

| 속성            | Type  | 값                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

CNAME 레코드에 대한 자세한 내용은 [CNAME DNS 레코드 만들기](./cdn-map-content-to-custom-domain.md)를 참조하세요.

CNAME 레코드가 올바른 형식이면 DigiCert는 사용자 지정 도메인 이름을 자동으로 확인하고 도메인에 대한 인증서를 만듭니다. DigitCert는 확인 메일을 보내지 않으며 요청을 승인할 필요가 없습니다. 인증서는 1년 동안 유효하며 만료되기 전에 자동으로 갱신됩니다. 계속해서 [전파 대기](#wait-for-propagation)를 진행합니다. 

자동 유효성 검사는 일반적으로 몇 시간이 걸립니다. 24시간 내에 도메인의 유효성이 확인되지 않으면 지원 티켓을 엽니다.

>[!NOTE]
>DNS 공급자가 포함된 CAA(Certificate Authority Authorization) 레코드가 있으면 DigiCert가 유효한 CA로 포함되어야 합니다. CAA 레코드를 사용하면 도메인 소유자가 자신의 도메인에 대한 인증서를 발급할 권한이 있는 CA를 DNS 공급자로 지정할 수 있습니다. CA에서 CAA 레코드가 있는 도메인에 대한 인증서 주문을 받고 해당 CA가 인증된 발급자로 나열되지 않은 경우 해당 도메인 또는 하위 도메인에 인증서를 발급할 수 없습니다. CAA 레코드 관리에 대한 내용은 [CAA 레코드 관리](https://support.dnsimple.com/articles/manage-caa-record/)를 참조하세요. CAA 레코드 도구는 [CAA 레코드 도우미](https://sslmate.com/caa/)를 참조하세요.

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>사용자 지정 도메인이 CDN 엔드포인트에 매핑되지 않음

>[!NOTE]
>**Akamai의 Azure CDN** 을 사용하는 경우 자동 도메인 유효성 검사를 사용하도록 다음 CNAME을 설정해야 합니다. "_acme-challenge.&lt;사용자 지정 도메인 호스트 이름&gt; -> CNAME -> &lt;사용자 지정 도메인 호스트 이름&gt;.ak-acme-challenge.azureedge.net"

CNAME 레코드 항목에 cdnverify 하위 도메인이 포함된 경우 이 단계의 나머지 지침을 따릅니다.

DigiCert는 다음 이메일 주소로 확인 이메일을 보냅니다. 다음 주소 중 하나에서 직접 승인할 수 있는지 확인합니다.

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

잠시 후 승인 요청 이메일을 받게 됩니다. 스팸 필터를 사용하는 경우 verification@digicert.com을 허용 목록에 추가합니다. 24시간 이내에 전자 메일을 받지 못하면 Microsoft 지원에 문의하세요.
    
![도메인 유효성 검사 전자 메일](./media/cdn-custom-ssl/domain-validation-email.png)

승인 링크를 선택하면 다음과 같은 온라인 승인 양식으로 이동됩니다. 
    
![도메인 유효성 검사 양식](./media/cdn-custom-ssl/domain-validation-form.png)

양식의 지침을 따르세요. 다음과 같이 두 가지 확인 옵션이 있습니다.

- 동일한 루트 도메인의 동일한 계정을 통해 이루어진 모든 향후 주문을 승인할 수 있습니다(예: consoto.com). 이 방법은 동일한 루트 도메인에 대해 다른 사용자 지정 도메인을 추가하려는 경우에 권장됩니다.

- 이 요청에 사용된 특정 호스트 이름을 승인할 수 있습니다. 이후 요청에 다른 승인이 필요합니다.

승인 후에 DigiCert는 사용자 지정 도메인 이름에 대한 인증서 생성을 완료합니다. 인증서는 1년 동안 유효하며 만료되기 전 자동으로 갱신됩니다.

## <a name="wait-for-propagation"></a>전파 대기

도메인 이름이 확인된 후 사용자 지정 도메인 HTTPS 기능이 활성 상태가 될 때까지는 최대 6-8시간 소요됩니다. 프로세스가 완료되면 Azure Portal의 사용자 지정 HTTPS 상태가 **사용** 으로 변경됩니다. 사용자 지정 도메인 대화 상자의 네 가지 작업 단계가 완료로 표시됩니다. 사용자 지정 도메인은 이제 HTTPS를 활성화할 준비가 되었습니다.

![HTTPS 대화 상자 활성화](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>작업 진행 상태

다음 표에는 HTTPS를 활성화하도록 설정했을 때 나타나는 작업 진행률을 표시합니다. HTTPS를 사용하도록 설정하면 네 가지 작업 단계가 사용자 지정 도메인 대화 상자에 나타납니다. 각 단계가 활성화되면 진행되는 동안 단계 아래에 다른 하위 단계 정보가 표시됩니다. 이러한 하위 단계가 모두 발생하지는 않습니다. 단계가 성공적으로 완료되면 옆에 녹색 확인 표시가 나타납니다. 

| 작업 단계 | 작업 하위 단계 정보 | 
| --- | --- |
| 1 요청 제출 | 요청 제출 |
| | HTTPS 요청을 제출하는 중입니다. |
| | HTTPS 요청이 제출되었습니다. |
| 2 도메인 유효성 검사 | 도메인이 CDN 엔드포인트에 매핑된 CNAME인 경우 자동으로 유효성이 검사됩니다. 그렇지 않으면 도메인의 등록 레코드(WHOIS 등록자)에 나열된 이메일로 확인 요청이 발송됩니다.|
| | 도메인 소유권을 확인했습니다. |
| | 도메인 소유권 유효성 검사 요청이 만료되었습니다(고객이 6일 이내의 응답하지 않았음). 도메인에서 HTTPS를 사용할 수 없습니다. * |
| | 고객에 의해 도메인 소유권 유효성 검사 요청이 거부되었습니다. 도메인에서 HTTPS를 사용할 수 없습니다. * |
| 3 인증서 프로비전 | 인증 기관이 현재 도메인에서 HTTPS를 활성화하는 데 필요한 인증서를 발급 중입니다. |
| | 인증서가 발급되었고 현재 CDN 네트워크로 배포되고 있습니다. 최대 6시간이 걸릴 수 있습니다. |
| | 인증서가 CDN 네트워크에 배포되었습니다. |
| 4 완료 | 도메인에서 HTTPS를 활성화하도록 설정했습니다. |

\* 오류가 발생하지 않으면 이 메시지가 표시되지 않습니다. 

요청을 제출하기 전에 오류가 발생하는 경우 다음과 같은 오류 메시지가 표시됩니다.

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>리소스 정리 - HTTPS를 사용하지 않도록 설정

이 섹션에서는 사용자 지정 도메인에 대해 HTTPS를 사용하지 않도록 설정하는 방법을 알아봅니다.

### <a name="disable-the-https-feature"></a>HTTPS 기능을 사용하지 않도록 설정 

1. [Azure Portal](https://portal.azure.com)에서 **CDN 프로필** 을 검색하고 선택합니다. 

2. **Microsoft의 Azure CDN 표준**, **Verizon의 Azure CDN 표준** 또는 **Verizon의 Azure CDN 프리미엄** 프로필을 선택합니다.

3. 엔드포인트 목록에서 사용자 지정 도메인을 포함하는 엔드포인트를 선택합니다.

4. HTTPS를 비활성화하도록 설정할 사용자 지정 도메인을 선택합니다.

    ![사용자 지정 도메인 목록](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. **끄기** 를 선택하여 HTTPS를 비활성화한 다음, **적용** 을 선택합니다.

    ![사용자 지정 HTTPS 대화 상자](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>전파 대기

사용자 지정 도메인 HTTPS 기능을 비활성화한 후 최대 6-8시간까지 걸릴 수 있습니다. 프로세스가 완료되면 Azure Portal의 사용자 지정 HTTPS 상태가 **사용 안 함** 으로 변경됩니다. 사용자 지정 도메인 대화 상자의 세 가지 작업 단계가 완료로 표시됩니다. 사용자 지정 도메인은 더 이상 HTTPS를 사용할 수 없습니다.

![HTTPS 대화 상자 비활성화](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>작업 진행 상태

다음 표에는 HTTPS를 비활성화하도록 설정했을 때 나타나는 작업 진행률을 표시합니다. HTTPS를 사용하지 않도록 설정하면 세 가지 작업 단계가 사용자 지정 도메인 대화 상자에 나타납니다. 각 단계가 활성화되면 단계 아래에 세부 정보가 표시됩니다. 단계가 성공적으로 완료되면 옆에 녹색 확인 표시가 나타납니다. 

| 작업 진행 상태 | 작업 세부 정보 | 
| --- | --- |
| 1 요청 제출 | 요청 제출 |
| 2 인증서 프로비전 해제 | 인증서 삭제 |
| 3 완료 | 인증서 삭제됨 |

## <a name="frequently-asked-questions"></a>질문과 대답

1. *인증서 공급자는 누구이며 어떤 유형의 인증서가 사용되나요?*

    Digicert에서 제공한 전용 인증서가 사용자 지정 도메인에 사용됩니다.
    
    * **Verizon의 Azure CDN**
    * **Microsoft의 Azure CDN**

2. *IP 기반 또는 SNI TLS/SSL을 사용하나요?*

    **Verizon의 Azure CDN** 및 **Microsoft의 Azure CDN 표준** 은 모두 SNI TLS/SSL을 사용합니다.

3. *DigiCert로부터 도메인 확인 메일을 받지 못한 경우 어떻게 하나요?*

    *cdnverify* 하위 도메인을 사용하지 않고 CNAME 항목이 엔드포인트 호스트 이름용인 경우 도메인 확인 이메일을 받지 못합니다.
     
    유효성 검사가 자동으로 수행됩니다. 그렇지 않은 경우 CNAME 항목이 없고 24시간 내에 이메일을 받지 못했으면 Microsoft 지원에 문의하세요.

4. *SAN 인증서를 사용하는 것보다 전용 인증서를 사용하는 것이 더 안전한가요?*
    
    SAN 인증서는 전용 인증서와 동일한 암호화 및 보안 표준을 따릅니다. 발급된 모든 TLS/SSL 인증서는 향상된 서버 보안을 위해 SHA-256을 사용합니다.

5. *내 DNS 공급자에게 CAA(Certificate Authority Authorization) 레코드가 필요합니까?*

    CAA 레코드는 현재 필요하지 않습니다. 그러나 이 레코드가 있으면 DigiCert가 유효한 CA로 포함되어야 합니다.

6. *기본적으로 2018년 6월 20일에 SNI TLS/SSL에서 전용 인증서를 사용하여 Verizon의 Azure CDN을 시작했습니다. SAN(주체 대체 이름) 인증서와 IP 기반 TLS/SSL을 사용하는 기존 사용자 지정 도메인은 어떻게 되나요?*

    Microsoft가 애플리케이션에 대한 SNI 클라이언트 요청이 있는지 분석하는 경우 기존 도메인은 향후 몇 달 안에 단일 인증서로 점진적으로 마이그레이션됩니다. 
    
    비 SNI 클라이언트가 감지되면 도메인은 IP 기반 TLS/SSL을 사용하여 SAN 인증서에 유지됩니다. SNI가 아닌 서비스 또는 클라이언트에 대한 요청은 영향을 받지 않습니다.

7. *사용자 고유의 인증서 가져오기로 인증서를 갱신하는 방법*

    최신 인증서가 PoP 인프라에 배포되었는지 확인하려면 새 인증서를 Azure KeyVault에 업로드합니다. Azure CDN의 TLS 설정에서 최신 인증서 버전을 선택하고 저장을 선택합니다. 그러면 Azure CDN이 새로 업데이트된 인증서를 전파합니다. 

8. *엔드포인트가 다시 시작된 후 HTTPS를 다시 사용하도록 설정해야 하나요?*

    예. **Akamai의 Azure CDN** 을 사용하는 경우 엔드포인트가 중지되었다가 다시 시작되는 경우 설정이 이전에 활성화된 경우 HTTPS 설정을 다시 활성화해야 합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> - 사용자 지정 도메인에서 HTTPS 프로토콜을 사용하도록 설정
> - CDN 관리되는 인증서 사용 
> - 사용자 고유의 인증서 사용
> - 도메인의 유효성 검사
> - 사용자 지정 도메인에서 HTTPS 프로토콜을 사용하지 않도록 설정합니다.

그 다음 자습서로 넘어가서 CDN 엔드포인트에서 캐싱을 구성하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: Azure CDN 캐싱 규칙 설정](cdn-caching-rules-tutorial.md)
