---
title: 비밀 및 환경 변수를 통해 부하 테스트 매개 변수화
titleSuffix: Azure Load Testing
description: Azure Load Testing에서 비밀 및 환경 변수를 매개 변수로 사용하여 구성 가능한 부하 테스트를 만드는 방법을 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: ninallam
author: ninallam
ms.date: 11/30/2021
ms.topic: how-to
ms.openlocfilehash: 534ec510dd9046ec63648e10d09532d60c141984
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133305601"
---
# <a name="make-configurable-load-tests-with-secrets-and-environment-variables"></a>비밀 및 환경 변수를 통해 구성 가능한 부하 테스트 만들기

Apache JMeter 스크립트를 수정하지 않고 부하 테스트의 동작을 변경하는 방법을 알아봅니다. Azure 부하 테스트 미리 보기를 사용하면 매개 변수를 사용하여 구성 가능한 테스트 스크립트를 만들 수 있습니다. 예를 들어 애플리케이션 엔드포인트를 매개 변수로 전환하여 여러 환경에서 테스트 스크립트를 다시 사용할 수 있습니다.

Azure Load Testing 서비스는 다음 두 가지 유형의 매개 변수를 지원합니다.

- 비밀: 중요한 정보를 포함하며 부하 테스트 엔진에 안전하게 전달됩니다. 예를 들어 테스트 스크립트에서 하드 코딩하는 대신 웹 서비스 자격 증명을 제공합니다. 자세한 내용은 [비밀을 통해 부하 테스트 구성을 참조하세요.](#secrets)

- 환경 변수: 중요하지 않은 정보를 포함하며 부하 테스트 엔진에서 환경 변수로 사용할 수 있습니다. 예를 들어 애플리케이션 엔드포인트 URL을 구성할 수 있도록 합니다. 자세한 내용은 [환경 변수를 통해 부하 테스트 구성을 참조하세요.](#envvars)

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소  

- 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.  

- Azure 부하 테스트 리소스. Azure Load Testing 리소스를 만들어야 하는 경우 빠른 시작 [부하 테스트 만들기 및 실행을](./quickstart-create-and-run-load-test.md)참조하세요.  

## <a name="configure-load-tests-with-secrets"></a><a name="secrets"></a> 비밀을 사용하는 부하 테스트 구성

이 섹션에서는 부하 테스트 스크립트에 비밀을 전달하도록 부하 테스트를 구성합니다.

1. 비밀 입력 매개 변수를 수락하고 사용하도록 Apache JMeter 스크립트를 업데이트합니다. 예를 들어 HTTP 헤더에 전달하는 웹 서비스 인증 토큰입니다.

1. 액세스를 엄격하게 제어할 수 있는 비밀 저장소에 비밀 값을 저장합니다. Azure Load Testing은 Azure Key Vault 또는 CI/CD 워크플로에 연결된 비밀 저장소와 통합됩니다.

1. 부하 테스트를 구성하고 비밀에 대한 참조를 테스트 스크립트에 전달합니다.

### <a name="use-secrets-in-apache-jmeter"></a>Apache JMeter에서 비밀 사용

이 섹션에서는 비밀을 입력 매개 변수로 사용하도록 Apache JMeter 스크립트를 업데이트합니다.

먼저 비밀 값을 검색하는 사용자 정의 변수를 정의합니다. 그런 다음 테스트 실행에서 이 변수를 사용하여 HTTP 요청 헤더를 설정할 수 있습니다.

1. JMX 파일에 사용자 정의 변수를 만들고 사용자 지정 함수를 사용하여 비밀 값을 `GetSecret` 할당합니다.

    `GetSecret(<my-secret-name>)`함수는 비밀 이름을 인수로 받습니다. 이후 단계에서 부하 테스트를 구성할 때 이 동일한 이름을 사용합니다.

    다음 스크린샷에서는 Apache JMeter IDE를 사용하여 사용자 정의 변수를 만드는 방법을 보여줍니다.

    :::image type="content" source="media/how-to-parameterize-load-tests/user-defined-variables.png" alt-text="Apache JMeter 스크립트에 사용자 정의 변수를 추가하는 방법을 보여 주는 스크린샷":::

    대신 다음 예제 코드 조각과 같이 JMX 파일을 직접 편집할 수도 있습니다.

    ```xml
    <Arguments guiclass="ArgumentsPanel" testclass="Arguments" testname="User Defined Variables" enabled="true">
      <collectionProp name="Arguments.arguments">
        <elementProp name="appToken" elementType="Argument">
          <stringProp name="Argument.name">udv_appToken</stringProp>
          <stringProp name="Argument.value">${__GetSecret(appToken)}</stringProp>
          <stringProp name="Argument.desc">Value for x-secret header </stringProp>
          <stringProp name="Argument.metadata">=</stringProp>
        </elementProp>
      </collectionProp>
    </Arguments>
    ```

1. 테스트 스크립트에서 사용자 정의 변수를 참조합니다.

    구문을 사용하여 `${}` 스크립트에서 변수를 참조할 수 있습니다. 다음 예제에서는 변수를 사용하여 `udv_appToken` HTTP 헤더를 설정합니다.

    ```xml
      <HeaderManager guiclass="HeaderPanel" testclass="HeaderManager" testname="HTTP Header Manager" enabled="true">
        <collectionProp name="HeaderManager.headers">
          <elementProp name="" elementType="Header">
            <stringProp name="Header.name">api-key</stringProp>
            <stringProp name="Header.value">${udv_appToken}</stringProp>
          </elementProp>
        </collectionProp>
      </HeaderManager>
    ```

### <a name="use-azure-key-vault"></a><a name="akv_secrets"></a> Azure Key Vault 사용

Azure Portal 부하 테스트를 만들거나 [YAML 테스트 구성 파일을](./reference-test-config-yaml.md)사용하는 경우 Azure Key Vault 비밀에 대한 참조를 사용합니다.

> [!NOTE]
> CI/CD 프로세스의 일부로 부하 테스트를 실행하는 경우 관련 비밀 저장소를 사용할 수도 있습니다. [CI/CD 비밀 저장소 사용으로](#cicd_secrets)건너뜁니다.

1. 아직 존재하지 않는 경우 [비밀을 Azure Key Vault 추가합니다.](/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)

1. 비밀에 대한 Key Vault 비밀 식별자를 검색합니다. 이 비밀 식별자를 사용하여 부하 테스트를 구성합니다.

    :::image type="content" source="media/how-to-parameterize-load-tests/key-vault-secret.png" alt-text="Azure Key Vault 비밀의 세부 정보를 보여 주는 스크린샷.":::
    
    비밀 식별자는 Azure Key Vault 비밀의 전체 URI입니다. 필요에 따라 버전 번호를 포함할 수도 있습니다. 예를 들어 `https://myvault.vault.azure.net/secrets/mysecret/` 또는 `https://myvault.vault.azure.net/secrets/mysecret/abcdef01-2345-6789-0abc-def012345678`로 이름을 지정할 수 있습니다.

1. Azure Load Testing 리소스에 Key Vault 대한 액세스 권한을 부여합니다.

    Azure Load Testing 리소스에는 Azure Key Vault 비밀을 검색할 수 있는 권한이 없습니다. 먼저 부하 테스트 리소스에 대해 시스템 할당 관리 ID를 사용하도록 설정합니다. 그런 다음, 이 관리 ID에 대한 읽기 권한을 부여합니다. 
    
    Key Vault 대한 Azure 부하 테스트 액세스를 제공하려면 [Azure 부하 테스트에 관리 ID 사용을](how-to-use-a-managed-identity.md)참조하세요.

1. 부하 테스트 구성에서 비밀을 참조합니다.
    
    Apache JMeter 스크립트에서 참조하는 각 비밀에 대해 부하 테스트 비밀 매개 변수를 정의합니다. 매개 변수 이름은 테스트 스크립트에서 사용한 이름과 일치해야 합니다. 비밀 매개 변수 값은 Key Vault 보안 식별자입니다.

    Azure Portal 또는 부하 테스트 YAML 구성 파일에서 비밀 매개 변수를 지정할 수 있습니다.

    Azure Portal 부하 테스트를 선택하고 **구성을** 선택한 다음 **매개** 변수 탭에 매개 변수 세부 정보를 입력합니다.
    
    :::image type="content" source="media/how-to-parameterize-load-tests/test-creation-secrets.png" alt-text="부하 테스트에 비밀 세부 정보를 추가하는 방법을 보여 주는 스크린샷":::

    대신 YAML 구성 파일에서 비밀을 지정할 수도 있습니다. 구문에 대한 자세한 내용은 테스트 [구성 YAML 참조 를 참조하세요.](./reference-test-config-yaml.md)

### <a name="use-the-cicd-secret-store"></a><a name="cicd_secrets"></a> CI/CD 비밀 저장소 사용

CI/CD 워크플로에서 Azure Load Testing을 사용하는 경우 연결된 비밀 저장소를 사용할 수도 있습니다. 예를 들어 Azure Pipelines [GitHub 리포지토리 비밀](https://docs.github.com/actions/security-guides/encrypted-secrets)또는 [비밀 변수를](/azure/devops/pipelines/process/variables?view=azure-devopsd&tabs=yaml%2Cbatch#secret-variables&preserve-view=true)사용할 수 있습니다.

> [!NOTE]
> 이미 Azure Key Vault 사용하는 경우 부하 테스트 비밀을 저장하는 데 사용할 수도 있습니다. Azure [KeyVault 사용으로](#akv_secrets)건너뜁니다.

1. 아직 존재하지 않는 경우 CI/CD 비밀 저장소에 비밀 값을 추가합니다.

    Azure Pipelines 파이프라인을 편집하고 [변수를 추가할](/azure/devops/pipelines/process/variables?view=azure-devopsd&tabs=yaml%2Cbatch#secret-variables&preserve-view=true)수 있습니다.

    :::image type="content" source="media/how-to-parameterize-load-tests/new-variable.png" alt-text="Azure Pipelines 변수를 추가하는 방법을 보여 주는 스크린샷":::

    GitHub [GitHub 리포지토리 비밀을](https://docs.github.com/actions/security-guides/encrypted-secrets)사용할 수 있습니다.

    :::image type="content" source="media/how-to-parameterize-load-tests/github-new-secret.png" alt-text="GitHub 리포지토리 비밀을 추가하는 방법을 보여 주는 스크린샷":::

    > [!NOTE]
    > Key Vault 비밀 식별자가 아닌 실제 비밀 값을 값으로 사용해야 합니다.

1. CI/CD 워크플로에서 부하 테스트 작업/작업에 대한 입력 매개 변수로 비밀을 전달합니다.
    
    다음 YAML 조각은 GitHub Actions 예제를 보여줍니다.

    ```yaml
    - name: 'Azure Load Testing'
      uses: azure/load-testing@v1
      with:
        loadtestConfigFile: 'SampleApp.yaml'
        loadtestResource: 'MyTest'
        resourceGroup: 'loadtests-rg'
        secrets: |
        [
            {
            "name": "appToken",
            "value": "${{ secrets.MY_SECRET }}",
            }
        ]
    ```

    다음 YAML 조각은 Azure Pipelines 예제를 보여줍니다.

    ```yml
    - task: AzureLoadTest@1
      inputs:
        azureSubscription: 'MyAzureLoadTestingRG'
        loadTestConfigFile: 'SampleApp.yaml'
        loadTestResource: 'MyTest'
        resourceGroup: 'loadtests-rg'
        secrets: |
          [
              {
              "name": "appToken",
              "value": "$(mySecret)",
              }
          ]
    ```

    > [!IMPORTANT]
    > 비밀 매개 변수의 이름은 Apache JMeter 스크립트에 사용된 이름과 일치해야 합니다.


## <a name="configure-load-tests-with-environment-variables"></a><a name="envvars"></a> 환경 변수를 사용하는 부하 테스트 구성

이 섹션에서는 환경 변수를 사용하여 부하 테스트에 매개 변수를 전달합니다.

1. 환경 변수를 사용하도록 Apache JMeter 스크립트를 업데이트합니다. 예를 들어 애플리케이션 엔드포인트 호스트 이름을 구성합니다.

1. 부하 테스트를 구성하고 환경 변수를 테스트 스크립트에 전달합니다.

### <a name="use-environment-variables-in-apache-jmeter"></a>Apache JMeter에서 환경 변수 사용

이 섹션에서는 환경 변수를 사용하여 스크립트 동작을 제어하도록 Apache JMeter 스크립트를 업데이트합니다.

먼저 환경 변수를 읽는 사용자 정의 변수를 정의합니다. 그런 다음, 테스트 실행에서 이 변수를 사용하여 HTTP 도메인을 업데이트할 수 있습니다.

1. JMX 파일에 사용자 정의 변수를 만들고 함수를 사용하여 환경 변수의 값을 `System.getenv` 할당합니다.

    `System.getenv("<my-variable-name>")`함수는 환경 변수 이름을 인수로 사용하며, 부하 테스트를 구성할 때 이 동일한 이름을 사용합니다.

    다음 스크린샷에서는 Apache JMeter IDE를 사용하여 사용자 정의 변수를 만드는 방법을 보여줍니다.

    :::image type="content" source="media/how-to-parameterize-load-tests/user-defined-variables-env.png" alt-text="JMeter 스크립트에 환경 변수에 대한 사용자 정의 변수를 추가하는 방법을 보여 주는 스크린샷":::

    대신 다음 예제 코드 조각과 같이 JMX 파일을 직접 편집할 수도 있습니다.

    ```xml
    <Arguments guiclass="ArgumentsPanel" testclass="Arguments" testname="User Defined Variables" enabled="true">
      <collectionProp name="Arguments.arguments">
        <elementProp name="appToken" elementType="Argument">
          <stringProp name="Argument.name">udv_webapp</stringProp>
          <stringProp name="Argument.value">${__BeanShell( System.getenv("webapp") )}</stringProp>
          <stringProp name="Argument.desc">Web app URL</stringProp>
          <stringProp name="Argument.metadata">=</stringProp>
        </elementProp>
      </collectionProp>
    </Arguments>
    ```

1. 테스트 스크립트에서 사용자 정의 변수를 참조합니다.

    구문을 사용하여 `${}` 스크립트에서 변수를 참조할 수 있습니다. 다음 예제에서는 변수를 사용하여 `udv_webapp` 애플리케이션 엔드포인트 URL을 구성합니다.

    ```xml
    <stringProp name="HTTPSampler.domain">${udv_webapp}</stringProp>
    ```

### <a name="configure-environment-variables-in-azure-load-testing"></a>Azure Load Testing에서 환경 변수 구성

환경 변수를 Apache JMeter 스크립트에 전달하려면 Azure Portal, YAML 테스트 구성 파일 또는 CI/CD 워크플로에서 직접 부하 테스트를 구성할 수 있습니다.

> [!IMPORTANT]
> 부하 테스트에 대한 환경 변수를 정의할 때 해당 이름은 Apache JMeter 스크립트에서 사용한 변수 이름과 일치해야 합니다.

Azure Portal 사용하여 부하 테스트에 환경 변수를 지정하려면 다음 단계를 수행합니다.

1. 테스트 구성 페이지에서 **매개 변수** 탭으로 이동합니다.

1. 환경 **변수** 섹션에서 환경 변수 **이름** 및 **값** 을 입력한 **다음, 적용을** 선택합니다.
    
    :::image type="content" source="media/how-to-parameterize-load-tests/test-creation-env.png" alt-text="부하 테스트에 환경 변수를 추가하는 방법을 보여 주는 스크린샷":::
    
CI/CD 워크플로에서 부하 테스트를 실행하는 경우 YAML 테스트 구성 파일에서 환경 변수를 정의할 수 있습니다. 구문에 대한 자세한 내용은 테스트 [구성 YAML 참조 를 참조하세요.](./reference-test-config-yaml.md)

또는 CI/CD 워크플로 정의에서 환경 변수를 직접 지정할 수도 있습니다. GitHub 작업 또는 Azure Pipelines 작업에 대한 입력 매개 변수를 사용하여 환경 변수를 Apache JMeter 스크립트에 전달합니다.

다음 YAML 조각은 GitHub Actions 예제를 보여줍니다.

```yaml
- name: 'Azure Load Testing'
  uses: azure/load-testing
  with:
    loadtestConfigFile: 'SampleApp.yaml'
    loadtestResource: 'MyTest'
    resourceGroup: 'loadtests-rg'
    env: |
    [
        {
        "name": "webapp",
        "value": "myapplication.contoso.com",
        }
    ]
```

다음 YAML 조각은 Azure Pipelines 예제를 보여줍니다.

```yml
- task: AzureLoadTest@1
  inputs:
    azureSubscription: 'MyAzureLoadTestingRG'
    loadTestConfigFile: 'SampleApp.yaml'
    loadTestResource: 'MyTest'
    resourceGroup: 'loadtests-rg'
    env: |
      [
          {
          "name": "webapp",
          "value": "myapplication.contoso.com",
          }
      ]
```

## <a name="faq"></a>FAQ  

### <a name="are-my-secrets-values-stored-by-the-azure-load-testing-service"></a>내 비밀 값은 Azure Load Testing 서비스에서 저장하나요?  

아니요. 비밀 값은 Azure Load Testing 서비스에서 저장되지 않습니다. Azure Key Vault 비밀 URI를 사용하는 경우 서비스는 비밀 URI만 저장하고 각 테스트 실행에 대한 비밀 값을 가져옵니다. CI/CD 워크플로에서 비밀 값을 제공한 경우 테스트 실행 후에 비밀 값을 사용할 수 없습니다. 각 테스트 실행에 대해 이러한 값을 제공합니다.

### <a name="what-happens-if-i-have-parameters-both-in-my-yaml-configuration-file-and-in-the-cicd-workflow"></a>내 YAML 구성 파일과 CI/CD 워크플로에 매개 변수가 있으면 어떻게 되나요?  

YAML 구성 파일과 Azure Pipelines 작업 또는 GitHub 작업에 매개 변수가 있는 경우 테스트 실행에 Azure Pipelines 작업 또는 GitHub 작업 값이 사용됩니다.

### <a name="i-created-and-ran-a-test-from-my-cicd-workflow-by-passing-parameters-using-the-azure-load-testing-task--action-can-i-run-this-test-from-the-azure-portal-with-the-same-parameters"></a>Azure Load Testing 작업/작업을 사용하여 매개 변수를 전달하여 CI/CD 워크플로에서 테스트를 만들고 실행했습니다. 동일한 매개 변수를 Azure Portal 이 테스트를 실행할 수 있나요?

매개 변수 값은 CI/CD 워크플로에서 전달될 때 저장되지 않습니다. Azure Portal 테스트를 실행할 때 매개 변수 값을 다시 제공해야 합니다. 누락된 값을 입력하라는 메시지가 표시됩니다. 비밀 값의 경우 Azure Key Vault 비밀 URI를 입력합니다. 테스트 실행 또는 다시 실행 페이지에서 입력한 값은 해당 테스트 실행에만 유효합니다. 테스트 수준에서 변경하려면 테스트 *구성으로* 이동하여 매개 변수 값을 입력합니다.
