---
title: HTML 템플릿을 사용하여 사용자 인터페이스 사용자 지정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용하는 애플리케이션의 사용자 인터페이스를 HTML 템플릿을 사용하여 사용자 지정하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 76832f02f1c1337a705f33d26de97b0b5823c2c1
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112981112"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 HTML 템플릿을 사용하여 사용자 인터페이스 사용자 지정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C(Azure AD B2C)가 고객에게 표시하는 사용자 인터페이스를 브랜딩하고 사용자 지정하면 애플리케이션에서 원활한 사용자 환경을 제공하는 데 도움이 됩니다. 해당 환경에는 가입, 로그인, 프로필 편집, 암호 재설정이 포함됩니다. 이 문서에서는 사용자 인터페이스(UI)를 사용자 지정하는 방법을 소개합니다. 

> [!TIP]
> 배너 로고, 배경 이미지, 사용자 흐름 페이지의 배경색만을 수정하려는 경우 [회사 브랜딩](customize-ui.md) 기능을 사용해 볼 수 있습니다.

## <a name="custom-html-and-css-overview"></a>사용자 지정 HTML 및 CSS 개요

Azure AD B2C는 [CORS(원본 간 리소스 공유)](https://www.w3.org/TR/cors/)를 사용하여 고객의 브라우저에서 코드를 실행합니다. 런타임 시 사용자 흐름이나 사용자 지정 정책에서 지정하는 URL에서 콘텐츠를 로드합니다. 사용자 환경의 각 페이지는 해당 페이지에 대해 지정한 URL에서 콘텐츠를 로드합니다. URL에서 콘텐츠가 로드된 후 Azure AD B2C가 삽입한 HTML 조각과 병합된 다음 해당 페이지가 고객에게 표시됩니다.

![사용자 지정 페이지 콘텐츠 여백](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>사용자 지정 HTML 페이지 콘텐츠

자체 브랜딩으로 HTML 페이지를 만들어 사용자 지정 페이지 콘텐츠를 제공하세요. 이 페이지는 정적 `*.html` 페이지이거나 .net, Node.js 또는 PHP와 같은 동적 페이지일 수 있습니다.

사용자 지정 페이지 콘텐츠는 CSS 및 JavaScript를 비롯한 HTML 요소를 포함할 수 있지만 iframe과 같은 안전하지 않은 요소는 포함할 수 없습니다. 유일하게 필수적인 요소는 HTML 페이지의 `<div id="api"></div>`과 같이 `id`가 `api`로 설정된 div 요소입니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>기본 Azure AD B2C 페이지 사용자 지정

사용자 지정 페이지 콘텐츠를 처음부터 만드는 대신 Azure AD B2C의 기본 페이지 콘텐츠를 사용자 지정할 수 있습니다.

다음 표에는 Azure AD B2C에서 제공하는 기본 페이지 콘텐츠가 나와 있습니다. 파일을 다운로드하여 자체 사용자 지정 페이지를 만들기 위한 시작점으로 사용하세요.

| 기본 페이지 | 설명 | 콘텐츠 정의 ID<br/>(사용자 지정 정책에만 해당) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **오류 페이지** 입니다. 예외 또는 오류가 발생하면 이 페이지가 표시됩니다. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **자체 어설션된 페이지** 입니다. 소셜 계정 회원가입 페이지, 로컬 계정 회원가입 페이지, 로컬 계정 로그인 페이지, 암호 재설정 등의 사용자 지정 페이지 콘텐츠로 사용합니다. 양식에는 텍스트 입력란, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 상자 및 다중 선택 확인란과 같이 다양한 입력 컨트롤이 포함될 수 있습니다. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication 페이지** 입니다. 등록 또는 로그인 중에 사용자가 이 페이지에서 텍스트 또는 음성을 사용하여 전화 번호를 확인할 수 있습니다. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **프로필 업데이트 페이지** 입니다. 이 페이지에는 사용자가 자신의 프로필을 업데이트하기 위해 액세스할 수 있는 양식이 있습니다. 암호 입력 필드를 제외하고는 소셜 계정 등록 페이지와 비슷합니다. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **통합 등록 또는 로그인 페이지** 입니다. 이 페이지는 사용자 등록 및 로그인 프로세스를 처리합니다. 사용자는 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook, Google+) 또는 로컬 계정을 사용할 수 있습니다. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>페이지 콘텐츠 호스팅

자체 HTML 및 CSS 파일을 사용하여 UI를 사용자 지정하는 경우 CORS를 지원하는 모든 공개적으로 사용 가능한 HTTPS 엔드포인트에서 UI 콘텐츠를 호스트할 수 있습니다. 예를 들어 [Azure Blob 스토리지](../storage/blobs/storage-blobs-introduction.md), [Azure App Services](../app-service/index.yml), 웹 서버, CDNs, AWS S3 또는 파일 공유 시스템이 있습니다.

## <a name="guidelines-for-using-custom-page-content"></a>사용자 지정 페이지 콘텐츠 사용에 대한 지침

- HTML 파일에 미디어, CSS, JavaScript 파일과 같은 외부 리소스를 포함하는 경우 절대 URL을 사용합니다.
- [페이지 레이아웃 버전](page-layout.md) 1.2.0 이상을 사용하여 HTML 태그에 `data-preload="true"` 특성을 추가하면 CSS 및 JavaScript에 대한 로드 순서를 제어할 수 있습니다. `data-preload="true"`의 경우 페이지는 사용자에게 표시되기 전에 구성됩니다. 해당 특성을 사용하면 스타일이 지정되지 않은 HTML을 사용자에게 표시하지 않고 CSS 파일을 미리 로드하여 페이지가 “깜빡이는” 것을 방지할 수 있습니다. 다음 HTML 코드 조각은 `data-preload` 태그를 사용하는 방법을 보여줍니다.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- 기본 페이지 콘텐츠로 시작하여 이를 기반으로 빌드하는 것이 좋습니다.
- 사용자 지정 콘텐츠에 [JavaScript를 포함](javascript-and-page-layout.md)할 수 있습니다.
- 지원되는 브라우저 버전은 다음과 같습니다.
  - Internet Explorer 11, 10, Microsoft Edge
  - Internet Explorer 9 및 8에 대한 제한된 지원
  - Google Chrome 42.0 이상
  - Mozilla Firefox 38.0 이상
  - iOS 및 macOS용 Safari, 버전 12 이상
- 보안 제안 사항으로 인해 Azure AD B2C는 `frame`, `iframe`, 또는 `form` HTML 요소를 지원하지 않습니다.

## <a name="localize-content"></a>콘텐츠 지역화

Azure AD B2C 테넌트에서 [사용자 언어 지정](language-customization.md)을 사용하여 HTML 콘텐츠를 지역화합니다. 이 기능을 사용하도록 설정하면 Azure AD B2C가 OpenID Connect 매개 변수 `ui_locales`를 엔드포인트로 전달할 수 있습니다. 콘텐츠 서버는 이 매개 변수를 사용하여 언어별 HTML 페이지를 제공할 수 있습니다.

> [!NOTE]
> Azure AD B2C는 `ui_locales`와 같은 OpenID Connect 매개 변수를 [예외 페이지](page-layout.md#exception-page-globalexception)로 전달하지 않습니다.


사용되는 로캘에 따라 다른 위치에서 콘텐츠를 끌어올 수 있습니다. CORS 사용 엔드포인트에서 특정 언어에 대한 콘텐츠를 호스트하도록 폴더 구조를 설정합니다. 와일드 카드 값 `{Culture:RFC5646}`을 사용하는 경우 적합한 구조를 호출합니다.

예를 들어 사용자 지정 페이지 URI는 다음과 같은 모습일 수 있습니다.

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

다음에서 콘텐츠를 끌어서 페이지를 프랑스어로 로드할 수 있습니다.

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>사용자 지정 페이지 콘텐츠 연습

다음은 프로세스의 개요입니다.

1. 사용자 지정 페이지 콘텐츠를 호스트할 위치를 준비합니다(공개적으로 액세스할 수 있는 CORS 사용 HTTPS 엔드포인트).
1. 기본 페이지 콘텐츠 파일을 다운로드하고 사용자 지정합니다(예: `unified.html`).
1. 사용자 지정 페이지 콘텐츠를 공개적으로 사용 가능한 HTTPS 엔드포인트에 게시합니다.
1. 웹앱에 대한 CORS(원본 간 리소스 공유) 설정
1. 정책에서 사용자 지정 정책 콘텐츠 URI를 가리킵니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. HTML 콘텐츠 만들기

제목에 제품의 브랜드 이름이 포함된 사용자 지정 페이지 콘텐츠를 만듭니다.

1. 다음 HTML 코드 조각을 복사합니다. *\<body\>* 태그 내에 위치한 *\<div id="api"\>\</div\>* 라는 빈 요소를 사용하여 잘 구성된(Well-Formed) HTML5입니다. 이 요소는 Azure AD B2C 콘텐츠가 삽입되는 위치를 나타냅니다.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. 복사한 코드 조각을 텍스트 편집기에 붙여넣습니다.
1. CSS를 사용하여 Azure AD B2C가 페이지에 삽입하는 UI 요소의 스타일을 지정합니다. 다음 예제에서는 등록된 HTML 요소에 대한 설정도 포함하는 간단한 CSS 파일을 보여줍니다.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  파일을 *customize-ui.html* 로 저장합니다.

> [!NOTE]
> login.microsoftonline.com을 사용하는 경우 보안 제한 사항으로 인해 HTML 양식 요소가 제거됩니다. 사용자 지정 HTML 콘텐츠에서 HTML 양식 요소를 사용하려면 [b2clogin.com을 사용](b2clogin.md)합니다.

### <a name="2-create-an-azure-blob-storage-account"></a>2. Azure Blob 스토리지 계정 만들기

이 가이드에서는 Azure Blob Storage를 사용하여 콘텐츠를 호스팅합니다. 웹 서버에서 콘텐츠를 호스팅하도록 선택할 수 있지만 [웹 서버에서 CORS를 사용하도록 설정](https://enable-cors.org/server.html)(영문)해야 합니다.

Blob 스토리지에서 HTML 콘텐츠를 호스트하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **허브** 메뉴에서 **새로 만들기** > **스토리지** > **스토리지 계정** 을 차례로 선택합니다.
1. 스토리지 계정에 대한 **구독** 을 선택합니다.
1. **리소스 그룹** 을 만들거나 기존 그룹을 선택합니다.
1. 스토리지 계정의 고유한 **이름** 을 입력합니다.
1. 스토리지 계정에 대한 **지리적 위치** 를 선택합니다.
1. **배포 모델** 은 **Resource Manager** 로 유지하면 됩니다.
1. **성능** 은 **표준** 으로 유지하면 됩니다.
1. **계정 종류** 를 **Blob Storage** 로 변경합니다.
1. **복제** 는 **RA-GRS** 로 유지하면 됩니다.
1. **액세스 계층** 은 **핫** 으로 유지하면 됩니다.
1. **검토 + 만들기** 를 선택하여 스토리지 계정을 만듭니다.
    배포가 완료되면 **스토리지 계정** 페이지가 자동으로 열립니다.

#### <a name="21-create-a-container"></a>2.1 컨테이너 만들기

Blob 스토리지에 퍼블릭 컨테이너를 만들려면 다음 단계를 수행합니다.

1. 왼쪽 메뉴의 **Blob service** 에서 **Blob** 을 선택합니다.
1. **+컨테이너** 를 선택합니다.
1. **이름** 으로 *root* 를 입력합니다. 해당 이름을 직접 선택(예: *contoso*)할 수도 있지만, 이 예제에서는 간단히 하기 위해 *root* 를 사용합니다.
1. **퍼블릭 액세스 수준** 에 대해 **Blob**, **확인** 을 차례로 선택합니다.
1. **root** 를 선택하여 새 컨테이너를 엽니다.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 사용자 지정 페이지 콘텐츠 파일 업로드

1. **업로드** 를 선택합니다.
1. **파일 선택** 옆에 있는 폴더 아이콘을 선택합니다.
1. 이전에 페이지 UI 사용자 지정 섹션에서 만든 **customize-ui.html** 로 이동하고 선택합니다.
1. 하위 폴더에 업로드하려는 경우 **고급** 을 펼치고 **폴더에 업로드** 에 폴더 이름을 입력합니다.
1. **업로드** 를 선택합니다.
1. 업로드한 **customize-ui.html** Blob을 선택합니다.
1. **URL** 텍스트 상자 오른쪽에서 **클립보드에 복사** 아이콘을 선택하여 URL을 클립보드에 복사합니다.
1. 웹 브라우저에서 복사한 URL로 이동하여 업로드한 Blob에 액세스할 수 있는지 확인합니다. 액세스할 수 없는 경우, 예를 들어 `ResourceNotFound` 오류가 발생하는 경우 컨테이너 액세스 형식이 **Blob** 으로 설정되어 있는지 확인합니다.

### <a name="3-configure-cors"></a>3. CORS 구성

다음 단계를 수행하여 CORS(원본 간 리소스 공유)에 Blob 스토리지를 구성합니다.

1. 메뉴에서 **CORS** 를 선택합니다.
1. **허용된 원본** 에 `https://your-tenant-name.b2clogin.com`을 입력합니다. `your-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다. 예들 들어 `https://fabrikam.b2clogin.com`입니다. 테넌트 이름을 입력할 때는 모두 소문자를 사용합니다.
1. **허용된 메소드** 에서 `GET`과 `OPTIONS`를 모두 선택합니다.
1. **허용된 헤더** 에 별표(*)를 입력합니다.
1. **노출된 헤더** 에 별표(*)를 입력합니다.
1. **최대 기간** 에 200을 입력합니다.
1. **저장** 을 선택합니다.

#### <a name="31-test-cors"></a>3.1 CORS 테스트

다음 단계를 수행하여 준비가 되었는지 확인합니다.

1. CORS 구성 단계를 반복합니다. **허용된 원본** 에 `https://www.test-cors.org`을 입력합니다.
1. [www.test-cors.org](https://www.test-cors.org/)로 이동합니다. 
1. **원격 URL** 상자에 HTML 파일의 URL을 붙여넣습니다. 예를 들어 `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. **요청 보내기** 를 선택합니다.
    결과는 `XHR status: 200`여야 합니다. 
    오류가 발생하는 경우 CORS 설정이 올바른지 확인합니다. Ctrl+Shift+P를 눌러 브라우저 캐시를 비우거나 개인 검색 세션을 열어야 할 수도 있습니다.

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. 사용자 흐름 업데이트

1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 흐름**, *B2C_1_signupsignin1* 사용자 흐름을 차례로 선택합니다.
1. **페이지 레이아웃** 을 선택한 다음, **통합 가입 또는 로그인 페이지** 아래에서 **사용자 지정 페이지 콘텐츠 사용** 에 대해 **예** 를 클릭합니다.
1. **사용자 지정 페이지 URI** 에서 이전에 기록한 *custom-ui.html* 파일의 URI를 입력합니다.
1. 페이지 맨 위에서 **저장** 을 선택합니다.

### <a name="5-test-the-user-flow"></a>5. 사용자 흐름 테스트

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택하고, *B2C_1_signupsignin1* 사용자 흐름을 선택합니다.
1. 페이지 맨 위에서 **사용자 흐름 실행** 을 클릭합니다.
1. **사용자 흐름 실행** 단추를 클릭합니다.

생성한 CSS 파일에 따라 요소가 중심에 있는 다음 예제와 유사한 페이지가 표시됩니다.

![사용자 지정 UI 요소를 사용하여 등록 또는 로그인 페이지를 표시하는 웹 브라우저](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. 확장 파일 수정

UI 사용자 지정을 구성하려면 **ContentDefinition** 및 해당 자식 요소를 기본 파일에서 확장 파일로 복사합니다.

1. 정책의 기본 파일을 엽니다(예: 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>입니다. 해당 기본 파일은 사용자 지정 정책 시작 팩에 포함된 정책 파일 중 하나로, 필수 구성 요소인 [사용자 지정 정책 시작](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)에서 가져와야 합니다.
1. **ContentDefinitions** 요소의 전체 내용을 검색한 후 복사합니다.
1. 확장 파일을 엽니다(예: 예: *TrustFrameworkExtensions.xml* **BuildingBlocks** 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. 복사한 **ContentDefinitions** 의 전체 내용을 **BuildingBlocks** 요소의 자식으로 붙여 넣습니다.
1. 복사한 XML에서 `Id="api.signuporsignin"`을 포함하는 **ContentDefinition** 요소를 검색합니다.
1. **LoadUri** 값을 스토리지에 업로드한 HTML 파일의 URL로 변경합니다. 예: `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    사용자 지정 정책은 다음 코드 조각과 비슷해야 합니다.

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. 확장 파일을 저장합니다.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. 업데이트된 사용자 지정 정책 업로드 및 테스트

#### <a name="51-upload-the-custom-policy"></a>5.1 사용자 지정 정책 업로드

1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **사용자 지정 정책 업로드** 를 선택합니다.
1. 이전에 변경한 확장 파일을 업로드합니다.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 **지금 실행** 을 사용하여 사용자 지정 정책 테스트

1. 업로드했던 정책을 선택한 다음 **지금 실행** 을 선택합니다.
1. 이메일 주소를 사용하여 등록할 수 있습니다.

## <a name="configure-dynamic-custom-page-content-uri"></a>동적 사용자 지정 페이지 콘텐츠 URI 구성

Azure AD B2C 사용자 지정 정책을 사용하면 URL 경로 또는 쿼리 문자열에 매개 변수를 보낼 수 있습니다. 매개 변수를 HTML 엔드포인트로 전달하면 페이지 콘텐츠를 동적으로 변경할 수 있습니다. 예를 들어 웹 또는 모바일 애플리케이션에서 전달한 매개 변수를 기반으로 Azure AD B2C 등록 또는 로그인 페이지에서 배경 이미지를 변경할 수 있습니다. 애플리케이션 ID, 언어 ID 또는 사용자 지정 쿼리 문자열 매개 변수(예: `campaignId`)와 같은 모든 [클레임 해결자](claim-resolver-overview.md)가 해당 매개 변수가 될 수 있습니다.

### <a name="sending-query-string-parameters"></a>쿼리 문자열 매개 변수 보내기

쿼리 문자열 매개 변수를 보내려면 [신뢰 당사자 정책](relyingparty.md)에서 아래와 같이 `ContentDefinitionParameters` 요소를 추가합니다.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

콘텐츠 정의에서 `LoadUri`의 값을 `https://<app_name>.azurewebsites.net/home/unified`로 변경합니다. 사용자 지정 정책 `ContentDefinition`은 다음 코드 조각과 비슷해야 합니다.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C는 페이지를 로드할 때 웹 서버 엔드포인트에 대한 호출을 수행합니다.

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>동적 페이지 콘텐츠 URI

사용되는 매개 변수에 따라 다른 위치에서 콘텐츠를 끌어올 수 있습니다. CORS 사용 엔드포인트에서 콘텐츠를 호스트하도록 폴더 구조체를 설정합니다. 예를 들어 다음 구조체로 콘텐츠를 구성할 수 있습니다. 루트 ‘폴더/언어별 폴더/HTML 파일’. 예를 들어 사용자 지정 페이지 URI는 다음과 같은 모습일 수 있습니다.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C는 언어에 대한 2자 ISO 코드를 보냅니다. 프랑스어의 경우 `fr`입니다.

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>샘플 템플릿

UI 사용자 지정을 위한 샘플 템플릿은 다음에서 찾을 수 있습니다.

```bash
git clone https://github.com/azure-ad-b2c/html-templates
```

이 프로젝트에는 다음 템플릿이 포함됩니다.
- [바다색](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/AzureBlue)
- [녹회색](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/MSA)
- [클래식](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/classic)
- [템플릿 리소스](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/src)

샘플을 사용하려면 다음 단계를 수행합니다.

1. 로컬 머신에 리포지토리를 복제합니다. 템플릿 폴더 `/AzureBlue`, `/MSA` 또는 `/classic`을 선택합니다.
1. 이전 섹션에서 설명한 대로 템플릿 폴더 및 `/src` 폴더 아래의 모든 파일을 Blob 스토리지에 업로드합니다.
1. 다음으로 템플릿 폴더에서 각 `\*.html` 파일을 엽니다. 그런 다음 `https://login.microsoftonline.com` URL의 모든 인스턴스를 2단계에서 업로드한 URL로 바꿉니다. 예를 들면 다음과 같습니다.
    
    원본:
    ```html
    https://login.microsoftonline.com/templates/src/fonts/segoeui.WOFF
    ```

    대상:
    ```html
    https://your-storage-account.blob.core.windows.net/your-container/templates/src/fonts/segoeui.WOFF
    ```
    
1. `\*.html` 파일을 저장하고 Blob 스토리지에 업로드합니다.
1. 이제 앞에서 언급한 대로 HTML 파일을 가리키는 정책을 수정합니다.
1. 글꼴, 이미지 또는 CSS가 누락되었다면 확장 정책 및 `\*.html` 파일의 참조를 확인합니다.

## <a name="use-company-branding-assets-in-custom-html"></a>사용자 지정 HTML에서 회사 브랜딩 자산 사용

사용자 지정 HTML에서 [회사 브랜딩](customize-ui.md#configure-company-branding) 자산을 사용하려면 `<div id="api">` 태그 외부에 다음 태그를 추가합니다. 이미지 소스는 배경 이미지 및 배너 로고의 소스로 바뀝니다.

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>다음 단계

[클라이언트 쪽 JavaScript 코드](javascript-and-page-layout.md)를 사용하도록 설정하는 방법을 알아봅니다.
