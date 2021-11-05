---
title: Arkose Labs를 사용하여 Azure Active Directory B2C를 구성하는 자습서
titleSuffix: Azure AD B2C
description: 위험한 사용자 및 사기성 사용자를 식별하도록 Arkose Labs를 사용하여 Azure Active Directory B2C를 구성하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/22/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c6e31a9026e4598d12367d623cfb166663c6fe0a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044820"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 Arkose Labs 구성

이 샘플 자습서에서는 AD(Azure Active Directory) B2C 인증과 [Arkose Labs](https://www.arkoselabs.com/)를 통합하는 방법에 대해 알아봅니다. Arkose Labs는 봇 공격, 계정 인수 공격 및 사기성 계정 입구에 대해 조직을 지원합니다.  

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 Azure 구독에 연결

- [Arkose Labs](https://www.arkoselabs.com/book-a-demo/) 계정.

## <a name="scenario-description"></a>시나리오 설명

Arkose Labs 통합에는 다음 구성 요소가 포함됩니다.

- **Arkose Labs** - 봇 및 기타 자동화된 남용으로부터 보호하기 위한 사기 및 남용 서비스입니다.

- **Azure AD B2C 등록 사용자 흐름** - Arkose Labs 서비스를 사용하는 등록 환경입니다. 사용자 지정 HTML 및 JavaScript를 사용하고 API 커넥터를 사용하여 Arkose Labs 서비스와 통합합니다.

- **Azure Functions** - 사용자에게 호스팅되어 API 커넥터 기능에서 작동하는 API 엔드포인트입니다. 이 API는 Arkose Labs 세션 토큰의 서버 쪽 유효성 검사를 수행하는 작업을 담당합니다.

다음 다이어그램에서는 Arkose Labs를 Azure AD B2C와 통합하는 방법을 설명합니다.

![Arkose Labs 아키텍처 다이어그램을 보여 주는 이미지](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| 단계  | 설명 |
|---|---|
|1     | 사용자가 등록하고 계정을 만듭니다. 사용자가 제출을 선택하면 Arkose Labs 적용 챌린지가 표시됩니다.         |
|2     |  사용자가 챌린지를 완료한 후 Azure AD B2C는 상태를 Arkose Labs로 보내 토큰을 생성합니다. |
|3     |  Arkose Labs는 토큰을 생성하고 Azure AD B2C로 다시 보냅니다.   |
|4     |  Azure AD B2C는 중간 웹 API를 호출하여 등록 양식을 전달합니다.      |
|5     |  중간 웹 API는 토큰 확인을 위해 Arkose Lab으로 등록 양식을 보냅니다.    |
|6     | Arkose Lab은 테스트를 처리하고 중간 웹 API로 확인 결과를 다시 보냅니다.|
|7     | 중간 웹 API는 챌린지의 성공 또는 실패 결과를 Azure AD B2C로 보냅니다. |
|8     | 챌린지가 성공적으로 완료되면 등록 양식이 Azure AD B2C로 전송되고 Azure AD B2C는 인증을 완료합니다.|

## <a name="onboard-with-arkose-labs"></a>Arkose Labs를 사용하여 온보딩

1. [Arkose](https://www.arkoselabs.com/book-a-demo/)에 문의하여 계정을 만드세요.

2. 계정을 만든 후 https://dashboard.arkoselabs.com/login 으로 이동합니다.  

3. 대시보드 내에서 사이트 설정으로 이동하여 공개 키와 프라이빗 키를 찾습니다. 이 정보는 나중에 Azure AD B2C를 구성하는 데 필요합니다. 공개 및 프라이빗 키의 값은 [샘플 코드](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)에서 `ARKOSE_PUBLIC_KEY` 및 `ARKOSE_PRIVATE_KEY`로 참조됩니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>1부 - ArkoseSessionToken 사용자 지정 특성 만들기

사용자 지정 특성을 만들려면 다음 단계를 수행합니다.  

1. **Azure Portal** > **Azure AD B2C** 로 이동합니다.

2. **사용자 특성** 을 선택합니다.

3. **추가** 를 선택합니다.

4. 특성 이름으로 **ArkoseSessionToken** 을 입력합니다.

5. **만들기** 를 선택합니다.

[사용자 지정 특성](./user-flow-custom-attributes.md?pivots=b2c-user-flow)에 대해 자세히 알아보세요.

### <a name="part-2---create-a-user-flow"></a>2부 - 사용자 흐름 만들기

사용자 흐름은 **등록** 및 **로그인** 하거나 **등록** 만 할 수 있습니다. Arkose Labs 사용자 흐름은 등록하는 동안에만 표시됩니다.

1. 사용자 흐름을 만드는 방법에 대한 [지침](./tutorial-create-user-flows.md)을 참조하세요. 기존 사용자 흐름을 사용하는 경우 **권장되는** 버전 유형이어야 합니다.

2. 사용자 흐름 설정에서 **사용자 특성** 으로 이동하여 **ArkoseSessionToken** 클레임을 선택합니다.

![사용자 지정 특성을 선택하는 방법을 보여 주는 이미지](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>3부 - 사용자 지정 HTML, JavaScript 및 페이지 레이아웃 구성

제공된 [HTML 스크립트](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html)로 이동합니다. 파일에는 다음 세 가지 작업을 수행하는 JavaScript 태그 `<script>`를 포함하는 HTML 템플릿이 포함되어 있습니다.

1. Arkose Labs 위젯을 렌더링하고 클라이언트 쪽 Arkose Labs 유효성 검사를 수행하는 Arkose Labs 스크립트를 로드합니다.

2. 사용자에게 표시되는 UI에서 `ArkoseSessionToken` 사용자 지정 특성에 해당하는 `extension_ArkoseSessionToken` 입력 요소 및 레이블을 숨깁니다.

3. 사용자가 Arkose Labs 챌린지를 완료하면 Arkose Labs에서 사용자의 응답을 확인하고 토큰을 생성합니다. 사용자 지정 JavaScript의 콜백 `arkoseCallback`은 생성된 토큰 값을 `extension_ArkoseSessionToken`의 값으로 설정합니다. 이 값은 다음 섹션에 설명된 대로 API 엔드포인트에 전송됩니다.

    Arkose Labs 클라이언트 쪽 유효성 검사에 대한 자세한 내용은 [이 문서](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup)를 참조하세요.

사용자 흐름에 사용자 지정 HTML 및 JavaScript를 사용하려면 앞에서 설명한 단계를 따르세요.

1. `<ARKOSE_PUBLIC_KEY>`가 클라이언트 쪽 유효성 검사를 위해 생성되었으며 계정에 대한 Arkose Labs 스크립트를 로드하는 데 사용되는 값과 일치하도록 [selfAsserted.html](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) 파일을 수정합니다.

2. CORS(크로스-원본 자원 공유) 사용 웹 엔드포인트에서 HTML 페이지를 호스팅합니다. [Azure Blob Storage 계정](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 및 [CORS 구성](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)

  >[!NOTE]
  >사용자 고유의 사용자 지정 HTML이 있는 경우 `<script>` 요소를 복사하여 HTML 페이지에 붙여넣습니다.

3. 다음 단계에 따라 페이지 레이아웃을 구성합니다.

   a. Azure Portal에서 **Azure AD B2C** 로 이동합니다.

   b. **사용자 흐름** 으로 이동하여 사용자 흐름을 선택합니다.

   다. **페이지 레이아웃** 을 선택합니다.

   d. **로컬 계정 등록 페이지 레이아웃** 을 선택합니다.

   e. **사용자 지정 페이지 콘텐츠 사용** 을 **예** 로 설정합니다.

   f. 사용자 지정 HTML이 존재하는 URI를 **사용자 지정 페이지 콘텐츠를 사용** 에 붙여넣습니다.

   g. 소셜 ID 공급자를 사용하는 경우 **소셜 계정 등록 페이지** 레이아웃에 대해 **e단계** 및 **f단계** 를 반복합니다.

   ![페이지 레이아웃을 보여 주는 이미지](media/partner-arkose-labs/page-layouts.png)

4. 사용자 흐름에서 **속성** 으로 이동하고 **JavaScript 적용** 을 선택하여 페이지 레이아웃(미리 보기)을 사용합니다. 자세한 내용은 이 [문서](./javascript-and-page-layout.md?pivots=b2c-user-flow)를 참조하세요.

### <a name="part-4---create-and-deploy-your-api"></a>4부 - API 만들기 및 배포

Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) 설치

>[!Note]
>이 섹션에서 설명하는 단계에서는 Azure Function을 배포하는 Visual Studio Code을 사용하고 있다고 가정합니다. Azure Portal, 터미널 또는 명령 프롬프트를 사용하거나 다른 코드 편집기를 사용하여 배포할 수 있습니다.

#### <a name="run-the-api-locally"></a>로컬로 API 실행

1. 왼쪽 탐색 표시줄에서 Visual Studio Code의 Azure 확장으로 이동합니다. 로컬 Azure 기능을 나타내는 **로컬 프로젝트** 폴더를 선택합니다.

2. **F5** 를 눌러서(또는 **디버그** > **디버깅 시작** 메뉴를 사용하여) 디버거를 시작하고 Azure Functions 호스트에 연결합니다. 이 명령은 Azure Functions에서 생성된 단일 디버그 구성을 자동으로 사용합니다.

3. Azure Function 확장은 로컬 개발을 위해 몇 개의 파일을 자동으로 생성하고, 종속성을 설치하고, 함수 핵심 도구(없는 경우)를 설치합니다. 이러한 도구는 디버깅 환경에 도움이 됩니다.

4. 함수 코어 도구의 출력은 Visual Studio Code **터미널** 패널에 표시됩니다. 호스트가 시작되면 출력에 표시된 로컬 URL을 **Alt키를 누른 채로 클릭** 하여 브라우저를 열고 함수를 실행합니다. Azure Functions 탐색기에서 함수를 마우스 오른쪽 단추로 클릭하여 로컬로 호스팅된 함수의 URL을 확인합니다.

테스트하는 동안 로컬 인스턴스를 다시 배포하려면 1-4단계를 반복합니다.

#### <a name="add-environment-variables"></a>환경 변수 추가

이 샘플은 [HTTP 기본 인증](https://tools.ietf.org/html/rfc7617)을 사용하여 웹 API 엔드포인트를 보호합니다.

사용자 이름 및 암호는 리포지토리의 일부가 아니라 환경 변수로 저장됩니다. 자세한 내용은 [local.settings.js](../azure-functions/functions-develop-local.md#local-settings-file) 파일을 참조하세요.

1. 루트 폴더의 파일에 local.settings.js를 만듭니다.

2. 아래 코드를 복사하여 파일에 붙여넣습니다.

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
**BASIC_AUTH_USERNAME** 및 **BASIC_AUTH_PASSWORD** 값은 Azure Function에 대한 API 호출을 인증하는 데 사용되는 자격 증명이 됩니다. 원하는 값을 선택합니다.

`<ARKOSE_PRIVATE_KEY>`는 Arkose Labs 서비스에서 생성한 서버 쪽 암호입니다. [Arkose Labs 서버 쪽 유효성 검사 API](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions)를 호출하여 프런트엔드에서 생성된 `ArkoseSessionToken`의 값에 대한 유효성을 검사하는 데 사용됩니다.

`<B2C_EXTENSIONS_APP_ID>`는 디렉터리에 사용자 지정 특성을 저장하는 Azure AD B2C에서 사용하는 앱의 애플리케이션 ID입니다. 앱 등록으로 이동하여 b2c-extensions-app를 검색하고 **개요** 창에서 애플리케이션 (클라이언트) ID를 복사하여 이 애플리케이션 ID를 찾을 수 있습니다. `-` 문자를 제거합니다.

![앱 ID로 검색을 보여 주는 이미지](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>웹에 애플리케이션 배포

1. [이](/azure/javascript/tutorial-vscode-serverless-node-04) 가이드에 설명된 단계에 따라 Azure Function을 클라우드에 배포합니다. Azure Function의 엔드포인트 웹 URL을 복사합니다.

2. 배포되면 **업로드 설정** 옵션을 선택합니다. 사용자 환경 변수를 App Service의 [애플리케이션 설정](../azure-functions/functions-develop-vs-code.md?tabs=csharp#application-settings-in-azure)에 업로드합니다. 이러한 애플리케이션 설정은 [Azure Portal을 통해 관리하거나](../azure-functions/functions-how-to-use-azure-function-app-settings.md) 구성할 수도 있습니다.

Azure Functions에 대한 Visual Studio Code 개발에 대해 자세히 알아보려면 [이 문서](../azure-functions/functions-develop-vs-code.md?tabs=csharp#republish-project-files)를 참조하세요.

#### <a name="configure-and-enable-the-api-connector"></a>API 커넥터 구성 및 사용

[API 커넥터를 만들고](./add-api-connector.md) 사용자 흐름에 사용하도록 설정합니다. API 커넥터 구성은 다음과 같아야 합니다.

![API 커넥터를 구성하는 방법을 보여 주는 이미지](media/partner-arkose-labs/configure-api-connector.png)

- **엔드포인트 URL** - 이전에 Azure Function을 배포하는 동안 복사한 함수 URL입니다.

- **사용자 이름 및 암호** - 이전에 환경 변수로 정의한 사용자 이름 및 암호입니다.

API 커넥터를 사용하도록 설정하려면 사용자 흐름에 대한 **API 커넥터** 설정에서 **사용자 단계를 만들기 전에** 호출할 API 커넥터를 선택합니다. 그러면 사용자가 등록 흐름에서 **만들기** 를 선택할 때 API가 호출됩니다. 이 API는 Arkose 위젯 `arkoseCallback`의 콜백에 의해 설정된 `ArkoseSessionToken` 값의 서버 쪽 유효성 검사를 수행합니다.

![API 커넥터 사용을 보여 주는 이미지](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트를 열고 정책 아래에서 **사용자 흐름** 을 선택합니다.

2. 이전에 만든 사용자 흐름을 선택합니다.

3. **사용자 흐름 실행** 을 선택하고 설정을 선택합니다.

   a. 애플리케이션: 등록된 앱을 선택합니다(JWT는 샘플).

   b. 회신 URL: 리디렉션 URL을 선택합니다.

   다. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름을 진행하고 계정을 만듭니다.

5. 로그아웃

6. 로그인 흐름을 진행합니다.  

7. **계속** 을 선택한 후 Arkose Labs 퍼즐이 표시됩니다.

## <a name="additional-resources"></a>추가 리소스

- Azure AD B2C 등록 사용자 흐름에 대한 [샘플 코드](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
