---
title: Nevis를 사용하여 Azure Active Directory B2C를 구성하는 방법에 대한 자습서
titleSuffix: Azure AD B2C
description: 암호 없는 인증을 위해 Nevis와 Azure AD B2C 인증을 통합하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 0bbbe80a9f4c743fb658aa313641ab790e194509
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234341"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>암호 없는 인증을 위해 Azure Active Directory B2C를 구성하는 방법에 대한 자습서

이 샘플 자습서에서는 암호 없는 인증을 사용하기 위해 [Nevis](https://www.nevis.net/en/solution/authentication-cloud)로 Azure AD B2C를 확장하는 방법을 알아봅니다. Nevis는 강력한 고객 인증을 제공하고 PSD2(결제 서비스 지시어 2) 트랜잭션 요구 사항을 준수하기 위해 Nevis 액세스 앱에서 완전히 브랜딩된 모바일 우선적인 최종 사용자 환경을 제공합니다.

## <a name="prerequisites"></a>필수 요건

시작하려면 다음이 필요합니다.

- Nevis [평가판 계정](https://www.nevis-security.com/aadb2c/)

- Azure AD 구독 계정이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻습니다.

- [Azure AD B2C 테넌트](./tutorial-create-tenant.md)를 Azure 구독에 연결합니다.

- 등록 정책 흐름에 Nevis를 통합하려는 경우 [사용자 지정 정책](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)을 사용하도록 Azure AD B2C 환경을 구성합니다.

## <a name="scenario-description"></a>시나리오 설명

이 시나리오에서는 암호 없는 인증을 위해 백 엔드 애플리케이션에 완전히 브랜딩된 액세스 앱을 추가합니다. 이 솔루션은 다음 구성 요소를 포함합니다.

- 백 엔드에 대한 결합된 로그인 및 등록 정책을 포함하는 Azure AD B2C 테넌트
- Azure AD B2C 향상을 위한 Nevis 인스턴스 및 해당 REST API
- 사용자 고유의 브랜딩된 액세스 앱

다이어그램은 구현된 모습을 보여 줍니다.

![Azure AD B2C 및 Nevis를 사용하는 높은 수준의 암호 로그인 흐름](./media/partner-nevis/nevis-architecture-diagram.png)

|단계 | 설명 |
|:-----| :-----------|
| 1. | 사용자가 Azure AD B2C 로그인 및 등록 정책을 통해 애플리케이션에 로그인 또는 등록을 시도합니다.
| 2. | 등록하는 동안 Nevis 액세스 앱은 QR 코드를 사용하여 사용자 디바이스에 등록됩니다. 사용자 디바이스에 프라이빗 키가 생성되고 이는 사용자 요청을 서명하는 데 사용됩니다.
| 3. |  Azure AD B2C RESTful 기술 프로필을 사용하여 Nevis 솔루션으로 로그인을 시작합니다.
| 4. | 로그인 요청은 푸시 메시지, QR 코드 또는 딥 링크로 액세스 앱에 전송됩니다.
| 5. | 사용자가 생체 인식을 사용하여 로그인 시도를 승인합니다. 그러면 Nevis에 저장된 공개 키를 사용하여 로그인을 확인하는 메시지가 반환됩니다.
| 6. | Azure AD B2C는 마지막 요청 하나를 Nevis로 보내 로그인이 성공적으로 완료되었음을 확인합니다.
| 7. |Azure AD B2C 사용자의 성공/실패 메시지에 따라 애플리케이션에 대한 액세스 권한이 부여되거나 거부됩니다.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 통합

### <a name="onboard-to-nevis"></a>Nevis에 온보딩 

[Nevis 계정 등록](https://www.nevis-security.com/aadb2c/).
두 개의 이메일을 받게 됩니다.

1. 관리 계정 알림

2. 모바일 앱 초대

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Azure AD B2C 테넌트를 Nevis 계정에 추가합니다.

1. Nevis 관리 계정 평가판 이메일에서 관리 키를 클립보드에 복사합니다.

2. 브라우저에서 https://console.nevis.cloud/ 를 엽니다.

3. 키를 이용해 관리 콘솔에 로그인합니다.

4. **인스턴스 추가** 를 선택합니다.

5. 새로 만든 인스턴스를 선택하여 엽니다.

6. 측면의 탐색 모음에서 **사용자 지정 통합** 을 선택합니다.

7. **사용자 지정 통합 추가** 를 선택합니다.

8. 통합 이름에는 Azure AD B2C 테넌트의 이름을 입력합니다.

9. URL/도메인에 `https://yourtenant.onmicrosoft.com`을 입력합니다.

10. **다음** 을 선택합니다.

>[!NOTE]
>Nevis 액세스 토큰은 나중에 필요합니다.

11. **완료** 를 선택합니다.

### <a name="install-the-nevis-access-app-on-your-phone"></a>휴대폰에 Nevis 액세스 앱 설치

1. Nevis 모바일 앱 평가판 이메일에서 **테스트 비행 앱** 초대를 엽니다.

2. 앱을 설치합니다.

3. 제공된 지침에 따라 Nevis 액세스 앱을 설치합니다.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Azure AD B2C와 Nevis 통합

1. [Azure Portal](https://portal.azure.com/)을 엽니다.

2. Azure AD B2C 테넌트로 전환합니다. Azure AD B2C 테넌트는 일반적으로 별도의 테넌트에 있으므로 올바른 테넌트를 선택했는지 확인합니다.

3. 메뉴에서 **IEF(Identity Experience Framework)** 를 선택합니다.

4. **정책 키** 를 선택합니다.

5. **추가** 를 선택하고 다음 설정에 따라 새 키를 만듭니다.

      a. 옵션에서 **수동** 을 선택합니다.

      b. 이름을 **AuthCloudAccessToken** 으로 설정합니다.

      c. 이전에 저장된 **Nevis 액세스 토큰** 을 비밀 필드에 붙여넣습니다.

      d. 키 사용에 **암호화** 를 선택합니다.

      e. **만들기** 를 선택합니다.

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>nevis.html을 구성하여 Azure Blob Storage에 업로드합니다.

1. IDE(ID 환경)에서 [**policy**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) 폴더로 이동합니다.

2. [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) 파일을 엽니다.

3. **authentication_cloud_url** 을 Nevis 관리자 콘솔의 URL `https://<instance_id>.mauth.nevis.cloud`로 바꿉니다.

4. 변경 내용을 파일에 **저장** 합니다.

5. [지침](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account)에 따라 **nevis.html** 파일을 Azure Blob Storage에 업로드합니다.

6. [지침](./customize-ui-with-html.md#3-configure-cors)에 따라 이 파일에 대한 CORS(크로스-원본 자원 공유)를 사용하도록 설정합니다.

7. 업로드가 완료되고 CORS가 사용하도록 설정되면 목록에서 **nevis.html** 파일을 선택합니다.

8. **개요** 탭에서 **URL** 옆에 있는 **링크 복사** 아이콘을 선택합니다.

9. 새 브라우저 탭에서 링크를 열어 회색 상자가 표시되는지 확인합니다.

>[!NOTE]
>Blob 링크는 나중에 필요합니다.

### <a name="customize-your-trustframeworkbasexml"></a>TrustFrameworkBase.xml 사용자 지정

1. IDE에서 [**policy**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) 폴더로 이동합니다.

2. [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) 파일을 엽니다.

3. **yourtenant** 를 **TenantId** 의 Azure 테넌트 계정 이름으로 바꿉니다.

4. **yourtenant** 를 **PublicPolicyURI** 의 Azure 테넌트 계정 이름으로 바꿉니다.

5. 모든 **authentication_cloud_url** 인스턴스를 Nevis 관리자 콘솔의 URL로 바꿉니다.

6. 문서의 변경 내용을 **저장** 합니다.

### <a name="customize-your-trustframeworkextensionsxml"></a>TrustFrameworkExtensions.xml 사용자 지정

1. IDE에서 [**policy**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) 폴더로 이동합니다.

2. [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) 파일을 엽니다.

3. **yourtenant** 를 **TenantId** 의 Azure 테넌트 계정 이름으로 바꿉니다.

4. **yourtenant** 를 **PublicPolicyURI** 의 Azure 테넌트 계정 이름으로 바꿉니다.

5. **Basepolicy** 의 **TenantId** 에서도 _yourtenant_ 를 Azure 테넌트 계정 이름으로 바꿉니다.

6. **BuildingBlocks** 아래에서 **LoadUri** 를 Blob Storage 계정에 있는 _nevis.html_ 의 BLOB 링크 URL로 바꿉니다.

7. 파일을 **저장** 합니다.

### <a name="customize-your-signuporsigninxml"></a>SignUpOrSignin.xml 사용자 지정

1. IDE에서 [**policy**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) 폴더로 이동합니다.

2. [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) 파일을 엽니다.

3. **yourtenant** 를 **TenantId** 의 Azure 테넌트 계정 이름으로 바꿉니다.

4. **yourtenant** 를 **PublicPolicyURI** 의 Azure 테넌트 계정 이름으로 바꿉니다.

5. **Basepolicy** 의 **TenantId** 에서도 **yourtenant** 를 Azure 테넌트 계정 이름으로 바꿉니다.

6. 파일을 **저장** 합니다.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Azure AD B2C에 사용자 지정 정책 업로드

1. [Azure AD B2C 테넌트](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) 홈을 엽니다.

2. **ID 경험 프레임워크** 를 선택합니다.

3. **사용자 지정 정책 업로드** 를 선택합니다.

4. 수정한 **TrustFrameworkBase.xml** 파일을 선택합니다.

5. **사용자 지정 정책이 이미 있는 경우 덮어쓰기** 확인란을 선택합니다.
6. **업로드** 를 선택합니다.

7. **TrustFrameworkExtensions.xml** 의 5단계와 6단계를 반복합니다.

8. **SignUpOrSignin.xml** 의 5단계와 6단계를 반복합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

### <a name="test-account-creation-and-nevis-access-app-setup"></a>계정 만들기 및 Nevis 액세스 앱 설정 테스트

1. [Azure AD B2C 테넌트](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) 홈을 엽니다.

2. **ID 경험 프레임워크** 를 선택합니다.

3. 사용자 지정 정책으로 스크롤하고 **B2C_1A_signup_signin** 을 선택합니다.

4. **지금 실행** 을 선택합니다.

5. 팝업 창에서 **지금 등록** 을 선택합니다.

6. 이메일 주소를 추가합니다.

7. **확인 코드 보내기** 를 클릭합니다.

8. 이메일에서 확인 코드를 복사합니다.

9. **확인** 을 선택합니다.

10. 양식에 새 암호 및 표시 이름을 입력합니다.

11. **만들기** 를 선택합니다.

12. QR 코드 검색 페이지로 이동합니다.

13. 휴대폰에서 **Nevis 액세스 앱** 을 엽니다.

14. **Face ID** 를 선택합니다.

15. **인증자 등록 성공** 메시지가 표시되면 **계속** 을 선택합니다.

16. 휴대폰에서 얼굴로 다시 인증합니다.

17. 디코딩된 토큰 세부 정보를 표시하는 [jwt.ms](https://jwt.ms) 방문 페이지로 이동합니다.

### <a name="test-the-pure-passwordless-sign-in"></a>암호가 전혀 없는 로그인 테스트

1. **Identity Experience Framework** 에서 **B2C_1A_signup_signin** 을 선택합니다.

2. **지금 실행** 을 선택합니다.

3. 팝업 창에서 **암호 없는 인증** 을 선택합니다.

4. 전자 메일 주소를 입력합니다.

5. **계속** 을 선택합니다.

6. 휴대폰의 알림에서 **Nevis 액세스 앱 알림** 을 선택합니다.

7. 얼굴로 인증합니다.

8. 토큰을 표시하는 [jwt.ms](https://jwt.ms) 방문 페이지로 자동으로 이동됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
