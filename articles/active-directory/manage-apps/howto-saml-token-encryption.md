---
title: SAML 토큰 암호화
description: Azure Active Directory SAML 토큰 암호화를 구성하는 방법을 알아봅니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: davidmu
ms.reviewer: alamaral
ms.collection: M365-identity-device-management
ms.openlocfilehash: d15e4425b7506ada2ac1dcaf9f83bb4112a21639
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617647"
---
# <a name="configure-azure-active-directory-saml-token-encryption"></a>Azure Active Directory SAML 토큰 암호화 구성

> [!NOTE]
> 토큰 암호화는 Azure AD(Azure Active Directory) 프리미엄 기능입니다. Azure AD 버전, 기능 및 가격 책정에 대한 자세한 내용은 [Azure AD 가격 책정](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)을 참조하세요.

SAML 토큰 암호화에서는 암호화된 SAML 어셜선을 지원되는 애플리케이션에서 이 어셜선 기능을 사용할 수 있습니다. 애플리케이션용으로 구성한 Azure AD는 Azure AD에 저장된 인증서에서 가져온 공개 키를 사용하여 해당 애플리케이션용으로 내보낸 SAML 어설션을 암호화합니다. 애플리케이션이 먼저 일치하는 프라이빗 키를 사용하여 토큰 암호를 해독해야만 로그인한 사용자의 인증 증명 정보로 사용할 수 있습니다.

Azure AD와 애플리케이션 간의 SAML 어설션을 암호화하면 토큰 콘텐츠를 가로챌 수 없고, 개인 또는 회사 데이터를 손상시킬 수 없도록 추가적으로 보장됩니다.

토큰 암호화를 사용하지 않더라도 Azure AD SAML 토큰이 네트워크에서 암호화되지 않은 상태로 전달되는 일은 없습니다. Azure AD는 토큰 요청/응답 교환이 암호화된 HTTPS/TLS 채널을 통해 수행되도록 요구하므로 IDP, 브라우저 및 애플리케이션 간의 통신이 암호화된 연결을 통해 수행됩니다. 추가 인증서 관리에 따른 오버헤드와 비교하면서 작업 상황에서 토큰 암호화를 사용할 때의 가치를 고려하세요.

토큰 암호화를 구성하려면 애플리케이션을 나타내는 Azure AD 애플리케이션 개체에 공개 키를 포함하는 X.509 인증서 파일을 업로드해야 합니다. X.509 인증서를 가져오려면 애플리케이션 자체에서 다운로드하거나, 애플리케이션 공급 기업에서 암호화 키를 제공하는 경우 또는 사용자가 애플리케이션에 프라이빗 키를 제공해야 하는 경우 애플리케이션 공급 기업에서 가져옵니다. 암호화 도구, 애플리케이션의 키 저장소에 업로드된 프라이빗 키 부분 및 Azure AD에 업로드된 일치하는 공개 키 인증서를 사용하여 만들 수 있습니다.

Azure AD는 AES-256을 사용하여 SAML 어설션 데이터를 암호화합니다.

## <a name="configure-saml-token-encryption"></a>SAML 토큰 암호화 구성

SAML 토큰 암호화를 구성하려면 다음 단계를 수행합니다.

1. 애플리케이션에 구성된 프라이빗 키와 일치하는 공개 키 인증서를 가져옵니다.

    암호화에 사용할 비대칭 키 쌍을 만듭니다. 또는 애플리케이션이 암호화에 사용할 공개 키를 제공하는 경우 애플리케이션의 지침에 따라 X.509 인증서를 다운로드합니다.

    공개 키는 .cer 형식의 X.509 인증서 파일에 저장해야 합니다.

    애플리케이션에서 인스턴스를 위해 사용자가 만든 키를 사용하면, 애플리케이션이 Azure AD 테넌트의 토큰 암호를 해독하는 데 사용할 프라이빗 키를 설치하기 위해 애플리케이션에서 제공한 지침을 따릅니다.

1. Azure AD의 애플리케이션 구성에 인증서를 추가합니다.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Azure Portal에서 토큰 암호화를 구성하려면

Azure Portal 내의 애플리케이션 구성에 공용 인증서를 추가할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. **Azure Active Directory > 엔터프라이즈 애플리케이션** 블레이드로 이동한 후 해당 토큰 암호화를 구성하려는 애플리케이션을 선택합니다.

