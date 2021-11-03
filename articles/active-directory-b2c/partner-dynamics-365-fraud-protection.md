---
title: Microsoft Dynamics 365 Fraud Protection을 사용하여 Azure Active Directory B2C를 구성하는 자습서
titleSuffix: Azure AD B2C
description: 위험한 계정 및 사기성 계정을 식별하기 위해 Microsoft Dynamics 365 Fraud Protection을 사용하여 Azure Active Directory B2C를 구성하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 5/12/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 83715b34578176ba08fb364cfae11df8b1cf1209
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044725"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C을 사용하여 Microsoft Dynamics 365 Fraud Protection 구성

이 샘플 자습서에서는 Azure AD(Active Directory) B2C를 사용하여 [Microsoft DFP](/dynamics365/fraud-protection/overview)(Dynamics 365 Fraud Protection)를 통합하는 방법을 알아봅니다.

Microsoft DFP는 사기성 계정 및 로그인을 만들기 위한 시도의 위험을 평가하는 기능을 조직에 제공합니다. 고객은 Microsoft DFP 평가를 사용하여 새로운 가짜 계정을 만들거나 기존 계정의 보안을 침해하려는 의심스러운 시도를 차단하거나 방해할 수 있습니다.

이 샘플에서는 Microsoft DFP 디바이스 지문 인식과 계정 생성 및 로그인 평가 API 엔드포인트를 Azure AD B2C 사용자 지정 정책에 통합하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테넌트](./tutorial-create-tenant.md). 테넌트는 Azure 구독과 연결됩니다.

