---
title: 빠른 시작 - Azure Spring Cloud 서비스 프로비전
description: 앱 배포를 위한 Azure Spring Cloud 서비스 인스턴스를 만드는 방법을 설명합니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/12/2021
ms.custom: devx-track-java, devx-track-azurecli, mode-other
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 561bd2e6742496fd821c3204b1ac02c3181c792c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133077352"
---
# <a name="quickstart-provision-an-azure-spring-cloud-service"></a>빠른 시작: Azure Spring Cloud 서비스 프로비저닝

::: zone pivot="programming-language-csharp"
이 빠른 시작에서는 Azure CLI를 사용하여 Azure Spring Cloud 서비스의 인스턴스를 프로비저닝합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Azure Spring Cloud 서비스는 .NET Core 3.1 이상 버전을 지원합니다.
* [Azure CLI 버전 2.0.67 이상](/cli/azure/install-azure-cli).
* [Git](https://git-scm.com/)

## <a name="install-azure-cli-extension"></a>Azure CLI 확장 설치

Azure CLI 버전이 2.0.67 이상인지 확인합니다.

```azurecli
az --version
```

다음 명령을 사용하여 Azure CLI용 Azure Spring Cloud 확장을 설치합니다.

```azurecli
az extension add --name spring-cloud
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

1. Azure CLI에 로그인합니다.

    ```azurecli
    az login
    ```

1. 구독이 둘 이상 있는 경우 이 빠른 시작에 사용할 구독을 선택합니다.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Azure Spring Cloud의 인스턴스 프로비저닝

1. Azure Spring Cloud 서비스를 포함할 [리소스 그룹](../azure-resource-manager/management/overview.md)을 만듭니다. 리소스 그룹 이름에는 영숫자, 밑줄, 괄호, 하이픈, 마침표(맨 끝 제외) 및 유니코드 문자가 포함될 수 있습니다.

   ```azurecli
   az group create --location eastus --name <resource group name>
   ```

1. Azure Spring Cloud 서비스의 인스턴스를 프로비저닝합니다. 서비스 인스턴스 이름은 4~32자로 고유해야 하며 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있습니다. 서비스 이름의 첫 글자는 문자여야 하며 마지막 문자는 문자 또는 숫자여야 합니다.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

1. 후속 명령에서 이러한 값을 반복적으로 지정할 필요가 없도록 기본 리소스 그룹 이름과 서비스 인스턴스 이름을 설정합니다.

   ```azurecli
   az config set defaults.group=<resource group name>
   ```

   ```azurecli
   az config set defaults.spring-cloud=<service instance name>
   ```

::: zone-end

::: zone pivot="programming-language-java"
Azure Portal 또는 Azure CLI를 사용하여 Azure Spring Cloud 서비스의 인스턴스를 프로비전할 수 있습니다.  두 방법 모두 다음 절차에 설명되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [JDK 8 또는 JDK 11 설치](/azure/developer/java/fundamentals/java-jdk-install)
* [Azure 구독에 가입](https://azure.microsoft.com/free/)
* (선택 사항) [Azure CLI 버전 2.0.67 이상을 설치](/cli/azure/install-azure-cli)하고 `az extension add --name spring-cloud` 명령을 사용하여 Azure Spring Cloud 확장을 설치합니다.
* (선택 사항) [Azure Toolkit for IntelliJ IDEA를 설치](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/)하고 [로그인](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)합니다.

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Azure Spring Cloud의 인스턴스 프로비저닝

#### <a name="portal"></a>[포털](#tab/Azure-portal)

다음 절차에서는 Azure Portal을 사용하여 Azure Spring Cloud의 인스턴스를 만듭니다.

1. 새 탭에서 [Azure Portal](https://ms.portal.azure.com/)을 엽니다.

2. 상단 검색 상자에서 **Azure Spring Cloud** 를 검색합니다.

3. 결과에서 **Azure Spring Cloud** 를 선택합니다.

    ![ASC 아이콘 시작](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Azure Spring Cloud 페이지에서 **만들기** 를 선택합니다.

    ![ASC 아이콘 추가](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-create.png)

5. Azure Spring Cloud **만들기** 페이지에 있는 양식을 채웁니다.  다음 지침을 고려하세요.

    - **구독**: 이 리소스 대한 요금이 청구될 구독을 선택합니다.
    - **리소스 그룹**: 새 리소스에 대한 리소스 그룹을 새로 만드는 것이 가장 좋습니다. 이후 단계에서 이 값을 **\<resource group name\>** 로 사용합니다.
    - **서비스 세부 정보/이름**: **\<service instance name\>** 를 지정합니다.  이름은 4-32자 사이여야 하며, 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있습니다.  서비스 이름의 첫 글자는 문자여야 하며 마지막 문자는 문자 또는 숫자여야 합니다.
    - **위치**: 서비스 인스턴스에 대한 위치를 선택합니다.
    - **가격 책정 계층** 옵션에 대해 **표준** 을 선택합니다.

    ![ASC 포털 시작](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. **검토 및 만들기** 를 선택합니다.

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

다음 절차에서는 Azure CLI 확장을 사용하여 Azure Spring Cloud의 인스턴스를 프로비저닝합니다.

1. Azure Spring Cloud 확장을 사용하여 Azure CLI를 업데이트합니다.

    ```azurecli
    az extension update --name spring-cloud
    ```

1. Azure CLI에 로그인하고 활성 구독을 선택합니다.

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Azure Spring Cloud 서비스의 이름을 준비합니다.  이름은 4-32자 사이여야 하며, 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있습니다.  서비스 이름의 첫 글자는 문자여야 하며 마지막 문자는 문자 또는 숫자여야 합니다.

1. Azure Spring Cloud 서비스를 포함할 리소스 그룹을 만듭니다.  Azure Spring Cloud 서비스의 인스턴스에서 만듭니다.

    ```azurecli
    az group create --name <resource group name>
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)에 대해 자세히 알아봅니다.

1. 다음 명령을 사용하여 기본 리소스 그룹 이름과 Spring Cloud 서비스 이름을 설정합니다.

    ```azurecli
    az config set defaults.group=<resource group name> defaults.spring-cloud=<service name>
    ```

---
::: zone-end

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 구독에 남아있는 경우 요금이 계속 청구되는 Azure 리소스를 만들었습니다. 다음 빠른 시작을 계속 진행하지 않으려는 경우 [리소스 정리](./quickstart-logs-metrics-tracing.md#clean-up-resources)를 참조하세요. 그렇지 않으면 다음 빠른 시작으로 이동하세요.

> [!div class="nextstepaction"]
> [구성 서버 설정](./quickstart-setup-config-server.md)
