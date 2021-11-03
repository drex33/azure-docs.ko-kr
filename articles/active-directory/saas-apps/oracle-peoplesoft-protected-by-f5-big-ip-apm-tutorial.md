---
title: '자습서: Oracle PeopleSoft - Protected by F5 BIG-IP APM과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Oracle PeopleSoft - Protected by F5 BIG-IP APM 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 2583397a34635d54fa7982656019433442e2e96b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058885"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>자습서: Oracle PeopleSoft - Protected by F5 BIG-IP APM과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Oracle PeopleSoft - Protected by F5 BIG-IP APM을 통합하는 방법에 대해 알아봅니다. Oracle PeopleSoft - Protected by F5 BIG-IP APM을 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Oracle PeopleSoft - Protected by F5 BIG-IP APM에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Oracle PeopleSoft - Protected by F5 BIG-IP APM에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.
* 이 자습서에는 Oracle PeopleSoft ELM에 대한 지침이 포함되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

1. Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
1. Oracle PeopleSoft - Protected by F5 BIG-IP APM SSO(Single Sign-On)가 설정된 구독.

1. 공동 솔루션을 배포하려면 다음 라이선스가 필요합니다.

    1. F5 BIG-IP® Best 번들 (또는) 
    2. F5 BIG-IP APM(Access Policy Manager)™ 독립 실행형 라이선스 
    3. 기본 BIG-IP F5 BIG-IP® LTM(Local Traffic Manager)™에 대한 F5 BIG-IP Access Policy Manager™ (APM) 추가 기능 라이선스
    4. F5 시스템에는 위의 라이선스 외에도 다음 라이선스가 부여될 수 있습니다. 
        * URL 범주 데이터베이스를 사용하는 URL 필터링 구독 
        * 알려진 공격자와 악성 트래픽을 탐지 및 차단하는 F5 IP 인텔리전스 구독 
        * 강력한 인증을 위해 디지털 키를 보호하고 관리하는 HSM(네트워크 하드웨어 보안 모듈)
1. F5 BIG-IP 시스템은 APM 모듈을 통해 프로비저닝됩니다(LTM은 선택 사항).
1. 선택 사항이지만, HA(고가용성)를 위해 부동 IP 주소를 사용하여 F5 시스템을 활성-대기 쌍이 포함된 [S/F DG](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html)(동기화/장애 조치 디바이스 그룹)에 배포하는 것이 좋습니다. LACP(링크 집계 제어 프로토콜)를 사용하여 더 많은 인터페이스 중복성을 달성할 수 있습니다. LACP는 연결된 실제 인터페이스를 단일 가상 인터페이스(집계 그룹)로 관리하고 그룹 내의 인터페이스 오류를 검색합니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Oracle PeopleSoft - Protected by F5 BIG-IP APM에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="add-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>갤러리에서 Oracle PeopleSoft - Protected by F5 BIG-IP APM 추가