- Microsoft EFP [구독](https://dynamics.microsoft.com/pricing/#Sales)을 가져옵니다. [평가판 클라이언트 버전](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin)도 설정할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

Microsoft EFP 통합에는 다음 구성 요소가 포함됩니다.

- **Azure AD B2C 테넌트**: 사용자를 인증하고 Microsoft DFP의 클라이언트 역할을 합니다. 대상 정책을 실행하는 모든 사용자의 식별 및 진단 데이터를 수집하는 핑거프린팅 스크립트를 호스팅합니다. 나중에 Microsoft DFP에서 반환된 규칙 평가 결과에 따라 로그인 또는 등록 시도를 차단하거나 방해합니다.

- **사용자 지정 UI 템플릿**: Azure AD B2C에서 렌더링되는 페이지의 HTML 콘텐츠를 사용자 지정하는 데 사용됩니다. 해당 페이지에는 Microsoft DFP 지문 인식에 필요한 JavaScript 코드 조각이 포함되어 있습니다.

- **Microsoft DFP 핑거프린팅 서비스**: 동적으로 포함된 스크립트이며, 디바이스 원격 분석 및 자체 어설션된 사용자 세부 정보를 기록하여 사용자가 의사 결정 과정에서 나중에 사용할 수 있도록 고유하게 식별할 수 있는 지문을 만듭니다.

- **Microsoft DFP API 엔드포인트**: 의사 결정 결과를 제공하고 클라이언트 애플리케이션에서 수행하는 작업을 반영하는 최종 상태를 수락합니다. Azure AD B2C는 REST API 커넥터를 사용하여 Microsoft DFP 엔드포인트와 직접 통신합니다. API 인증은 전달자 토큰을 확보하기 위해 Microsoft DFP에 라이선스가 부여되고 설치되는 Microsoft Azure AD 테넌트에 대한 client_credentials 부여를 통해 발생합니다.

다음 아키텍처 다이어그램은 구현된 상황을 보여 줍니다.

![Microsoft Dynamics 365 Fraud Protection 아키텍처 다이어그램을 보여 주는 이미지](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|단계 | 설명 |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지에 도달합니다. 사용자는 등록을 선택하여 새 계정을 만들고 페이지에 정보를 입력합니다. Azure AD B2C는 사용자 특성을 수집합니다.
| 2. | Azure AD B2C는 Microsoft DFP API를 호출하고 사용자 특성을 전달합니다.
| 3. | Microsoft DFP API는 정보를 사용하고 처리한 후 Azure AD B2C에 결과를 반환합니다.
| 4. | Azure AD B2C는 Microsoft DFP API에서 정보를 다시 받습니다. 오류 응답이 표시되면 사용자에게 오류 메시지가 표시됩니다. 성공 응답이 표시되면 사용자가 인증되고 디렉터리에 기록됩니다.

## <a name="set-up-the-solution"></a>솔루션 설정

1. Azure AD B2C 페더레이션을 허용하도록 구성된 [Facebook 애플리케이션을 만듭니다](./identity-provider-facebook.md#create-a-facebook-application).
2. 만든 [Facebook 비밀](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#create-the-facebook-key)을 Identity Experience Framework 정책 키로 추가합니다.

## <a name="configure-your-application-under-microsoft-dfp"></a>Microsoft DFP에서 애플리케이션 구성

Microsoft DFP를 사용하도록 [Azure AD 테넌트를 설정](/dynamics365/fraud-protection/integrate-real-time-api)합니다.

## <a name="set-up-your-custom-domain"></a>사용자 지정 도메인 설정

프로덕션 환경에서는 [Azure AD B2C](./custom-domain.md?pivots=b2c-custom-policy) 및 [Microsoft DFP 지문 인식 서비스를 위한 사용자 지정 도메인](/dynamics365/fraud-protection/device-fingerprinting#set-up-dns)을 사용해야 합니다. 브라우저 개인 정보 설정이 도메인 간 쿠키를 차단하는 것을 방지하려면 두 서비스에 대한 도메인이 동일한 루트 DNS 영역에 있어야 합니다. 이는 비프로덕션 환경에서는 필요하지 않습니다.

다음은 예제입니다.

| 환경 | 서비스 | 도메인 |
|:------------|:---------------|:---------------|
| 개발 | Azure AD B2C | `contoso-dev.b2clogin.com` |
| 개발 | Microsoft DFP 지문 인식 | `fpt.dfp.microsoft-int.com` |
| UAT | Azure AD B2C | `contoso-uat.b2clogin.com` |
| UAT | Microsoft DFP 지문 인식 | `fpt.dfp.microsoft.com` |
| 프로덕션 | Azure AD B2C | `login.contoso.com` |
| 프로덕션 | Microsoft DFP 지문 인식 | `fpt.login.contoso.com` |

## <a name="deploy-the-ui-templates"></a>UI 템플릿 배포

1. 제공된 [Azure AD B2C UI 템플릿](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/ui-templates)을 Azure Blob Storage와 같은 퍼블릭 인터넷 호스팅 서비스에 배포합니다.

2. `https://<YOUR-UI-BASE-URL>/` 값을 배포 위치의 루트 URL로 바꿉니다.

  >[!NOTE]
  >나중에 Azure AD B2C 정책을 구성하려면 기준 URL이 필요합니다.

3. `ui-templates/js/dfp.js` 파일에서 `<YOUR-DFP-INSTANCE-ID>`를 Microsoft DFP 인스턴스 ID로 바꿉니다.

4. Azure AD B2C 도메인 이름 `https://{your_tenant_name}.b2clogin.com` 또는 `your custom domain`에 대해 CORS를 사용하도록 설정했는지 확인합니다.

자세한 내용은 [UI 사용자 지정 설명서](./customize-ui-with-html.md?pivots=b2c-custom-policy)를 참조하세요.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C 구성 업데이트

### <a name="add-policy-keys-for-your-microsoft-dfp-client-app-id-and-secret"></a>Microsoft DFP 클라이언트 앱 ID 및 비밀에 대한 정책 키 추가

1. Microsoft DFP가 설정된 Azure AD 테넌트에서 [Azure AD 애플리케이션을 만들고 관리자 동의를 부여](/dynamics365/fraud-protection/integrate-real-time-api#create-azure-active-directory-applications)합니다.
2. 애플리케이션 등록에 대한 비밀 값을 만들고 애플리케이션의 클라이언트 ID 및 클라이언트 암호 값을 메모합니다.
3. 클라이언트 ID 및 클라이언트 암호 값을 [Azure AD B2C 테넌트에 정책 키](./policy-keys-overview.md)로 저장합니다.

 >[!NOTE]
 >나중에 Azure AD B2C 정책을 구성하려면 정책 키가 필요합니다.

### <a name="replace-the-configuration-values"></a>구성 값 바꾸기

제공된 [사용자 지정 정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/policies)에서 다음 자리 표시자를 찾아 인스턴스의 해당 값으로 바꿉니다.

| 자리표시자 | 다음 항목으로 교체 | 참고 |
| :-------- | :------------| :-----------|
|{Settings:Production} | 프로덕션 모드에서 정책을 배포할지 여부 | `true` 또는 `false`  |
|{Settings:Tenant} | 테넌트 약식 이름 |  `your-tenant` -your-tenant.onmicrosoft.com에서  |
| {Settings:DeploymentMode}    |  사용할 Application Insights 배포 모드   |  `Production` 또는 `Development`  |
|  {Settings:DeveloperMode}    | Application Insights 개발자 모드에서 정책을 배포할지 여부      |   `true` 또는 `false`    |
|  {Settings:AppInsightsInstrumentationKey}  |  Application Insights 인스턴스의 계측 키*   |  `01234567-89ab-cdef-0123-456789abcdef` |
|  {Settings:IdentityExperienceFrameworkAppId}  | Azure AD B2C 테넌트에 구성된 IdentityExperienceFramework 앱의 앱 ID  | `01234567-89ab-cdef-0123-456789abcdef`|
|  {Settings:ProxyIdentityExperienceFrameworkAppId}  |   Azure AD B2C 테넌트에 구성된 ProxyIdentityExperienceFramework 앱의 앱 ID |   `01234567-89ab-cdef-0123-456789abcdef`|
| {Settings:FacebookClientId}  | B2C와 페더레이션을 위해 구성된 Facebook 앱의 앱 ID    | `000000000000000`   |
|   {Settings:FacebookClientSecretKeyContainer}  | Facebook의 앱 비밀을 저장한 정책 키의 이름    |  `B2C_1A_FacebookAppSecret` |
|   {Settings:ContentDefinitionBaseUri} |  UI 파일을 배포한 엔드포인트   | `https://<my-storage-account>.blob.core.windows.net/<my-storage-container>`   |
|  {Settings:DfpApiBaseUrl}   |  DFP API 인스턴스의 기본 경로 - DFP 포털 참조   | `https://tenantname-01234567-89ab-cdef-0123-456789abcdef.api.dfp.dynamics.com/v1.0/`   |
| {Settings:DfpApiAuthScope} | DFP API 서비스의 client_credentials 범위 | `https://api.dfp.dynamics-int.com/.default or https://api.dfp.dynamics.com/.default` |
| {Settings:DfpTenantId} | DFP에 라이선스가 부여되고 설치된 Azure AD 테넌트(B2C 아님)의 ID | `01234567-89ab-cdef-0123-456789abcdef` 또는 `consoto.onmicrosoft.com` |
| {Settings:DfpAppClientIdKeyContainer} | DFP 클라이언트 ID를 저장하는 정책 키의 이름 | `B2C_1A_DFPClientId` |
| {Settings:DfpAppClientSecretKeyContainer} | DFP 클라이언트 암호를 저장하는 정책 키의 이름 | `B2C_1A_DFPClientSecret` |

*모든 Azure AD 테넌트/구독에서 Application insights를 설정할 수 있습니다. 이 값은 선택 사항이지만 [디버깅을 지원하기 위해 권장](./troubleshoot-with-application-insights.md)됩니다.

>[!NOTE]
>특성 컬렉션 페이지에 동의 알림을 추가합니다. 계정 보호를 위해 사용자의 원격 분석 및 사용자 ID 정보가 기록됨을 알립니다.

## <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C 정책 구성

1. 정책 폴더의 [Azure AD B2C 정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/policies)으로 이동합니다.

2. 이 [문서](./tutorial-create-user-flows.md?pivots=b2c-custom-policy?tabs=applications#custom-policy-starter-pack)에 따라 [LocalAccounts 스타터 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)을 다운로드합니다.

3. Azure AD B2C 테넌트에 대한 정책을 구성합니다.

>[!NOTE]
>특정 테넌트와 관련하여 제공되는 정책을 업데이트합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트를 열고 정책에서 **Identity Experience Framework** 를 선택합니다.

2. 이전에 만든 **SignUpSignIn** 을 선택합니다.

3. **사용자 흐름 실행** 을 선택하고 설정을 선택합니다.

   a. **애플리케이션**: 등록된 앱을 선택합니다(JWT는 샘플).

   b. **회신 URL**: **리디렉션 URL** 을 선택합니다.

   다. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름을 따라 계정 만들기

5. 사용자 특성을 만든 후 흐름을 따라 진행하는 동안 Microsoft DFP 서비스가 호출됩니다. 흐름이 불완전한 경우 사용자가 디렉터리에 저장되지 않았는지 확인합니다.

>[!NOTE]
>[Microsoft DFP 규칙 엔진](/dynamics365/fraud-protection/rules)을 사용하는 경우 Microsoft DFP 포털에서 규칙을 직접 업데이트합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Microsoft DFP 샘플](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
