---
title: 양식 기반 인증을 위한 F5 BIG-IP와 Azure Active Directory 통합 Single Sign-on
description: 양식 기반 애플리케이션에 대한 보안 하이브리드 액세스를 위해 F5의 BIG-IP APM(액세스 정책 관리자)과 Azure Active Directory를 통합하는 방법을 알아봅니다.
author: gargi-sinha
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/20/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51fb95b6f79bd306a1e936fa99da7a55fe2eea2a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039933"
---
# <a name="tutorial-integrate-azure-active-directory-with-f5-big-ip-for-forms-based-authentication-single-sign-on"></a>자습서: 양식 기반 인증을 위해 Azure Active Directory와 F5 BIG-IP 통합 Single Sign-On

이 자습서에서는 양식 기반 애플리케이션에 대한 보안 하이브리드 액세스를 위해 F5의 BIG-IP APM(액세스 정책 관리자) 및 Azure AD(Azure Active Directory)를 통합하는 방법을 알아봅니다.

BIG-IP 게시 애플리케이션을 Azure AD와 통합하면 다음과 같은 많은 이점이 있습니다.

- Azure AD 사전 인증 및 권한 부여를 통해 제로 트러스트 거버넌스 개선

- Azure AD와 BIG-IP 게시 서비스 간의 전체 SSO(Single Sign-On)