Oracle PeopleSoft - Protected by F5 BIG-IP APM이 Azure AD에 통합되도록 구성하려면 갤러리의 Oracle PeopleSoft - Protected by F5 BIG-IP APM을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Oracle PeopleSoft - Protected by F5 BIG-IP APM** 을 입력합니다.
1. 결과 패널에서 **Oracle PeopleSoft - Protected by F5 BIG-IP APM** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Azure AD SSO for Oracle PeopleSoft - Protected by F5 BIG-IP APM에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Oracle PeopleSoft - Protected by F5 BIG-IP APM에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Oracle PeopleSoft - Protected by F5 BIG-IP APM의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Oracle PeopleSoft - Protected by F5 BIG-IP APM에서 Azure AD SSO를 구성하고 테스트하려면 다음을 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Oracle PeopleSoft - Protected by F5 BIG-IP APM SSO 구성](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Oracle PeopleSoft - Protected by F5 BIG-IP APM 테스트 사용자 만들기](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** - B.Simon의 Azure AD 표현과 연결되는 대응 사용자를 Oracle PeopleSoft - Protected by F5 BIG-IP APM에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Oracle PeopleSoft - Protected by F5 BIG-IP APM** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://<FQDN>.peoplesoft.f5.com` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs` 패턴을 사용하여 URL을 입력합니다.

    다. **로그아웃 URL** 텍스트 상자에 `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<FQDN>.peoplesoft.f5.com/` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    >이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자, 회신 URL 및 로그아웃 URL로 이러한 값을 업데이트합니다. 이 값을 얻으려면 [Oracle PeopleSoft - Protected by F5 BIG-IP APM 클라이언트 지원 팀](https://support.f5.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. Oracle PeopleSoft - Protected by F5 BIG-IP APM 애플리케이션에는 특정 형식의 SAML 어설션이 필요하므로 사용자 지정 특성 매핑을 SAML 토큰 특성 구성에 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도, Oracle PeopleSoft - Protected by F5 BIG-IP APM 애플리케이션에는 SAML 응답에서 다시 전달되어야 하는 다음과 같은 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.
    
    | Name |  원본 특성|
    | ------------------ | --------- |
    | EMPLID | user.employeeid |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 및 **인증서(Base64)** 를 컴퓨터에 다운로드하고 저장합니다.

    ![인증서 다운로드 링크](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Oracle PeopleSoft - Protected by F5 BIG-IP APM에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Oracle PeopleSoft-Protected by F5 BIG IP APM** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>Oracle PeopleSoft-Protected by F5 BIG-IP APM SSO 구성

### <a name="f5-saml-sp-configuration"></a>F5 SAML SP 구성

나중에 설정 프로세스에서 사용할 F5로 메타데이터 인증서를 가져옵니다. **System > Certificate Management > Traffic Certificate Management > SSL Certificate List**(시스템 > 인증서 관리 > 트래픽 인증서 관리 >> SSL 인증서 목록)로 차례로 이동합니다. 오른쪽 모서리에서 **Import**(가져오기)를 선택합니다.

![F5 SAML SP 구성](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>SAML IDP 커넥터 설정 

1. **액세스 > 페더레이션 > SAML: 서비스 공급자 > 외부 Idp 커넥터** 로 이동하여 **만들기 > 메타데이터에서** 를 클릭합니다.

    ![F5 SAML IDP 커넥터](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. 다음 페이지에서 **찾아보기** 를 클릭하여 xml 파일을 업로드합니다.

1. **ID 공급자 이름** 텍스트 상자에 유효한 이름을 입력하고 **확인** 을 클릭합니다.

    ![새 SAML IDP 커넥터](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. **보안 설정** 탭에서 필요한 단계를 수행하고 **확인** 을 클릭합니다.

    ![SAML IDP 커넥터 편집](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>SAML SP 설정

1. **액세스 > 페더레이션 > SAML 서비스 공급자 > 로컬 SP 서비스** 로 이동하여 **만들기** 를 클릭합니다. 다음 정보를 완성하고, **OK**(확인)를 클릭합니다.

    * 이름: `<Name>`
    * 엔터티 ID: `https://<FQDN>.peoplesoft.f5.com`
    * SP Name Settings(SP 이름 설정)
        * 구성표: `https`
        * 호스트: `<FQDN>.peoplesoft.f5.com`
        * 설명: `<Description>`

    ![새 SAML SP 서비스](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. SP 구성, PeopleSoftAppSSO를 차례로 선택하고 **IdP 커넥터 바인딩/바인딩 해제** 를 클릭합니다.
**Add New Row**(새 행 추가)를 클릭하고, 이전 단계에서 만든 **External IdP connector**(외부 IdP 커넥터)를 선택하고, **Update**(업데이트)를 클릭한 다음, **OK**(확인)를 클릭합니다.

    ![SAML SP 서비스 만들기](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>애플리케이션 구성

### <a name="create-a-new-pool"></a>새 풀 만들기
1. **로컬 트래픽 > 풀 > 풀 목록** 으로 이동하여 **만들기** 를 클릭하고 다음 정보를 입력한 다음, **마침** 을 클릭합니다.

    * 이름: `<Name>`
    * 설명: `<Description>`
    * 상태 모니터: `http`
    * 주소: `<Address>`
    * 서비스 포트: `<Service Port>`

    ![새 풀 만들기](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>새 클라이언트 SSL 프로필 만들기

**로컬 트래픽 > 프로필 > SSL > 클라이언트 > +** 로 이동하여 다음 정보를 입력한 다음, **마침** 을 클릭합니다.

* 이름: `<Name>`
* 인증서: `<Certificate>`
* 키: `<Key>`

![새 클라이언트 SSL 프로필](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>새 가상 서버 만들기

1. **로컬 트래픽 > 가상 서버 > 가상 서버 목록** 으로 이동하여 다음 정보를 입력한 다음, **마침** 을 클릭합니다.
    * 이름: `<Name>`
    * 대상 주소/마스크: `<Address>`
    * Service Port(서비스 포트): Port(포트) 443 HTTPS
    * HTTP 프로필(클라이언트): http

    ![새 가상 서버 만들기](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. 아래 페이지에서 다음 값을 입력합니다.

    * SSL 프로필(클라이언트): `<SSL Profile>`
    * 원본 주소 변환: 자동 매핑
    * 액세스 프로필: `<Access Profile>`
    * 기본 풀: `<Pool>`


    ![새 가상 서버 peoplesoft 풀 만들기](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>F5 Big-IP APM을 Single Sign-On 솔루션으로 지원하도록 PeopleSoft 애플리케이션 설정

>[!Note]
> https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm 참조

1. 관리자 자격 증명을 사용하여 Peoplesoft 콘솔 `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start`에 로그온(예: PS/PS)

    ![관리자 셀프 서비스](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. PeopleSoft 애플리케이션에서 새 사용자 프로필로 **OAMPSFT** 를 만들고 **PeopleSoft 사용자** 와 같은 낮은 보안 역할을 연결합니다.
**Peopletools > 보안 > 사용자 프로필 > 사용자 프로필** 로 이동하여 새 사용자 프로필을 만듭니다. 예를 들면 다음과 같습니다. **OAMPSFT** 및 **Peoplesoft 사용자** 를 추가합니다.

    ![Peoplesoft 사용자](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. 웹 프로필에 액세스하고 공용 액세스 **사용자 ID** 로 **OAMPSFT** 를 입력합니다.

    ![사용자 프로필](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. **PeopleTools 애플리케이션 디자이너** 에서 **FUNCLIB_LDAP** 레코드를 엽니다.

    ![웹 프로필 구성](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. 사용자 헤더를 **OAMSSO_AUTHENTICATION** 함수의 **PS_SSO_UID** 로 업데이트합니다.
**getWWWAuthConfig()** 함수에서 **&defaultUserId** 에 할당된 값을 웹 프로필에서 정의한 **OAMPSFT** 로 바꿉니다. 레코드 정의를 저장합니다.

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. **Signon PeopleCode** 페이지(PeopleTools, 보안, 보안 개체, Signon PeopleCode)에 액세스하여 **OAMSSO_AUTHENTICATION** 함수(Oracle Access Manager SSO를 위한 Signon PeopleCode)를 사용하도록 설정합니다.

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>“PS_SSO_UID” HTTP 헤더를 PeopleSoft 사용자 ID로 채우도록 F5 Big-IP APM 구성

### <a name="configuring-per-request-policy"></a>요청별 정책 구성
1. **액세스 > 프로필/정책 > 요청별 정책** 으로 이동하여 **만들기** 를 클릭하고 다음 정보를 입력한 다음, **마침** 을 클릭합니다.

    * 이름: `<Name>`
    * Profile Type(프로필 유형): 모두
    * 언어: `<Language>`

    ![요청별 정책 구성 ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. **편집** 요청당 정책 `<Name>` ![요청당 정책 PeopleSoftSSO 편집 ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png) 클릭

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>가상 서버에 요청당 정책 할당

**로컬 트래픽 > 가상 서버 > 가상 서버 목록 > PeopleSoftApp** 으로 이동하여 `<Name>`을 요청당 정책으로 지정

![PeopleSoftSSO 요청당 정책 ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>PeopleSoft 애플리케이션에서 단일 로그아웃을 지원하도록 F5 Big-IP APM 설정

모든 PeopleSoft 사용자에게 단일 로그아웃 지원을 추가하려면 다음 단계를 수행합니다.

1. PeopleSoft 포털에 적합한 로그아웃 URL 확인
    * PeopleSoft 애플리케이션에서 사용자 세션을 종료하는 데 사용하는 주소를 확인하려면 아래 예제와 같이 아무 웹 브라우저에서 포털을 열고 브라우저 디버그 도구를 사용하도록 설정해야 합니다.

        ![PeopleSoft 포털의 로그아웃 URL ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * `PT_LOGOUT_MENU` id를 사용하여 요소를 찾고 URL 경로를 쿼리 매개 변수와 함께 저장합니다. 이 예제에서는 `/psp/ps/?cmd=logout` 값을 얻었습니다.

1. 사용자를 APM 로그아웃 URL `/my.logout.php3`로 리디렉션하는 LTM iRule을 만듭니다.

    * **로컬 트래픽 > iRule** 로 이동하여 **만들기** 를 클릭하고 다음 정보를 입력한 다음, **마침** 을 클릭합니다.

      ```text
      Name: `<Name>`
      Definition:
                  _when HTTP_REQUEST {
                      switch -glob -- [HTTP::URI] {
                          `/psp/ps/?cmd=logout` {
                              HTTP::redirect `/my.logout.php3`
                          }
                      }
                  }_
      ```

1. 만든 iRule을 가상 서버에 할당

    * **로컬 트래픽 > 가상 서버 > 가상 서버 목록 > PeopleSoftApp > 리소스** 로 이동합니다. **관리…** 단추를 클릭합니다.   

    * `<Name>`을 활성화된 iRule로 지정하고 **마침** 을 클릭합니다.

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * **이름** 텍스트 상자에 `<Name>` 값을 입력합니다. 

        ![_iRule_PeopleSoftApp 완료](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>Oracle PeopleSoft-Protected by F5 BIG-IP APM 테스트 사용자 만들기

이 섹션에서는 Oracle PeopleSoft-Protected by F5 BIG-IP APM에서 Simon이라는 사용자를 만듭니다. Oracle PeopleSoft-Protected by F5 BIG-IP APM 플랫폼에 사용자를 추가하려면 [Oracle PeopleSoft-Protected by F5 BIG-IP APM 지원 팀](https://support.f5.com)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Oracle PeopleSoft-Protected by F5 BIG-IP APM 로그온 URL로 리디렉션됩니다.  

* Oracle PeopleSoft-Protected by F5 BIG-IP APM 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Oracle PeopleSoft-Protected by F5 BIG-IP APM에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Oracle PeopleSoft-Protected by F5 BIG-IP APM 타일을 클릭할 때 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Oracle PeopleSoft-Protected by F5 BIG-IP APM에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Oracle PeopleSoft-Protected by F5 BIG-IP APM이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
