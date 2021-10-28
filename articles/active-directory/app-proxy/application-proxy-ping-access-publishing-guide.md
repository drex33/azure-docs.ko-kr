---
title: Azure Active Directory 애플리케이션 프록시용 PingAccess를 통한 헤더 기반 인증
description: PingAccess 및 앱 프록시를 사용하여 애플리케이션을 게시하여 헤더 기반 인증을 지원합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: ff80311cc4fc8fd335eb14c28f9c28f2efacb27d
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989003"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>애플리케이션 프록시 및 PingAccess를 사용하여 Single Sign-On에 대한 헤더 기반 인증

Azure AD(Azure Active Directory) 애플리케이션 프록시는 Azure AD 고객이 더 많은 애플리케이션에 액세스할 수 있도록 PingAccess와 파트너 관계를 맺고 있습니다. PingAccess는 통합된 [헤더 기반 Single Sign-On](application-proxy-configure-single-sign-on-with-headers.md) 이외의 다른 옵션을 제공합니다.

## <a name="whats-pingaccess-for-azure-ad"></a>Azure AD용 PingAccess란?

Azure AD용 PingAccess를 사용하면 사용자에게 인증에 헤더를 사용하는 애플리케이션에 대한 액세스 권한 및 SSO(Single Sign-On)를 제공할 수 있습니다. 애플리케이션 프록시는 이러한 애플리케이션을 Azure AD를 사용하여 액세스를 인증한 다음, 커넥터 서비스를 통해 트래픽을 전달하는 방식으로 다른 앱과 유사하게 처리합니다. PingAccess는 애플리케이션 앞에 배치되고 액세스 토큰을 Azure AD에서 헤더로 변환합니다. 그러면 애플리케이션이 읽을 수 있는 형식으로 인증을 받습니다.

사용자는 회사 애플리케이션을 사용하기 위해 로그인할 때 다른 점을 알아차리지 못합니다. 여전히 어디에서든지 모든 디바이스에서 작업할 수 있습니다. 애플리케이션 프록시 커넥터는 인증 유형에 관계없이 모든 앱에 원격 트래픽을 전달하므로 계속해서 자동으로 부하를 분산합니다.

## <a name="how-do-i-get-access"></a>액세스 권한은 어떻게 얻을 수 있나요?

이 시나리오는 Azure Active Directory와 PingAccess 간의 파트너 관계를 통해 제공되므로 두 서비스에 대한 라이선스가 필요합니다. 그러나 Azure Active Directory Premium 구독에는 최대 20개의 애플리케이션을 보장하는 기본 PingAccess 라이선스가 포함되어 있습니다. 헤더 기반 애플리케이션을 20개 이상 게시해야 하는 경우 PingAccess에서 라이선스를 추가로 구입할 수 있습니다.

자세한 내용은 [Azure Active Directory 버전](../fundamentals/active-directory-whatis.md)을 참조하세요.

## <a name="publish-your-application-in-azure"></a>Azure에 애플리케이션 게시

