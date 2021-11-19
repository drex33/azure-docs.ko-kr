---
title: '자습서: Key Vault를 연결하기 위한 관리 ID'
description: 관리 ID를 설정하여 Key Vault를 Azure Spring Cloud 앱에 연결
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 875763dbbc3beb39133f04579d8c46a9e0343f3a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492514"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>자습서: 관리 ID를 사용하여 Key Vault를 Azure Spring Cloud 앱에 연결

**이 문서는 다음에 적용됩니다.** ✔️ Java

이 문서에서는 Azure Spring Cloud 앱에 대한 관리 ID를 만들고, 이를 사용하여 Azure Key Vault에 액세스하는 방법을 보여 줍니다.

Azure Key Vault는 앱의 토큰, 암호, 인증서, API 키 및 기타 비밀에 대한 액세스를 안전하게 저장하고 긴밀하게 제어하는 데 사용할 수 있습니다. AAD(Azure Active Directory)에서 관리 ID를 만들고, 코드에 자격 증명을 표시하지 않고도 Key Vault를 포함하여 AAD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

다음 동영상에서는 Azure Key Vault를 사용하여 비밀을 관리하는 방법을 설명합니다.

<br>

> [!VIDEO https://www.youtube.com/embed/A8YQOoZncu8?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

## <a name="prerequisites"></a>필수 구성 요소

* [Azure 구독에 가입](https://azure.microsoft.com/free/)
* [Azure CLI 버전 2.0.67 이상 설치](/cli/azure/install-azure-cli)
* [Maven 3.0 이상 설치](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 Key Vault 및 Spring Cloud를 모두 포함하는 리소스 그룹을 만듭니다.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Key Vault 설정

Key Vault를 만들려면 [az keyvault create](/cli/azure/keyvault#az_keyvault_create) 명령을 사용합니다.

> [!Important]
> 각 Key Vault마다 고유한 이름이 있어야 합니다. 다음 예제에서 *\<your-keyvault-name>* 을 Key Vault의 이름으로 바꿉니다.

```azurecli
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

반환된 `vaultUri`을 기록해 둡니다. 형식은 `https://<your-keyvault-name>.vault.azure.net`입니다. 다음 단계에서 사용됩니다.

이제 [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) 명령을 사용하여 비밀을 Key Vault에 배치할 수 있습니다.

```azurecli
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Azure Spring Cloud 서비스 및 앱 만들기

해당 확장이 설치되면 `az spring-cloud create` Azure CLI 명령을 사용하여 Azure Spring Cloud 인스턴스를 만듭니다.

```azurecli
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```

다음 예제에서는 `--assign-identity` 매개 변수에서 요청한 대로 시스템이 할당한 관리 ID를 사용하여 `springapp`이라는 앱을 만듭니다.

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --assign-endpoint true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

반환된 `url`을 기록해 둡니다. 형식은 `https://<your-app-name>.azuremicroservices.io`입니다. 다음 단계에서 사용됩니다.

## <a name="grant-your-app-access-to-key-vault"></a>Key Vault에 앱 액세스 권한 부여

`az keyvault set-policy`를 사용하여 앱에 대한 적절한 액세스 권한을 Key Vault에 부여합니다.

```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```

> [!NOTE]
> 시스템 할당 관리 ID를 사용하지 않도록 설정한 후 `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}`를 사용하여 앱에 대한 액세스를 제거합니다.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Spring Boot 스타터를 사용하여 Spring Boot 앱 샘플 빌드

이 앱에는 Azure Key Vault에서 비밀을 가져올 수 있는 액세스 권한이 있습니다. 스타터 앱([Azure Key Vault 비밀 Spring Boot 스타터](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets))을 사용합니다.  Azure Key Vault는 Spring **PropertySource** 의 인스턴스로 추가됩니다.  Azure Key Vault에 저장된 비밀은 외부화된 구성 속성(예: 파일의 속성)과 같이 편리하게 액세스하고 사용할 수 있습니다. 

1. Azure Key Vault Spring 스타터를 사용하여 start.spring.io에서 프로젝트 샘플을 생성합니다. 

    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. 앱에서 Key Vault를 지정합니다.

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    관리 ID를 Azure Spring Cloud 앱에 사용하려면 아래 내용이 포함된 속성을 src/main/resources/application.properties에 추가합니다.

    ```properties
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

    > [!Note]
    > 위와 같이 키 자격 증명 모음 URL을 `application.properties`에 추가해야 합니다. 그렇지 않으면 런타임 중에 키 자격 증명 모음 URL이 캡처되지 않을 수 있습니다.

3. 코드 예제를 src/main/java/com/example/demo/DemoApplication.java에 추가합니다. 이는 Key Vault에서 연결 문자열을 검색합니다.

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    pom.xml을 열면 `azure-keyvault-secrets-spring-boot-starter`의 종속성이 표시됩니다. 이 종속성을 pom.xml의 프로젝트에 추가합니다.

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. 앱 샘플을 패키지합니다.

    ```azurecli
    mvn clean package
    ```

5. 이제 `az spring-cloud app deploy` Azure CLI 명령을 사용하여 앱을 Azure에 배포할 수 있습니다.

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6. 앱을 테스트하려면 퍼블릭 엔드포인트 또는 테스트 엔드포인트에 액세스합니다.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    `Successfully got the value of secret connectionString from Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;` 메시지가 표시됩니다.

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Java SDK를 사용하여 Spring Boot 앱 샘플 빌드

이 샘플은 Azure Key Vault에서 비밀을 설정하고 가져올 수 있습니다. [Java용 Azure Key Vault 비밀 클라이언트 라이브러리](/java/api/overview/azure/security-keyvault-secrets-readme)는 Azure SDK에서 Azure Active Directory 토큰 인증을 지원합니다. AAD 토큰 인증을 지원하도록 Azure SDK 클라이언트를 구성하는 데 사용할 수 있는 `TokenCredential` 구현 세트를 제공합니다.

Azure Key Vault 비밀 클라이언트 라이브러리를 사용하면 토큰, 암호, API 키 및 기타 비밀에 대한 액세스를 안전하게 저장하고 제어할 수 있습니다. 라이브러리는 비밀 및 해당 버전을 생성, 검색, 업데이트, 삭제, 제거, 백업, 복원 및 나열하는 작업을 제공합니다.

1. 프로젝트 샘플을 복제합니다.

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. 앱에서 Key Vault를 지정합니다.

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    관리 ID를 Azure Spring Cloud 앱에 사용하려면 다음 내용이 포함된 속성을 *src/main/resources/application.properties* 에 추가합니다.

    ```properties
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. 코드에서 [ManagedIdentityCredentialBuilder](/java/api/com.azure.identity.managedidentitycredentialbuilder)를 포함하여 Azure Active Directory에서 토큰을 가져오고, [SecretClientBuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder)를 포함하여 Key Vault에서 비밀을 설정하거나 가져옵니다.

    [MainController.java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28)에서 복제된 프로젝트 샘플의 예제를 가져옵니다.

    또한 pom.xml에서 `azure-identity` 및 `azure-security-keyvault-secrets`를 종속성으로 포함합니다. [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21)에서 복제된 프로젝트 샘플의 예제를 가져옵니다.

4. 앱 샘플을 패키지합니다.

    ```azurecli
    mvn clean package
    ```

5. 이제 `az spring-cloud app deploy` Azure CLI 명령을 사용하여 앱을 Azure에 배포합니다.

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. 퍼블릭 엔드포인트 또는 테스트 엔드포인트에 액세스하여 앱을 테스트합니다.

    먼저, Azure Key Vault에 설정한 비밀 값을 가져옵니다.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    `Successfully got the value of secret connectionString from Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;` 메시지가 표시됩니다.

    이제 비밀을 만든 다음, Java SDK를 사용하여 해당 비밀을 검색합니다.

    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    `Successfully got the value of secret test from Key Vault https://<your-keyvault-name>.vault.azure.net: success` 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Spring Cloud에서 관리 ID를 사용하여 스토리지 Blob에 액세스하는 방법](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [시스템이 할당한 관리 ID를 Azure Spring Cloud의 애플리케이션에 사용하도록 설정하는 방법](./how-to-enable-system-assigned-managed-identity.md)
* [Azure 리소스의 관리 ID에 대해 자세히 알아보기](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [GitHub Actions에서 Key Vault를 사용하여 Azure Spring Cloud 인증](./github-actions-key-vault.md)
