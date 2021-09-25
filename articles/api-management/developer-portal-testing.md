---
title: 자체 호스팅 개발자 포털 테스트
titleSuffix: Azure API Management
description: 자체 호스팅 API Management 포털에 대해 단위 테스트와 엔드투엔드 테스트를 설정하는 방법을 알아봅니다.
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 62c5ab98e067bb735bcfa1510cae405b17461cac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646442"
---
# <a name="test-the-self-hosted-developer-portal"></a>자체 호스팅 개발자 포털 테스트

이 문서에서는 [자체 호스팅 포털](developer-portal-self-host.md)에 대해 단위 테스트와 엔드투엔드 테스트를 설정하는 방법을 설명합니다.

## <a name="unit-tests"></a>단위 테스트

단위 테스트는 기능의 작은 단위들에 대해 유효성 검사를 진행하는 접근 방식입니다. 단위 테스트는 애플리케이션의 나머지 부분과 격리된 상태로 진행됩니다.

### <a name="example-scenario"></a>예제 시나리오

이 시나리오에서는 암호 입력 컨트롤을 테스트해 봅니다. 이 컨트롤은 최소한 다음과 같은 요소를 포함하는 암호만 허용합니다.

- 문자 1개

- 숫자 1개

- 특수 문자 1개
 
따라서 다음과 같은 테스트로 이러한 요구 사항의 유효성을 검사합니다.

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>프로젝트 구조

단위 테스트는 보통 해당 단위 테스트로 유효성을 검사할 구성 요소와 함께 나란히 보관합니다.

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>모의 HTTP 요청

구성 요소에서 HTTP 요청을 생성해야 하는 경우가 있습니다. 구성 요소는 다양한 유형의 요청에 올바르게 반응해야 합니다. 특정 HTTP 응답을 시뮬레이션하려면 `MockHttpClient`를 사용합니다. MockHttpClient는 프로젝트의 다른 여러 구성 요소에서 사용하는 `HttpClient` 인터페이스를 구현합니다.

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>엔드투엔드 테스트

엔드투엔드 테스트는 사용자가 수행할 것으로 예상되는 정확한 단계에 따라 특정 사용자 시나리오를 실행합니다. Azure API Management 개발자 포털과 같은 웹 애플리케이션에서는 사용자가 콘텐츠를 스크롤하다 옵션을 선택하여 특정 결과를 얻습니다. 

사용자 탐색을 그대로 재현하려면 [Puppeteer](https://github.com/puppeteer/puppeteer)와 같은 브라우저 조작 도우미 라이브러리를 사용할 수 있습니다. Puppeteer를 사용하면 사용자 동작을 시뮬레이션하고 가정한 시나리오를 자동화할 수 있습니다. 또한 Puppeteer는 테스트의 어느 단계에서도 자동으로 페이지 또는 구성 요소의 스크린샷을 찍습니다. 나중에 스크린샷을 이전 결과와 비교해 보면 달라진 부분과 잠재적인 회귀를 발견할 수 있습니다.

### <a name="example-scenario"></a>예제 시나리오

이 시나리오에서는 사용자 로그인 흐름의 유효성을 검사합니다. 이 시나리오에는 다음과 같은 단계가 필요합니다.

1. 브라우저를 열고 로그인 페이지로 이동합니다.

1. 메일 주소를 입력합니다.

1. 암호를 입력합니다.

1. **로그인** 을 선택합니다.

1. 사용자가 홈페이지로 리디렉션되는지 확인합니다.

1. 페이지에 **프로필** 메뉴 항목이 있는지 확인합니다. 이 메뉴 항목은 성공적으로 로그인했음을 나타내 주는 여러 표시자 중 하나입니다.

테스트를 자동으로 실행하려면 위의 단계를 스크립트로 만듭니다.

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> “#email”, “#password”, “#signin”과 같은 문자열은 페이지에서 HTML 요소를 식별하는 CSS 스타일의 선택기입니다. 자세한 내용은 [Selectors Level 3](https://www.w3.org/TR/selectors-3/)(선택기 수준 3) W3C 사양을 참조하세요.

### <a name="ui-component-maps"></a>UI 구성 요소 맵

사용자 흐름은 종종 동일한 페이지나 구성 요소를 거치곤 합니다. 대표적인 예로 모든 페이지에 있는 기본 웹 사이트 메뉴를 들 수 있습니다. 

모든 테스트에서 동일한 선택기를 구성하고 업데이트하지 않아도 되도록 UI 구성 요소 맵을 만듭니다. 예를 들어, 앞에 나온 예제의 2~6단계는 다음의 두 줄로 바꿀 수 있습니다.

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>테스트 구성

미리 만든 데이터나 구성이 필요한 시나리오도 있습니다. 일례로 소셜 미디어 계정을 사용하는 사용자 로그인을 자동화해야 하는 경우를 들 수 있습니다. 이와 같은 데이터를 쉽고 빠르게 만들기 어렵습니다.

따라서 이 용도로 테스트 시나리오에 특별한 구성 파일을 추가할 수 있습니다. 테스트 스크립트는 이 파일에서 필요한 데이터를 가져올 수 있습니다. 테스트는 빌드 및 테스트 파이프라인에 따라 명명된 보안 저장소에서 비밀 정보를 풀할 수 있습니다.

다음은 프로젝트의 `src` 폴더에 저장될 `validate.config.json`의 예입니다.

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>헤드리스 테스트와 일반 테스트

Chrome이나 Microsoft Edge와 같은 최신 브라우저에서는 헤드리스 모드와 일반 모드 중 어느 모드로도 자동화를 실행할 수 있습니다. 헤드리스 모드에서는 브라우저가 그래픽 사용자 인터페이스 없이 작동하며, 이 경우에도 여전히 동일한 페이지 및 DOM(문서 개체 모델) 조작을 수행합니다. 일반적으로 전송 파이프라인에서는 브라우저 UI가 필요하지 않습니다. 그렇기 때문에 헤드리스 모드에서 테스트를 실행하는 것이 좋은 옵션이 될 수 있습니다.

테스트 스크립트를 개발할 때 브라우저에서 실제로 어떤 일이 일어나고 있는지를 직접 보는 것이 도움이 됩니다. 이와 같은 경우에는 일반 모드를 사용합니다.

도 모드 사이를 전환하려면 `constants.ts` 파일에서 `headless` 옵션을 변경합니다. 이 파일은 프로젝트의 `tests` 폴더에서 찾을 수 있습니다.

```typescript
export const LaunchOptions = {
    headless: false
};
```

또 다른 유용한 옵션은 `slowMo`입니다. 이 옵션은 각 동작과 동작 사이에 테스트의 실행을 일시 중지합니다.

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>테스트 실행

이 프로젝트에서 테스트를 실행하는 두 가지 기본 제공되는 방법이 있습니다.

**npm 명령**

```console
npm run test
```

**테스트 탐색기**

VS Code용 테스트 탐색기 확장(예: [Mocha 테스트 탐색기](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter))에는 편리한 UI와 소스 코드가 변경될 때마다 자동으로 테스트를 실행하는 옵션이 있습니다.

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="Visual Studio Code 테스트 탐색기 스크린샷":::

## <a name="next-steps"></a>다음 단계

다음 문서를 통해 개발자 포털에 대해 자세히 알아봅니다.

- [Azure API Management 개발자 포털 개요](api-management-howto-developer-portal.md)

- [Self-host the developer portal](developer-portal-self-host.md)(개발자 포털 자체 호스팅)
