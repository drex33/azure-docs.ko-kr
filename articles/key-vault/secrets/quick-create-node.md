---
title: 빠른 시작 - JavaScript용 Azure Key Vault 비밀 클라이언트 라이브러리(버전 4)
description: JavaScript 클라이언트 라이브러리를 사용하여 Azure Key Vault에서 비밀을 생성, 검색 및 삭제하는 방법을 알아봅니다.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/29/2021
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js, mode-other
ms.openlocfilehash: 78ab150e12cc4a0ef9e22573d89176f52234607f
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133217764"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>빠른 시작: JavaScript용 Azure Key Vault 비밀 클라이언트 라이브러리(버전 4)

JavaScript용 Azure Key Vault 비밀 클라이언트 라이브러리를 시작합니다. [Azure Key Vault](../general/overview.md)는 보안 비밀 저장소를 제공하는 클라우드 서비스입니다. 키, 암호, 인증서 및 기타 비밀을 안전하게 저장할 수 있습니다. Azure Portal을 통해 Azure Key Vault를 만들고 관리할 수 있습니다. 이 빠른 시작에서는 JavaScript 클라이언트 라이브러리를 사용하여 Azure Key Vault에서 비밀을 만들고, 검색하고, 삭제하는 방법을 알아봅니다.

Key Vault 클라이언트 라이브러리 리소스:

