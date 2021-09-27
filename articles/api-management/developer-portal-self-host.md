---
title: 개발자 포털 자체 호스팅
titleSuffix: Azure API Management
description: API Management 개발자 포털을 자체 호스트하는 방법을 알아봅니다.
author: dlepow
ms.author: danlep
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: f3e0004115b417c85914c7cc40fd98acf3c63d73
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592644"
---
# <a name="self-host-the-api-management-developer-portal"></a>API Management 개발자 포털 자체 호스팅

이 자습서에서는 [API Management 개발자 포털](api-management-howto-developer-portal.md)을 자체 호스트하는 방법에 대해 설명합니다. 자체 호스팅은 사용자 지정 논리로 개발자 포털을 확장할 수 있는 유연성과 런타임에서 동적으로 사용자 지정하는 위젯을 제공합니다. 다양한 기능을 사용하여 API Management 인스턴스에 대해 여러 포털을 자체 호스트할 수 있습니다. 포털을 자체 호스팅하는 경우 사용자는 관리자가 되며 업그레이드를 담당하게 됩니다. 

다음 단계에서는 개발자 포털에서 변경 내용을 적용하고 Azure Storage 계정에 게시하고 배포하는 방법을 보여줍니다.

관리 포털에서 미디어 파일을 이미 업로드하거나 수정한 경우 이 문서 뒷부분에 있는 [관리에서 자체 호스트로 이동](#move-from-managed-to-self-hosted-developer-portal)을 참조하세요.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>사전 요구 사항

로컬 개발 환경을 설정하려면 다음을 갖춰야 합니다.

- API Management 서비스 인스턴스 없는 경우 [빠른 시작 - Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.
- [정적 웹 사이트 기능](../storage/blobs/storage-blob-static-website.md)이 있는 Azure Storage 계정 [스토리지 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.
- 컴퓨터의 Git [이 Git 자습서](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)에 따라 설치하세요.
- Node.js(LTS 버전 `v10.15.0` 이상) 및 컴퓨터의 npm [Node.js 및 npm 다운로드 및 설치](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)를 참조하세요.
- Azure CLI. [Azure CLI 설치 단계](/cli/azure/install-azure-cli-windows)를 수행합니다.

## <a name="step-1-set-up-local-environment"></a>1단계: 로컬 환경 설정

로컬 환경을 설정하려면 리포지토리를 복제하고, 개발자 포털의 최신 릴리스로 전환하고, npm 패키지를 설치해야 합니다.

1. GitHub에서 [api-management-developer-portal](https://github.com/Azure/api-management-developer-portal.git) 리포지토리를 복제합니다.

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. 리포지토리의 로컬 복사본으로 이동합니다.

    ```console
    cd api-management-developer-portal
    ```

1. 포털의 최신 릴리스를 체크 아웃합니다.

    다음 코드를 실행하기 전에 [리포지토리의 릴리스 섹션](https://github.com/Azure/api-management-developer-portal/releases) 에서 현재 릴리스 태그를 확인하고 `<current-release-tag>` 값을 최신 릴리스 태그로 바꿉니다.
    
    ```console
    git checkout <current-release-tag>
    ```

1. 사용 가능한 npm 패키지를 설치합니다.

    ```console
    npm install
    ```

> [!TIP]
> 항상 [최신 포털 릴리스](https://github.com/Azure/api-management-developer-portal/releases)를 사용하고 포크된 포털을 최신 상태로 유지하세요. 소프트웨어 엔지니어는 `master` 일상적인 개발을 위해 이 리포지토리의 분기를 사용합니다. 이 리포지토리에는 불안정한 소프트웨어 버전이 있습니다.

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>2단계: JSON 파일, 정적 웹 사이트 및 CORS 설정 구성

개발자 포털에서 콘텐츠를 관리하려면 API Management의 REST API가 필요합니다.

### <a name="configdesignjson-file"></a>config.design.json 파일

`src` 폴더로 이동하여 `config.design.json` 파일을 엽니다.

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

파일 구성:

1. `managementApiUrl` 값에서 `<service-name>`를 API Management 인스턴스의 이름으로 대체합니다. [사용자 지정 도메인](configure-custom-domain.md)을 구성한 경우 이를 대신 사용합니다(예: `https://management.contoso.com`).

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. [수동으로 SAS 토큰을 만들어](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken) 직접 REST API가 API Management에 액세스하도록 할 수 있습니다.

1. 생성된 토큰을 복사하여 `managementApiAccessToken` 값으로 붙여 넣습니다.

1. `backendUrl` 값에서 `<service-name>`을 API Management 인스턴스의 이름으로 대체합니다. [사용자 지정 도메인](configure-custom-domain.md)을 구성한 경우 이를 대신 사용합니다(예: `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. 개발자 포털에서 CAPTCHA를 사용하도록 설정하려면 [CAPTCHA 사용](#enable-captcha)을 참조하세요.

### <a name="configpublishjson-file"></a>config.publish.json 파일

`src` 폴더로 이동하여 `config.publish.json` 파일을 엽니다.

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

파일 구성:

1. 이전 구성 파일에서 `managementApiUrl` 및 `managementApiAccessToken` 값을 복사하여 붙여 넣습니다.

1. 개발자 포털에서 CAPTCHA를 사용하도록 설정하려면 [CAPTCHA 사용](#enable-captcha)을 참조하세요.

### <a name="configruntimejson-file"></a>config.runtime.json 파일

`src` 폴더로 이동하여 `config.runtime.json` 파일을 엽니다.

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

파일 구성:

1. 이전 구성 파일에서 `managementApiUrl` 값을 복사하여 붙여 넣습니다.

1. `backendUrl` 값에서 `<service-name>`을 API Management 인스턴스의 이름으로 대체합니다. [사용자 지정 도메인](configure-custom-domain.md)을 구성한 경우 이를 대신 사용합니다(예: `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>정적 웹 사이트 구성

인덱스 및 오류 페이지에 대한 경로를 제공하여 스토리지 계정에서 **정적 웹 사이트** 기능을 구성합니다.

1. Azure Portal의 저장소 계정으로 이동하고 왼쪽 메뉴에서 **정적 웹 사이트** 를 선택합니다.

1. **정적 웹 사이트** 페이지에서 **사용** 을 선택합니다.

1. **인덱스 문서 이름** 에 *index.html* 을 입력합니다.

1. **오류 문서 경로** 에 *404/index.html* 을 입력합니다.

1. **저장** 을 선택합니다.

### <a name="configure-the-cors-settings"></a>CORS 설정 구성

CORS(원본 간 리소스 공유) 설정 구성

1. Azure Portal의 저장소 계정으로 이동하고 왼쪽 메뉴에서 **CORS** 를 선택합니다.

1. **Blob service** 탭에서 다음 규칙을 구성합니다.

    | 규칙 | 값 |
    | ---- | ----- |
    | 허용된 원본 | * |
    | 허용된 메서드 | 모든 HTTP 동사를 선택합니다. |
    | 허용된 헤더 | * |
    | 노출된 헤더 | * |
    | 최대 기간 | 0 |

1. **저장** 을 선택합니다.

## <a name="step-3-run-the-portal"></a>3단계: 포탈 실행

이제 개발 모드에서 로컬 포털 인스턴스를 빌드하고 실행할 수 있습니다. 개발 모드에서는 모든 최적화가 해제되고 원본 맵이 설정됩니다.

다음 명령을 실행합니다.

```console
npm start
```

잠시 후 기본 브라우저가 로컬 개발자 포털 인스턴스와 함께 자동으로 열립니다. 기본 주소는 `http://localhost:8080`이지만 `8080`이 이미 점유된 경우 포트가 변경될 수 있습니다. 프로젝트의 코드베이스가 변경되면 다시 빌드 트리거가 발생하며 브라우저 창을 새로 고칩니다.

## <a name="step-4-edit-through-the-visual-editor"></a>4단계: 비주얼 편집기를 통해 편집

비주얼 편집기를 사용하여 다음 작업을 수행할 수 있습니다.

- 포털 사용자 지정
- 작성자 콘텐츠
- 웹 사이트 구조 구성
- 외관 스타일 지정

[자습서: 개발자 포털 액세스 및 사용자 지정](api-management-howto-developer-portal-customize.md)을 참조하세요. 관리자 사용자 인터페이스의 기본 사항에 대해 설명하고, 기본 콘텐츠에 대해 권장되는 변경 사항을 나열합니다. 로컬 환경의 모든 변경 내용을 저장하고 **Ctrl+C** 를 눌러 닫습니다.

## <a name="step-5-publish-locally"></a>5단계: 로컬에 게시

포털 데이터는 강력한 개체 형식으로 발생합니다. 다음 명령은 개체를 정적 파일로 변환하고 출력을 `./dist/website` 디렉터리에 배치합니다.

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>6단계: BLOB에 정적 파일 업로드

Azure CLI를 사용하여 로컬로 생성된 정적 파일을 BLOB에 업로드하고 방문자가 해당 파일에 액세스할 수 있는지 확인합니다.

1. Windows 명령 프롬프트, PowerShell 또는 다른 명령 셸을 엽니다.

1. 다음 Azure CLI 명령을 실행합니다.
   
    `<account-connection-string>`을 스토리지 계정의 연결 문자열로 바꿉니다. 연결 문자열은 스토리지 계정의 **액세스 키** 섹션에서 가져올 수 있습니다.

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>7단계: 웹 사이트로 이동

이제 웹 사이트가 Azure Storage 속성(**정적 웹 사이트** 의 **기본 엔드포인트**)에 지정된 호스트 이름 아래에 있습니다.

## <a name="step-8-change-api-management-notification-templates"></a>8단계: API Management 알림 템플릿 변경

자체 호스팅 포털을 가리키도록 API Management 알림 템플릿의 개발자 포털 URL을 바꿉니다. [Azure API Management에서 알림 및 메일 템플릿을 구성하는 방법](api-management-howto-configure-notifications.md)을 참조하세요.

구체적으로는 기본 템플릇에 다음과 같은 변경 사항을 적용합니다.

> [!NOTE]
> 다음 **업데이트됨** 섹션의 값은 사용자가 **https:\//portal.contoso.com/** 에서 포털을 호스트하고 있는 것으로 가정합니다. 

### <a name="email-change-confirmation"></a>전자 메일 변경 확인

**전자 메일 변경 확인** 알림 템플릿에서 개발자 포털 URL을 업데이트합니다.

**원본 콘텐츠**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**업데이트됨**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>새 개발자 계정 확인

**새 개발자 계정 확인** 알림 템플릿에서 개발자 포털 URL을 업데이트합니다.

**원본 콘텐츠**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**업데이트됨**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>사용자 초대

**사용자 알림 초대** 템플릿에서 개발자 포털 URL을 업데이트 합니다.

**원본 콘텐츠**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**업데이트됨**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>새 구독 활성화

**새 구독 활성화** 알림 템플릿에서 개발자 포털 URL을 업데이트합니다.

**원본 콘텐츠**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**업데이트됨**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**원본 콘텐츠**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**업데이트됨**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**원본 콘텐츠**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**업데이트됨**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**원본 콘텐츠**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**업데이트됨**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>암호 변경 확인

**암호 변경 확인** 알림 템플릿에서 개발자 포털 URL을 업데이트합니다.

**원본 콘텐츠**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**업데이트됨**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>모든 템플릿

바닥글에 링크가 포함된 모든 템플릿에서 개발자 포털 URL을 업데이트합니다.

**원본 콘텐츠**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**업데이트됨**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>관리에서 자체 호스트된 개발자 포털로 이동

시간이 지남에 따라 비즈니스 요구 사항이 변경될 수 있습니다. API Management 개발자 포털의 관리형 버전이 더 이상 요구 사항을 충족하지 못하는 상황이 발생할 수 있습니다. 예를 들어 새로운 요구 사항으로 인해 타사 데이터 공급자와 통합되는 사용자 지정 위젯을 빌드해야 할 수 있습니다. 관리형 버전과 달리 자체 호스팅 버전의 포털은 완전한 유연성과 확장성을 제공합니다.

### <a name="transition-process"></a>전환 프로세스

관리형 버전에서 동일한 API Management 서비스 인스턴스 내의 자체 호스팅 버전으로 전환할 수 있습니다. 이 프로세스는 포털의 관리형 버전에서 수행한 수정 사항을 유지합니다. 포털의 콘텐츠를 미리 백업했는지 확인하세요. API Management 개발자 포털 [GitHub 리포지토리](https://github.com/Azure/api-management-developer-portal)의 `scripts` 폴더에서 백업 스크립트를 찾을 수 있습니다.

변환 프로세스는 이 문서의 이전 단계에 표시된 것처럼 일반 자체 호스팅 포털을 설정하는 프로세스와 거의 동일합니다. 구성 단계에는 한 가지 예외가 있습니다. `config.design.json` 파일의 저장소 계정은 관리형 버전의 포털의 저장소 계정과 동일해야 합니다. SAS URL을 검색하는 방법에 대한 지침은 [자습서: SAS 자격 증명을 통해 Azure Storage에 액세스하기 위해 Linux VM 시스템 할당 ID 사용](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls)을 참조하세요.

> [!TIP]
> `config.publish.json` 파일에 별도의 스토리지 계정을 사용하는 것이 좋습니다. 이러한 방식으로 더 많은 제어권을 확보하고 포털의 호스팅 서비스 관리를 간소화할 수 있습니다.

## <a name="enable-captcha"></a>CAPTCHA 사용

자체 호스팅 포털을 설정하는 경우 `useHipCaptcha` 설정을 통해 CAPTCHA을 사용하지 않도록 설정했을 수 있습니다. CAPTCHA와의 통신은 엔드포인트를 통해 수행되며, 관리형 개발자 포털 호스트 이름에 대해서만 CORS(원본 간 리소스 공유)가 수행될 수 있습니다. 개발자 포털이 자체 호스팅되는 경우, 포털에서는 다른 호스트 이름을 사용하고 CAPTCHA는 통신을 허용하지 않습니다.

### <a name="update-the-json-config-files"></a>JSON 구성 파일 업데이트

자체 호스팅 포털에서 CAPTCHA을 사용하도록 설정하려면 다음을 수행합니다.

1. 사용자 지정 도메인(예: `api.contoso.com`)을 API Management 서비스의 **개발자 포털** 엔드포인트에 할당합니다.

    이 도메인은 포털의 관리형 버전 및 CAPTCHA 엔드포인트에 적용됩니다. 자세한 단계는 [API Management 인스턴스에 사용자 지정 도메인 이름 구성](configure-custom-domain.md)을 참조하세요.

1. 자체 호스팅 포털은 [로컬 환경](#step-1-set-up-local-environment)의 `src` 폴더에 있습니다.

1. 구성 `json` 파일을 업데이트합니다.

    | 파일 | 새 값 | 참고 |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | `<custom-domain>`을 첫 번째 단계에서 설정한 사용자 지정 도메인으로 바꿉니다. |
    |  | `"useHipCaptcha": true` | 값을 `true`로 변경합니다. |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | `<custom-domain>`을 첫 번째 단계에서 설정한 사용자 지정 도메인으로 바꿉니다. |
    |  | `"useHipCaptcha": true` | 값을 `true`로 변경합니다. |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | `<custom-domain>`을 첫 번째 단계에서 설정한 사용자 지정 도메인으로 바꿉니다. |

1. 포털을 [게시](#step-5-publish-locally)합니다.

1. 새로 게시된 포털을 [업로드](#step-6-upload-static-files-to-a-blob)하고 호스팅합니다.

1. 사용자 지정 도메인을 통해 자체 호스팅 포털을 노출합니다.

포털 도메인의 첫 번째와 두 번째 수준은 1단계에서 설정한 도메인과 일치해야 합니다. 예: `portal.contoso.com`. 정확한 절차는 선택한 호스팅 플랫폼에 따라 달라집니다. Azure Storage 계정을 사용하는 경우 [Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑](../storage/blobs/storage-custom-domain-name.md) 지침을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [자체 호스팅을 위한 다른 방법](developer-portal-alternative-processes-self-host.md)에 대해 알아봅니다.
