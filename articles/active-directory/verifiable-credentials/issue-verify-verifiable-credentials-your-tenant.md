---
title: 자습서 - Azure 테넌트를 사용하여 확인 가능한 자격 증명 발급 및 확인(미리 보기)
description: Azure 테넌트에서 작동하도록 확인 가능한 자격 증명 코드 샘플 변경
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: barclayn
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 3b2c4e49e7d7b892b581876918152acc4bbe6aa3
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006739"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>자습서 - 테넌트를 사용하여 확인 가능한 자격 증명 발급 및 확인(미리 보기)

> [!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이제 확인 가능한 자격 증명 서비스로 Azure 테넌트를 설정했으므로 샘플 앱을 사용하여 Azure AD(Azure Active Directory)에서 확인 가능한 자격 증명을 발급하는 데 필요한 단계를 살펴보겠습니다.

이 문서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure AD에 샘플 앱 등록
> * 규칙 및 표시 파일 만들기
> * 규칙 및 표시 파일 업로드
> * Azure Key Vault를 사용하도록 확인 가능한 자격 증명 발급자 서비스 설정
> * 테넌트 정보로 샘플 코드 업데이트

샘플 코드에서는 사용자가 ID 공급자, 특히 Azure AD B2C에 인증해야 확인된 자격 증명 전문가 VC를 발급할 수 있습니다. 모든 확인 가능한 자격 증명 발급자가 자격 증명 발급 전에 인증을 요구하는 것은 아닙니다.

ID 토큰을 인증하면 사용자가 자격 증명을 받기 전에 자신이 누구인지 증명할 수 있습니다. 사용자가 성공적으로 로그인하면 ID 공급자는 사용자에 대한 클레임이 포함된 보안 토큰을 반환합니다. 그러면 발급자 서비스는 이러한 보안 토큰과 해당 클레임을 확인 가능한 자격 증명으로 변환합니다. 확인 가능한 자격 증명은 발급자의 DID로 서명됩니다.

OpenID Connect 프로토콜을 지원하는 모든 ID 공급자가 지원됩니다. 지원되는 ID 공급자의 예로 [Azure Active Directory](../fundamentals/active-directory-whatis.md)와 [Azure AD B2C](../../active-directory-b2c/overview.md)가 있습니다. 이 자습서에서는 AAD를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 [이전 자습서](enable-your-tenant-verifiable-credentials.md)의 단계를 이미 완료했으며 사용한 환경에 액세스할 수 있다고 가정합니다.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>DID 지갑에서 사용자를 로그인할 수 있도록 앱 등록

확인 가능한 자격 증명을 발급하려면 Authenticator 또는 기타 확인 가능한 자격 증명 지갑에서 사용자 로그인을 허용하도록 앱을 등록해야 합니다.  

Azure AD에 'VC Wallet 앱'이라는 애플리케이션을 등록하고 클라이언트 ID를 가져옵니다.

1. [Azure AD](../develop/quickstart-register-app.md)에 애플리케이션 등록 지침을 따릅니다. 등록할 때 아래 값을 사용합니다.

   - 이름: "VC Wallet 앱"
   - 지원되는 계정 유형: 이 조직 디렉터리의 계정만
   - 리디렉션 URI: vcclient://openid/

   ![애플리케이션 등록](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. 애플리케이션을 등록한 후 애플리케이션(클라이언트) ID를 기록해 둡니다. 이 값은 나중에 필요합니다.

   ![애플리케이션 클라이언트 ID](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. **엔드포인트** 단추를 선택하고 OpenID Connect 메타데이터 문서 URI를 복사합니다. 이 정보는 다음 섹션에서 필요합니다. 

   ![발급자 엔드포인트](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)


지금까지 샘플 앱으로 작업했습니다. 앱에서는 [Azure Active Directory B2C](../../active-directory-b2c/overview.md)를 사용하며, 이제 Azure AD를 사용하도록 전환 중이므로 환경에 맞추고 이전에 사용되지 않은 추가 클레임을 지원하기 위해 몇 가지 변경 작업을 수행해야 합니다.

1. 아래의 규칙 파일을 복사하고 **modified-expertRules.js** 에 저장합니다. 

    > [!NOTE]
    > **"scope": "openid profile"** 은 이 규칙 파일에 포함되어 있으며 샘플 앱의 규칙 파일에는 포함되지 않았습니다. 다음 섹션에서는 Azure Active Directory 테넌트에서 선택적 클레임을 사용하도록 설정하는 방법을 설명합니다. 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. 파일을 열고 **client_id** 및 **구성** 값을 이전 섹션에서 복사한 두 값으로 바꿉니다.

    ![이 단계의 일부로 수정해야 하는 두 값 강조 표시](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  **구성** 값은 OpenID Connect 메타데이터 문서 URI입니다.

  샘플 코드는 Azure Active Directory B2C를 사용하고 있고 우리는 Azure Active Directory를 사용하고 있으므로 이러한 클레임이 확인 가능한 자격 증명에 기록될 ID 토큰에 포함되도록 범위를 통해 선택적 클레임을 추가해야 합니다. 

3. 다시 Azure Portal에서 Azure Active Directory를 엽니다.
4. **앱 등록** 을 선택합니다.
5. 앞에서 만든 VC Wallet 앱을 엽니다.
6. **토큰 구성** 을 선택합니다.
7. **+선택적 클레임 추가** 를 선택합니다.

     ![토큰 구성에서 선택적 클레임 추가](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. **토큰 형식** 에서 **ID** 를 선택하고 사용 가능한 클레임 목록에서 **given_name** 과 **family_name** 을 선택합니다.

     ![선택적 클레임 추가](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. **추가** 를 누릅니다.
10. 아래와 같이 권한 경고가 표시되면 확인란을 선택하고 **추가** 를 선택합니다.

     ![선택적 클레임에 대한 권한 추가](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

이제 사용자에게 확인 가능한 자격 증명을 발급하기 위한 "로그인"이 표시되면 VC Wallet 앱은 범위 매개 변수를 통해 특정 클레임을 포함하여 확인 가능한 자격 증명에 기록할 것을 알고 있습니다.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>이 규칙 파일 및 이전 표시 파일을 사용하여 새 VC 만들기

1. 컨테이너에 새 규칙 파일 업로드
1. 확인 가능한 자격 증명 페이지에서 이전 표시 파일과 새 규칙 파일(**modified-credentialExpert.json**)을 사용하여 **modifiedCredentialExpert** 라는 새 자격 증명을 만듭니다.
1. **개요** 페이지에서 자격 증명 만들기 프로세스가 완료되면 **자격 증명 발급 URL** 을 복사하고 다음 섹션에서 필요하므로 저장합니다.

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>Azure Key Vault에 대한 액세스 권한으로 노드 앱 설정

사용자의 자격 증명 발급 요청을 인증하기 위해 발급자 웹 사이트는 Azure Key Vault의 암호화 키를 사용합니다. Azure Key Vault에 액세스하려면 웹 사이트에 Azure Key Vault를 인증하는 데 사용할 수 있는 클라이언트 ID와 클라이언트 암호가 필요합니다.

먼저 다른 애플리케이션을 등록해야 합니다. 이 등록은 웹 사이트에 대한 것입니다. 이전의 전자지갑 앱에 대한 등록은 사용자가 전자지갑 앱을 사용하여 디렉터리에 로그인할 수 있도록 하는 것입니다. 이 경우 동일한 디렉터리에 있지만 다른 디렉터리에서도 전자지갑 앱 등록이 수행되었을 수 있습니다. 애플리케이션의 책임이 다른 경우 앱 등록을 분리하는 것이 좋습니다. 이 경우 웹 사이트에서 Key Vault에 액세스할 수 있어야 합니다.

1. [Azure AD](../develop/quickstart-register-app.md)에 애플리케이션 등록 지침을 따릅니다. 등록할 때 아래 값을 사용합니다.

   - 이름: "VC 웹 사이트"
   - 지원되는 계정 유형: 이 조직 디렉터리의 계정만

   :::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/vc-website-app-app-registration.png" alt-text="애플리케이션 등록 방법을 보여 주는 스크린샷":::

1. 애플리케이션을 등록한 후 애플리케이션(클라이언트) ID를 기록해 둡니다. 이 값은 나중에 필요합니다.

   :::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/vc-website-app-app-details.png" alt-text="애플리케이션 클라이언트 ID를 보여 주는 스크린샷":::

1. VC 웹 사이트 앱 개요 페이지를 보는 동안 **인증서 및 비밀** 을 선택합니다.

    :::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/vc-website-app-certificates-secrets.png" alt-text="인증서 및 비밀 창을 보여 주는 스크린샷":::

1. **클라이언트 암호** 섹션에서 **새 클라이언트 암호** 를 선택합니다.
    1. "노드 VC 클라이언트 암호"와 같은 설명을 추가합니다.
    1. 만료: 1년 후

    ![1년 후에 만료되는 애플리케이션 암호](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)

1. 비밀을 복사합니다. 샘플 노드 앱을 업데이트하려면 이 정보가 필요합니다.

>[!WARNING]
> 비밀을 복사할 수 있는 기회는 한 번입니다. 이후에는 비밀이 해시됩니다. ID를 복사하지 마세요. 

Azure AD에서 애플리케이션 및 클라이언트 암호를 만든 후에는 Key Vault에서 작업을 수행하는 데 필요한 권한을 애플리케이션에 부여해야 합니다. 웹 사이트에서 저장된 프라이빗 키에 액세스하고 사용할 수 있도록 하려면 이러한 권한을 변경해야 합니다.

1. Key Vault로 이동합니다.
2. 이러한 자습서에 사용 중인 키 자격 증명 모음을 선택합니다.
3. 왼쪽 탐색 창에서 **액세스 정책** 을 선택합니다.
4. **+액세스 정책 추가** 를 선택합니다.
5. **키 권한** 섹션에서 **가져오기** 와 **서명** 을 선택합니다.
6. **보안 주체** 를 선택하고 애플리케이션 ID를 사용하여 이전에 등록한 애플리케이션을 검색합니다. 이 폴더를 선택합니다.
7. **추가** 를 선택합니다.
8. **저장** 을 선택합니다.

:::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png" alt-text="액세스 정책 추가를 보여 주는 스크린샷":::

Key Vault 권한 및 액세스 제어에 대한 자세한 내용은 [Key Vault RBAC 가이드](../../key-vault/general/rbac-guide.md)를 참조하세요.

## <a name="make-changes-to-the-sample-app"></a>샘플 앱 변경

필요한 코드를 변경하기 전에 몇 가지 값을 함께 입력해야 합니다. 다음 섹션에서 이러한 값을 사용하여 샘플 코드에서 자격 증명 모음에 저장된 고유 키를 사용하도록 합니다. 다음 값이 준비되어 있어야 합니다.

- 방금 만든 자격 증명의 **계약 URI**(자격 증명 발급 URL)
- 노드 앱을 등록할 때 얻은 **애플리케이션 클라이언트 ID**
- 앱에 키 자격 증명 모음에 대한 액세스 권한을 부여할 때 만든 **클라이언트 암호**

샘플 앱에서 한 번 변경하기 전에 몇 가지 다른 값을 구해야 합니다. 이제 이들 값을 구하겠습니다.

### <a name="verifiable-credentials-settings"></a>확인 가능한 자격 증명 설정

1. 확인 가능한 자격 증명 페이지로 이동하고 **설정** 을 선택합니다.  
1. 다음 값을 복사합니다.

    - 테넌트 식별자 
    - 발급자 식별자(DID)
    - 키 자격 증명 모음(URI)

1. 서명 키 식별자 아래에 URI가 있지만 URI의 일부만 필요합니다. 아래 이미지에서 빨간색 사각형으로 강조 표시된 대로 **issuerSigningKeyION** 이라고 표시된 부분에서 복사합니다.

   ![로그인 키 식별자](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>DID 문서

1. [DIF ION Network Explorer](https://identity.foundation/ion/explorer/)를 엽니다.

2. 검색 창에 DID를 붙여 넣습니다.

4. 형식이 지정된 응답에서 **verificationMethod** 라는 섹션을 찾습니다.
5. "verificationMethod"에서 `id`를 복사하고 kvSigningKeyId로 레이블을 지정합니다.
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

이제 샘플 코드를 변경하는 데 필요한 모든 것이 있습니다.

- **발급자:** app.js, 새 계약 URI로 const 자격 증명 업데이트
- **확인자:** app.js, 발급자 식별자로 issuerDid 업데이트
- **발급자 및 확인자**  다음 값으로 didconfig.js 업데이트


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>이것은 데모 애플리케이션이며 일반적으로 애플리케이션에 직접 비밀을 제공하면 안 됩니다.


이제 자체 키를 사용하여 Azure Active Directory 테넌트에서 자체 확인 가능한 자격 증명을 발급하고 확인하는 데 필요한 모든 것이 준비되었습니다. 

## <a name="issue-and-verify-the-vc"></a>VC 발급 및 확인

이전 자습서에서 수행했던 것과 동일한 단계에 따라 확인 가능한 자격 증명을 발급하고 앱으로 유효성을 검사합니다. 확인 프로세스를 성공적으로 완료하면 이제 확인 가능한 자격 증명에 대해 계속 학습할 준비가 된 것입니다.

1. 명령 프롬프트를 열고 발급자 폴더를 엽니다.
2. 업데이트된 노드 앱을 실행합니다.

    ```terminal
    node app.js
    ```

3. 다른 명령 프롬프트를 사용하여 ngrok를 실행하고 URL을 8081에 설정합니다.

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > 이 앱이나 웹 사이트가 위험할 수 있다는 경고도 표시될 수 있습니다. 아직 DID를 도메인에 연결하지 않았기 때문에 현재 이 메시지가 표시될 수 있습니다. 이 내용을 구성하려면 [DNS 바인딩](how-to-dnsbind.md) 지침을 참조하세요.

    
4. ngrok에서 생성된 HTTPS URL을 엽니다.

    ![NGROK 전달 엔드포인트](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. **자격 증명 가져오기** 를 선택합니다.
6. Authenticator가 QR 코드를 스캔합니다.
7. **이 앱 또는 웹 사이트는 위험할 수 있습니다** 경고 메시지에서 **고급** 을 선택합니다.

  ![초기 경고](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. 위험한 웹 사이트에서 경고에서 **계속 진행(안전하지 않음)** 을 선택합니다.

  ![발급자에 대한 두 번째 경고](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. **자격 증명 추가** 화면에서 다음 몇 가지를 확인합니다. 
    1. 화면 맨 위에 빨간색 **확인 안 됨** 메시지가 보입니다.
    1. 디스플레이 파일의 변경 사항을 기반으로 자격 증명이 사용자 지정되었습니다.
    1. **귀하 계정에 로그인** 옵션은 Azure AD 로그인 페이지를 가리킵니다.
    
   ![경고가 있는 자격 증명 추가 화면](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. **귀하 계정에 로그인** 을 선택하고 Azure AD 테넌트에서 사용자를 사용하여 인증합니다.
11. 인증에 성공하면 **추가** 단추가 더 이상 회색으로 표시되지 않습니다. **추가** 를 선택합니다.

  ![인증 후 자격 증명 추가 화면](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

이제 인증을 위해 원래 B2C 테넌트를 사용하면서 vc를 생성하기 위해 테넌트를 사용하여 확인 가능한 자격 증명을 발급했습니다.

  ![Azure AD에서 발급되고 Azure B2C 인스턴스에서 인증된 VC](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>샘플 앱을 사용하여 VC 확인 테스트

이제 Azure AD의 클레임을 사용하여 자체 테넌트에서 확인 가능한 자격 증명을 발급했으므로 샘플 앱을 사용하여 확인해 보겠습니다.

1. 발급자 ngrok 서비스 실행을 중지합니다.

    ```cmd
    control-c
    ```

2. 이제 ngrok를 검증 도구 포트 8082를 사용하여 실행합니다.

    ```cmd
    ngrok http 8082
    ```

3. 다른 터미널 창에서 검증 도구 앱으로 이동하여 발급자 앱을 실행한 방법과 유사하게 실행합니다.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. 브라우저에서 ngrok URL을 열고 모바일 디바이스에서 Authenticator를 사용하여 QR 코드를 스캔합니다.
5. 모바일 디바이스의 **새 권한 요청** 화면에서 **허용** 을 선택합니다.

   >[!IMPORTANT]
    > 샘플 앱도 DID를 사용하여 프레젠테이션 요청에 서명하기 때문에 이 앱 또는 웹 사이트가 위험할 수 있다는 경고가 표시됩니다. 아직 DID를 도메인에 연결하지 않았기 때문에 현재 이 메시지가 표시될 수 있습니다. 이 내용을 구성하려면 [DNS 바인딩](how-to-dnsbind.md) 지침을 참조하세요.
    
   ![새 권한 요청](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. 이제 자격 증명을 성공적으로 확인했으며 웹 사이트에 Azure AD 사용자 계정의 이름과 성이 표시됩니다. 

귀하는 자습서를 완료했으므로 공식적으로 확인된 자격 증명 전문가입니다. 샘플 앱은 발급 및 확인을 위해 DID를 사용하는 동시에 Azure AD의 확인 가능한 자격 증명에 클레임을 작성합니다. 

## <a name="next-steps"></a>다음 단계

- [사용자 지정 자격 증명](credential-design.md)을 만드는 방법 알아보기
- 발급자 서비스 통신 [예](issuer-openid.md)
