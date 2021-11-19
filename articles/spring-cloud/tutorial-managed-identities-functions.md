---
title: '자습서: Azure Functions를 호출하기 위한 관리 ID'
description: 관리 ID를 사용하여 Azure Spring Cloud 앱에서 Azure Functions 호출
author: karlerickson
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: e246fa6c20e506952001dff59d3a2f0a9eccc8d1
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491605"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>자습서: 관리 ID를 사용하여 Azure Spring Cloud 앱에서 Azure Functions 호출

이 문서에서는 Azure Spring Cloud 앱에 대한 관리 ID를 만들고, 이를 사용하여 Http 트리거 함수를 호출하는 방법을 보여 줍니다.

Azure Functions 및 App Services는 모두 Azure AD(Azure Active Directory) 인증을 기본적으로 지원합니다. Azure Spring Cloud용 관리 ID와 함께 이 기본 제공 인증 기능을 활용하면 최신 OAuth 의미 체계를 사용하여 RESTful 서비스를 호출할 수 있습니다. 이 방법을 사용하면 비밀을 코드에 저장할 필요가 없으며 외부 리소스에 대한 액세스를 더 세부적으로 제어할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure 구독에 가입](https://azure.microsoft.com/free/)
* [Azure CLI 버전 2.0.67 이상 설치](/cli/azure/install-azure-cli)
* [Maven 3.0 이상 설치](https://maven.apache.org/download.cgi)
* [Azure Functions Core Tools 버전 3.0.2009 이상 설치](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 함수 앱 및 Spring Cloud가 모두 포함되는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-function-app"></a>함수 앱 만들기

함수 앱을 만들려면 먼저 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 사용하여 지원하는 스토리지 계정을 만들어야 합니다.

> [!Important]
> 각 함수 앱 및 스토리지 계정에는 고유한 이름이 있어야 합니다. 다음 예에서 *\<your-functionapp-name>* 을 함수 앱의 이름으로 바꾸고 *\<your-storageaccount-name>* 을 스토리지 계정의 이름으로 바꿉니다.

```azurecli
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

스토리지 계정이 만들어지면 함수 앱을 만들 수 있습니다.

```azurecli
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

반환된 **hostNames** 를 적어 둡니다. 형식은 *https://\<your-functionapp-name>.azurewebsites.net* 입니다. 이는 다음 단계에서 사용됩니다.

## <a name="enable-azure-active-directory-authentication"></a>Azure Active Directory 인증 사용

[Azure Portal](https://portal.azure.com)에서 새로 만든 함수 앱에 액세스하고, 설정 메뉴에서 "인증/권한 부여"를 선택합니다. App Service 인증을 사용하도록 설정하고, "요청이 인증되지 않은 경우 수행할 작업"을 "Azure Active Directory로 로그인"으로 설정합니다. 이 설정을 사용하면 인증되지 않은 모든 요청이 거부됩니다(401 응답).

![Azure Active Directory를 기본 공급자로 보여 주는 인증 설정](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

[인증 공급자] 아래에서 Azure Active Directory를 선택하여 애플리케이션 등록을 구성합니다. [기본 관리 모드]를 선택하면 올바른 구성을 사용하여 애플리케이션 등록이 Azure AD 테넌트에 자동으로 만들어집니다.

![기본 관리 모드로 설정된 Azure Active Directory 공급자](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

설정을 저장하면 함수 앱이 다시 시작되고 모든 후속 요청에서 Azure AD를 통해 로그인하라는 메시지가 표시됩니다. 이제 함수 앱 루트 URL(위 단계에서 **hostNames** 출력으로 반환됨)로 이동하여 인증되지 않은 요청이 거부되는지 테스트할 수 있습니다. 조직 Azure AD 로그인 화면으로 리디렉션됩니다.

## <a name="create-an-http-triggered-function"></a>Http 트리거 함수 만들기

빈 로컬 디렉터리에서 새 함수 앱을 만들고, Http 트리거 함수를 추가합니다.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

기본적으로 Functions는 키 기반 인증을 사용하여 Http 엔드포인트를 보호합니다. Functions에 대한 액세스를 보호하기 위해 Azure AD 인증을 사용하도록 설정하므로 *function.json* 파일에서 [함수 인증 수준을 익명으로 설정](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production)하려고 합니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

이제 앱을 이전 단계에서 만든 [함수 앱](#create-a-function-app) 인스턴스에 게시할 수 있습니다.

```console
func azure functionapp publish <your-functionapp-name>
```

publish 명령의 출력에 새로 만든 함수의 URL이 나열됩니다.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Azure Spring Cloud 서비스 및 앱 만들기

spring-cloud 확장이 설치되면 `az spring-cloud create` Azure CLI 명령을 사용하여 Azure Spring Cloud 인스턴스를 만듭니다.

```azurecli
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

다음 예제에서는 `--assign-identity` 매개 변수에서 요청한 대로 시스템이 할당한 관리 ID를 사용하여 `msiapp`이라는 앱을 만듭니다.

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>함수를 호출하는 Spring Boot 앱 샘플 빌드

이 샘플은 먼저 [MSI 엔드포인트](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http)에 액세스 토큰을 요청하고 해당 토큰을 사용하여 함수 http 요청을 인증함으로써 Http 트리거 함수를 호출합니다.

1. 샘플 프로젝트를 복제합니다.

    ```bash
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. 앱 속성에서 함수 URI와 트리거 이름을 지정합니다.

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    관리 ID를 Azure Spring Cloud 앱에 사용하려면 다음 내용이 포함된 속성을 *src/main/resources/application.properties* 에 추가합니다.

    ```properties
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. 앱 샘플을 패키지합니다.

    ```bash
    mvn clean package
    ```

4. 이제 `az spring-cloud app deploy` Azure CLI 명령을 사용하여 앱을 Azure에 배포합니다.

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. 퍼블릭 엔드포인트 또는 테스트 엔드포인트에 액세스하여 앱을 테스트합니다.

    ```bash
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    응답 본문에 반환된 다음 메시지가 표시됩니다.

    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```

    경로 매개 변수를 변경하여 다른 값을 함수에 전달해 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [시스템이 할당한 관리 ID를 Azure Spring Cloud의 애플리케이션에 사용하도록 설정하는 방법](./how-to-enable-system-assigned-managed-identity.md)
* [Azure 리소스의 관리 ID에 대해 자세히 알아보기](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [App Service에 액세스하도록 클라이언트 앱 구성](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
