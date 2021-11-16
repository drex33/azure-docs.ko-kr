---
title: 자습서 - Zscaler를 사용하여 Azure Active Directory B2C 구성
titleSuffix: Azure AD B2C
description: Zscaler를 사용하여 Azure AD B2C 인증을 통합하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: fb4ff79e1115086ef84694485cd9e7978295a9c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598844"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 Zscaler 프라이빗 액세스 구성

이 자습서에서는 [ZPA(Zscaler Private Access)](https://www.zscaler.com/products/zscaler-private-access)와 Azure AD B2C(Azure Active Directory B2C) 인증을 통합하는 방법에 대해 알아봅니다. ZPA는 VPN(가상 사설망)의 비용, 부담이나 보안 위험 없이 프라이빗 애플리케이션 및 자산에 대해 정책 기반의 보안 액세스를 제공합니다. Zscaler 보안 하이브리드 액세스 기능을 사용하면 소비자 지향 애플리케이션이 Azure AD B2C와 결합될 때 제로 어택 표면이 설정됩니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 조건을 충족해야 합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.  
- Azure 구독에 연결된 [Azure AD B2C 테넌트](./tutorial-create-tenant.md)  
- [ZPA 구독](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>시나리오 설명

ZPA 통합에는 다음 구성 요소가 포함됩니다.

- **Azure AD B2C**: 사용자의 자격 증명 확인을 담당하는 IdP(ID 공급자)입니다. 또한 새 사용자 등록을 담당합니다.  
- **ZPA**: [제로 트러스트 액세스](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.)를 적용하여 웹 애플리케이션 보안을 담당하는 서비스입니다.  
- **웹 애플리케이션**: 사용자가 액세스하려고 하는 서비스를 호스팅합니다.

다음 다이어그램에서는 ZPA를 Azure AD B2C와 통합하는 방법을 보여 줍니다.

![ZPA를 Azure AD B2C와 통합하는 방법을 보여 주는 Zscaler 아키텍처의 다이어그램입니다.](media/partner-zscaler/zscaler-architecture-diagram.png)

아래 표에 이 시퀀스에 대한 설명이 나와 있습니다.

|단계 | 설명 |
| :-----:| :-----------|
| 1 | 사용자가 ZPA 사용자 포털 또는 ZPA 브라우저 액세스 애플리케이션을 엽니다.
| 2 | 사용자가 웹 애플리케이션에 액세스할 수 있도록 허용할지 여부를 결정하려면 ZPA에 사용자 컨텍스트 정보가 필요합니다. 사용자를 인증하기 위해 ZPA는 Azure AD B2C 로그인 페이지로 SAML 리디렉션을 수행합니다.  
| 3 | 사용자가 Azure AB B2C 로그인 페이지를 엽니다. 새 사용자는 계정을 만들기 위해 등록하고, 기존 사용자는 기존 자격 증명을 사용하여 로그인합니다. Azure AD B2C는 사용자 ID의 유효성을 검사합니다.
| 4 | 인증에 성공하면 Azure AD B2C는 SAML 어설션과 함께 사용자를 ZPA로 다시 리디렉션합니다. ZPA는 SAML 어설션을 확인하고 사용자 컨텍스트를 설정합니다.
| 5 | ZPA는 사용자에 대한 액세스 정책을 평가합니다. 사용자가 웹 애플리케이션에 액세스할 수 있는 경우에는 연결은 통과됩니다.

## <a name="onboard-to-zpa"></a>ZPA에 온보딩

이 자습서에서는 이미 작동하는 ZPA 설정이 있다고 가정합니다. ZPA를 시작하는 경우 [ZPA 단계별 구성 가이드](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)를 참조하세요.

## <a name="integrate-zpa-with-azure-ad-b2c"></a>Azure AD B2C와 ZPA 통합

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>1단계: ZPA의 IdP로 Azure AD B2C 구성

[ZPA의 IdP](https://help.zscaler.com/zpa/configuring-idp-single-sign)로 Azure AD B2C를 구성하려면 다음을 수행합니다.

1. [ZPA 관리 포털](https://admin.private.zscaler.com)에 로그인합니다.

1. **관리** > **IdP 구성** 으로 이동합니다.

1. **IdP 구성 추가** 를 선택합니다.

   **IdP 구성 추가** 창이 열립니다.

   !["IdP 구성 추가" 창의 "IdP 정보" 탭 스크린샷](media/partner-zscaler/add-idp-configuration.png)

1. **IdP 정보** 탭을 선택하고 다음을 수행합니다.

   a. **이름** 상자에 **Azure AD B2C** 를 입력합니다.  
   b. **Single Sign-On** 에서 **사용자** 를 선택합니다.  
   다. **도메인** 드롭다운 목록에서 이 IdP와 연결하려는 인증 도메인을 선택합니다.

1. **다음** 을 선택합니다.

1. **SP 메타데이터** 탭을 선택하고 다음을 수행합니다.

   a. **서비스 공급자 URL** 에서 나중에 사용할 값을 복사하거나 적어 둡니다.  
   b. **서비스 공급자 엔터티 ID** 에서 나중에 사용할 값을 복사하거나 적어 둡니다.

   !["IdP 구성 추가" 창의 "SP 메타데이터" 탭 스크린샷](media/partner-zscaler/sp-metadata.png)

1. **일시 중지** 를 선택합니다.

Azure AD B2C를 구성한 후에는 나머지 IdP 구성이 다시 시작됩니다.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>2단계: Azure AD B2C에서 사용자 지정 정책 구성

>[!Note]
>이 단계는 사용자 지정 정책을 아직 구성하지 않은 경우에만 필요합니다. 사용자 지정 정책이 이미 하나 이상 있는 경우 이 단계를 건너뛸 수 있습니다.

Azure AD B2C 테넌트에서 사용자 지정 정책을 구성하려면 [Azure Active Directory B2C에서 사용자 지정 정책 시작](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)을 참조하세요.

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>3단계: Azure AD B2C에서 SAML 애플리케이션으로 ZPA 등록

Azure AD B2C에서 SAML 애플리케이션을 구성하려면 [Azure AD B2C에 SAML 애플리케이션 등록](./saml-service-provider.md)을 참조하세요. 

["정책 업로드"](./saml-service-provider.md#upload-your-policy) 단계에서 Azure AD B2C에 사용되는 IdP SAML 메타데이터 URL을 복사하거나 적어 둡니다. 나중에 필요합니다.

["Azure AD B2C에서 애플리케이션 구성"](./saml-service-provider.md#configure-your-application-in-azure-ad-b2c) 단계를 수행하여 지침을 따르세요. 4\.2단계에서는 다음과 같이 앱 매니페스트 속성을 업데이트합니다.

- **identifierUris**: 이전에 "1.6.b단계"에서 복사하거나 적어 둔 서비스 공급자 엔터티 ID를 사용합니다.  
- **samlMetadataUrl**: ZPA가 SAML 메타데이터 URL을 호스팅하지 않으므로 이 속성은 건너뜁니다.  
- **replyUrlsWithType**: 이전에 "1.6.a 단계"에서 복사하거나 적어 둔 서비스 공급자 URL을 사용합니다.  
- **logouturl**: ZPA가 로그아웃 URL을 지원하지 않으므로 이 속성은 건너뜁니다.

나머지 단계는 이 자습서와 관련이 없습니다.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>4단계: Azure AD B2C에서 IdP SAML 메타데이터 추출

이제 다음과 같은 형식으로 SAML 메타데이터 URL을 가져와야 합니다.

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata`

`<tenant-name>`은 Azure AD B2C 테넌트의 이름이고 `<policy-name>`은 이전 단계에서 만든 사용자 지정 SAML 정책의 이름입니다.

예를 들어 URL은 다음과 같을 수 있습니다.

`https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml/Samlp/metadata`.

웹 브라우저를 열고 SAML 메타데이터 URL로 이동합니다. 페이지의 아무 곳이나 마우스 오른쪽 단추로 클릭하고 **다른 이름으로 저장** 을 선택한 후 다음 단계에서 사용할 수 있도록 파일을 컴퓨터에 저장합니다.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>5단계: ZPA에서 IdP 구성 완료

이전에 "1단계: Azure AD B2C를 ZPA의 IdP로 구성"에서 부분적으로 구성한 [ZPA 관리자 포털에서 IdP 구성](https://help.zscaler.com/zpa/configuring-idp-single-sign)을 완료합니다.

1. [ZPA 관리 포털](https://admin.private.zscaler.com)에서 **관리**  >  **IdP 구성** 으로 이동합니다.

1. "1단계"에서 구성한 IdP를 선택한 다음 **다시 시작** 을 선택합니다.

1. **IdP 구성 추가** 창에서 **IdP 만들기** 탭을 선택하고 다음을 수행합니다.

   a. **IdP 메타데이터 파일** 에 이전에 "4단계: Azure AD B2C에서 IdP SAML 메타데이터 추출"에서 저장한 메타데이터 파일을 업로드합니다.  
   b. IdP 구성에 대한 **상태** 가 **사용** 인지 확인합니다.  
   c. **저장** 을 선택합니다.

   !["IdP 구성 추가" 창의 "IdP 만들기" 탭 스크린샷](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>솔루션 테스트

ZPA 사용자 포털 또는 브라우저 액세스 애플리케이션으로 이동하여 등록 또는 로그인 프로세스를 테스트합니다. 테스트로 인해 SAML 인증이 성공적으로 수행되어야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토하세요.

- [Azure AD B2C에서 사용자 지정 정책 시작](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
- [Azure AD B2C에 SAML 애플리케이션 등록](./saml-service-provider.md)
- [ZPA 단계별 구성 가이드](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [Single Sign-On에 대한 IdP 구성](https://help.zscaler.com/zpa/configuring-idp-single-sign)