이 문서는 처음으로 이 시나리오를 사용하여 애플리케이션을 게시하는 사람들을 위한 것입니다. 게시 단계를 자세히 설명하는 것 외에도 애플리케이션 프록시 및 PingAccess를 시작하는 과정을 안내합니다. 두 서비스를 이미 구성했지만 게시 단계에서 리프레셔를 원하는 경우 [애플리케이션 프록시를 사용하여 Azure AD에 애플리케이션 추가](#add-your-application-to-azure-ad-with-application-proxy)로 건너뜁니다.

> [!NOTE]
> 이 시나리오는 Azure AD와 PingAccess 간의 파트너 관계이므로 일부 지침은 Ping ID 사이트에 존재합니다.

### <a name="install-an-application-proxy-connector"></a>애플리케이션 프록시 커넥터 설치

이미 애플리케이션 프록시를 사용하도록 설정되어 있고 커넥터가 설치되어 있는 경우 이 섹션을 건너뛰고 [애플리케이션 프록시를 사용하여 Azure AD에 애플리케이션 추가](#add-your-application-to-azure-ad-with-application-proxy)로 이동할 수 있습니다.

애플리케이션 프록시 커넥터는 원격 직원의 트래픽을 게시된 애플리케이션으로 전달하는 Windows Server 서비스입니다. 자세한 설치 지침은 [자습서: Azure Active Directory에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](../app-proxy/application-proxy-add-on-premises-application.md)를 참조하세요.

1. [Azure Active Directory 포털](https://aad.portal.azure.com/)에 애플리케이션 관리자 권한으로 로그인합니다. **Azure Active Directory 관리 센터** 페이지가 나타납니다.
1. **Azure Active Directory** > **애플리케이션 프록시** > **다운로드 커넥터 서비스** 를 선택합니다. **애플리케이션 프록시 커넥터 다운로드** 페이지가 나타납니다.

   ![애플리케이션 프록시 커넥터 다운로드](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. 설치 지침을 따릅니다.

커넥터를 다운로드하면 디렉터리에 대해 애플리케이션 프록시가 자동으로 활성화되지만 그렇지 않은 경우 **애플리케이션 프록시 사용** 을 선택할 수 있습니다.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>애플리케이션 프록시를 사용하여 Azure AD에 애플리케이션 추가

Azure Portal에서 수행해야 하는 두 가지 작업이 있습니다. 먼저 애플리케이션 프록시를 사용하여 애플리케이션을 게시해야 합니다. 그런 다음 PingAccess 단계에서 사용할 수 있는 해당 애플리케이션에 대한 정보를 수집해야 합니다.

#### <a name="publish-your-application"></a>애플리케이션 게시

먼저 애플리케이션을 게시해야 합니다. 이 작업은 다음을 포함합니다.

- Azure AD에 온-프레미스 애플리케이션을 추가
- 애플리케이션 테스트를 위한 사용자를 할당하고 헤더 기반 SSO를 선택
- 애플리케이션의 리디렉션 URL 설정
- 사용자 및 다른 애플리케이션에서 온-프레미스 애플리케이션을 사용할 수 있는 권한 부여

사용자 고유의 온-프레미스 애플리케이션을 게시하려면

1. 마지막 섹션에서 로그인하지 않았다면 [Azure Active Directory 포털](https://aad.portal.azure.com/)에 애플리케이션 관리자 권한으로 로그인합니다.
1. **엔터프라이즈 애플리케이션** > **새 애플리케이션** > **온-프레미스 애플리케이션 추가** 를 선택합니다. **사용자 고유의 온-프레미스 애플리케이션 추가** 페이지가 나타납니다.

   ![사용자 고유의 온-프레미스 애플리케이션 추가](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. 새 애플리케이션에 대한 정보로 필수 필드를 작성합니다. 설정은 다음 지침을 사용합니다.

   > [!NOTE]
   > 이 단계에 대한 자세한 연습은 [Azure AD에 온-프레미스 앱 추가](../app-proxy/application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)를 참조하세요.

   1. **내부 URL**: 회사 네트워크에 있을 때 일반적으로 앱의 로그인 페이지로 안내하는 URL을 제공합니다. 이 시나리오의 경우 커넥터에서 PingAccess 프록시를 애플리케이션의 기본 페이지로 처리해야 합니다. `https://<host name of your PingAccess server>:<port>` 형식을 사용합니다. 포트는 기본적으로 3000이지만 PingAccess에서 구성할 수 있습니다.

      > [!WARNING]
      > 이 유형의 Single Sign-On에서는 내부 URL은 `https`를 사용해야 하고 `http`를 사용할 수 없습니다. 또한 애플리케이션을 구성할 때 두 앱이 동일한 내부 URL을 사용할 수 없다는 제약 조건이 있습니다. 이는 앱 프록시가 애플리케이션을 구분할 수 있도록 하기 위한 것입니다.

   1. **사전 인증 방법**: **Azure Active Directory** 를 선택합니다.
   1. **헤더에서 URL 변환**: **아니요** 를 선택합니다.

   > [!NOTE]
   > 첫 번째 애플리케이션인 경우 3000 포트를 사용하여 시작하고 PingAccess 구성을 변경하면 다시 돌아와서 이 설정을 업데이트합니다. 후속 애플리케이션의 경우 포트는 사용자가 PingAccess에서 구성한 수신기와 일치해야 합니다. [PingAccess의 수신기](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)에 대해 자세히 알아봅니다.

1. **추가** 를 선택합니다. 새 애플리케이션의 개요 페이지가 나타납니다.

이제 애플리케이션 테스트를 위해 사용자를 할당하고 헤더 기반 Single Sign-On을 선택합니다.

1. 애플리케이션 사이드바에서 **사용자 및 그룹** > **사용자 추가** > **사용자 및 그룹(\<Number> 선택됨)** 를 선택합니다. 선택할 수 있는 사용자 및 그룹의 목록이 표시됩니다.

   ![사용자 및 그룹의 목록을 보여 줍니다.](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. 애플리케이션 테스트를 위한 사용자를 선택하고 **선택** 을 선택합니다. 이 테스트 계정에 온-프레미스 애플리케이션에 대한 액세스 권한이 있는지 확인합니다.
1. **할당** 을 선택합니다.
1. 애플리케이션 사이드바에서 **Single Sign-On** > **헤더 기반** 을 선택합니다.

   > [!TIP]
   > 헤더 기반 Single Sign-On을 처음 사용하는 경우 PingAccess를 설치해야 합니다. Azure 구독이 PingAccess 설치와 자동으로 연결되도록 하려면 이 Single Sign-On 페이지의 링크를 사용하여 PingAccess를 다운로드합니다. 지금 다운로드 사이트를 열거나 나중에 이 페이지로 다시 돌아올 수 있습니다.

   ![헤더 기반 로그온 화면 및 PingAccess를 보여 줍니다.](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. **저장** 을 선택합니다.

그런 다음 리디렉션 URL이 외부 URL로 설정되었는지 확인합니다.

1. **Azure Active Directory 관리 센터** 사이드바에서 **Azure Active Directory** > **앱 등록** 을 선택합니다. 애플리케이션의 목록이 나타납니다.
1. 애플리케이션을 선택합니다.
1. 웹 및 공용 클라이언트에 대해 설정된 리디렉션 URI 수를 표시하는 **리디렉션 URI** 옆의 링크를 선택합니다. **\<application name> - 인증** 페이지가 나타납니다.
1. 이전에 애플리케이션에 할당한 외부 URL이 **리디렉션 URI** 목록에 있는지 확인합니다. 그렇지 않으면 **웹** 의 리디렉션 URI 형식을 사용하여 지금 외부 URL을 추가하고 **저장** 을 선택합니다.

외부 URL 외에도 외부 URL에 대한 Azure Active Directory의 권한 부여 엔드포인트를 리디렉션 URI 목록에 추가해야 합니다.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

마지막으로, 사용자에게 읽기 액세스 권한을 부여하고 다른 애플리케이션에게 읽기/쓰기 액세스 권한을 부여하도록 온-프레미스 애플리케이션을 설정합니다.

1. 애플리케이션의 **앱 등록** 사이드바에서 **API 사용 권한** > **권한 추가** > **Microsoft API** > **Microsoft Graph** 를 선택합니다. **Microsoft Graph** 의 **API 사용 권한 요청** 페이지가 나타납니다. 여기에 Windows Azure Active Directory에 대한 API가 포함되어 있습니다.

   ![API 사용 권한 요청 페이지를 보여 줍니다.](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. **위임된 권한** > **사용자** > **User.Read** 를 선택합니다.
1. **애플리케이션 사용 권한** > **애플리케이션** > **Application.ReadWrite.All** 을 선택합니다.
1. **모든 권한** 을 선택합니다.
1. **API 사용 권한** 페이지에서 **\<your directory name>에 대한 관리자 동의 허용** 을 선택합니다.

#### <a name="collect-information-for-the-pingaccess-steps"></a>PingAccess 단계에 대한 정보 수집

다음 세 가지 정보(모든 GUID)를 수집하여 PingAccess로 애플리케이션을 설정해야 합니다.

| Azure AD 필드의 이름 | PingAccess 필드의 이름 | 데이터 형식 |
| --- | --- | --- |
| **애플리케이션(클라이언트) ID** | **클라이언트 ID** | GUID |
| **디렉터리(테넌트) ID** | **발급자** | GUID |
| `PingAccess key` | **클라이언트 암호** | 임의 문자열 |

이 정보를 수집하려면

1. **Azure Active Directory 관리 센터** 사이드바에서 **Azure Active Directory** > **앱 등록** 을 선택합니다. 애플리케이션의 목록이 나타납니다.
1. 애플리케이션을 선택합니다. 애플리케이션의 **앱 등록** 페이지가 나타납니다.

   ![애플리케이션의 등록 개요](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. **애플리케이션(클라이언트) ID** 값 옆에서 **클립보드에 복사** 아이콘을 선택하여 값을 복사하고 저장합니다. 나중에 이 값을 PingAccess의 클라이언트 ID로 지정합니다.
1. **디렉터리(테넌트) ID** 값 옆에서도 **클립보드에 복사** 아이콘을 선택하여 값을 복사하고 저장합니다. 나중에 이 값을 PingAccess의 발급자로 지정합니다.
1. 애플리케이션의 **앱 등록** 사이드바에서 **인증서 및 비밀** > **새 클라이언트 비밀** 을 선택합니다. **클라이언트 비밀 추가** 페이지가 나타납니다.

   ![클라이언트 비밀 추가 페이지를 보여 줍니다.](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. **설명** 에서 `PingAccess key`를 입력합니다.
1. **만료** 에서 PingAccess 키를 설정하는 방법을 **1년 후**, **2년 후** 또는 **기한 없음** 중에서 선택합니다.
1. **추가** 를 선택합니다. PingAccess 키가 클라이언트 비밀 테이블에 **값** 필드가 임의 문자열로 채워진 상태로 표시됩니다.
1. PingAccess 키의 **값** 필드 옆에서 **클립보드로 복사** 아이콘을 선택하여 값을 복사하고 저장합니다. 나중에 이 값을 PingAccess의 클라이언트 비밀로 지정합니다.

**`acceptMappedClaims` 필드를 업데이트합니다.**

1. [Azure Active Directory 포털](https://aad.portal.azure.com/)에 애플리케이션 관리자 권한으로 로그인합니다.
1. **Azure Active Directory** > **앱 등록** 을 선택합니다. 애플리케이션의 목록이 나타납니다.
1. 애플리케이션을 선택합니다.
1. 애플리케이션의 **앱 등록** 페이지의 사이드바에서 **매니페스트** 를 선택합니다. 애플리케이션의 등록에 대한 매니페스트 JSON 코드가 표시됩니다.
1. `acceptMappedClaims`를 검색하여 값을 `True`로 변경합니다.
1. **저장** 을 선택합니다.

### <a name="use-of-optional-claims-optional"></a>선택적 클레임 사용(선택 사항)

선택적 클레임을 사용하여 모든 사용자 및 테넌트에 포함된 기본 제공 클레임을 추가할 수 있습니다. 애플리케이션 매니페스트를 수정하여 애플리케이션의 선택적 클레임을 구성할 수 있습니다. 자세한 내용은 [Azure AD 애플리케이션 매니페스트 이해 문서](../develop/reference-app-manifest.md)를 참조하세요.

PingAccess가 사용할 access_token에 이메일 주소를 포함하는 예제는 다음과 같습니다.

```json
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>클레임 매핑 정책 사용(선택 사항)

AzureAD에 없는 특성에 대한 [클레임 매핑 정책(미리 보기)](../develop/reference-claims-mapping-policy-type.md#claims-mapping-policy-properties)입니다. 클레임 매핑을 사용하면 ADFS 또는 사용자 개체에 의해 지원되는 사용자 지정 클레임을 추가하여 오래된 온-프레미스 앱을 클라우드로 마이그레이션할 수 있습니다.

애플리케이션에서 사용자 지정 클레임을 사용하도록 하고 추가 필드를 포함하려면 [사용자 지정 클레임 매핑 정책을 만들고 애플리케이션에 할당](../develop/active-directory-claims-mapping.md)해야 합니다.

> [!NOTE]
> 사용자 지정 클레임을 사용하려면 사용자 지정 정책도 정의하고 애플리케이션에 할당해야 합니다. 이 정책은 필요한 모든 사용자 지정 특성을 포함해야 합니다.
>
> PowerShell 또는 Microsoft Graph를 통해 정책 정의 및 할당을 수행할 수 있습니다. 이 작업을 PowerShell에서 수행하는 경우 먼저 `New-AzureADPolicy`를 사용한 후 `Add-AzureADServicePrincipalPolicy`를 사용하여 애플리케이션에 할당해야 할 수 있습니다. 자세한 내용은 [클레임 매핑 정책 할당](../develop/active-directory-claims-mapping.md)을 참조하세요.

예제:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>PingAccess에서 사용자 지정 클레임을 사용하도록 설정

PingAccess에서 사용자 지정 클레임을 사용하도록 설정하는 것은 선택 사항이지만, 애플리케이션에서 추가 클레임을 사용해야 하는 경우에는 필수입니다.

다음 단계에서 PingAccess를 구성할 때 만들 웹 세션(설정->액세스->웹 세션)에서 **요청 프로필** 을 선택 취소하고 **사용자 특성 새로 고침** 을 **아니요** 로 설정해야 합니다.

## <a name="download-pingaccess-and-configure-your-application"></a>PingAccess 다운로드 및 애플리케이션 구성

이제 Azure Active Directory 설정 단계를 모두 완료했으므로 PingAccess 구성으로 이동할 수 있습니다.

이 시나리오의 PingAccess 부분에 대한 자세한 단계는 Ping ID 문서에서 계속됩니다. Ping ID 웹 사이트에서 [Microsoft Azure AD 애플리케이션 프록시를 사용하여 게시된 애플리케이션을 보호하도록 Azure AD용 PingAccess 구성](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)의 지침에 따라 [최신 버전의 PingAccess](https://www.pingidentity.com/en/lp/azure-download.html?)를 다운로드합니다.

이러한 단계를 통해 PingAccess를 설치하고 PingAccess 계정(아직 없는 경우)을 설정할 수 있습니다. 그런 다음 Azure AD OIDC(OpenID Connect) 연결을 만들려면 Azure AD 포털에서 복사한 **디렉터리(테넌트) ID** 값을 사용하여 토큰 공급자를 설정합니다. 다음으로, PingAccess에서 웹 세션을 만들려면 **애플리케이션(클라이언트) ID** 및 `PingAccess key` 값을 사용합니다. 그런 다음 ID 매핑을 설정하고 가상 호스트, 사이트 및 애플리케이션을 만들 수 있습니다.

### <a name="test-your-application"></a>애플리케이션 테스트

이러한 단계를 모두 완료하면 애플리케이션이 준비되고 실행되어야 합니다. 테스트하려면 브라우저를 열고 Azure에 애플리케이션을 게시할 때 만든 외부 URL로 이동합니다. 애플리케이션에 할당한 테스트 계정으로 로그인합니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure AD 애플리케이션 프록시를 사용하여 게시된 애플리케이션을 보호하도록 Azure AD용 PingAccess 구성](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](../manage-apps/what-is-single-sign-on.md)
- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)