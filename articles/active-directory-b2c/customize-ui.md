---
title: 사용자 인터페이스 사용자 지정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용하는 애플리케이션의 사용자 인터페이스를 사용자 지정하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/06/2021
ms.custom: project-no-code, b2c-support
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f227c63ef837b0de60ee72e2f33084e744f2f96f
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535602"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 인터페이스 사용자 지정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C(Azure Active Directory B2C)가 고객에게 표시하는 사용자 인터페이스를 브랜딩하고 사용자 지정하면 애플리케이션에서 원활한 사용자 환경을 제공하는 데 도움이 됩니다. 해당 환경에는 가입, 로그인, 프로필 편집, 암호 재설정이 포함됩니다. 이 문서에서는 페이지 템플릿 및 회사 브랜딩을 사용하여 Azure AD B2C 페이지를 사용자 지정합니다. 

> [!TIP]
> 페이지 템플릿, 배너 로고, 배경 이미지 또는 배경색 이외의 사용자 흐름 페이지의 다른 측면을 사용자 지정하려면 HTML 템플릿을 사용하여 [UI를 사용자 지정하는](customize-ui-with-html.md) 방법을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>개요

Azure AD B2C 사용자 환경 페이지에 전문적인 디자인을 제공하기 위해 선택할 수 있는 몇 가지 기본 제공 템플릿을 제공합니다. 페이지 템플릿은 [회사 브랜딩](#company-branding) 기능을 사용하여 사용자 지정의 시작점으로 사용할 수도 있습니다.

> [!NOTE]
> 클래식 템플릿에 지원되는 브라우저에는 현재 및 이전 버전의 Internet Explorer, Microsoft Edge, Google Chrome, Mozilla Firefox, Safari가 있습니다. 바다색 및 쥐색 템플릿은 Internet Explorer 11 및 10과 같은 이전 브라우저 버전에서 제한적으로 지원될 수 있습니다. 지원하려는 브라우저를 사용하여 애플리케이션을 테스트하는 것이 좋습니다.

### <a name="ocean-blue"></a>바다색

가입 로그인 페이지에서 렌더링된 바다색 템플릿의 예:

![바다색 템플릿 스크린샷](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>녹회색

가입 로그인 페이지에서 렌더링된 쥐색 템플릿의 예:

![쥐색 템플릿 스크린샷](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>Classic

가입 로그인 페이지에서 렌더링된 클래식 템플릿의 예:

![클래식 템플릿 스크린샷](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>회사 브랜딩

Azure Active Directory [회사 브랜딩](../active-directory/fundamentals/customize-branding.md)을 사용하여 배너 로고, 배경 이미지, 배경색으로 Azure AD B2C 페이지를 사용자 지정할 수 있습니다. 회사 브랜딩에는 가입, 로그인, 프로필 편집, 암호 재설정이 포함됩니다. 

다음 예제에서는 바다색 템플릿을 사용하여 사용자 지정 로고, 배경 이미지가 있는 ‘가입 및 로그인’ 페이지를 보여 줍니다.

![Azure AD B2C에서 제공하는 브랜드 가입/로그인 페이지](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>페이지 템플릿 선택하기

::: zone pivot="b2c-user-flow"

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 사용자 지정하려는 사용자 흐름을 선택합니다.
1. 왼쪽 메뉴의 **사용자 지정** 에서 **페이지 레이아웃** 을 선택한 다음, **템플릿** 을 선택합니다.
    ![Azure Portal 사용자 흐름 페이지의 템플릿 선택 드롭다운](./media/customize-ui/select-page-template.png)

템플릿을 선택하면 선택한 템플릿이 사용자 흐름의 모든 페이지에 적용됩니다. 각 페이지의 URI는 **사용자 지정 페이지 URI** 필드에 표시됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

페이지 템플릿을 선택하려면 [콘텐츠 정의](contentdefinitions.md)의 `LoadUri` 요소를 설정합니다. 다음 예제에서는 콘텐츠 정의 식별자와 해당하는 `LoadUri`를 보여 줍니다. 

바다색:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

쥐색:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

클래식: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>회사 브랜딩 구성

사용자 흐름 페이지를 사용자 지정하려면 먼저 Azure Active Directory에서 회사 브랜딩을 구성한 다음, Azure AD B2C의 사용자 흐름에서 이를 사용합니다.

**회사 브랜딩** 내에서 배너 로고, 배경 이미지, 배경색을 설정하여 시작합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **관리** 에서 **회사 브랜딩** 을 선택합니다.
1. [조직의 Azure Active Directory 로그인 페이지에 브랜딩 추가](../active-directory/fundamentals/customize-branding.md)에 나온 단계를 따릅니다.

Azure AD B2C 회사 브랜딩을 구성할 때 다음 사항을 염두에 두어야 합니다.

* Azure AD B2C 회사 브랜딩은 현재 **배경 이미지**, **배너 로고**, **배경색** 사용자 지정으로 제한됩니다. **고급 설정** 과 같은 회사 브랜딩 창의 다른 속성은 ‘지원되지 않습니다’.
* 사용자 흐름 페이지에서 배경 이미지가 로드되기 전에 배경색이 표시됩니다. 더 부드러운 로딩 경험을 위해 배경 이미지의 색과 거의 일치하는 배경색을 선택하는 것이 좋습니다.
* 배너 로고는 가입 사용자 흐름을 시작할 때 사용자에게 전송되는 확인 메일에 표시됩니다.


::: zone pivot="b2c-user-flow"

## <a name="enable-company-branding-in-user-flow-pages"></a>사용자 흐름 페이지에서 회사 브랜딩 사용

회사 브랜딩을 구성한 후에는 사용자 흐름에서 이를 사용합니다.

1. Azure Portal 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다.
1. **정책** 에서 **사용자 흐름(정책)** 을 선택합니다.
1. 회사 브랜딩을 사용할 사용자 흐름을 선택합니다. 표준 ‘로그인’ 및 표준 ‘프로필 편집’ 사용자 흐름 유형에는 회사 브랜딩이 **지원되지 않습니다**. 
1. **사용자 지정** 에서 **페이지 레이아웃** 을 선택한 다음, 브랜딩할 페이지를 선택합니다. 예를 들어 **통합 가입 또는 로그인 페이지** 를 선택합니다.
1. **페이지 레이아웃 버전(미리 보기)** 의 경우 버전 **1.2.0** 이상을 선택합니다.
1. **저장** 을 선택합니다.

사용자 흐름의 모든 페이지를 브랜딩하려면 사용자 흐름의 각 페이지 레이아웃에 대한 페이지 레이아웃 버전을 설정합니다.

![Azure Portal의 Azure AD B2C에서 페이지 레이아웃 선택](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-company-branding-in-custom-policy-pages"></a>사용자 지정 정책 페이지에서 회사 브랜딩 사용

회사 브랜딩을 구성한 후에는 사용자 지정 정책에서 이를 사용합니다. 사용자 지정 정책의 ‘모든’ 콘텐츠 정의에 대해 페이지 `contract` 버전으로 [페이지 레이아웃 버전](contentdefinitions.md#migrating-to-page-layout)을 구성합니다. 값 형식에는 _urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_ 과 같이 `contract`라는 단어가 포함되어야 합니다. 이전 **DataUri** 값을 사용하는 사용자 지정 정책에서 페이지 레이아웃을 지정하려는 경우가 이에 해당합니다. 자세한 내용은 페이지 버전을 사용하여 [페이지 레이아웃으로 마이그레이션](contentdefinitions.md#migrating-to-page-layout)하는 방법을 알아보세요.

다음 예제는 해당 페이지 계약 및 ‘바다색’ 페이지 템플릿이 있는 콘텐츠 정의를 보여 줍니다. 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>다음 단계

[Azure Active Directory B2C에서 애플리케이션의 사용자 인터페이스 사용자 지정](customize-ui-with-html.md)에서 애플리케이션의 사용자 인터페이스를 사용자 지정하는 방법에 대한 자세한 정보를 확인합니다.
