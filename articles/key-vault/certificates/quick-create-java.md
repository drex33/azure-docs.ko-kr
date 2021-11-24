---
title: Azure Key Vault 인증서 클라이언트 라이브러리 빠른 시작 - Java
description: 이 빠른 시작의 단계를 사용하여 Java용 Azure Key Vault 인증서 클라이언트 라이브러리에 대해 알아보세요.
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli, devx-track-azurepowershell, mode-other
ms.author: mbaldwin
ms.date: 12/18/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: 62ff6517797b89b213c37d00064985d6b03d16dd
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133038882"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-java-certificates"></a>빠른 시작: Java용 Azure Key Vault 인증서 클라이언트 라이브러리(인증서)
Java용 Azure Key Vault 인증서 클라이언트 라이브러리를 시작합니다. 아래 단계에 따라 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해 봅니다.

추가 리소스:

* [소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)
* [API 참조 설명서](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [제품 설명서](index.yml)
* [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates/src/samples/java/com/azure/security/keyvault/certificates)

## <a name="prerequisites"></a>필수 구성 요소
- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [JDK(Java Development Kit)](/java/azure/jdk/), 버전 8 이상
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

이 빠른 시작에서는 Linux 터미널 창에서 [Azure CLI](/cli/azure/install-azure-cli) 및 [Apache Maven](https://maven.apache.org)을 실행하고 있다고 가정합니다.

## <a name="setting-up"></a>설치
이 빠른 시작에서는 Azure CLI와 함께 Azure ID 라이브러리를 사용하여 사용자를 Azure Services에 인증합니다. 개발자는 Visual Studio 또는 Visual Studio Code를 사용하여 해당 호출을 인증할 수도 있습니다. 자세한 내용은 [Azure ID 클라이언트 라이브러리를 사용하여 클라이언트 인증](/java/api/overview/azure/identity-readme)을 참조하세요.

### <a name="sign-in-to-azure"></a>Azure에 로그인
1. `login` 명령을 실행합니다.

    ```azurecli-interactive
    az login
    ```

   CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다.

   그렇지 않으면 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)에서 브라우저 페이지를 열고 터미널에 표시된 권한 부여 코드를 입력합니다.

2. 브라우저에서 계정 자격 증명으로 로그인합니다.

### <a name="create-a-new-java-console-app"></a>새 Java 콘솔 앱 만들기
콘솔 창에서 `mvn` 명령을 사용하여 `akv-certificates-java`이라는 새 Java 콘솔 앱을 만듭니다.

```console
mvn archetype:generate -DgroupId=com.keyvault.certificates.quickstart
                       -DartifactId=akv-certificates-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

프로젝트를 생성하는 출력은 다음과 같습니다.

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-certificates-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

새로 만든 `akv-certificates-java/` 폴더로 디렉터리를 변경합니다.

```console
cd akv-certificates-java
```

### <a name="install-the-package"></a>패키지 설치
텍스트 편집기에서 *pom.xml* 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-certificates</artifactId>
      <version>4.1.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>리소스 그룹 및 키 자격 증명 모음 만들기
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한 부여
인증서 권한을 사용자 계정에 부여하는 키 자격 증명 모음에 대한 액세스 정책을 만듭니다.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
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
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>개체 모델
Java용 Azure Key Vault 인증서 클라이언트 라이브러리를 사용하면 인증서를 관리할 수 있습니다. [코드 예제](#code-examples) 섹션에서는 클라이언트를 만들고, 인증서를 만들고, 인증서를 검색하고, 인증서를 삭제하는 방법을 보여 줍니다.

전체 콘솔 앱은 [아래에](#sample-code) 있습니다.

## <a name="code-examples"></a>코드 예제
### <a name="add-directives"></a>지시문 추가
다음 지시문을 코드 위쪽에 추가합니다.

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;
```

### <a name="authenticate-and-create-a-client"></a>클라이언트 인증 및 만들기
이 빠른 시작에서 로그인한 사용자는 로컬 개발에서 권장되는 방법인 Key Vault에 인증하는 데 사용됩니다. Azure에 배포된 애플리케이션의 경우 관리 ID를 App Service 또는 Virtual Machine에 할당해야 합니다. 자세한 내용은 [관리 ID 개요](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

아래 예제에서 키 자격 증명 모음 이름은 "https://\<your-key-vault-name\>.vault.azure.net" 형식의 키 자격 증명 모음 URI로 확장됩니다. 이 예제에서는 ID를 제공하는 다양한 옵션이 있는 서로 다른 환경에서 동일한 코드를 사용할 수 있도록 하는 ['DefaultAzureCredential()'](/java/api/com.azure.identity.defaultazurecredential) 클래스를 사용합니다. 자세한 내용은 [기본 Azure 자격 증명 인증](/java/api/overview/azure/identity-readme)을 참조하세요.

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

CertificateClient certificateClient = new CertificateClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>비밀 저장
이제 애플리케이션이 인증되었으므로 `certificateClient.beginCreateCertificate` 메서드를 사용하여 키 자격 증명 모음에 인증서를 만들 수 있습니다. 이를 위해서는 인증서 이름과 인증서 정책이 필요합니다. 이 샘플에서는 `certificateName` 변수에 "myCertificate" 값을 할당하고 기본 정책을 사용합니다.

인증서 만들기는 장기 실행 작업으로, 진행률을 폴링하거나 완료될 때까지 기다릴 수 있습니다.

```java
SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
    certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
certificatePoller.waitForCompletion();
```

만들기가 완료되면 다음 호출을 통해 인증서를 가져올 수 있습니다.

```java
KeyVaultCertificate createdCertificate = certificatePoller.getFinalResult();
```

### <a name="retrieve-a-certificate"></a>인증서 검색
이제 `certificateClient.getCertificate` 메서드를 사용하여 이전에 만든 인증서를 검색할 수 있습니다.

```java
KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);
 ```

이제 `retrievedCertificate.getName`, `retrievedCertificate.getProperties` 등과 같은 작업으로 검색된 인증서의 세부 정보에 액세스할 수 있습니다. `retrievedCertificate.getCer`로 인증서 내용에도 액세스할 수 있습니다.

### <a name="delete-a-certificate"></a>인증서 삭제
마지막으로, 역시 장기 실행 작업인 `certificateClient.beginDeleteCertificate` 메서드를 사용하여 키 자격 증명 모음에서 인증서를 삭제해 보겠습니다.

```java
SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
deletionPoller.waitForCompletion();
```

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 Azure CLI 또는 Azure PowerShell을 사용하여 키 자격 증명 모음 및 해당 리소스 그룹을 제거할 수 있습니다.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>예제 코드
```java
package com.keyvault.certificates.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, keyVaultUri);

        CertificateClient certificateClient = new CertificateClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String certificateName = "myCertificate";

        System.out.print("Creating a certificate in " + keyVaultName + " called '" + certificateName + " ... ");

        SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
                certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
        certificatePoller.waitForCompletion();

        System.out.print("done.");
        System.out.println("Retrieving certificate from " + keyVaultName + ".");

        KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);

        System.out.println("Your certificate's ID is '" + retrievedCertificate.getId() + "'.");
        System.out.println("Deleting your certificate from " + keyVaultName + " ... ");

        SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 키 자격 증명 모음을 만들고, 인증서를 만든 후 해당 인증서를 삭제했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Azure Key Vault 개발자 가이드](../general/developers-guide.md) 참조
- [키 자격 증명 모음에 대한 액세스를 보호](../general/security-features.md)하는 방법
