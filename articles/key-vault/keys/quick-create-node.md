---
title: 빠른 시작 - JavaScript용 Azure Key Vault 클라이언트 라이브러리(버전 4)
description: JavaScript 클라이언트 라이브러리를 사용하여 Azure Key Vault에서 키를 생성, 검색 및 삭제하는 방법을 알아봅니다.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js, mode-other
ms.openlocfilehash: b7fdc002dd9ca928bec860f8e5eb6610a6f84d75
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133058082"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>빠른 시작: JavaScript용 Azure Key Vault 클라이언트 라이브러리(버전 4)

JavaScript용 Azure Key Vault 키 클라이언트 라이브러리를 시작합니다. [Azure Key Vault](../general/overview.md)는 암호화 키에 대한 보안 저장소를 제공하는 클라우드 서비스입니다. 키, 암호, 인증서 및 기타 비밀을 안전하게 저장할 수 있습니다. Azure Portal을 통해 Azure Key Vault를 만들고 관리할 수 있습니다. 이 빠른 시작에서는 JavaScript 키 클라이언트 라이브러리를 사용하여 Azure Key Vault에서 키를 만들고, 검색하고, 삭제하는 방법을 알아봅니다.

Key Vault 클라이언트 라이브러리 리소스:

[API 참조 설명서](/javascript/api/overview/azure/key-vault-index) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [패키지(npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

Key Vault 및 키에 대한 자세한 내용은 다음을 참조하세요.
- [Key Vault 개요](../general/overview.md)
- [키 개요](about-keys.md).

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 현재 운영 체제의 [Node.js](https://nodejs.org)입니다.
- [Azure CLI](/cli/azure/install-azure-cli)
- Key Vault - [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md) 또는 [Azure PowerShell](../general/quick-create-powershell.md)을 사용하여 만들 수 있습니다.

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

다음으로, 클라우드에 배포할 수 있는 Node.js 애플리케이션을 만듭니다. 

1. 명령 셸에서 `key-vault-node-app`이라는 폴더를 만듭니다.

```azurecli
mkdir key-vault-node-app
```

1. 새로 만든 *key-vault-node-app* 디렉터리로 변경하고, 'init' 명령을 실행하여 노드 프로젝트를 초기화합니다.

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Key Vault 패키지 설치

콘솔 창에서 Node.js용 Azure Key Vault [키 라이브러리](https://www.npmjs.com/package/@azure/keyvault-keys)를 설치합니다.

```azurecli
npm install @azure/keyvault-keys
```

[azure.identity](https://www.npmjs.com/package/@azure/identity) 패키지를 설치하여 Key Vault에 인증합니다.

```azurecli
npm install @azure/identity
```

## <a name="set-environment-variables"></a>환경 변수 설정

이 애플리케이션은 키 자격 증명 모음 이름을 `KEY_VAULT_NAME`이라는 환경 변수로 사용합니다.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS 또는 Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한 부여

키 권한을 사용자 계정에 부여하는 키 자격 증명 모음에 대한 액세스 정책을 만듭니다.

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>코드 예제

아래의 코드 샘플에서는 클라이언트 생성, 키 설정, 키 검색 및 키 삭제 방법을 보여줍니다. 

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

1. 새 텍스트 파일을 만들고 'index.js'로 저장합니다.

1. Azure 및 Node.js 모듈을 로드하는 데 필요한 호출 추가

1. 기본적인 예외 처리를 포함하여 프로그램의 구조 만들기

```javascript
const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>지시문 추가

다음 지시문을 코드 위쪽에 추가합니다.

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>클라이언트 인증 및 만들기

이 빠른 시작에서 로그인한 사용자는 로컬 개발에서 기본적으로 설정되는 방법인 키 자격 증명 모음에 인증하는 데 사용됩니다. Azure에 배포된 애플리케이션의 경우 관리 ID를 App Service 또는 Virtual Machine에 할당해야 합니다. 자세한 내용은 [관리 ID 개요](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

아래 예제에서 키 자격 증명 모음 이름은 "https://\<your-key-vault-name\>.vault.azure.net" 형식의 키 자격 증명 모음 URI로 확장됩니다. 이 예제에서는 ID를 제공하는 다양한 옵션이 있는 서로 다른 환경에서 동일한 코드를 사용할 수 있도록 하는 [Azure Identity Library](/javascript/api/overview/azure/identity-readme)에서 ['DefaultAzureCredential()'](/javascript/api/@azure/identity/defaultazurecredential) 클래스를 사용합니다. 키 자격 증명 모음 인증에 대한 자세한 내용은 [개발자 가이드](../general/developers-guide.md#authenticate-to-key-vault-in-code)를 참조하세요.

'main()' 함수에 다음 코드를 추가합니다.

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>키 저장

이제 애플리케이션을 인증했으므로 [createKey 메서드](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_)를 사용하여 키를 keyvault에 배치할 수 있습니다. 메서드의 매개 변수는 키 이름과 [키 형식](/javascript/api/@azure/keyvault-keys/keytype)을 허용합니다.

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>키 검색

이제 [getKey 메서드](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_)를 사용하여 이전에 설정한 값을 검색할 수 있습니다.

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>키 삭제

마지막으로 [beginDeleteKey](/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) 및 [purgeDeletedKey](/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_) 메서드를 사용하여 키 자격 증명 모음에서 키를 삭제하고 제거하겠습니다.

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>예제 코드

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log(`Purging your key from ${keyVaultName} ...`);
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>테스트 및 확인

다음 명령을 실행하여 앱을 실행합니다.

```azurecli
npm install
npm index.js
```

다음과 유사한 출력이 표시됩니다.

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 키 자격 증명 모음을 만들고, 키를 저장하고, 해당 키를 검색했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Azure Key Vault 개요](about-keys.md) 읽기
- [키 자격 증명 모음에 대한 액세스를 보호](../general/security-features.md)하는 방법
- [Azure Key Vault 개발자 가이드](../general/developers-guide.md) 참조
- [Azure Key Vault 보안 개요](../general/security-features.md)를 검토하세요.
