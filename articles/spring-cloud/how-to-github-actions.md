---
title: GitHub Actions를 사용하는 Azure Spring Cloud CI/CD
description: GitHub Actions를 사용하여 Azure Spring Cloud용 CI/CD 워크플로를 빌드하는 방법
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2cdbec553909b354cc85e15d2b33c731c67e12b7
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529583"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>GitHub Actions를 사용하는 Azure Spring Cloud CI/CD

GitHub Actions는 자동화된 소프트웨어 개발 수명 주기 워크플로를 지원합니다. Azure Spring Cloud용 GitHub Actions를 사용하여 Azure에 빌드, 테스트하고 패키징, 릴리스 및 배포하기 위한 리포지토리에 워크플로를 만들 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 예제에는 [Azure CLI](/cli/azure/install-azure-cli)가 필요합니다.

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>GitHub 리포지토리 설정 및 인증

Azure 로그인 작업에 권한을 부여하려면 Azure 서비스 사용자 자격 증명이 필요 합니다. Azure 자격 증명을 가져오려면 로컬 컴퓨터에서 다음 명령을 실행합니다.

```azurecli
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

특정 리소스 그룹에 액세스하기 위해 범위를 줄일 수 있습니다.

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

명령은 JSON 개체를 출력해야 합니다.

```json
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

이 예제에서는 [GitHub의 steeltoe 샘플](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample)을 사용합니다.  리포지토리를 포크하고, 포크에 대한 GitHub 리포지토리 페이지를 연 다음 **설정** 탭을 선택합니다. **비밀** 메뉴를 열고 **새 비밀** 을 선택합니다.

![새 비밀 추가](./media/github-actions/actions1.png)

비밀 이름을 `AZURE_CREDENTIALS`(으)로 설정하고 해당 값을 *GitHub 리포지토리 설정 및 인증* 이라는 제목 아래에서 찾을 수 있는 JSON 문자열로 설정합니다.

![비밀 데이터 설정](./media/github-actions/actions2.png)

[GitHub Actions에서 Key Vault를 사용하여 Azure Spring 인증](./github-actions-key-vault.md)에 설명된 대로 GitHub Actions의 Key Vault에서 Azure 로그인 자격 증명도 가져올 수 있습니다.

## <a name="provision-service-instance"></a>서비스 인스턴스 프로비저닝

Azure Spring Cloud 서비스 인스턴스를 프로비저닝하려면 Azure CLI를 사용하 여 다음 명령을 실행합니다.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>워크플로 빌드

워크플로는 다음 옵션을 사용하여 정의됩니다.

### <a name="prepare-for-deployment-with-azure-cli"></a>Azure CLI를 사용하여 배포 준비

`az spring-cloud app create` 명령이 현재 idempotent가 아닙니다. 한 번 실행한 후 동일한 명령을 다시 실행하면 오류가 발생합니다. 기존 Azure Spring Cloud 앱 및 인스턴스에서 이 워크플로를 권장합니다.

준비 시 다음 Azure CLI 명령을 사용합니다.

```azurecli
az config set defaults.group=<service group name>
az config set defaults.spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Azure CLI를 사용하여 직접 배포

다음 콘텐츠를 사용하여 리포지토리에 *.github/workflows/main.yml* 파일을 만듭니다. *\<your resource group name>* 및 *\<your service name>* 을 올바른 값으로 바꿉니다.

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>

    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}

      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes

      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```

::: zone-end

::: zone pivot="programming-language-java"

## <a name="set-up-github-repository-and-authenticate"></a>GitHub 리포지토리 설정 및 인증

Azure 로그인 작업에 권한을 부여하려면 Azure 서비스 사용자 자격 증명이 필요 합니다. Azure 자격 증명을 가져오려면 로컬 컴퓨터에서 다음 명령을 실행합니다.

