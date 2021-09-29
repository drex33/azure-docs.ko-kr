---
title: 빠른 시작 - .NET용 Azure Key Vault 비밀 클라이언트 라이브러리(버전 4)
description: .NET 클라이언트 라이브러리(버전 4)를 사용하여 Azure Key Vault에서 비밀을 생성, 검색 및 삭제하는 방법을 알아봅니다.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: e639a096032d0f5c7688bf739317abbeb31d8856
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730705"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>빠른 시작: .NET용 Azure Key Vault 비밀 클라이언트 라이브러리(SDK v4)

.NET용 Azure Key Vault 비밀 클라이언트 라이브러리를 시작합니다. [Azure Key Vault](../general/overview.md)는 보안 비밀 저장소를 제공하는 클라우드 서비스입니다. 키, 암호, 인증서 및 기타 비밀을 안전하게 저장할 수 있습니다. Azure Portal을 통해 Azure Key Vault를 만들고 관리할 수 있습니다. 이 빠른 시작에서는 .NET 클라이언트 라이브러리를 사용하여 Azure Key Vault에서 비밀을 만들고, 검색하고, 삭제하는 방법을 알아봅니다.

Key Vault 클라이언트 라이브러리 리소스:

[API 참조 설명서](/dotnet/api/azure.security.keyvault.secrets) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [패키지(NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

Key Vault 및 비밀에 대한 자세한 내용은 다음을 참조하세요.
- [Key Vault 개요](../general/overview.md)
- [비밀 개요](about-secrets.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK 이상](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* Key Vault - [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md) 또는 [Azure PowerShell](../general/quick-create-powershell.md)을 사용하여 만들 수 있습니다.

이 빠른 시작에서는 `dotnet` 및 Azure CLI를 사용합니다.

## <a name="setup"></a>설치 프로그램

이 빠른 시작에서는 Azure CLI와 함께 Azure ID 라이브러리를 사용하여 사용자를 Azure Services에 인증합니다. 개발자는 Visual Studio 또는 Visual Studio Code를 사용하여 해당 호출을 인증할 수도 있습니다. 자세한 내용은 [Azure ID 클라이언트 라이브러리를 사용하여 클라이언트 인증](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)을 참조하세요.

### <a name="sign-in-to-azure"></a>Azure에 로그인

1. `login` 명령을 실행합니다.

    ```azurecli-interactive
    az login
    ```

    CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다.

    그렇지 않으면 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)에서 브라우저 페이지를 열고 터미널에 표시된 권한 부여 코드를 입력합니다.

2. 브라우저에서 계정 자격 증명으로 로그인합니다.

### <a name="grant-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한 부여

비밀 권한을 사용자 계정에 부여하는 키 자격 증명 모음에 대한 액세스 정책을 만듭니다.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

### <a name="create-new-net-console-app"></a>새 .NET 콘솔 앱 만들기

1. 명령 셸에서 다음 명령을 실행하여 `key-vault-console-app`이라는 이름의 프로젝트를 만듭니다.

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. 새로 만든 *key-vault-console-app* 디렉터리로 변경하고, 다음 명령을 실행하여 프로젝트를 빌드합니다.

    ```dotnetcli
    dotnet build
    ```

    빌드 출력에 경고나 오류가 포함되지 않아야 합니다.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>패키지 설치

명령 셸에서 .NET용 Azure Key Vault 비밀 클라이언트 라이브러리를 설치합니다.

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

이 빠른 시작에서는 Azure ID용 Azure SDK 클라이언트 라이브러리도 설치해야 합니다.

```dotnetcli
dotnet add package Azure.Identity
```
#### <a name="set-environment-variables"></a>환경 변수 설정

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
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>개체 모델

.NET용 Azure Key Vault 비밀 클라이언트 라이브러리를 사용하면 비밀을 관리할 수 있습니다. [코드 예제](#code-examples) 섹션에서는 클라이언트를 만들고, 비밀을 설정, 검색 및 삭제하는 방법을 보여줍니다.

## <a name="code-examples"></a>코드 예제

### <a name="add-directives"></a>지시문 추가

다음 지시문을 *Program.cs* 의 위쪽에 추가합니다.

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>클라이언트 인증 및 만들기

이 빠른 시작에서 로그인한 사용자는 로컬 개발에서 기본적으로 설정되는 방법인 키 자격 증명 모음에 인증하는 데 사용됩니다. Azure에 배포된 애플리케이션의 경우 관리 ID를 App Service 또는 Virtual Machine에 할당해야 합니다. 자세한 내용은 [관리 ID 개요](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

아래 예제에서 키 자격 증명 모음 이름은 "https://\<your-key-vault-name\>.vault.azure.net" 형식의 키 자격 증명 모음 URI로 확장됩니다. 이 예제에서는 ID를 제공하는 다양한 옵션이 있는 서로 다른 환경에서 동일한 코드를 사용할 수 있도록 하는 [Azure Identity Library](/dotnet/api/overview/azure/identity-readme)에서 ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) 클래스를 사용합니다. 키 자격 증명 모음 인증에 대한 자세한 내용은 [개발자 가이드](../general/developers-guide.md#authenticate-to-key-vault-in-code)를 참조하세요.

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>비밀 저장

이제 콘솔 앱이 인증되었으므로 비밀을 키 자격 증명 모음에 추가합니다. 이 작업에는 [SetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync) 메서드를 사용합니다. 메서드의 첫 번째 매개 변수는 비밀 이름(이 샘플의 경우 "mySecret")을 허용합니다.

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> 비밀 이름이 있는 경우 위의 코드에서 해당 비밀의 새 버전을 만듭니다.


### <a name="retrieve-a-secret"></a>비밀 검색

이제 [GetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync) 메서드를 사용하여 이전에 설정한 값을 검색할 수 있습니다.

```csharp
var secret = await client.GetSecretAsync(secretName);
```

이제 비밀이 `secret.Value`로 저장됩니다.

### <a name="delete-a-secret"></a>비밀 삭제

마지막으로, [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) 및 [PurgeDeletedSecretAsync](/dotnet/api/azure.security.keyvault.keys.keyclient) 메서드를 사용하여 키 자격 증명 모음에서 비밀을 삭제해 보겠습니다.

```csharp
var operation = await client.StartDeleteSecretAsync("mySecret");
// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

await client.PurgeDeletedKeyAsync("mySecret");
```

## <a name="sample-code"></a>예제 코드

다음 단계를 완료하여 Key Vault와 상호 작용하도록 .NET Core 콘솔 앱을 수정합니다.

1. *Program.cs* 의 코드를 다음 코드로 바꿉니다.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>테스트 및 확인

1. 다음 명령을 실행하여 앱을 실행합니다.

    ```dotnetcli
    dotnet run
    ```

1. 메시지가 표시되면 비밀 값을 입력합니다. 예를 들어 mySecretPassword입니다.

다음과 유사한 출력이 표시됩니다.

```console
Input the value of your secret > mySecretPassword
Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
Forgetting your secret.
Your secret is ''.
Retrieving your secret from <your-unique-keyvault-name>.
Your secret is 'mySecretPassword'.
Deleting your secret from <your-unique-keyvault-name> ... done.    
Purging your secret from <your-unique-keyvault-name> ... done.
```

## <a name="next-steps"></a>다음 단계

Key Vault 및 이를 앱과 통합하는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure Key Vault 개요](../general/overview.md) 참조
- [App Service 애플리케이션에서 Key Vault 액세스 자습서](../general/tutorial-net-create-vault-azure-web-app.md) 참조
- [Virtual Machine에서 Key Vault 액세스 자습서](../general/tutorial-net-virtual-machine.md) 참조
- [Azure Key Vault 개발자 가이드](../general/developers-guide.md) 참조
- [Azure Key Vault 보안 개요](../general/security-features.md)를 검토하세요.
