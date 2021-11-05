---
title: Strata의 Azure Active Directory B2C를 구성하는 자습서
titleSuffix: Azure AD B2C
description: 사용자 확인을 위해 whoIam을 사용한 Azure AD B2C 인증 통합 방법 알아보기.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f2322a6a445c908528163673662cc59227c01711
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044425"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>Strata를 사용하여 온-프레미스 애플리케이션을 보호하는 Azure AD B2C 확장을 위한 자습서

이 샘플 자습서에서는 Strata의 [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/)와 AD(Azure Active Directory) B2C를 통합하는 방법에 대해 알아봅니다.
Maverics Identity Orchestrator는 온-프레미스 애플리케이션을 보호하기 위해 Azure AD B2C를 확장합니다. 모든 ID시스템에 연결하여 투명하게 사용자와 자격 증명을 마이그레이션하고 정책과 구성을 동기화하며 인증 및 세션 관리를 추상화합니다. Strata 엔터프라이즈를 사용하면 애플리케이션을 다시 작성하지 않고도 레거시에서 Azure AD B2C로 신속하게 전환할 수 있습니다. 이 솔루션에는 다음과 같은 이점이 있습니다.

- **온-프레미스 하이브리드 앱에 대한 고객 SSO(Single Sign-On)** : Azure AD B2C는 Maverics Identity Orchestrator를 통해 고객 SSO를 지원합니다. 사용자는 Azure AD B2C 또는 IdP(소셜 ID 공급자)에서 호스트되는 계정으로 로그인합니다. Maverics는 Symantec SiteMinder와 같은 레거시 ID 시스템으로 보호된 앱에 대한 SSO를 확장합니다.

- **표준 기반 SSO를 다시 작성하지 않고 앱으로 확장**: Azure AD B2C를 통해 사용자 액세스를 관리하고 Maverics Identity Orchestrator SAML 또는 OIDC 커넥터로 SSO를 사용하도록 설정합니다.

- **간편한 구성**: Azure AD B2C는 Maverics Identity Orchestrator SAML 또는 OIDC 커넥터를 Azure AD B2C에 연결하는 간단한 단계별 사용자 인터페이스를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결된 [Azure AD B2C 테넌트](./tutorial-create-tenant.md)입니다.

- Maverics Identity Orchestrator에서 사용되는 비밀을 저장하는 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)의 인스턴스입니다. LDAP(Lightweight Directory Access Protocol) 디렉터리나 데이터베이스와 같은 Azure AD B2C 또는 다른 특성 공급자에 연결하는 데 사용됩니다.

