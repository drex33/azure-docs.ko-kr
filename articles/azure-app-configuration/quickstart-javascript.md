---
title: 빠른 시작 - JavaScript 앱에서 Azure App Configuration 사용 | Microsoft Docs
description: 이 빠른 시작에서는 Azure App Configuration에서 Node.js 앱을 만들어 코드와 별도로 애플리케이션 설정의 저장 및 관리를 중앙 집중화합니다.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: quickstart, mode-other
ms.date: 07/12/2021
ms.author: drewbat
ms.openlocfilehash: 8abdea698508a0d1f2f27733c9c11a5a0d059b57
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133047204"
---
# <a name="quickstart-create-a-javascript-app-with-azure-app-configuration"></a>빠른 시작: Azure App Configuration을 사용하여 JavaScript 앱 만들기

이 빠른 시작에서는 Azure App Configuration에서 [JavaScript용 App Configuration 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/appconfiguration/app-configuration/README.md)를 사용하여 애플리케이션 설정의 저장 및 관리를 중앙 집중화합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- [Node.js의 LTS 버전](https://nodejs.org/en/about/releases/). Windows에서 직접 또는 WSL(Linux용 Windows 하위 시스템)을 사용하여 Node.js를 설치하는 방법에 대한 자세한 내용은 [Node.js 시작하기](/windows/dev-environment/javascript/nodejs-overview)를 참조하세요.

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **구성 탐색기** > **만들기** > **키-값** 을 차례로 선택하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 정보 |

    지금은 **레이블** 과 **콘텐츠 형식** 을 비워 두세요.

8. **적용** 을 선택합니다.

## <a name="setting-up-the-nodejs-app"></a>Node.js 앱 설정

1. 이 자습서에서는 *app-configuration-quickstart* 라는 프로젝트에 대한 새 디렉터리를 만듭니다.

    ```console
    mkdir app-configuration-quickstart
    ```

1. 새로 만든 *app-configuration-quickstart* 디렉터리로 전환합니다.

    ```console
    cd app-configuration-quickstart
    ```

1. `npm install` 명령을 사용하여 Azure App Configuration 클라이언트 라이브러리를 설치합니다.

    ```console
    npm install @azure/app-configuration
    ```

1. *app.js* 라는 새 파일을 *app-configuration-quickstart* 디렉터리에 만들고 다음 코드를 추가합니다.

   ```javascript
   const appConfig = require("@azure/app-configuration");
   ```

## <a name="configure-your-connection-string"></a>연결 문자열 구성

1. **AZURE_APP_CONFIG_CONNECTION_STRING** 이라는 환경 변수를 설정하고, 이를 App Configuration 저장소에 대한 액세스 키로 설정합니다. 명령줄에서 다음 명령을 실행합니다.

    ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    ### <a name="command-line"></a>[명령줄](#tab/command-line)

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    ### <a name="macos"></a>[macOS](#tab/macOS)
    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

    ---

2. 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다. 환경 변수의 값을 출력하여 올바르게 설정되었는지 확인합니다.

## <a name="connect-to-an-app-configuration-store"></a>App Configuration 저장소에 연결

다음 코드 조각에서는 환경 변수에 저장된 연결 문자열을 사용하여 **AppConfigurationClient** 의 인스턴스를 만듭니다.

```javascript
const connection_string = process.env.AZURE_APP_CONFIG_CONNECTION_STRING;
const client = new appConfig.AppConfigurationClient(connection_string);
```

## <a name="get-a-configuration-setting"></a>구성 설정 가져오기

다음 코드 조각에서는 `key` 이름을 기준으로 구성 설정을 검색합니다. 이 예제에 표시된 키는 이 문서의 이전 단계에서 만들어졌습니다.

```javascript
async function run() {
  
  let retrievedSetting = await client.getConfigurationSetting({
    key: "TestApp:Settings:Message"
  });

  console.log("Retrieved value:", retrievedSetting.value);
}

run().catch((err) => console.log("ERROR:", err));
```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. 다음 명령을 실행하여 Node.js 앱을 실행합니다.

   ```powershell
   node app.js
   ```
1. 명령 프롬프트에 다음 출력이 표시됩니다.

   ```powershell
   Retrieved value: Data from Azure App Configuration
   ```
## <a name="clean-up-resources"></a>리소스 정리


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 App Configuration 저장소를 만들고 Node.js 앱에서 키-값에 액세스하는 방법을 알아보았습니다.

추가 코드 샘플은 다음을 방문하세요.

> [!div class="nextstepaction"]
> [Azure App Configuration 클라이언트 라이브러리 샘플](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/appconfiguration/app-configuration/samples/v1/javascript)