- 단일 컨트롤 플레인에서 ID 및 액세스 관리 - [Azure Portal](https://portal.azure.com)

모든 이점에 대해 알아보려면 [F5 BIG-IP 및 Azure AD 통합](f5-aad-integration.md) 및 [Azure AD로 애플리케이션 액세스 및 Single Sign-On이란?](../active-directory-appssoaccess-whatis.md) 문서를 참조하세요.

## <a name="scenario-description"></a>시나리오 설명

이 시나리오의 경우 FBA(양식 기반 인증)용으로 구성된 내부 레거시 애플리케이션이 있습니다.

이상적인 시나리오는 애플리케이션을 Azure AD를 통해 직접 관리하고 제어하는 것입니다. 그러나 최신 프로토콜 상호 운용성이 없기 때문에 현대화하는 데 상당한 노력과 시간이 소요되어 불가피한 비용과 잠재적 가동 중지 위험이 발생합니다.

대신 인터넷과 애플리케이션이 연결된 내부 Azure VNet 사이에 배포된 BIG-IP VE(가상 버전)는 권한 부여 및 권한 부여 기능의 광범위한 선택을 위한 Azure AD와 함께 인바운드 액세스를 게이트하는 데 사용됩니다.

애플리케이션 앞에 BIG-IP가 있으면 서비스를 Azure AD 사전 인증 및 양식 기반 SSO로 오버레이하여 애플리케이션의 전반적인 보안 태세를 크게 개선하여 비즈니스가 중단 없이 속도로 계속 운영할 수 있습니다.

이 시나리오에 대한 보안 하이브리드 액세스 솔루션은 다음으로 구성됩니다.

**애플리케이션**: Azure AD 및 BIG-IP 보안 하이브리드 액세스로 보호되는 백 엔드 서비스입니다. 이 특정 애플리케이션은 오픈 소스에 대해 사용자 자격 증명의 유효성을 검사하지만 Active Directory, LDS 등을 포함한 모든 디렉터리가 될 수 있습니다.

**Azure AD**: BIG-IP APM에 대한 사용자 자격 증명, CA(조건부 액세스) 및 SSO 확인을 담당하는 SAML ID 공급자(IdP)입니다.

**BIG-IP**: 백 엔드 애플리케이션에 대한 양식 기반 SSO를 수행하기 전에 SAML IdP에 인증을 위임하는 역방향 프록시 및 SAML SP(서비스 공급자)입니다.

![스크린샷은 흐름도를 보여 줍니다.](./media/f5-big-ip-forms-advanced/flow-diagram.png)

| 단계 | 설명|
|:-------|:----------|
| 1. | 사용자가 애플리케이션의 SAML SP 엔드포인트(BIG-IP APM)에 연결합니다.|
|2. | APM 액세스 정책은 사전 인증을 위해 사용자를 SAML IdP(Azure AD)로 리디렉션합니다.|
| 3. | SAML IdP는 사용자를 인증하고 적용된 CA 정책을 적용합니다.|
| 4. | Azure AD는 발급된 토큰 및 클레임을 사용하여 사용자를 SAML SP로 다시 리디렉션합니다. |
| 5. | APM은 애플리케이션 암호를 묻고 캐시에 저장합니다. |
| 6. |  애플리케이션에 대한 BIG-IP 요청은 로그인 양식을 수신합니다.|
| 7. | APM 스크립팅은 양식을 제출하기 전에 사용자 이름과 암호를 채우는 데 응답합니다.|
| 8. | 애플리케이션 페이로드는 웹 서버에서 제공하고 클라이언트로 전송됩니다. 선택적으로 APM은 응답 헤더를 검사하고 쿠키 또는 리디렉션 URI를 찾아 성공적인 로그온을 탐지합니다. |

## <a name="prerequisites"></a>사전 요구 사항

이전의 BIG-IP 경험은 필요하지 않지만 다음이 필요합니다.

- Azure AD 무료 구독 이상

- 기존 BIG-IP 또는 [Azure에 BIG-IP VE(Virtual Edition) 배포](f5-bigip-deployment-guide.md)

- 다음 F5 BIG-IP 라이선스 SKU 중 하나

  - F5 BIG-IP&reg; 최고의 번들

  - F5 BIG-IP APM(Access Policy Manager)&trade; 독립 실행형 라이선스

  - 기존 BIG-IP F5 BIG-IP&reg; LTM(Local Traffic Manager)&trade;에 대한 F5 BIG-IP APM(Access Policy Manager)&trade; 추가 기능 라이선스

  - BIG-IP 전체 기능 90일 [평가판 라이선스](https://www.f5.com/trial/big-ip-trial.php)

- 온-프레미스 디렉터리에서 Azure AD로 [동기화된](../hybrid/how-to-connect-sync-whatis.md) 사용자 ID

- Azure AD 애플리케이션 관리자 [사용 권한](../roles/permissions-reference.md#application-administrator)이 있는 계정

- HTTPS를 통해 서비스를 게시하거나 테스트하는 동안 기본 인증서를 사용하기 위한 [SSL 인증서](f5-bigip-deployment-guide.md#ssl-profile)

- 기존 양식 기반 인증 애플리케이션 또는 테스트를 위한 [IIS FBA 앱 설정](/troubleshoot/aspnet/forms-based-authentication)

## <a name="deployment-modes"></a>배포 모드

몇 가지 마법사 기반 옵션 또는 고급 구성을 포함하여 이 시나리오에 대해 BIG-IP를 구성하기 위한 몇 가지 방법이 있습니다.

이 자습서에서는 모든 BIG-IP 구성 개체를 수동으로 만들어 보안 하이브리드 액세스를 구현하는 데 보다 유연한 접근 방식을 제공하는 고급 접근 방식을 다룹니다. 단계별 구성에서 다루지 않는 시나리오에도 이 접근 방식을 사용합니다.

>[!NOTE]
>이 문서 전체에서 참조되는 모든 예시 문자열 또는 값은 실제 환경의 문자열로 대체해야 합니다.

## <a name="adding-f5-big-ip-from-the-azure-ad-gallery"></a>Azure AD 갤러리에서 F5 BIG-IP 추가

BIG-IP APM과 Azure AD 간에 SAML 페더레이션 트러스트를 설정하는 것은 보안 하이브리드 액세스를 구현하는 첫 번째 단계 중 하나입니다. 게시된 서비스에 대한 액세스 권한을 부여하기 전에 BIG-IP가 사전 인증 및 [CA](../conditional-access/overview.md)를 Azure AD에 전달하는 데 필요한 통합을 설정합니다.

1. 애플리케이션 관리 권한이 있는 계정을 사용하여 **Azure Portal** 에 로그인합니다.

2. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.

3. **엔터프라이즈 애플리케이션** 으로 이동하고 위쪽 리본에서 **+ 새 애플리케이션** 을 선택합니다.

4. 갤러리에서 **F5** 를 검색하고 **F5 BIP-IP APM Azure AD 통합** 을 선택합니다.

5. 애플리케이션 이름을 입력한 다음 **추가/만들기** 를 사용하여 테넌트에 추가합니다. 이 이름은 VPN과 같은 해당 특정 서비스를

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

1. 새 **F5** 애플리케이션 속성이 표시된 상태에서 **관리** > **Single Sign-On** 으로 이동합니다.

2. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택한 다음 **아니요, 나중에 저장하겠습니다** 를 선택하여 Single Sign-On 설정을 저장하라는 메시지를 건너뜁니다.

3. **SAML로 Single Sign-On 설정** 블레이드에서 **기본 SAML 구성** 에 대한 펜 아이콘을 선택하여 다음을 제공합니다.

   a. 미리 정의된 **식별자** URL을 BIG-IP 게시 서비스의 URL로 바꿉니다. 예를 들어 `https://myvacation.contoso.com`

   b. **회신 URL** 과 동일하게 수행하되 APM의 SAML 엔드포인트 경로를 포함합니다. 예를 들어 `https://myvacation.contoso.com/saml/sp/profile/post/acs`

      >[!NOTE]
      >이 구성에서 SAML 흐름은 IdP 시작 모드에서 작동합니다. 여기서 Azure AD는 사용자가 애플리케이션의 BIG-IP 서비스 엔드포인트로 리디렉션되기 전에 SAML 어설션을 발행합니다. BIG-IP APM은 IdP 및 SP 시작 모드를 모두 지원합니다.

   다. `Logout URI`의 경우 게시 중인 서비스의 호스트 헤더가 앞에 추가된 BIG-IP APM SLO(단일 로그아웃) 엔드포인트를 입력합니다. `Providing an SLO URI`은 사용자의 BIG-IP APM 세션도 Azure AD에서 로그아웃한 후 종료되도록 합니다. 예를 들어 `https://myvacation.contoso.com/saml/sp/profile/redirect/slr`

     ![스크린샷은 기본 SAML 구성 편집을 보여 줍니다.](./media/f5-big-ip-forms-advanced/basic-saml-configuration.png)

     >[!Note]
     > TMOS v16에서 SAML SLO 엔드포인트가 /saml/sp/profile/redirect/slo로 변경되었습니다.

4. SAML 구성 블레이드를 종료하기 전에 **저장** 을 선택하고 SSO 테스트 프롬프트를 건너뜁니다.

   Azure AD가 백 엔드 애플리케이션에 대한 BIG-IP APM 인증 및 SSO를 위해 사용자에게 발급하는 **사용자 특성 및 클레임** 섹션의 특성을 확인합니다.

5. **SAML 서명 인증서** 섹션에서 **다운로드** 단추를 선택하여 **페더레이션 메타데이터 XML** 파일을 컴퓨터에 저장합니다.

   ![페더레이션 메타데이터 다운로드 링크의 스크린샷](./media/f5-big-ip-forms-advanced/saml-certificate.png)

   Azure AD에서 만든 SAML 서명 인증서의 수명은 3년이며 게시된 [지침](manage-certificates-for-federated-single-sign-on.md)을 사용하여 관리해야 합니다.

### <a name="azure-ad-authorization"></a>Azure AD 권한 부여

기본적으로 Azure AD는 애플리케이션에 대한 액세스 권한이 부여된 사용자에게만 토큰을 발급합니다.

1. 애플리케이션의 구성 보기에서 **사용자 및 그룹** 을 선택합니다.

2. **+** **사용자 추가** 를 선택하고 **할당 추가** 블레이드에서 **사용자 및 그룹** 을 선택합니다.

3. **사용자 및 그룹** 대화 상자에서 내부 애플리케이션에 액세스할 권한이 있는 사용자 그룹을 추가한 다음 **선택** \> **할당** 을 추가합니다.

이는 SAML 페더레이션 트러스트의 Azure AD 부분을 완료합니다. 이제 BIG-IP APM을 설정하여 내부 웹 애플리케이션을 게시하고 해당 속성 집합으로 구성하여 SAML 사전 인증 및 SSO에 대한 신뢰를 완료할 수 있습니다.

## <a name="advanced-configuration"></a>고급 구성

### <a name="service-provider"></a>서비스 공급자

이러한 설정은 APM이 SAML 사전 인증으로 레거시 애플리케이션을 오버레이하는 데 사용할 SAML SP 속성을 정의합니다.

1. **액세스** > **페더레이션** > **SAML 서비스 공급자** > **로컬 SP 서비스** > **만들기** 를 선택합니다.

   ![스크린샷은 F5 양식 구성을 보여 줍니다.](./media/f5-big-ip-forms-advanced/f5-forms-configuration.png)

2. **이름** 및 이전에 Azure AD에서 정의한 것과 정확히 동일한 **엔터티 ID** 를 제공합니다.

   ![스크린샷은 새로운 saml sp 서비스를 보여 줍니다.](./media/f5-big-ip-forms-advanced/saml-sp-service.png)

    **SP 이름 설정** 은 엔터티 ID가 게시된 URL의 호스트 이름 부분과 정확히 일치하지 않거나 일반 호스트 이름 기반 URL 형식이 아닌 경우에만 필요합니다. 엔터티 ID가 `urn:myvacation:contosoonline`인 경우 게시되는 애플리케이션의 외부 스키마 및 호스트 이름을 제공합니다.

### <a name="external-idp-connector"></a>외부 IdP 커넥터

SAML IdP 커넥터는 BIG-IP APM이 Azure AD를 SAML IDP로 신뢰하는 데 필요한 설정을 정의합니다. 이러한 설정은 SAML SP를 SAML IdP에 매핑하여 APM과 Azure AD 간의 페더레이션 트러스트를 설정합니다.

1. 아래로 스크롤하여 새 SAML SP 개체를 선택하고 **IDP 커넥터 바인딩/바인딩 해제** 를 선택합니다.

   ![스크린샷은 로컬 sp 서비스를 보여 줍니다.](./media/f5-big-ip-forms-advanced/local-services.png)

2. **새 IdP 커넥터 만들기** 를 선택하고 드롭다운 메뉴에서 **메타데이터에서** 를 선택합니다.

   ![스크린샷은 메타데이터 드롭다운을 보여 줍니다.](./media/f5-big-ip-forms-advanced/from-metadata.png)
  
3. 이전에 다운로드한 페더레이션 메타데이터 XML 파일을 찾아 외부 SAML IdP를 나타내는 APM 개체의 **ID 공급자 이름** 을 제공 합니다. 예를 들어 `MyVacation\_AzureAD`

   ![스크린샷은 새로운 idp saml 커넥터를 보여 줍니다.](./media/f5-big-ip-forms-advanced/new-idp-saml-connector.png)

4. **새 행 추가** 를 선택하여 새 **SAML IdP 커넥터** 를 선택한 다음 **업데이트** 를 선택합니다.
  
   ![스크린샷은 새 행 추가를 보여 줍니다.](./media/f5-big-ip-forms-advanced/add-new-row.png)

5. **확인을 선택하여 해당 설정을 저장합니다.**

   ![스크린샷은 이 sp를 사용하는 편집 saml idp를 보여 줍니다.](./media/f5-big-ip-forms-advanced/edit-saml-idp-using-sp.png)

### <a name="forms-based-sso"></a>양식 기반 SSO

FBA SSO는 클라이언트 시작 모드 또는 BIG-IP 자체에서 수행할 수 있습니다. 두 방법 모두 양식을 자동으로 제출하기 전에 사용자 이름과 암호 태그에 자격 증명을 삽입하여 사용자 로그인을 에뮬레이트합니다. 사용자가 FBA 애플리케이션에 액세스할 때 암호를 한 번 제공해야 한다는 점을 제외하고 흐름은 거의 투명합니다. 그런 다음 암호는 다른 FBA 애플리케이션에서 재사용할 수 있도록 캐시됩니다.

여기에는 백 엔드 애플리케이션에 대한 SSO를 직접 처리하는 APM 접근 방식이 포함됩니다.

**액세스** > **Single Sign-On** > **양식 기반** > **만들기** 를 선택하고 다음을 제공합니다.

|속성 | 설명 |
|:------|:---------|
| Name | SSO APM 개체는 게시된 다른 애플리케이션에서 재사용할 수 있으므로 구성을 설명하는 이름을 사용합니다. 예를 들어, `Contoso\FBA\sso`입니다.|
| SSO 템플릿 사용 | 없음 |
| 사용자 이름 원본 | 암호 수집 양식을 미리 채우기 위한 기본 사용자 이름 원본입니다. 모든 APM 세션 변수를 사용할 수 있지만 기본 `session.sso.token.last.username`은 로그인한 사용자의 Azure AD UPN을 보유하므로 가장 잘 작동하는 경향이 있습니다. |
| 암호 원본 | 기본 `session.sso.token.last.password`는 BIG-IP가 사용자가 제공한 암호를 캐시하는 데 사용할 APM 변수이므로 그대로 둡니다. |

![스크린샷은 새로운 sso 구성을 보여 줍니다.](./media/f5-big-ip-forms-advanced/new-sso-configuration.png)

|속성 | 설명 |
|:------|:---------|
| 시작 URI | FBA 애플리케이션의 로그인 URI입니다. APM 양식 기반 인증은 요청 URI가 이 URI 값과 일치할 때 SSO를 실행합니다.|
| 양식 작업 | 원래 요청 URL이 SSO에 사용되도록 비워 둡니다. |
| 사용자 이름에 대한 양식 매개 변수 | 로그인 양식의 사용자 이름 필드의 요소 이름입니다. 이를 확인하려면 브라우저의 개발 도구를 사용합니다.| 
| 암호에 대한 양식 매개 변수 | 로그인 양식의 암호 필드 요소 이름입니다. 마찬가지로 개발 도구를 사용합니다.|

![스크린샷은 sso 메서드 구성을 보여 줍니다.](./media/f5-big-ip-forms-advanced/sso-method-configuration.png)

![스크린샷은 contoso 내 휴가 로그인 페이지를 보여 줍니다.](./media/f5-big-ip-forms-advanced/contoso-example.png)

FBA SSO용 APM 구성에 대한 자세한 내용은 [여기](https://techdocs.f5.com/en-us/bigip-14-1-0/big-ip-access-policy-manager-single-sign-on-concepts-configuration-14-1-0/single-sign-on-methods.html#GUID-F8588DF4-F395-4E44-881B-8D16EED91449)에서 확인할 수 있습니다.

### <a name="access-profile-configuration"></a>액세스 프로필 구성

액세스 프로필은 액세스 정책, SSO 구성 및 UI 설정을 포함하여 BIG-IP 가상 서버에 대한 액세스를 관리하는 많은 APM 요소를 바인딩합니다.

1. **액세스** > **프로필/정책** > **액세스 프로필(세션별 정책)**  > **만들기** 를 선택하여 다음을 제공합니다.

   | 속성 | 설명 |
   |:-----|:-------|
   | Name | 예를 들어 `MyVacation` |
   |프로필 유형 | 모두 |
   | SSO 구성 | 방금 만든 FBA SSO 구성 개체|
   |허용되는 언어 | 하나 이상의 언어를 추가합니다.|

   ![스크린샷은 새 액세스 프로필 만들기를 보여 줍니다.](./media/f5-big-ip-forms-advanced/create-new-access-profile.png)

2. 사용자 이름이 미리 채워진 로그온 페이지를 표시하도록 세션 정책을 수정합니다. 방금 만든 세션별 프로필에 대한 **편집** 링크를 선택하여 APM VPE(Visual Policy Editor)를 시작합니다.

   ![스크린샷은 세션별 정책 편집을 보여 줍니다.](./media/f5-big-ip-forms-advanced/edit-per-session-policy.png)

3. VPE가 실행되면 대체 옆에 있는 **+** 기호를 선택합니다.

   ![스크린샷은 vpe가 시작된 후 대체 옆에 +를 보여 줍니다.](./media/f5-big-ip-forms-advanced/vpe-launched.png)

4. 팝업에서 **인증** > **SAML 인증** > **항목 추가** 를 선택합니다.

   ![스크린샷은 saml 인증 및 항목 추가 단추를 보여 줍니다.](./media/f5-big-ip-forms-advanced/saml-auth-add-item.png)

5. **SAML 인증 SP** 구성에서 이름을 **Azure AD Auth** 로 변경하고 이전에 만든 SAML SP 개체를 사용하도록 **AAA 서버** 를 설정합니다.

   ![스크린샷은 Azure AD 인증 서버 설정을 보여 줍니다.](./media/f5-big-ip-forms-advanced/azure-ad-auth-server.png)

6. **성공** 분기에서 **+** 기호를 선택합니다.

7. 팝업에서 **인증** > **로그온 페이지** > **항목 추가** 를 선택합니다.

   ![스크린샷은 로그인 페이지 설정을 보여 줍니다.](./media/f5-big-ip-forms-advanced/logon-page.png)

8. 사용자 이름 필드의 **읽기 전용** 옵션을 **예** 로 변경합니다.

   ![스크린샷은 읽기 전용이 yes로 설정된 것을 보여 줍니다.](./media/f5-big-ip-forms-advanced/set-read-only-as-yes.png)

9. 로그온 페이지 대체에 대해 **+** 기호를 선택하여 SSO 자격 증명 매핑 개체를 추가합니다.

10. 팝업 창에서 **할당** > **SSO 자격 증명 매핑** **> 항목 추가** 를 선택합니다.

    ![스크린샷은 sso 자격 증명 매핑 정보를 보여 줍니다.](./media/f5-big-ip-forms-advanced/sso-credential-mapping.png)

11. 팝업에 표시된 기본 설정을 그대로 두고 계속합니다.

    ![스크린샷은 save sso 자격 증명 매핑 정보를 보여 줍니다.](./media/f5-big-ip-forms-advanced/save-sso-credential-mapping.png)

12. 상단의 **거부** 상자에서 링크를 선택하여 **성공** 분기를 **허용** > **저장** 으로 변경합니다.

**특성 매핑**

선택 사항이지만 LogonID_Mapping 구성을 추가하면 BIG-IP 활성 세션 목록이 세션 번호 대신 로그인한 사용자의 UPN을 표시할 수 있습니다. 로그를 분석하거나 문제를 해결할 때 유용합니다.

1. SAML 인증 **성공** 분기에 대한 **+** 기호를 선택합니다.

2. 팝업에서 **할당** > **변수 할당** > **항목 추가** 를 선택합니다.

   ![스크린샷은 변수 할당 정보를 보여 줍니다.](./media/f5-big-ip-forms-advanced/variable-assign.png)

3. 설명이 포함된 이름을 제공하고 **변수 할당** 섹션에서 **새 항목 추가** > **변경** 을 선택합니다. 예들 들어 `LogonID_Mapping`입니다.

   ![스크린샷은 새 입력 필드 추가를 보여 줍니다.](./media/f5-big-ip-forms-advanced/add-new-entry.png)

4. 다음을 사용하도록 두 변수를 모두 설정합니다.

   | 속성 | 설명 |
   |:-----|:-------|
   | 사용자 지정 변수 | `session.logon.last.username` |
   | 세션 변수 | `session.saml.last.identity`|

   그런 다음 **완료** > **저장** 을 선택합니다.

5. 왼쪽 상단 모서리에서 **액세스 정책 적용** 을 선택하여 해당 설정을 커밋하고 시각적 정책 편집기를 닫습니다.

   ![스크린샷은 액세스 정책 적용을 보여 줍니다.](./media/f5-big-ip-forms-advanced/apply-access-policy.png)

### <a name="backend-pool-configuration"></a>백 엔드 풀 구성

BIG-IP가 클라이언트 트래픽을 전달할 위치를 알기 위해서는 애플리케이션을 호스팅하는 백 엔드 서버를 나타내는 BIG-IP 노드 개체를 만들어야 합니다. 그런 다음 해당 노드를 BIG-IP 서버 풀에 배치합니다.

1. **로컬 트래픽** > **풀** > **풀 목록** > **만들기** 를 선택하고 서버 풀 개체의 이름을 제공합니다. 예들 들어 `MyApps_VMs`입니다.

   ![스크린샷은 풀 목록을 보여 줍니다.](./media/f5-big-ip-forms-advanced/pool-list.png)

2. 다음을 사용하여 풀 구성원 개체를 추가합니다.

   | 속성 | 설명 |
   |:-----|:-------|
   | 노드 이름 | 백 엔드 웹 애플리케이션을 호스팅하는 서버의 선택적 표시 이름 |
   | 주소 | 애플리케이션을 호스팅하는 서버의 IP 주소 |
   | 서비스 포트 | 애플리케이션이 수신 대기 중인 HTTP/S 포트 |

   ![스크린샷은 풀 멤버를 보여 줍니다.](./media/f5-big-ip-forms-advanced/pool-member.png)

>[!NOTE]
>상태 모니터에는 이 자습서에서 다루지 않은 추가 [구성](https://support.f5.com/csp/article/K13397)이 필요합니다.

## <a name="virtual-server-configuration"></a>가상 서버 구성

가상 서버는 애플리케이션에 대한 클라이언트 요청을 수신 대기하는 가상 IP 주소로 표시되는 BIG-IP 데이터 평면 개체입니다. 수신된 모든 트래픽은 정책 결과 및 설정에 따라 전달되기 전에 가상 서버와 연결된 APM 액세스 프로필에 대해 처리 및 평가됩니다.

1. **로컬 트래픽** > **가상 서버** > **가상 서버 목록** > **만들기** 를 선택합니다.

2. 클라이언트 트래픽을 수신하기 위해 BIG-IP에 할당할 수 있는 사용되지 않은 IP IPv4/IPv6인 **이름** 을 가상 서버에 제공하고 **서비스 포트** 를 443으로 설정합니다.

   ![스크린샷은 가상 서버를 보여 줍니다.](./media/f5-big-ip-forms-advanced/virtual-server.png)

3. **HTTP 프로필**: http로 설정합니다.

4. **SSL 프로필(클라이언트)** : HTTPS를 통해 서비스를 게시할 수 있도록 TLS(전송 계층 보안)를 사용하도록 설정합니다. 사전 요구 사항의 일부로 만든 클라이언트 SSL 프로필을 선택하거나 테스트하는 경우 기본값을 그대로 둡니다.

   ![스크린샷은 SSL 프로필을 보여 줍니다.](./media/f5-big-ip-forms-advanced/ssl-profile.png)

5. **원본 주소 변환** 을 **자동 맵** 으로 변경합니다.

   ![스크린샷은 자동 맵을 보여 줍니다.](./media/f5-big-ip-forms-advanced/auto-map.png)

6. **액세스 정책** 에서 앞에서 만든 **액세스 프로필** 을 설정합니다. 그러면 Azure AD SAML 사전 인증 프로필 및 FBA SSO 정책이 가상 서버에 바인딩됩니다.

   ![스크린샷은 액세스 정책을 보여 줍니다.](./media/f5-big-ip-forms-advanced/access-policy.png)

7. 마지막으로 **기본 풀** 을 이전 섹션에서 만든 백 엔드 풀 개체로 설정합니다.

   ![스크린샷은 기본 풀을 보여 줍니다.](./media/f5-big-ip-forms-advanced/default-pool.png)

## <a name="session-management"></a>세션 관리

BIG-IP 세션 관리 설정은 사용자 세션이 종료되거나 계속되도록 허용되는 조건, 사용자 및 IP 주소 제한, 오류 페이지를 정의하는 데 사용됩니다. **액세스 정책** > **액세스 프로필** 로 이동하고 목록에서 애플리케이션을 선택하여 고유한 정책을 만들 수 있습니다.

SLO 기능과 관련하여 Azure AD에서 단일 로그아웃 URI를 정의하면 IdP가 MyApps 포털에서 로그아웃을 시작하면 클라이언트와 BIG-IP APM 간의 세션도 종료됩니다.

애플리케이션의 페더레이션 metadata.xml을 가져오면 SP 시작 로그아웃에 대한 Azure AD SAML 로그아웃 엔드포인트가 있는 APM을 제공합니다. 그러나 이것이 진정으로 효과적이려면 APM은 사용자가 언제 로그아웃하는지 정확히 알아야 합니다.

BIG-IP 웹 포털이 사용되지 않고 사용자가 APM에 로그아웃하도록 지시할 방법이 없는 시나리오를 고려합니다. 사용자가 애플리케이션 자체에서 로그아웃하더라도 BIG-IP는 이를 기술적으로 인식하지 못하므로 SSO를 통해 애플리케이션 세션을 쉽게 복원할 수 있습니다. 이러한 이유로 SP에서 시작한 로그아웃은 더 이상 필요하지 않을 때 세션이 안전하게 종료되도록 세심한 고려가 필요합니다.

이를 달성하는 한 가지 방법은 SLO 기능을 애플리케이션 로그아웃 단추에 추가하여 클라이언트를 Azure AD SAML 로그아웃 엔드포인트로 리디렉션할 수 있도록 하는 것입니다. 테넌트의 SAML 로그아웃 엔드포인트는 **앱 등록** > **엔드포인트** 에서 찾을 수 있습니다.

앱을 변경할 수 없는 경우 BIG-IP가 앱 로그아웃 호출을 수신하도록 하고 요청을 탐지하면 SLO를 트리거하도록 합니다. 이를 달성하기 위해 BIG-IP iRule을 사용하는 방법에 대한 자세한 내용은 F5의 [문서 K42052145](https://support.f5.com/csp/article/K42052145) 및 [문서 K12056](https://support.f5.com/csp/article/K12056)에서 확인할 수 있습니다.

## <a name="summary"></a>요약

이제 URL을 통해 직접 또는 Microsoft의 애플리케이션 포털을 통해 보안 하이브리드 액세스를 통해 애플리케이션을 게시하고 액세스할 수 있습니다.

애플리케이션은 Azure AD CA에서 대상 리소스로도 표시되어야 합니다. CA 정책 구축에 대한 [지침](../conditional-access/concept-conditional-access-policies.md)을 참조하세요.

보안 강화를 위해 이 패턴을 사용하는 조직은 애플리케이션에 대한 모든 직접 액세스를 차단하여 BIG-IP를 통한 엄격한 경로를 강제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

브라우저에서 애플리케이션의 외부 URL에 연결하거나 MyApps 포털에서 애플리케이션 아이콘을 선택합니다. Azure AD에 인증하면 애플리케이션의 BIG-IP 가상 서버로 리디렉션되고 암호를 입력하라는 메시지가 표시됩니다.

>[!Note]
>APM은 Azure AD의 UPN으로 사용자 이름을 미리 채웁니다.

![스크린샷은 보안 sso를 보여 줍니다.](./media/f5-big-ip-forms-advanced/secured-sso.png)

제출되면 사용자는 자동으로 애플리케이션에 로그인해야 하며 FBA SSO 액세스 프로필을 사용하여 게시된 다른 애플리케이션에 대해 재사용할 수 있도록 암호를 캐시해야 합니다.

![스크린샷은 환영 메시지를 보여 줍니다.](./media/f5-big-ip-forms-advanced/welcome-message.png)

## <a name="troubleshoot"></a>문제 해결

보안 하이브리드 액세스 보호 애플리케이션에 대한 액세스 실패는 잘못된 구성을 비롯한 여러 요인으로 인해 발생할 수 있습니다. 문제 해결 시 알아야 할 몇 가지 사항:

- FBA SSO는 지정된 URI에서 로그인 양식을 구문 분석하고 구성에 정의된 사용자 이름 및 암호 요소 태그를 찾는 BIG-IP에 의해 수행됩니다.

- 요소 태그는 일관성이 있어야 하며 그렇지 않으면 SSO가 실패합니다. 동적으로 생성된 보다 복잡한 양식은 로그인 양식의 구성을 이해하기 위해 개발 도구를 사용하여 면밀한 분석이 필요할 수 있습니다.

- 클라이언트 시작 접근 방식은 양식 이름을 지정하고 JavaScript 양식 처리기 논리를 사용자 지정할 수 있으므로 여러 양식이 포함된 로그인 페이지에 더 적합할 수 있습니다.

- 두 FBA SSO 방법 모두 모든 양식 상호 작용을 숨겨 사용자 환경과 보안을 최적화하지만 경우에 따라 자격 증명이 실제로 주입되는지 여부를 확인하는 것이 유용할 수 있습니다. 이는 SSO 프로필에서 양식 자동 제출을 사용하지 않도록 설정한 다음 dev 도구를 사용하여 로그인 페이지가 표시되지 않도록 하는 두 가지 스타일 속성을 사용하지 않도록 설정하여 클라이언트 시작 모드에서 가능합니다.

  ![스크린샷은 속성을 보여 줍니다.](./media/f5-big-ip-forms-advanced/properties.png)

BIG-IP 로그는 모든 종류의 인증 및 SSO 문제를 분리하기 위한 훌륭한 정보 원본입니다. 문제를 해결할 때 로그 상세 수준을 높여야 합니다.

1. **액세스 정책** > **개요** > **이벤트 로그** > **설정** 으로 이동합니다.

2. 게시된 애플리케이션의 행을 선택한 다음 **편집** > **시스템 로그 액세스** 를 선택합니다.

3. SSO 목록에서 **디버그** 를 선택한 다음 **확인** 을 선택합니다. 그런 다음 로그를 보기 전에 문제를 재현하되 완료되면 다시 전환해야 합니다.

Azure AD 사전 인증에 성공한 직후에 BIG-IP 브랜드 오류가 표시되는 경우 Azure AD에서 BIG-IP로의 SSO와 관련된 문제일 수 있습니다.

**액세스** > **개요** > **액세스 보고서** 로 이동하여 지난 1시간 동안 보고서를 실행하여 로그에서 단서를 제공하는지 확인합니다. 세션에 대한 **세션 변수 보기** 링크는 APM이 Azure AD에서 예상되는 클레임을 수신하는지 이해하는 데도 도움이 됩니다.

BIG-IP 오류 페이지가 표시되지 않으면 문제가 BIG-IP에서 애플리케이션으로의 백 엔드 요청 또는 SSO와 더 관련이 있을 수 있습니다. 이 경우 **액세스 정책** > **개요** > **활성 세션** 으로 이동하여 활성 세션에 대한 링크를 선택합니다.

이 위치의 **변수 보기** 링크는 특히 APM이 올바른 사용자 ID와 암호를 얻지 못하는 경우 근본 원인을 파악하는 데 도움이 될 수 있습니다.

자세한 내용은 F5의 BIG-IP [세션 변수 참조](https://techdocs.f5.com/en-us/bigip-15-0-0/big-ip-access-policy-manager-visual-policy-editor/session-variables.html)를 참조하세요.