- Azure 가상 머신 또는 사용자가 선택한 온-프레미스 서버에 설치되고 실행되는 [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/)의 인스턴스입니다. 소프트웨어를 다운로드하고 설치 및 구성 설명서에 액세스하는 방법에 대한 자세한 내용은 [Strata](https://www.strata.io/contact/)에 문의하세요.

- 레거시 ID 시스템에서 Azure AD B2C로 전환할 온-프레미스 애플리케이션입니다.

## <a name="scenario-description"></a>시나리오 설명

Strata의 Maverics 통합은 다음 구성 요소를 포함합니다.

- **Azure AD B2C**: 사용자의 자격 증명 확인을 담당하는 권한 부여 서버입니다. 인증된 사용자는 Azure AD B2C 디렉터리에 저장된 로컬 계정을 사용하여 온-프레미스 앱에 액세스할 수 있습니다.

- **외부 소셜 또는 엔터프라이즈 IdP**: Facebook, Google 또는 GitHub와 같은 모든 OpenID Connect 공급자입니다. 자세한 내용은 [ID 공급자 정의](./add-identity-provider.md)를 참조하세요.  

- **Strata의 Maverics Identity Orchestrator**: 사용자 로그온을 오케스트레이션하고 HTTP 헤더로 ID를 앱에 투명하게 전달하는 서비스입니다.

다음 다이어그램은 아키텍처 구현을 보여 줍니다.

![이미지는 하이브리드 앱에 액세스할 수 있도록 설정하는 Strata Maverics와 Azure AD B2C의 통합 아키텍처를 보여 줍니다.](./media/partner-strata/strata-architecture-diagram.png)

| 단계 | 설명 |
|:-------|:---------------|
| 1. | 사용자가 온-프레미스 호스트된 애플리케이션에 대한 액세스를 요청합니다. Maverics Identity Orchestrator는 애플리케이션에 대한 사용자의 요청을 프록시합니다.|
| 2. | Orchestrator에서 사용자의 인증 상태를 확인합니다. 세션 토큰이 수신되지 않거나 제공된 세션 토큰이 유효하지 않은 경우 인증을 위해 사용자를 Azure AD B2C로 보냅니다.|
| 3. | Azure AD B2C는 구성된 소셜 IdP로 인증 요청을 보냅니다.|
| 4. | IdP는 사용자에게 자격 증명을 요구합니다. IdP에 따라 사용자는 MFA(다단계 인증)를 수행해야 할 수 있습니다.|
| 5. | IdP 인증에 대한 응답을 Azure AD B2C로 다시 보냅니다. 필요에 따라 해당 단계를 수행하는 동안 사용자가 Azure AD B2C 디렉터리에 로컬 계정을 만들 수 있습니다.|
| 6. | Azure AD B2C는 Azure AD B2C 테넌트에서 Orchestrator 앱 등록 중에 지정된 엔드포인트로 사용자 요청을 보냅니다.|
| 7. | Orchestrator는 액세스 정책을 평가하고 앱에 전달된 HTTP 헤더에 포함할 특성 값을 계산합니다. 이 단계를 수행하는 동안 Orchestrator는 추가 특성 공급자를 호출하여 헤더 값을 설정하는 데 필요한 정보를 검색할 수 있습니다. Orchestrator는 헤더 값을 설정하고 요청을 앱으로 보냅니다.|
| 8. | 이제 사용자가 인증되고 앱에 액세스할 수 있습니다.|

## <a name="get-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator 가져오기
레거시 온-프레미스 앱을 Azure AD B2C와 통합하는 데 사용할 소프트웨어를 가져오려면 [Strata](https://www.strata.io/contact/)에 문의하세요. 소프트웨어를 가져온 후에는 다음 단계에 따라 Orchestrator 관련 필수 구성 요소를 확인하고 필요한 설치 및 구성 단계를 수행합니다.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 구성

1. **애플리케이션 등록**

   a. Azure AD B2C 테넌트에서 [Orchestrator를 애플리케이션으로 등록](./tutorial-register-applications.md?tabs=app-reg-ga)합니다.
   >[!Note]
   >Orchestrator 인스턴스를 구성하는 경우 나중에 테넌트 이름 및 식별자, 클라이언트 ID, 클라이언트 비밀, 구성된 클레임, 리디렉션 URI가 필요합니다.

   b. 애플리케이션에 Microsoft MS Graph API 권한을 부여합니다. 애플리케이션에는 다음 권한 `offline_access`, `openid`가 필요합니다.

   다. 애플리케이션에 대한 리디렉션 URI를 추가합니다. 해당 URI는 `oauthRedirectURL` Orchestrator에 대한 Azure AD B2C 커넥터 구성의 매개 변수와 일치합니다(예: `https://example.com/oidc-endpoint`).

2. **사용자 흐름 만들기**: [등록 및 로그인 사용자 흐름](./tutorial-create-user-flows.md)을 만듭니다.

3. **IdP 추가**: 로컬 계정이나 소셜 또는 엔터프라이즈 [IdP](./add-identity-provider.md)를 통해 사용자가 로그인하도록 선택합니다.

4. **사용자 특성 정의**: 등록 중에 수집할 특성을 정의합니다.

5. **애플리케이션 클레임 지정**: Orchestrator 인스턴스를 통해 애플리케이션에 반환할 특성을 지정합니다. Orchestrator는 Azure AD B2C에서 반환된 클레임의 특성을 사용하며 LDAP 디렉터리 및 데이터베이스와 같은 연결된 기타 ID 시스템에서 추가적인 특성을 검색할 수 있습니다. 해당 특성은 HTTP 헤더에 설정되며 업스트림 온-프레미스 애플리케이션으로 전송됩니다.

## <a name="configure-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator 구성하기

다음 섹션에서는 Orchestrator 인스턴스를 구성하는 데 필요한 단계를 안내합니다. 추가적인 지원 및 설명서는 [Strata](https://www.strata.io/contact/)에 문의하세요.

### <a name="maverics-identity-orchestrator-server-requirements"></a>Maverics Identity Orchestrator 서버 요구 사항

온-프레미스이건 Azure, AWS, GCP와 같은 공급자의 퍼블릭 클라우드 인프라이건 관계없이 모든 서버에서 Orchestrator 인스턴스를 실행할 수 있습니다.

- OS: REHL 7.7 이상, CentOS 7 이상

- 디스크: 10GB(소형)

- 메모리: 16GB 이상

- 포트: 22(SSH/SCP), 443, 80

- 설치/관리 작업에 필요한 루트 액세스 권한

- Maverics Identity Orchestrator는 `systemd`의 `maverics`에서 사용자로 실행됩니다.

- Azure AD 테넌트에 도달할 수 있는 기능을 통해 Maverics Identity Orchestrator를 호스트하는 서버에서 네트워크 송신합니다.

### <a name="install-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator 설치

1. 최신 Maverics RPM 패키지를 받습니다. Maverics를 설치할 시스템에 패키지를 저장합니다. 원격 호스트에 파일을 복사하는 경우 [SCP](https://www.ssh.com/ssh/scp/)는 유용한 도구입니다.

2. Maverics 패키지를 설치하려면 `maverics.rpm` 대신 파일 이름을 바꿔서 다음 명령을 실행합니다.

   `sudo rpm -Uvf maverics.rpm`

   기본적으로 Maverics는 `/usr/local/bin` 디렉터리에 설치됩니다.

3. 설치가 완료되면 Maverics는 `systemd` 아래에서 서비스로 실행됩니다.  Maverics 서비스가 실행 중인지 확인하려면 다음 명령을 실행합니다.

   `sudo service maverics status`

  Orchestrator가 성공적으로 설치되면 다음과 유사한 메시지가 표시됩니다.

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Maverics 서비스가 시작되지 않으면 다음 명령을 실행하여 문제를 조사합니다.

   `journalctl --unit=maverics.service --reverse`

   가장 최근의 로그 항목이 출력의 시작 부분에 표시됩니다.

Maverics를 설치한 후에는 기본 `maverics.yaml` 파일이 `/etc/maverics` 디렉터리에 만들어집니다.

애플리케이션을 보호하도록 Orchestrator를 구성합니다. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9)에서 Azure AD B2C와 통합하고, 저장하고, 비밀을 검색합니다. Orchestrator에서 구성을 읽어올 위치를 정의합니다.

### <a name="supply-configuration-using-environment-variables"></a>환경 변수를 사용하여 구성 제공하기

환경 변수를 통해 Orchestrator 인스턴스에 구성을 제공합니다.

`MAVERICS_CONFIG`

환경 변수는 사용할 YAML 구성 파일과 시작하거나 다시 시작할 때 해당 파일을 찾을 수 있는 위치를 Orchestrator 인스턴스에 지시합니다. `/etc/maverics/maverics.env`의 환경 변수를 설정합니다.

### <a name="create-the-orchestrators-tls-configuration"></a>Orchestrator의 TLS 구성 만들기

`maverics.yaml`의 `tls` 필드는 Orchestrator 인스턴스가 사용하는 전송 레이어 보안 구성을 선언합니다. 커넥터는 TLS 개체와 Orchestrator 서버를 사용할 수 있습니다.

`maverics` 키는 Orchestrator 서버용으로 예약되어 있습니다. 그 외의 모든 키는 사용 가능하며 지정된 커넥터에 TLS 개체를 삽입하는 데 사용할 수 있습니다.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>Azure AD B2C 커넥터 구성하기

Orchestrator는 커넥터를 통해 인증 및 특성 공급자와 통합합니다. 해당 경우 Orchestrator App Gateway는 Azure AD B2C 커넥터를 인증 및 특성 공급자로 사용합니다. Azure AD B2C는 인증에 소셜 IdP를 사용하고, Orchestrator에 대한 특성 공급자 역할을 하며, HTTP 헤더에 설정된 클레임의 특성을 전달합니다.  

해당 커넥터의 구성은 Azure AD B2C 테넌트에 등록된 앱에 해당합니다.

1. 테넌트의 앱 구성에서 클라이언트 ID, 비밀 및 리디렉션 URI를 복사합니다.

2. 커넥터의 이름을 여기에 표시되는 `azureADB2C`로 지정하고 커넥터 `type`를 `azure`로 설정합니다. 해당 값은 아래의 다른 구성 매개 변수에 사용되므로 커넥터 이름을 적어 둡니다.

3. 해당 통합의 경우 `authType`를 `oidc`로 설정해야 합니다.

4. 1단계에서 복사한 클라이언트 ID를 `oauthClientID` 매개 변수의 값으로 설정합니다.

5. 1단계에서 복사한 클라이언트 비밀을 `oauthClientSecret` 매개 변수의 값으로 설정합니다.

6. 1 단계에서 복사한 리디렉션 URI를 `oauthRedirectURL` 매개 변수의 값으로 설정합니다.

7. Azure AD B2C OIDC 커넥터는 잘 알려진 OIDC 엔드포인트를 사용하여 URL 및 서명 키를 비롯한 메타데이터를 검색합니다. `oidcWellKnownURL`의 값을 테넌트의 엔드포인트로 설정합니다.

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>인증 공급자로 Azure AD B2C 정의

인증 공급자는 앱 리소스 요청의 일부로 유효한 세션을 표시하지 않는 사용자에 대해 인증을 수행하는 방법을 결정합니다. Azure AD B2C 테넌트의 구성은 사용자에게 자격 증명을 요구하고 추가 인증 정책을 적용하는 방법을 결정합니다. 예를 들어, 인증 프로세스를 완료하는 데 두 번째 요소를 요구하고 인증에 성공한 후 Orchestrator App Gateway로 어떤 클레임이 반환되어야 하는지 결정하는 것입니다.

`authProvider`의 값은 커넥터의 `name`와 일치해야 합니다.

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>Orchestrator App Gateway를 사용하여 온-프레미스 앱 보호

Orchestrator의 App Gateway 구성은 Azure AD B2C가 애플리케이션을 보호하는 방법 및 사용자가 앱에 액세스하는 방법을 선언합니다.

1. App Gateway의 이름을 만듭니다. 친숙한 이름 또는 정규화된 호스트 이름을 앱에 대한 식별자로 사용할 수 있습니다.

2. `location`를 설정합니다. 예제는 애플리케이션의 루트 `/`를 사용하지만, 애플리케이션의 모든 URL 경로일 수 있습니다.

3. 호스트를 사용하여 `upstream`에서 보호된 애플리케이션을 정의합니다(포트 규칙: `https://example.com:8080`).

4. 오류 및 권한이 없는 페이지에 대해 값을 설정합니다.

5. 애플리케이션에 제공해야 하는 HTTP 헤더 이름 및 특성 값을 정의하여 인증을 설정하고 앱에 대한 액세스를 제어합니다. 헤더 이름은 임의로 정해지며 일반적으로 앱의 구성에 해당합니다. 특성 값은 해당 값을 제공하는 커넥터에 의해 네임스페이스됩니다. 아래 예제의 경우 Azure AD B2C로부터 반환된 값에는 커넥터 이름이 접두사로 붙습니다. `azureADB2C`여기서 커넥터 이름은 필요한 값을 포함하는 특성이 접미사로 붙습니다(예: `given_name`).

6. 정책을 평가하고 적용하도록 설정합니다. `allowUnauthenticated`, `allowAnyAuthenticated` 및 `allowIfAny`, 세 가지 작업이 정의됩니다. 각 작업은 `resource`에 연결되며 `resource`에 대한 정책이 평가됩니다.

>[!NOTE]
>`headers`과 `policies` 모두 JavaScript 또는 GoLang service 확장을 사용하여 기본 기능을 크게 향상시키는 임의의 논리를 구현합니다.

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>Azure Key Vault를 비밀 공급자로 사용

Orchestrator가 Azure AD B2C 및 기타 ID 시스템 연결에 사용하는 비밀을 보호하는 것이 중요합니다. Maverics는 기본적으로 비밀을 `maverics.yaml`에서 일반 문자로 로드하지만 이 자습서에서는 Azure Key Vault를 비밀 공급자로 사용합니다.

지침에 따라 Orchestrator 인스턴스에서 비밀 공급자로 사용할 [새 Key Vault를 만듭니다](../key-vault/secrets/quick-create-portal.md). 자격 증명 모음에 비밀을 추가하고 각 비밀에 주어진 `SECRET NAME`을 기록해 둡니다. 예들 들어 `AzureADB2CClientSecret`입니다.

`maverics.yaml` 구성 파일에서 값을 비밀로 선언하려면 비밀을 꺾쇠 괄호로 묶습니다.

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

꺾쇠 괄호 안에 지정된 값은 Azure Key Vault에 비밀로 주어진 `SECRET NAME`과 일치해야 합니다.

Azure Key Vault에서 비밀을 로드하려면 다음 패턴을 사용하여 azure-credentials.json 파일에 있는 자격 증명을 통해 `MAVERICS_SECRET_PROVIDER`파일`/etc/maverics/maverics.env`의 환경 변수를 설정합니다.

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>모든 항목을 함께 넣기

위에서 설명한 구성을 완료할 때 Orchestrator의 구성이 표시되는 방법은 다음과 같습니다.

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>흐름 테스트

1. 온-프레미스 애플리케이션 URL로 이동합니다`https://example.com/sonar/dashboard`.

2. 사용자 흐름에서 구성한 페이지로 Orchestrator를 리디렉션해야 합니다.

3. 페이지의 목록에서 IdP를 선택합니다.

4. IdP로 리디렉션된 후에는 MFA 토큰을 포함하여(해당 IdP가 요구하는 경우) 요청에 따라 자격 증명을 제공합니다.

5. 성공적으로 인증되면 앱 요청을 Orchestrator 리디렉션 URI로 전달하는 Azure AD B2C로 리디렉션됩니다.

6. Orchestrator는 정책을 평가하고 헤더를 계산하며 사용자를 업스트림 애플리케이션으로 보냅니다.  

7. 요청된 애플리케이션을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