[API 참조 설명서](/javascript/api/overview/azure/key-vault-index) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [패키지(npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

Key Vault 및 비밀에 대한 자세한 내용은 다음을 참조하세요.
- [Key Vault 개요](../general/overview.md)
- [비밀 개요](about-secrets.md)

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 현재 [Node.js LTS](https://nodejs.org)입니다.
- [Azure CLI](/cli/azure/install-azure-cli)
- 기존 Key Vault-다음을 사용 하 여 만들 수 있습니다.
    - [Azure CLI](../general/quick-create-cli.md)
    - [Azure Portal](../general/quick-create-portal.md) 
    - [Azure PowerShell](../general/quick-create-powershell.md)

이 빠른 시작에서는 [Azure CLI](/cli/azure/install-azure-cli)를 실행하고 있다고 가정합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

1. `login` 명령을 실행합니다.

    ```azurecli-interactive
    az login
    ```

    CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다.

    그렇지 않으면 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)에서 브라우저 페이지를 열고 터미널에 표시된 권한 부여 코드를 입력합니다.

2. 브라우저에서 계정 자격 증명으로 로그인합니다.

## <a name="create-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

주요 자격 증명 모음을 사용 하는 Node.js 응용 프로그램을 만듭니다. 

1. 터미널에서 라는 폴더를 만들고 `key-vault-node-app` 해당 폴더로 변경 합니다.

    ```terminal
    mkdir key-vault-node-app && cd key-vault-node-app
    ```

1. Node.js 프로젝트를 초기화 합니다.

    ```terminal
    npm init -y
    ```

## <a name="install-key-vault-packages"></a>Key Vault 패키지 설치

1. 터미널을 사용 하 여 Node.js에 대 한 Azure Key Vault 비밀 라이브러리를 설치 [@azure/keyvault-secrets](https://www.npmjs.com/package/@azure/keyvault-secrets) 합니다.

    ```terminal
    npm install @azure/keyvault-secrets
    ```

1. Azure Id 라이브러리, 패키지를 설치 [@azure/identity](https://www.npmjs.com/package/@azure/identity) 하 여 Key Vault에 인증 합니다.

    ```terminal
    npm install @azure/identity
    ```

## <a name="create-a-service-principal"></a>서비스 주체 만들기

서비스 주체를 만들고 Azure 리소스에 대한 액세스를 구성합니다. 개별 사용자 계정 및 암호 대신 서비스 주체를 사용 합니다.

1. Azure CLI 또는 [Cloud Shell](https://shell.azure.com)에서 Azure [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 사용하여 서비스 주체를 만듭니다. 

    ```bash
    az ad sp create-for-rbac --name YOUR-SERVICE-PRINCIPAL-NAME
    ```

2. 이 빠른 시작의 뒷부분에서 azure SDK에 대 한 Azure 인증을 설정 하기 위해 명령 응답을 기록해 둡니다.

    ```json
    {
      "appId": "YOUR-SERVICE-PRINCIPAL-ID",
      "displayName": "YOUR-SERVICE-PRINCIPAL-NAME",
      "name": "http://YOUR-SERVICE-PRINCIPAL-NAME",
      "password": "YOUR-SERVICE-PRINCIPAL-SECRET",
      "tenant": "YOUR-TENANT-ID"
    }
    ```

## <a name="grant-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한 부여

[Az keyvault set 정책](/cli/azure/keyvault#az_keyvault_set_policy) 명령을 사용 하 여 서비스 사용자에 게 비밀 권한을 부여 하는 key vault에 대 한 액세스 정책을 만듭니다.

```azurecli
az keyvault set-policy --name <your-key-vault-name> --spn <your-service-principal-id> --secret-permissions delete get list set purge
```

## <a name="set-environment-variables"></a>환경 변수 설정

1. 다음 필수 환경 변수를 수집 합니다.

    |이름|값|
    |--|--|
    |AZURE_TENANT_ID|서비스 주체 결과에서-테 넌 트 ID입니다.|
    |AZURE_CLIENT_ID|서비스 주체 결과에서-서비스 보안 주체 ID|
    |AZURE_CLIENT_SECRET|서비스 주체 결과에서-서비스 보안 주체 암호|
    |KEYVAULT_URI|https://YOUR-KEY-VAULT-NAME.vault.azure.net/|

1. 명령을 선택 하 고 위의 표에 있는 각 키와 설정에 대해 한 번씩 4 번 실행 합니다. 

    # <a name="windows"></a>[Windows](#tab/env-windows)
    
    ```cmd
    set name=value
    ```
    # <a name="windows-powershell"></a>[Windows PowerShell](#tab/env-windows-powershell)
    
    ```powershell
    $Env:name="value"
    ```
    # <a name="macos-or-linux"></a>[macOS 또는 Linux](#tab/env-mac-linux)
    
    ```cmd
    export name=value
    ```
    
    ---

## <a name="code-example"></a>코드 예제

아래의 코드 샘플에서는 클라이언트를 만들고, 비밀을 설정, 검색 및 삭제하는 방법을 보여 줍니다. 

1. 새 텍스트 파일을 만들고 다음 코드를 **index.js** 파일에 붙여넣습니다. 

    ```javascript
    const { SecretClient } = require("@azure/keyvault-secrets");
    const { DefaultAzureCredential } = require("@azure/identity");
    
    // Load the .env file if it exists
    const dotenv = require("dotenv");
    dotenv.config();
    
    async function main() {
      // DefaultAzureCredential expects the following three environment variables:
      // - AZURE_TENANT_ID: The tenant ID in Azure Active Directory
      // - AZURE_CLIENT_ID: The application (client) ID registered in the AAD tenant
      // - AZURE_CLIENT_SECRET: The client secret for the registered application
      const credential = new DefaultAzureCredential();
    
      const url = process.env["KEYVAULT_URI"] || "<keyvault-url>";
    
      const client = new SecretClient(url, credential);
    
      // Create a secret
      const uniqueString = new Date().getTime();
      const secretName = `secret${uniqueString}`;
      const result = await client.setSecret(secretName, "MySecretValue");
      console.log("result: ", result);
    
      // Read the secret we created
      const secret = await client.getSecret(secretName);
      console.log("secret: ", secret);
    
      // Update the secret with different attributes
      const updatedSecret = await client.updateSecretProperties(secretName, result.properties.version, {
        enabled: false
      });
      console.log("updated secret: ", updatedSecret);
    
      // Delete the secret
      // If we don't want to purge the secret later, we don't need to wait until this finishes
      await client.beginDeleteSecret(secretName);
    }
    
    main().catch((error) => {
      console.error("An error occurred:", error);
      process.exit(1);
    });
    ```

## <a name="run-the-sample-application"></a>샘플 애플리케이션 실행

1. 앱 실행:

    ```terminal
    node index.js
    ```

1. Create 및 get 메서드는 암호에 대 한 전체 JSON 개체를 반환 합니다.

    ```JSON
    {
        "value": "MySecretValue",
        "name": "secret1637692472606",
        "properties": {
            "createdOn": "2021-11-23T18:34:33.000Z",
            "updatedOn": "2021-11-23T18:34:33.000Z",
            "enabled": true,
            "recoverableDays": 90,
            "recoveryLevel": "Recoverable+Purgeable",
            "id": "https: //YOUR-KEYVAULT-NAME.vault.azure.net/secrets/secret1637692472606/YOUR-VERSION",
            "vaultUrl": "https: //YOUR-KEYVAULT-NAME.vault.azure.net",
            "version": "YOUR-VERSION",
            "name": "secret1637692472606"
        }
    }
    ```

    Update 메서드는 이름/값 쌍 **속성** 을 반환 합니다.

    ```JSON
    "createdOn": "2021-11-23T18:34:33.000Z",
    "updatedOn": "2021-11-23T18:34:33.000Z",
    "enabled": true,
    "recoverableDays": 90,
    "recoveryLevel": "Recoverable+Purgeable",
    "id": "https: //YOUR-KEYVAULT-NAME.vault.azure.net/secrets/secret1637692472606/YOUR-VERSION",
    "vaultUrl": "https: //YOUR-KEYVAULT-NAME.vault.azure.net",
    "version": "YOUR-VERSION",
    "name": "secret1637692472606"
    ```

## <a name="integrating-with-app-configuration"></a>앱 구성과 통합

Azure SDK는 지정 된 Key Vault 비밀 ID를 구문 분석 하는 도우미 메서드인 [parseKeyVaultSecretIdentifier](/javascript/api/@azure/keyvault-secrets/#parseKeyVaultSecretIdentifier_string_)을 제공 합니다. Key Vault에 대 한 [앱 구성](/azure/azure-app-configuration/) 참조를 사용 하는 경우이 작업이 필요 합니다. 앱 구성은 Key Vault 비밀 ID를 저장 합니다. 비밀 이름을 가져오기 위해 해당 ID를 구문 분석 하려면 _parseKeyVaultSecretIdentifier_ 메서드가 필요 합니다. 비밀 이름이 있으면이 빠른 시작의 코드를 사용 하 여 현재 비밀 값을 가져올 수 있습니다.  

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 키 자격 증명 모음을 만들고, 비밀을 저장하고, 해당 비밀을 검색했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Azure Key Vault 비밀 개요](about-secrets.md) 읽기
- [키 자격 증명 모음에 대한 액세스를 보호](../general/security-features.md)하는 방법
- [Azure Key Vault 개발자 가이드](../general/developers-guide.md) 참조
- [Azure Key Vault 보안 개요](../general/security-features.md)를 검토하세요.