1. 애플리케이션의 페이지에서 **토큰 암호화** 를 선택합니다.

    ![Azure Portal의 토큰 암호화 옵션](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > **토큰 암호화** 옵션은 Azure Portal의 **엔터프라이즈 애플리케이션** 블레이드, 애플리케이션 갤러리 또는 Gallery 이외의 앱에서 설정한 SAML 애플리케이션에만 사용할 수 있습니다. 다른 애플리케이션의 경우 이 메뉴 옵션을 사용할 수 없습니다. Azure Portal의 **앱 등록** 환경을 통해 등록된 애플리케이션의 경우 애플리케이션 매니페스트를 사용하거나 Microsoft Graph 또는 PowerShell을 통해 SAML 토큰에 대한 암호화를 구성할 수 있습니다.

1. **토큰 암호화** 페이지에서 **인증서 가져오기** 를 선택하여 공용 X.509 인증서를 포함하는 .cer 파일을 가져옵니다.

    ![X.509 인증서를 포함하는 .cer 파일 가져오기](./media/howto-saml-token-encryption/import-certificate-small.png)

1. 인증서를 가져오고, 애플리케이션 쪽에서 사용할 수 있게 프라이빗 키가 구성되면 지문 상태 옆에 있는 **...** 를 선택하여 암호화를 활성화한 다음, 드롭다운 메뉴의 옵션에서 **토큰 암호화 활성화** 를 선택합니다.

1. **예** 를 선택하여 토큰 암호화 인증서 활성화를 확인합니다.

1. 애플리케이션용으로 내보낸 SAML 어설션이 암호화되었는지 확인합니다.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Azure Portal에서 토큰 암호화를 비활성화하려면

1. Azure Portal에서 **Azure Active Directory > 엔터프라이즈 애플리케이션** 으로 이동한 후 SAML 토큰 암호화를 사용하도록 설정한 애플리케이션을 선택합니다.

1. 애플리케이션의 페이지에서 **토큰 암호화** 를 선택하고 인증서를 찾은 후 **...** 옵션을 선택하여 드롭다운 메뉴를 표시합니다.

1. **토큰 암호화 비활성화** 를 선택합니다.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Graph API, PowerShell 또는 앱 매니페스트를 사용하여 SAML 토큰 암호화 구성

암호화 인증서는 `encrypt` 사용 태그를 사용하여 Azure AD의 애플리케이션 개체에 저장됩니다. 암호화 인증서를 여러 개 구성할 수 있습니다. 토큰 암호화용으로 활성화된 인증서는 `tokenEncryptionKeyID` 특성으로 식별됩니다.

Microsoft Graph API 또는 PowerShell을 사용하여 토큰 암호화를 구성하려면 애플리케이션의 개체 ID가 있어야 합니다. 이 값을 프로그래밍 방식으로 찾거나 Azure Portal의 애플리케이션 **속성** 페이지로 이동한 후 **개체 ID** 값을 확인하여 찾을 수 있습니다.

Graph 또는 PowerShell을 사용하거나 keyId에 사용할 GUID를 생성해야 합니다.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Microsoft Graph를 사용하여 토큰 암호화를 구성하려면

1. 암호화를 위해 애플리케이션의 `keyCredentials`를 X.509 인증서로 업데이트합니다. 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. 토큰 암호화를 위해 활성화된 암호화 인증서를 식별합니다. 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>PowerShell을 사용하여 토큰 암호화를 구성하려면

1. 최신 Azure AD PowerShell 모듈을 사용하여 테넌트에 연결합니다.

1. **[Set-AzureApplication](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview&preserve-view=true)** 명령을 사용하여 토큰 암호화 설정을 설정합니다.

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. 다음 명령을 사용하여 토큰 암호화 설정을 읽습니다.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>애플리케이션 매니페스트를 사용하여 토큰 암호화를 구성하려면

1. Azure Portal에서 **Azure Active Directory > 앱 등록** 으로 이동합니다.

1. 드롭다운에서 **모든 앱** 을 선택하여 모든 앱을 표시한 다음, 구성하려는 엔터프라이즈 애플리케이션을 선택합니다.

1. 애플리케이션의 페이지에서 **매니페스트** 를 선택하여 [애플리케이션 매니페스트](../develop/reference-app-manifest.md)를 편집합니다.

1. `tokenEncryptionKeyId` 특성 값을 설정합니다.

    다음 예제에서는 2개의 암호화 인증서로 구성되어 있으며 두 번째 인증서가 tokenEnryptionKeyId를 사용하여 활성 암호화 인증서로 선택된 애플리케이션 매니페스트를 보여 줍니다.

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>다음 단계

* [Azure AD에서 SAML 프로토콜을 사용하는 방법](../develop/active-directory-saml-protocol-reference.md) 알아보기
* [Azure AD의 SAML 토큰](../develop/reference-saml-tokens.md) 형식, 보안 특성 및 콘텐츠 알아보기
