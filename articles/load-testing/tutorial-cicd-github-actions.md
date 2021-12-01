---
title: '자습서: Azure 부하 테스트 및 GitHub 작업을 사용 하 여 성능 재발 식별'
titleSuffix: Azure Load Testing
description: 이 자습서에서는 Azure 부하 테스트와 GitHub 작업 CI/CD 워크플로를 사용 하 여 성능 재발 테스트를 자동화 하는 방법에 대해 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: ninallam
author: ninallam
ms.date: 11/30/2021
ms.topic: tutorial
ms.openlocfilehash: accbdc57c796cf7c88e993d28829e5327d96a194
ms.sourcegitcommit: 8152290a8817d0882035f7f3f1fd56b80f87dcda
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133312762"
---
# <a name="tutorial-identify-performance-regressions-with-github-actions-and-azure-load-testing-preview"></a>자습서: GitHub 작업 및 Azure 부하 테스트 미리 보기를 사용 하 여 성능 재발 식별

이 자습서에서는 Azure 부하 테스트 미리 보기 및 GitHub 작업을 사용 하 여 성능 재발 테스트를 자동화 하는 방법을 알아봅니다. GitHub 작업 CI/CD 워크플로를 구성 하 여 샘플 웹 응용 프로그램에 대 한 부하 테스트를 실행 한 다음 결과를 사용 하 여 성능 재발을 식별 합니다.

