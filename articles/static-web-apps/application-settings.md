---
title: Azure Static Web Apps에 대한 애플리케이션 설정 구성
description: Azure Static Web Apps에 대한 애플리케이션 설정을 구성하는 방법을 알아봅니다.
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/23/2021
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: a104860eb72a6376c2ab337f31bb06fd041f42a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597097"
---
# <a name="configure-application-settings-for-azure-static-web-apps"></a>Azure Static Web Apps에 대한 애플리케이션 설정 구성

애플리케이션 설정에는 데이터베이스 연결 문자열과 같이 변경될 수 있는 값에 대한 구성 설정이 포함됩니다. 애플리케이션 설정을 추가하면 애플리케이션 코드를 변경하지 않고도 앱에 대한 구성 입력을 수정할 수 있습니다.

애플리케이션 설정:

- 정적 웹앱의 백 엔드 API에 대한 환경 변수로 사용할 수 있습니다.
- [인증 구성에](key-vault-secrets.md) 사용되는 비밀을 저장하는 데 사용할 수 있습니다.
- 미사용 시 암호화
- [스테이징](review-publish-pull-requests.md) 및 프로덕션 환경에 복사됨
- 영숫자와 `.`, `_`만 포함 가능

> [!IMPORTANT]
> 이 문서에서 설명하는 애플리케이션 설정은 Azure Static Web App의 백 엔드 API에만 적용됩니다.
>
> 프런트 엔드 웹 애플리케이션을 빌드하는 데 필요한 환경 변수를 구성하려면 [빌드 구성을](build-configuration.md#environment-variables)참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Static Web Apps 애플리케이션
- [Azure CLI](/cli/azure/install-azure-cli) - 명령줄을 사용하는 경우에 필요합니다.

## <a name="configure-api-application-settings-for-local-development"></a>로컬 개발을 위한 API 애플리케이션 설정 구성

Azure Static Web Apps API는 로컬로 애플리케이션을 실행할 때 _local.settings.json_ 파일에서 애플리케이션 설정을 정의할 수 있는 Azure Functions 의해 구동됩니다. 이 파일은 구성의 `Values` 속성에서 애플리케이션 설정을 정의합니다.

> [!NOTE]
> _local.settings.json_ 파일은 로컬 개발에만 사용됩니다. [Azure Portal](https://portal.azure.com) 사용하여 프로덕션에 대한 애플리케이션 설정을 구성합니다.

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

속성에 정의된 설정 `Values` 코드에서 환경 변수로 참조할 수 있습니다. 예를 들어 Node.js 함수는 개체에서 사용할 수 `process.env` 있습니다.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

데이터베이스 연결 문자열과 같은 중요한 정보가 파일에 포함되는 경우가 많기 때문에 `local.settings.json` 파일은 GitHub 리포지토리에서 추적되지 않습니다. 로컬 설정은 컴퓨터에 남아 있기 때문에 Azure에서 설정을 수동으로 구성해야 합니다.

일반적으로 설정 구성은 자주 수행되지 않으며 모든 빌드에 필요하지 않습니다.

## <a name="configure-application-settings"></a>애플리케이션 설정 구성

Azure Portal 또는 Azure CLI를 통해 애플리케이션 설정을 구성할 수 있습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

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

### <a name="use-the-azure-cli"></a>Azure CLI 사용

`az rest` 명령을 사용하여 Azure에 대한 설정을 대량으로 업로드할 수 있습니다. 명령은 `properties`라는 부모 속성에서 애플리케이션 설정을 JSON 개체로 허용합니다.

적절한 값으로 JSON 파일을 만드는 가장 쉬운 방법은 _local.settings.json_ 파일의 수정된 버전을 만드는 것입니다.

1. 중요한 데이터가 포함된 새 파일이 공개적으로 노출되지 않도록 하려면 _.gitignore_ 파일에 다음 항목을 추가합니다.

   ```bash
   local.settings*.json
   ```

1. 다음으로 _local.settings.json_ 파일의 복사본을 만들고 _local.settings.properties.json_ 으로 이름을 지정합니다.

1. 새 파일 내에서 애플리케이션 설정을 제외한 파일에서 다른 모든 데이터를 제거하고 `Values`를 `properties`로 이름을 바꿉니다.

   이제 파일은 다음 예제와 같이 표시됩니다.

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

1. 다음 명령을 실행하여 구독의 정적 웹앱을 나열하고 세부 정보를 표시합니다.

    ```bash
    az staticwebapp list -o json
    ```

    구성하려는 정적 웹앱을 찾고 해당 ID를 기록해 둡다.

1. 터미널 또는 명령줄에서 다음 명령을 실행하여 설정을 업로드합니다. `<YOUR_APP_ID>`를 이전 단계에서 검색한 앱의 ID로 대체합니다.

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "<YOUR_APP_ID>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

  > [!IMPORTANT]
  > "local.settings.properties.json" 파일은 이 명령이 실행되는 디렉터리와 같은 디렉터리에 있어야 합니다. 이 파일의 이름은 원하는 대로 지정할 수 있습니다. 이름은 중요하지 않습니다.

### <a name="view-application-settings-with-the-azure-cli"></a>Azure CLI를 사용하여 애플리케이션 설정 보기

애플리케이션 설정은 Azure CLI를 통해 볼 수 있습니다.

- 터미널 또는 명령줄에서 다음 명령을 실행합니다. 자리 표시자를 `<YOUR_APP_ID>` 값으로 바꾸어야 합니다.

   ```bash
   az rest --method post --uri "<YOUR_APP_ID>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프런트 엔드 프레임워크 구성](front-end-frameworks.md)