```azurecli
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

특정 리소스 그룹에 액세스하기 위해 범위를 줄일 수 있습니다.

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

명령은 JSON 개체를 출력해야 합니다.

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

이 예제에서는 GitHub의 [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) 샘플을 사용합니다.  샘플을 포크하고, GitHub 리포지토리 페이지를 열고, **설정** 탭을 선택합니다. **비밀** 메뉴를 열고 **새 비밀 추가** 를 선택합니다.

![새 비밀 추가](./media/github-actions/actions1.png)

비밀 이름을 `AZURE_CREDENTIALS`(으)로 설정하고 해당 값을 *GitHub 리포지토리 설정 및 인증* 이라는 제목 아래에서 찾을 수 있는 JSON 문자열로 설정합니다.

![비밀 데이터 설정](./media/github-actions/actions2.png)

[GitHub Actions에서 Key Vault를 사용하여 Azure Spring 인증](./github-actions-key-vault.md)에 설명된 대로 GitHub Actions의 Key Vault에서 Azure 로그인 자격 증명도 가져올 수 있습니다.

## <a name="provision-service-instance"></a>서비스 인스턴스 프로비저닝

Azure Spring Cloud 서비스 인스턴스를 프로비저닝하려면 Azure CLI를 사용하 여 다음 명령을 실행합니다.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```

## <a name="build-the-workflow"></a>워크플로 빌드

워크플로는 다음 옵션을 사용하여 정의됩니다.

### <a name="prepare-for-deployment-with-azure-cli"></a>Azure CLI를 사용하여 배포 준비
`az spring-cloud app create` 명령이 현재 idempotent가 아닙니다.  기존 Azure Spring Cloud 앱 및 인스턴스에서 이 워크플로를 권장합니다.

준비 시 다음 Azure CLI 명령을 사용합니다.

```azurecli
az config set defaults.group=<service group name>
az config set defaults.spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Azure CLI를 사용하여 직접 배포

리포지토리에서 *.github/workflow/main.yml* 파일을 만듭니다.

```yaml
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - uses: actions/checkout@main

    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8

    - name: maven build, clean
      run: |
        mvn clean package -DskipTests

    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud

    - name: Deploy with AZ CLI commands
      run: |
        az config set defaults.group=$GROUP
        az config set defaults.spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```

### <a name="deploy-with-azure-cli-action"></a>Azure CLI 작업을 사용하여 배포

az `run` 명령은 최신 버전의 Azure CLI를 사용합니다. 호환성이 손상되는 변경이 있는 경우 azure/CLI `action`을(를) 사용하여 특정 버전의 Azure CLI도 사용할 수 있습니다.

> [!Note]
> 이 명령은 새 컨테이너에서 실행되므로, `env`이(가) 작동하지 않고 상호 작업 파일 액세스에 추가 제한이 있을 수 있습니다.

리포지토리에서 *.github/workflow/main.yml* 파일을 만듭니다.

```yaml
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - uses: actions/checkout@main

    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8

    - name: maven build, clean
      run: |
        mvn clean package -DskipTests

    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az config set defaults.group=<service group name>
          az config set defaults.spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Maven 플러그인을 사용하여 배포

또 다른 옵션은 [Maven 플러그인](./quickstart.md)을 사용하여 Jar를 배포하고 앱 설정을 업데이트하는 것입니다. `mvn azure-spring-cloud:deploy` 명령은 idempotent이며 필요한 경우 앱을 자동으로 만듭니다. 해당 앱을 미리 만들 필요는 없습니다.

```yaml
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - uses: actions/checkout@main

    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8

    - name: maven build, clean
      run: |
        mvn clean package -DskipTests

    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>워크플로 실행

GitHub **작업** 은 *.github/workflow/main.yml* 을 GitHub에 푸시한 후 자동으로 사용하도록 설정해야 합니다. 새 커밋을 푸시하면 작업이 트리거됩니다. 브라우저에서 이 파일을 만드는 경우 작업이 이미 실행되어 있어야 합니다.

작업이 사용하도록 설정되었는지 확인하려면 GitHub 리포지토리 페이지에서 **작업** 탭을 선택합니다.

![작업이 사용하도록 설정되었는지 확인](./media/github-actions/actions3.png)

작업에서 오류가 발생한 경우(예: Azure 자격 증명을 설정하지 않은 경우) 오류를 수정하면 검사를 다시 시작할 수 있습니다. GitHub 리포지토리 페이지에서 **작업** 을 선택하고 특정 워크플로 작업을 선택한 다음, **검사 다시 실행** 단추를 선택하여 검사를 다시 실행합니다.

![검사 다시 실행](./media/github-actions/actions4.png)

## <a name="next-steps"></a>다음 단계

* [Spring Cloud GitHub Actions용 Key Vault](./github-actions-key-vault.md)
* [Azure Active Directory 서비스 사용자](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)
* [Azure에 대한 GitHub 작업](https://github.com/Azure/actions/)