> [!IMPORTANT]
> Azure 부하 테스트에 대 한 GitHub 작업에 알려진 문제가 있으며,이로 인해 GitHub 작업 워크플로가 실패 합니다. 최신 상태 업데이트에 대 한 [Azure 부하 테스트의 알려진 문제](https://github.com/microsoft/azure-load-testing/wiki/Known-Issues-for-public-preview#error-unable-to-resolve-action-azureload-testing-repository-not-found-while-running-github-action-for-azure-load-testing) 를 확인 합니다.

CI/CD 워크플로에 대 한 Azure Pipelines를 사용 하는 경우 해당 [Azure Pipelines 자습서](./tutorial-cicd-azure-pipelines.md)를 참조 하세요.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
>
> * 부하 테스트에 필요한 파일을 사용 하 여 리포지토리를 설정 합니다.  
> * Azure 부하 테스트와 통합 하도록 GitHub 워크플로를 설정 합니다.  
> * 부하 테스트를 실행 하 고 워크플로에서 결과를 봅니다.  
> * 부하 테스트에서 임계값에 따라 통과 또는 실패 하는 테스트 조건을 정의 합니다.  
> * GitHub 암호를 사용 하 여 부하 테스트를 매개 변수화 합니다.  

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.  
* 리포지토리를 만들 수 있는 GitHub 계정 Microsoft 계정이 없는 경우에는 [무료로 만들 수 있습니다](https://github.com/).
* 기존 Azure 부하 테스트 리소스입니다. 부하 테스트 리소스를 만들려면 [부하 테스트 만들기 및 실행](./quickstart-create-and-run-load-test.md#create_resource)을 참조 하세요.

## <a name="set-up-your-repository"></a>리포지토리 설정

시작 하려면 먼저 샘플 웹 응용 프로그램을 사용 하는 GitHub 리포지토리가 필요 합니다. 그런 다음이 리포지토리를 사용 하 여 부하 테스트를 실행 하 GitHub 작업 워크플로를 구성 합니다.

1. 브라우저를 열고 샘플 응용 프로그램의 원본 GitHub 리포지토리로 이동 합니다.https://github.com/Azure-Samples/nodejs-appsvc-cosmosdb-bottleneck.git

    응용 프로그램 예제는 Azure App Service 웹 구성 요소 및 Cosmos DB 데이터베이스로 구성 된 Node.js 앱입니다.

1. **포크** 를 선택 하 여 샘플 응용 프로그램 리포지토리를 GitHub 계정으로 분기 합니다.

    :::image type="content" source="./media/tutorial-cicd-github-actions/fork-github-repo.png" alt-text="샘플 응용 프로그램 GitHub 리포지토리를 포크 하는 방법을 보여 주는 스크린샷":::

## <a name="configure-the-apache-jmeter-script"></a>Apache JMeter 스크립트 구성

샘플 응용 프로그램의 원본 리포지토리는 *sampleapp.exe. jmx* 라는 Apache jmeter 스크립트를 포함 합니다. 이 스크립트는 각 테스트 반복에 대 한 세 가지 API 호출을 수행 합니다.

* `add`-Cosmos DB에 대 한 데이터 삽입 작업을 수행 하 여 webapp 방문자 수를 확인 합니다.
* `get`-개수를 검색 하기 위해 Cosmos DB에서 GET 작업을 수행 합니다.
* `lasttimestamp` -마지막 사용자가 웹 사이트로 전환한 이후의 타임 스탬프를 업데이트 합니다.

이 섹션에서는 샘플 웹 앱의 URL을 사용 하 여 Apache JMeter 스크립트를 업데이트 합니다.

1. 샘플 응용 프로그램 리포지토리에서 편집을 위해 *sampleapp.exe. jmx* 를 엽니다.

    :::image type="content" source="./media/tutorial-cicd-github-actions/edit-jmx.png" alt-text="JMeter 테스트 스크립트를 편집 하는 방법을 보여 주는 스크린샷":::

1. `<stringProp name="HTTPSampler.domain">`를 검색합니다.

   파일에 세 개의 인스턴스가 표시 됩니다 `<stringProp name="HTTPSampler.domain">` .

1. 값을 세 개의 모든 인스턴스에서 예제 응용 프로그램 App Service의 URL로 바꿉니다. 

   ```xml
   <stringProp name="HTTPSampler.domain">{your-app-name}.azurewebsites.net</stringProp>
   ```

    이후 단계에서 GitHub 작업 워크플로를 사용 하 여 Azure App Service 웹 앱에 샘플 응용 프로그램을 배포 합니다. 지금은 *`{your-app-name}`* 이전 XML 조각에서 자리 표시자 텍스트를 App Service 웹 앱에 제공 하려는 고유한 이름으로 바꿉니다. 그런 다음 동일한 이름을 사용 하 여 웹 앱을 만듭니다.

    > [!IMPORTANT]
    > `https` `http` 예제 응용 프로그램의 URL에 또는를 포함 하지 마십시오.

1. 주 분기에 대 한 변경 내용을 커밋합니다.

## <a name="set-up-github-access-permissions-for-azure"></a>Azure에 대 한 GitHub 액세스 권한 설정

이 섹션에서는 Azure 부하 테스트 리소스에 액세스할 수 있는 권한을 갖도록 GitHub 리포지토리를 구성 합니다.

Azure 리소스에 액세스 하려면 Azure Active Directory 서비스 주체를 만들고 역할 기반 액세스 제어를 사용 하 여 필요한 사용 권한을 할당 합니다.

1. 다음 Azure CLI 명령을 실행 하 여 서비스 주체를 만듭니다.

    ```azurecli
    az ad sp create-for-rbac --name "my-load-test-cicd" --role contributor \
                             --scopes /subscriptions/<subscription-id> \
                             --sdk-auth
    ```

    이전 명령에서 자리 표시자 텍스트를 *`<subscription-id>`* Azure 부하 테스트 리소스의 azure 구독 ID로 바꿉니다.

    Azure CLI 명령의 결과는 JSON 문자열로, 이후 단계에서 GitHub 비밀에 추가 됩니다.

    ```json
    {
      "clientId": "<my-client-id>",
      "clientSecret": "<my-client-secret>",
      "subscriptionId": "<my-subscription-id>",
      "tenantId": "<my-tenant-id>",
      (...)
    }
    ```

1. 분기 샘플 응용 프로그램 GitHub 리포지토리로 이동 합니다.

1. **설정**  >  **비밀**  >  **새 리포지토리 비밀** 을 선택 하 여 GitHub 리포지토리에 새 비밀을 추가 합니다.

    :::image type="content" source="./media/tutorial-cicd-github-actions/github-new-secret.png" alt-text="GitHub 리포지토리에 새 리포지토리 암호를 추가 하는 방법을 보여 주는 스크린샷":::

1. **이름** 에 *AZURE_CREDENTIALS* 을 입력 하 고 **값** 에 대 한 Azure CLI의 JSON 응답을 붙여넣은 다음 **비밀 추가** 를 선택 합니다.

    :::image type="content" source="./media/tutorial-cicd-github-actions/github-new-secret-details.png" alt-text="새 GitHub 리포지토리 암호의 세부 정보를 보여 주는 스크린샷":::

1. 서비스 사용자에 게 Azure 부하 테스트 서비스에 대 한 액세스 권한을 부여 하려면 **부하 테스트 참가자** 역할을 서비스 주체에 할당 합니다. 

    먼저이 Azure CLI 명령을 실행 하 여 서비스 사용자 개체 ID를 검색 합니다.

    ```azurecli
    az ad sp list --filter "displayname eq 'my-load-test-cicd'" -o table
    ```

    그런 다음 서비스 사용자에 게 **부하 테스트 참가자** 역할을 할당 합니다. 자리 표시자 텍스트를 *`<sp-object-id>`* 이전 Azure CLI 명령의 **ObjectId** 값으로 바꿉니다. 또한를 *`<subscription-name-or-id>`* Azure 구독 ID로 바꿉니다.

    ```azurecli
    az role assignment create --assignee "<sp-object-id>" \
        --role "Load Test Contributor" \
        --subscription "<subscription-name-or-id>"
    ```

## <a name="configure-the-github-actions-workflow-to-run-a-load-test"></a>부하 테스트를 실행 하도록 GitHub 작업 워크플로 구성

이 섹션에서는 부하 테스트를 트리거하는 GitHub 작업 워크플로를 설정 합니다. 

CI/CD 워크플로에서 Azure 부하 테스트를 사용 하 여 테스트를 실행 하려면 부하 테스트 YAML 구성 파일이 필요 합니다. 샘플 응용 프로그램 리포지토리에는 테스트를 실행 하기 위한 매개 변수를 포함 하는 *sampleapp.exe* 파일이 포함 되어 있습니다.

1. 샘플 응용 프로그램 리포지토리에서 *github/워크플로/워크플로. yml* GitHub 작업 워크플로 파일을 엽니다.
 
1. 파일을 편집 하 고 다음 자리 표시자 텍스트를 바꿉니다.

    |자리 표시자  |값  |
    |---------|---------|
    |*`<your-azure-web-app>`*     | Azure App Service 웹 앱 이름입니다. 이 이름은 *sampleapp.exe jmx* 테스트 스크립트의 끝점 URL에 사용 된 이름과 일치 해야 합니다. |
    |*`<your-azure-load-testing-resource-name>`*     | Azure 부하 테스트 리소스의 이름입니다. |
    |*`<your-azure-load-testing-resource-group-name>`*     | Azure 부하 테스트 리소스를 포함 하는 리소스 그룹 이름입니다. |
    
    
    > [!IMPORTANT]
    > Azure 웹 앱의 이름은 *sampleapp.exe. jmx* 테스트 스크립트의 끝점 URL에 사용한 이름과 일치 해야 합니다.
    
    ```yaml
    env:
      AZURE_WEBAPP_NAME: "<your-azure-web-app>"
      LOAD_TEST_RESOURCE: "<your-azure-load-testing-resource-name>"
      LOAD_TEST_RESOURCE_GROUP: "<your-azure-load-testing-resource-group-name>"
    ```

1. 변경 내용을 기본 분기에 직접 커밋합니다.
    
    :::image type="content" source="./media/tutorial-cicd-github-actions/commit-workflow.png" alt-text="GitHub 작업 워크플로 파일의 변경 내용을 커밋하는 방법을 보여 주는 스크린샷":::

    커밋이 리포지토리의 GitHub 작업 워크플로를 트리거합니다. **작업** 탭으로 이동 하 여 워크플로가 실행 중인지 확인할 수 있습니다.

## <a name="view-load-test-results"></a>부하 테스트 결과 보기

GitHub 작업 워크플로는 main 분기의 모든 업데이트에 대해 다음 단계를 실행 합니다.

- Azure 앱 Services 웹 앱에 샘플 Node.js 응용 프로그램을 배포 합니다. 웹 앱의 이름은 워크플로 파일에 구성 됩니다.
- Azure 부하 테스트를 트리거하여 Apache JMeter 스크립트 및 리포지토리의 테스트 구성 YAML 파일을 기반으로 부하 테스트를 만들고 실행 합니다.

이 섹션에서는 GitHub 작업 워크플로 로그에서 부하 테스트 결과를 확인 합니다.

1. GitHub 리포지토리에서 **작업** 탭을 선택 하 여 워크플로 실행 목록을 볼 수 있습니다.
    
    :::image type="content" source="./media/tutorial-cicd-github-actions/workflow-run-list.png" alt-text="워크플로가 실행 되는 GitHub 작업 목록을 보여 주는 스크린샷":::
    
1. 목록에서 워크플로 실행을 선택 하 여 실행 세부 정보 및 로깅 정보로 이동 합니다.

    :::image type="content" source="./media/tutorial-cicd-github-actions/github-actions-workflow-completed.png" alt-text="워크플로 로깅 정보를 보여 주는 스크린샷":::

    부하 테스트가 완료 되 면 워크플로 로그에서 테스트 요약 정보 및 클라이언트 쪽 메트릭을 볼 수 있습니다. 이 로그에는이 부하 테스트에 대 한 Azure 부하 테스트 대시보드로 이동 하는 단계도 나와 있습니다.

1. 워크플로 실행 세부 정보 화면에서 **loadTestResults** 아티팩트를 선택 하 여 부하 테스트 결과 파일을 다운로드 합니다.

    :::image type="content" source="./media/tutorial-cicd-github-actions/github-actions-artifacts.png" alt-text="워크플로 실행의 아티팩트를 보여 주는 스크린샷":::

    
## <a name="define-test-passfail-criteria"></a>테스트 통과/실패 조건 정의

이 섹션에서는 부하 테스트의 결과를 확인 하는 데 실패 조건을 추가 합니다. 하나 이상의 실패 조건이 true로 평가 되 면 부하 테스트에 실패 합니다.

이러한 조건은 테스트 구성 YAML 파일에서 지정할 수 있습니다.

1. GitHub 리포지토리에서 *sampleapp.exe* 파일을 편집 합니다.  

1. 파일의 끝에 다음 코드 조각을 추가 합니다.

    ```yaml
    failureCriteria: 
    - avg(response_time_ms) > 100
    - percentage(error) > 20
    ```

    이제 부하 테스트에 대 한 오류 조건을 지정 했습니다. 다음 조건 중 하나 이상이 충족 되 면 테스트가 실패 합니다.
    
    - 집계 평균 응답 시간은 100 밀리초 보다 큽니다.    
    - 오류의 집계 비율은 20% 보다 큽니다.

1. 리포지토리의 주 분기에 대 한 변경 내용을 커밋하고 푸시합니다.
    
    변경 내용은 GitHub 작업 CI/CD 워크플로를 트리거합니다.

1. **작업** 탭을 선택 하 고 가장 최근에 실행 한 워크플로를 선택 하 여 워크플로 로그를 확인 합니다.

    :::image type="content" source="./media/tutorial-cicd-github-actions/github-actions-workflow-failed.png" alt-text="실패 한 워크플로 출력 로그를 보여 주는 스크린샷":::

    부하 테스트가 완료 된 후 평균 응답 시간이 실패 조건에 지정 된 것 보다 높기 때문에 워크플로가 실패 한 것을 알 수 있습니다.

    Azure 부하 테스트 서비스는 테스트 실행 중에 조건을 평가 합니다. 이러한 조건 중 하나라도 실패 하는 경우 Azure 부하 테스트 서비스는 0이 아닌 종료 코드를 반환 합니다. 이 코드는 CI/CD 워크플로에 테스트가 실패 했음을 알립니다.

1. *Sampleapp.exe* 파일을 편집 하 고 테스트 오류 조건을 변경 합니다.

    ```yaml
    failureCriteria: 
    - avg(response_time_ms) > 5000
    - percentage(error) > 20
    ```
    
1. 변경 내용을 커밋하여 GitHub 작업 워크플로를 트리거합니다. 

    :::image type="content" source="./media/tutorial-cicd-github-actions/github-actions-workflow-passed.png" alt-text="성공한 워크플로 출력 로그를 보여 주는 스크린샷":::

    이제 부하 테스트가 성공 하 고 워크플로가 성공적으로 완료 됩니다.

## <a name="pass-parameters-to-your-load-tests-from-the-workflow"></a>워크플로에서 부하 테스트에 매개 변수 전달

다음으로, 워크플로 변수를 사용 하 여 부하 테스트를 매개 변수화 합니다. 이러한 매개 변수는 암호 또는 비 암호와 같은 비밀이 될 수 있습니다.

이 자습서에서는 보안 요청만 수락 하도록 샘플 응용 프로그램을 다시 구성 합니다. 보안 요청을 보내려면 HTTP 요청에서 비밀 값을 전달 해야 합니다.

1. GitHub 리포지토리에서 *sampleapp.exe* 파일을 편집 합니다.

    *SampleApp_Secrets jmx* 파일을 사용 하도록 **testplan** 구성 설정을 업데이트 합니다.

    ```yml
    version: v0.1
    testName: SampleApp
    testPlan: SampleApp_Secrets.jmx
    description: 'SampleApp Test with secrets'
    engineInstances: 1
    ```

    Jmx Apache JMeter 스크립트 *SampleApp_Secrets* 는 사용자 지정 함수를 사용 하 여 비밀 값을 검색 하는 사용자 정의 변수를 사용 합니다 `${__GetSecret(secretName)}` . 그런 다음 Apache JMeter는이 비밀 값을 샘플 응용 프로그램 끝점에 전달 합니다.

1. YAML 파일에 대 한 변경 내용을 커밋합니다.

1. GitHub 리포지토리에서 *config.xml* 파일을 편집 합니다.
    
    `enableSecretsFeature`보안 요청만 수락 하도록 샘플 응용 프로그램을 다시 구성 하려면이 설정을 **true** 로 업데이트 합니다.
    
    ```json
    {
        "enableSecretsFeature": true
    }
    ```
    
1. *App.config* 파일에 대 한 변경 내용을 커밋합니다.

1. *SampleApp_Secrets jmx* 파일을 편집 합니다.

1. `<stringProp name="HTTPSampler.domain">`를 검색합니다.

   파일에 세 개의 인스턴스가 표시 됩니다 `<stringProp name="HTTPSampler.domain">` .

1. 값을 세 개의 모든 인스턴스에서 예제 응용 프로그램 App Service의 URL로 바꿉니다. 

   ```xml
   <stringProp name="HTTPSampler.domain">{your-app-name}.azurewebsites.net</stringProp>
   ```

    이후 단계에서 GitHub 작업 워크플로를 사용 하 여 Azure App Service 웹 앱에 보안 샘플 응용 프로그램을 배포 합니다. 이전 XML 코드 조각에서 자리 표시자 텍스트를 *`{your-app-name}`* App Service 웹 앱의 고유한 이름으로 바꿉니다. 그런 다음 동일한 이름을 사용 하 여 웹 앱을 만듭니다.

    > [!IMPORTANT]
    > `https` `http` 예제 응용 프로그램의 URL에 또는를 포함 하지 마십시오.

1. Apache JMeter 스크립트를 저장 하 고 커밋합니다.

1. **설정**  >  **비밀**  >  **새 리포지토리 비밀** 을 선택 하 여 GitHub 리포지토리에 새 비밀을 추가 합니다.

1. **이름** 에 *MY_SECRET* 을 입력 하 고 **값** 에 *1797669089* 을 입력 한 다음 **비밀 추가** 를 선택 합니다.

    :::image type="content" source="./media/tutorial-cicd-github-actions/github-new-secret-jmx.png" alt-text="JMeter 스크립트에서 사용 되는 리포지토리 비밀을 보여 주는 스크린샷":::

1. 암호를 부하 테스트에 전달 하려면 *github/workflow/workflow. yml* 파일을 편집 합니다.

    다음 YAML 코드 조각을 추가 하 여 Azure 부하 테스트 작업을 편집 합니다.

    ```yaml
    secrets: |
      [
          {
          "name": "appToken",
          "value": "${{ secrets.MY_SECRET }}"
          }
      ]
    ```

1. GitHub 작업 워크플로를 트리거하는 변경 내용을 커밋합니다.

    Azure 부하 테스트 작업은 워크플로에서 리포지토리 암호를 테스트 엔진에 안전 하 게 전달 합니다. 비밀 매개 변수는 부하 테스트를 실행 하는 동안에만 사용 되며, 해당 값은 메모리에서 삭제 됩니다.

## <a name="azure-load-testing-action"></a>Azure 부하 테스트 작업

이 섹션에서는 Azure 부하 테스트 GitHub 작업에 대해 설명 합니다. 워크플로에서 작업을 참조 하 여이 작업을 사용할 수 있습니다 `azure/load-testing@v1` . 작업은 Windows, Linux 및 Mac 러너에서 실행 됩니다. 

다음 매개 변수를 사용 하 여 GitHub 작업을 구성할 수 있습니다.

|매개 변수  |설명  |
|---------|---------|
|`loadTestConfigFile`    | **필수** 부하 테스트 YAML 구성 파일의 경로입니다. 경로가 정규화 된 경로 이거나 기본 작업 디렉터리에 대 한 상대 경로입니다.        |
|`resourceGroup`     |  **필수** Azure 부하 테스트 리소스를 포함 하는 리소스 그룹의 이름입니다.       |
|`loadTestResource`     |   **필수** 기존 Azure 부하 테스트 리소스의 이름입니다.      |
|`secrets`   |   각 암호의 **이름과** **값** 으로 구성 된 JSON 개체의 배열입니다. 이름은 Apache JMeter 테스트 스크립트에 사용 된 암호 이름과 일치 해야 합니다. |
|`env`     |   각 환경 변수의 **이름과** **값** 으로 구성 된 JSON 개체의 배열입니다. 이름은 Apache JMeter 테스트 스크립트에서 사용 되는 변수 이름과 일치 해야 합니다. |

다음 yaml 코드 조각은 GitHub 작업 워크플로에서 작업을 사용 하는 방법을 설명 합니다. 

```yaml
- name: 'Azure Load Testing'
  uses: azure/load-testing@v1
  with:
    loadTestConfigFile: '< YAML File path>'
    loadTestResource: '<name of the load test resource>'
    resourceGroup: '<name of the resource group of your load test resource>' 
    secrets: |
      [
          {
          "name": "<Name of the secret>",
          "value": "${{ secrets.MY_SECRET1 }}",
          },
          {
          "name": "<Name of the secret>",
          "value": "${{ secrets.MY_SECRET2 }}",
          }
      ]
    env: |
      [
          {
          "name": "<Name of the variable>",
          "value": "<Value of the variable>",
          },
          {
          "name": "<Name of the variable>",
          "value": "<Value of the variable>",
          }
      ]
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [alt-delete-resource-group](../../includes/alt-delete-resource-group.md)]  

## <a name="next-steps"></a>다음 단계

이제 Azure 부하 테스트를 사용 하 여 부하 테스트를 자동으로 실행 하는 GitHub 작업 워크플로를 만들었습니다. 통과/실패 조건을 사용 하 여 CI/CD 워크플로의 상태를 설정할 수 있습니다. 매개 변수를 사용 하 여 부하 테스트 실행을 구성할 수 있습니다.

* 부하 테스트 매개 변수화에 대 한 자세한 내용은 [부하 테스트 매개 변수화](./how-to-parameterize-load-tests.md)를 참조 하세요.
* 테스트 통과/실패 조건을 정의 하는 방법에 대 한 자세한 내용은 [테스트 조건 정의](./how-to-define-test-criteria.md)를 참조 하세요.
