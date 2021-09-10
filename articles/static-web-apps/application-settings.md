---
title: Azure Static Web Apps에 대한 애플리케이션 설정 구성
description: Azure Static Web Apps에 대한 애플리케이션 설정을 구성하는 방법을 알아봅니다.
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 8132ed61a1588c8ccdeb2ac9dc0eb6b5354fd0e0
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778848"
---
# <a name="configure-application-settings-for-azure-static-web-apps"></a>Azure Static Web Apps에 대한 애플리케이션 설정 구성

애플리케이션 설정에는 데이터베이스 연결 문자열과 같이 변경될 수 있는 값에 대한 구성 설정이 포함됩니다. 애플리케이션 설정을 추가하면 애플리케이션 코드를 변경하지 않고도 앱에 대한 구성 입력을 수정할 수 있습니다.

애플리케이션 설정:

- 미사용 시 암호화
- [스테이징](review-publish-pull-requests.md) 및 프로덕션 환경에 복사됨
- 영숫자와 `.`, `_`만 포함 가능
- 환경 변수라고도 함

> [!IMPORTANT]
> 이 문서에서 설명하는 애플리케이션 설정은 Azure Static Web App의 백 엔드 API에만 적용됩니다.
>
> 프런트 엔드 웹 애플리케이션에서 환경 변수를 사용하는 방법에 대한 자세한 내용은 [JavaScript 프레임워크](#javascript-frameworks-and-libraries) 또는 [정적 사이트 생성기](#static-site-generators)에 대한 문서를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Static Web Apps 애플리케이션
- [Azure CLI](/cli/azure/install-azure-cli)

## <a name="types-of-application-settings"></a>애플리케이션 설정의 유형

일반적으로 Azure Static Web Apps 애플리케이션에는 두 가지 측면이 있습니다. 첫 번째는 웹 애플리케이션 또는 HTML, CSS, JavaScript 및 이미지로 표현되는 정적 콘텐츠입니다. 두 번째는 Azure Functions 애플리케이션에서 구동하는 백 엔드 API입니다.

이 문서에서는 Azure Functions의 백 엔드 API에 대한 애플리케이션 설정을 관리하는 방법을 보여 줍니다.

이 문서에서 설명하는 애플리케이션 설정은 정적 웹 애플리케이션에서 사용하거나 참조할 수 없습니다. 그러나 많은 프런트 엔드 프레임워크 및 정적 사이트 생성기를 사용하면 개발 중에 환경 변수를 사용할 수 있습니다. 빌드 시 이러한 변수는 생성된 HTML 또는 JavaScript의 값으로 대체됩니다. HTML 및 JavaScript의 데이터는 사이트 방문자가 쉽게 검색할 수 있으므로 중요한 정보가 프런트 엔드 애플리케이션에 저장되지 않도록 하는 것이 좋습니다. 중요한 데이터를 보관하는 설정은 애플리케이션의 API 부분에 가장 잘 위치합니다.

JavaScript 프레임워크 또는 라이브러리에서 환경 변수를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

### <a name="javascript-frameworks-and-libraries"></a>JavaScript 프레임워크 및 라이브러리

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>정적 사이트 생성기

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>API 앱 설정 정보

Azure Static Web Apps의 API는 Azure Functions로 제공되며, 이를 통해 _local.settings.json_ 파일에서 애플리케이션 설정을 정의할 수 있습니다. 이 파일은 구성의 `Values` 속성에서 애플리케이션 설정을 정의합니다.

다음 샘플 _local.settings.json_ 에서는 `DATABASE_CONNECTION_STRING`에 대한 값을 추가하는 방법을 보여 줍니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

`Values` 속성에 정의된 설정은 `process.env` 개체에서 사용할 수 있는 환경 변수로 코드에서 참조될 수 있습니다.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

데이터베이스 연결 문자열과 같은 중요한 정보가 파일에 포함되는 경우가 많기 때문에 `local.settings.json` 파일은 GitHub 리포지토리에서 추적되지 않습니다. 로컬 설정이 머신에 남아 있으므로 Azure에 설정을 수동으로 업로드해야 합니다.

일반적으로 설정 업로드는 자주 수행되지 않으며, 모든 빌드에서 필요하지 않습니다.

## <a name="uploading-application-settings"></a>애플리케이션 설정 업로드

Azure Portal 또는 Azure CLI를 통해 애플리케이션 설정을 구성할 수 있습니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

Azure Portal은 애플리케이션 설정을 만들고 업데이트하고 삭제하기 위한 인터페이스를 제공합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. 검색 창에서 **Static Web Apps** 를 검색하고 선택합니다.

1. 사이드바에서 **구성** 옵션을 클릭합니다.

1. 애플리케이션 설정을 적용하려는 환경을 선택합니다. 스테이징 환경은 끌어오기 요청이 생성될 때 자동으로 만들어지며 끌어오기 요청이 병합되면 프로덕션으로 승격됩니다. 환경마다 애플리케이션 설정을 지정할 수 있습니다.

1. **추가 단추** 를 클릭하여 새 앱 설정을 추가합니다.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Azure Static Web Apps 구성 보기":::

1. **이름** 및 **값** 을 입력합니다.

1. **확인** 을 클릭합니다.

1. **저장** 을 클릭합니다.

### <a name="using-the-azure-cli"></a>Azure CLI 사용

`az rest` 명령을 사용하여 Azure에 대한 설정을 대량으로 업로드할 수 있습니다. 명령은 `properties`라는 부모 속성에서 애플리케이션 설정을 JSON 개체로 허용합니다.

적절한 값으로 JSON 파일을 만드는 가장 쉬운 방법은 _local.settings.json_ 파일의 수정된 버전을 만드는 것입니다.

1. 중요한 데이터가 포함된 새 파일이 공개적으로 노출되지 않도록 하려면 _.gitignore_ 파일에 다음 항목을 추가합니다.

   ```bash
   local.settings*.json
   ```

2. 다음으로 _local.settings.json_ 파일의 복사본을 만들고 _local.settings.properties.json_ 으로 이름을 지정합니다.

3. 새 파일 내에서 애플리케이션 설정을 제외한 파일에서 다른 모든 데이터를 제거하고 `Values`를 `properties`로 이름을 바꿉니다.

   이제 파일은 다음 예제와 같이 표시됩니다.

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

Azure CLI 명령을 사용하려면 업로드를 실행하기 위해 계정과 관련된 많은 값이 필요합니다. Static Web Apps 리소스의 _개요_ 창에서 다음 정보에 액세스할 수 있습니다.

1. 정적 사이트 이름
2. 리소스 그룹 이름
3. 구독 ID

:::image type="content" source="media/application-settings/overview.png" alt-text="Azure Static Web Apps 개요":::

4. 터미널 또는 명령줄에서 다음 명령을 실행합니다. `<YOUR_STATIC_SITE_NAME>`, `<YOUR_RESOURCE_GROUP_NAME>` 및 `<YOUR_SUBSCRIPTION_ID>`의 자리 표시자를 _개요_ 창의 값으로 바꾸어야 합니다.

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> "local.settings.properties.json" 파일은 이 명령이 실행되는 디렉터리와 같은 디렉터리에 있어야 합니다. 이 파일의 이름은 원하는 대로 지정할 수 있습니다. 이름은 중요하지 않습니다.

### <a name="view-application-settings-with-the-azure-cli"></a>Azure CLI를 사용하여 애플리케이션 설정 보기

애플리케이션 설정은 Azure CLI를 통해 볼 수 있습니다.

- 터미널 또는 명령줄에서 다음 명령을 실행합니다. 자리 표시자 `<YOUR_SUBSCRIPTION_ID>`, `<YOUR_RESOURCE_GROUP_NAME>`, `<YOUR_STATIC_SITE_NAME>`을 사용자의 값으로 바꿔야 합니다.

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프런트 엔드 프레임워크 구성](front-end-frameworks.md)
