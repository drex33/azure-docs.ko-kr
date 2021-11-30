---
title: '자습서: Azure 부하 테스트 및 Azure Pipelines를 사용 하 여 성능 재발 식별'
titleSuffix: Azure Load Testing
description: 이 자습서에서는 Azure 부하 테스트를 사용 하 여 성능 재발 테스트를 자동화 하 고 CI/CD 워크플로를 Azure Pipelines 하는 방법을 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: ninallam
author: ninallam
ms.date: 11/30/2021
ms.topic: tutorial
ms.openlocfilehash: 837f21c692d7ea44961f1d3a5f680314661ba70d
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133304060"
---
# <a name="tutorial-identify-performance-regressions-with-azure-pipelines-and-azure-load-testing-preview"></a>자습서: Azure Pipelines 및 Azure 부하 테스트 미리 보기를 사용 하 여 성능 재발 식별

이 자습서에서는 Azure 부하 테스트 미리 보기 및 Azure Pipelines를 사용 하 여 성능 재발 테스트를 자동화 하는 방법을 알아봅니다. 샘플 웹 응용 프로그램에 대 한 부하 테스트를 실행 하 고 결과를 사용 하 여 성능 재발을 식별 하도록 Azure Pipelines CI/CD 워크플로를 구성 합니다.

CI/CD 워크플로에 대 한 GitHub 작업을 사용 하는 경우 해당 [GitHub 작업 자습서](./tutorial-cicd-github-actions.md)를 참조 하세요.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 부하 테스트에 필요한 파일을 사용 하 여 리포지토리를 설정 합니다.
> * Azure Pipelines 설정 하 여 Azure 부하 테스트와 통합 합니다.
> * 부하 테스트를 실행 하 고 파이프라인 로그에서 결과를 확인 합니다.
> * 부하 테스트에 대 한 통과/실패 조건을 정의 합니다.
> * 파이프라인 변수를 사용 하 여 부하 테스트를 매개 변수화 합니다.

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!NOTE]
> Azure Pipelines는 전용 프로젝트용 Microsoft 호스팅 에이전트에서 실행 되는 작업에 대해 60 분의 시간 제한이 있습니다. 부하 테스트가 60 분 이상 실행 되는 경우 [추가 용량](/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml#capabilities-and-limitations)에 대 한 비용을 지불 해야 합니다. 그렇지 않으면 파이프라인은 테스트 결과를 기다리지 않고 시간 초과 됩니다. Azure Portal에서 부하 테스트 상태를 볼 수 있습니다.
>

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.  
* Azure DevOps 조직 및 프로젝트입니다. Azure DevOps 조직이 없는 경우 [무료로 만들](/azure/devops/pipelines/get-started/pipelines-sign-up.md?view=azure-devops&preserve-view=true)수 있습니다. Azure Pipelines 시작 하는 데 도움이 필요한 경우 [첫 번째 파이프라인 만들기](/azure/devops/pipelines/create-first-pipeline.md?preserve-view=true&view=azure-devops&tabs=java%2Ctfs-2018-2%2Cbrowser)를 참조 하세요.
* 리포지토리를 만들 수 있는 GitHub 계정. Microsoft 계정이 없는 경우에는 [무료로 만들 수 있습니다](https://github.com/).  
* 기존 Azure 부하 테스트 리소스입니다. 부하 테스트 리소스를 만들려면 [부하 테스트 만들기 및 실행](./quickstart-create-and-run-load-test.md#create_resource)을 참조 하세요.

## <a name="set-up-your-repository"></a>리포지토리 설정

시작 하려면 먼저 샘플 웹 응용 프로그램을 사용 하는 GitHub 리포지토리가 필요 합니다. 그런 다음이 리포지토리를 사용 하 여 부하 테스트를 실행 하는 Azure Pipelines 워크플로를 구성 합니다.

1. 브라우저를 열고 샘플 응용 프로그램의 원본 GitHub 리포지토리로 이동 합니다.https://github.com/Azure-Samples/nodejs-appsvc-cosmosdb-bottleneck.git

    응용 프로그램 예제는 Azure App Service 웹 구성 요소 및 Cosmos DB 데이터베이스로 구성 된 Node.js 앱입니다.

1. **포크** 를 선택 하 여 샘플 응용 프로그램 리포지토리를 GitHub 계정으로 분기 합니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/fork-github-repo.png" alt-text="샘플 응용 프로그램 GitHub 리포지토리를 포크 하는 방법을 보여 주는 스크린샷":::

## <a name="configure-the-apache-jmeter-script"></a>Apache JMeter 스크립트 구성

샘플 응용 프로그램의 원본 리포지토리는 *sampleapp.exe. jmx* 라는 Apache jmeter 스크립트를 포함 합니다. 이 스크립트는 각 테스트 반복에 대 한 세 가지 API 호출을 수행 합니다.

* `add`-Cosmos DB에 대 한 데이터 삽입 작업을 수행 하 여 webapp 방문자 수를 확인 합니다.
* `get`-개수를 검색 하기 위해 Cosmos DB에서 GET 작업을 수행 합니다.
* `lasttimestamp` -마지막 사용자가 웹 사이트로 전환한 이후의 타임 스탬프를 업데이트 합니다.

이 섹션에서는 샘플 웹 앱의 URL을 사용 하 여 Apache JMeter 스크립트를 업데이트 합니다.
 
1. 샘플 응용 프로그램 리포지토리에서 편집을 위해 *sampleapp.exe. jmx* 를 엽니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/edit-jmx.png" alt-text="Apache JMeter 테스트 스크립트를 편집 하는 방법을 보여 주는 스크린샷":::

1. `<stringProp name="HTTPSampler.domain">`를 검색합니다.

   파일에 세 개의 인스턴스가 표시 됩니다 `<stringProp name="HTTPSampler.domain">` .

1. 값을 세 개의 모든 인스턴스에서 예제 응용 프로그램 App Service의 URL로 바꿉니다. 

   ```xml
   <stringProp name="HTTPSampler.domain">{your-app-name}.azurewebsites.net</stringProp>
   ```

    이후 단계에서 Azure Pipelines를 사용 하 여 Azure App Service 웹 앱에 샘플 응용 프로그램을 배포 합니다. 지금은 *`{your-app-name}`* 이전 XML 조각에서 자리 표시자 텍스트를 App Service 웹 앱에 제공 하려는 고유한 이름으로 바꿉니다. 그런 다음 동일한 이름을 사용 하 여 웹 앱을 만듭니다.

    > [!IMPORTANT]
    > `https` `http` 예제 응용 프로그램의 URL에 또는를 포함 하지 마십시오.

1. 주 분기에 대 한 변경 내용을 커밋합니다.

## <a name="set-up-azure-pipelines-access-permissions-for-azure"></a>Azure에 대 한 Azure Pipelines 액세스 권한 설정

이 섹션에서는 Azure 부하 테스트 리소스에 액세스할 수 있는 권한을 갖도록 Azure DevOps 프로젝트를 구성 합니다.

Azure 리소스에 액세스 하려면 Azure DevOps에서 서비스 연결을 만들고 역할 기반 액세스 제어를 사용 하 여 필요한 사용 권한을 할당 합니다.

1. Azure DevOps 조직 ()에 로그인 *`https://dev.azure.com/<yourorganization>`* 합니다.

1. **Project 설정**  >  **서비스 연결** 을 선택 합니다.

1. **+ 새 서비스 연결** 을 선택 하 고 **Azure Resource Manager** 서비스 연결을 선택한 후 **다음** 을 선택 합니다.

1. **서비스 사용자 (자동)** 인증 방법을 선택한 후 **다음** 을 선택 합니다.

1. **구독** 범위 수준을 선택 하 고 Azure 부하 테스트 리소스를 포함 하는 azure 구독을 선택 합니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/new-service-connection.png" alt-text="새 서비스 연결을 만드는 방법을 보여 주는 스크린샷":::

    이후 단계에서 서비스 연결 이름을 사용 하 여 파이프라인을 구성 합니다.

1. **저장** 을 선택하여 연결을 만듭니다.

1. 목록에서 서비스 연결을 선택 하 고 **서비스 주체 관리** 를 선택 합니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/manage-service-principal.png" alt-text="서비스 주체를 관리 하는 방법을 보여 주는 스크린샷":::

    Azure Portal에서 서비스 주체에 대 한 세부 정보를 볼 수 있습니다. 서비스 주체 **응용 프로그램 (클라이언트) ID** 를 적어둡니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/service-connection-object-id.png" alt-text="서비스 연결 응용 프로그램 ID를 가져오는 방법을 보여 주는 스크린샷":::
    
1. 이제 서비스 주체에 **부하 테스트 참가자** 역할을 할당 하 여 Azure 부하 테스트 서비스에 대 한 액세스를 허용 합니다.

    먼저 서비스 사용자 개체 ID를 검색 합니다. 다음 Azure CLI 명령에서 **objectId** 결과를 선택 합니다.

    ```azurecli
    az ad sp show --id "<application-client-id>"
    ```
    
    자리 표시자 텍스트를 *`<sp-object-id>`* 서비스 사용자 개체 ID로 바꿉니다. 또한를 *`<subscription-name-or-id>`* Azure 구독 ID로 바꿉니다.

    ```azurecli
    az role assignment create --assignee "<sp-object-id>" \
        --role "Load Test Contributor" \
        --subscription "<subscription-name-or-id>"
    ```

## <a name="configure-the-azure-pipelines-workflow-to-run-a-load-test"></a>Azure Pipelines 워크플로를 구성 하 여 부하 테스트 실행

이 섹션에서는 부하 테스트를 트리거하는 Azure Pipelines 워크플로를 설정 합니다.

먼저 Azure DevOps Marketplace에서 Azure 부하 테스트 확장을 설치 하 고, 새 파이프라인을 만들고, 샘플 응용 프로그램의 분기 리포지토리에 연결 합니다.

1. Azure DevOps Marketplace에서 Azure 부하 테스트 작업 확장을 설치 합니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/browse-marketplace.png" alt-text="확장에 대 한 Visual Studio Marketplace를 찾아보는 방법을 보여 주는 스크린샷":::
    
    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/marketplace-load-testing-extension.png" alt-text="Visual Studio Marketplace에서 Azure 부하 테스트 확장을 설치 하는 방법을 보여 주는 스크린샷":::

1. Azure DevOps 프로젝트에서 **Pipelines** 을 선택한 다음 **파이프라인 만들기** 를 선택 합니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/create-pipeline.png" alt-text="새 Azure 파이프라인을 만드는 방법을 보여 주는 스크린샷":::

1. **커넥트** 탭에서 **GitHub** 를 선택 합니다.

1. Azure Pipelines에서 워크플로를 트리거하기 위해 GitHub 계정에 액세스할 수 있도록 하려면 **azurepipelines 권한 부여** 를 선택 합니다.

1. **선택** 탭에서 샘플 응용 프로그램의 분기 리포지토리를 선택 합니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/create-pipeline-select-repo.png" alt-text="샘플 응용 프로그램의 GitHub 리포지토리를 선택 하는 방법을 보여 주는 스크린샷":::

    리포지토리에 *azure-pipeline* 파이프라인 정의 파일이 포함 되어 있습니다. 이제이 정의를 수정 하 여 Azure 부하 테스트 서비스에 연결 합니다.

1. **검토** 탭에서 yaml 코드의 다음 자리 표시자 텍스트를 바꿉니다.

    |자리 표시자  |값  |
    |---------|---------|
    |*`<Name of your webapp>`*     | Azure App Service 웹 앱 이름입니다. 이 이름은 *sampleapp.exe jmx* 테스트 스크립트의 끝점 URL에 사용 된 이름과 일치 해야 합니다. |
    | *`<Name of your webARM Service connection>`* | 이전 섹션에서 만든 서비스 연결의 이름입니다. |
    |*`<Azure subscriptionId>`*     | Azure 구독 ID. |
    |*`<Name of your load test resource>`*     | Azure 부하 테스트 리소스의 이름입니다. |
    |*`<Name of your load test resource group>`*     | Azure 부하 테스트 리소스를 포함 하는 리소스 그룹 이름입니다. |
    
    > [!IMPORTANT]
    > Azure 웹 앱의 이름은 *sampleapp.exe. jmx* 테스트 스크립트의 끝점 URL에 사용한 이름과 일치 해야 합니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/create-pipeline-review.png" alt-text="파이프라인을 만들 때 Azure Pipelines 검토 탭을 보여 주는 스크린샷":::

1. **저장 및 실행** 을 선택 하 고 **커밋 메시지** 를 입력 한 다음 **저장 및 실행** 을 선택 합니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/create-pipeline-save.png" alt-text="새 Azure 파이프라인을 저장 하 고 실행 하는 방법을 보여 주는 스크린샷":::

    이제 Azure Pipelines CI/CD 워크플로를 실행합니다. 파이프라인 작업을 선택하여 상태 및 로그를 모니터링할 수 있습니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/create-pipeline-status.png" alt-text="파이프라인 작업 세부 정보를 보는 방법을 보여 주는 스크린샷":::

## <a name="view-load-test-results"></a>부하 테스트 결과 보기

 주 분기에 대한 모든 업데이트에 대해 Azure 파이프라인은 다음 단계를 실행합니다.

- 샘플 Node.js 애플리케이션을 Azure 앱 Services 웹앱에 배포합니다. 웹앱의 이름은 파이프라인 정의에 구성됩니다.
- Azure 부하 테스트를 트리거하여 리포지토리의 Apache JMeter 스크립트 및 테스트 구성 YAML 파일을 기반으로 부하 테스트를 만들고 실행합니다.

이 섹션에서는 파이프라인 로그 정보에서 부하 테스트 결과를 확인합니다.

1. Azure DevOps 프로젝트에서 **Pipelines** 선택한 다음, 목록에서 파이프라인 정의를 선택합니다.

1. 파이프라인 실행을 선택하여 실행 요약을 확인합니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/create-pipeline-run-summary.png" alt-text="파이프라인 실행 요약을 보여 주는 스크린샷":::

1. **작업** 섹션에서 **부하 테스트를** 선택하여 파이프라인 로그를 봅니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/create-pipeline-log.png" alt-text="Azure Pipeline 실행 로그를 보여 주는 스크린샷.":::

    부하 테스트가 완료되면 파이프라인 로그에서 테스트 요약 정보 및 클라이언트 쪽 메트릭을 볼 수 있습니다. 로그에는 이 부하 테스트에 대한 Azure 부하 테스트 대시보드로 이동하는 URL도 표시됩니다.

2. 파이프라인 로그 보기에서 **부하 테스트를** 선택한 다음, **생성된 아티팩트 1개** 를 선택하여 부하 테스트 결과 파일을 다운로드합니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/create-pipeline-download-results.png" alt-text="부하 테스트 결과를 다운로드하는 방법을 보여 주는 스크린샷":::

## <a name="define-test-passfail-criteria"></a>테스트 통과/실패 조건 정의

이 섹션에서는 부하 테스트의 결과를 결정하는 실패 조건을 추가합니다. 하나 이상의 실패 조건이 true로 평가되면 부하 테스트가 실패합니다.

테스트 구성 YAML 파일에서 이러한 조건을 지정할 수 있습니다.

1. GitHub 리포지토리에서 *SampleApp.yml* 파일을 편집합니다.  

1. 파일 끝에 다음 조각을 추가합니다.

    ```yaml
    failureCriteria: 
        - avg(response_time_ms) > 100
        - percentage(error) > 20
    ```

    이제 부하 테스트에 대한 실패 조건을 지정했습니다. 다음 조건 중 하나 이상을 충족하면 테스트가 실패합니다.
    
    - 집계 평균 응답 시간이 100ms를 초과합니다.    
    - 오류의 집계 백분율이 20%를 초과합니다.

1. 변경 내용을 커밋하고 리포지토리의 주 분기에 푸시합니다.
    
    변경 내용이 Azure Pipelines CI/CD 워크플로를 트리거합니다.

1. 파이프라인 실행 페이지의 목록에서 가장 최근 항목을 선택합니다.

    부하 테스트가 완료되면 평균 응답 시간이 실패 조건에 지정된 시간보다 높아서 파이프라인이 실패한 것을 알 수 있습니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/test-criteria-failed.png" alt-text="실패한 테스트 조건 이후의 파이프라인 로그를 보여 주는 스크린샷.":::

    Azure Load Testing 서비스는 테스트 실행 중에 조건을 평가합니다. 이러한 조건 중 어느 것이라도 실패하면 Azure Load Testing 서비스는 0이 아닌 종료 코드를 반환합니다. 이 코드는 테스트가 실패했다고 CI/CD 워크플로에 알릴 수 있습니다.

1. *SampleApp.yml* 파일을 편집하고 테스트 실패 조건을 변경합니다.

    ```yaml
    failureCriteria: 
        - avg(response_time_ms) > 5000
        - percentage(error) > 20
    ```
    
1. 변경 내용을 커밋하여 Azure Pipelines CI/CD 워크플로를 트리거합니다. 
    
    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/test-criteria-passed.png" alt-text="모든 테스트 조건을 통과한 후 파이프라인 로그를 보여 주는 스크린샷.":::

    이제 부하 테스트가 성공하고 파이프라인이 성공적으로 완료됩니다.

## <a name="pass-parameters-to-your-load-tests-from-the-pipeline"></a>파이프라인에서 부하 테스트에 매개 변수 전달

다음으로, 파이프라인 변수를 사용하여 부하 테스트를 매개 변수화합니다. 이러한 변수는 암호와 같은 비밀이거나 비밀이 아닌 변수일 수 있습니다.

이 자습서에서는 보안 요청만 수락하도록 샘플 애플리케이션을 다시 구성합니다. 보안 요청을 보내려면 HTTP 요청에 비밀 값을 전달해야 합니다.

1. GitHub 리포지토리에서 *SampleApp.yaml* 파일을 편집합니다.

    *SampleApp_Secrets.jmx* 파일을 사용하도록 **testPlan** 구성 설정을 업데이트합니다.

    ```yml
    version: v0.1
    testName: SampleApp
    testPlan: SampleApp_Secrets.jmx
    description: 'SampleApp Test with secrets'
    engineInstances: 1
    ```

    *SampleApp_Secrets.jmx* Apache JMeter 스크립트는 사용자 지정 함수 를 사용하여 비밀 값을 검색하는 사용자 정의 변수를 `${__GetSecret(secretName)}` 사용합니다. 그런 다음 Apache JMeter는 이 비밀 값을 샘플 애플리케이션 엔드포인트에 전달합니다.

1. 변경 내용을 YAML 파일에 커밋합니다.

1. GitHub 리포지토리에서 *config.json* 파일을 편집합니다.
    
    보안 `enableSecretsFeature` 요청만 수락하도록 샘플 애플리케이션을 다시 구성하려면 설정을 **true로** 업데이트합니다.
    
    ```json
    {
        "enableSecretsFeature": true
    }
    ```
    
1. *config.json* 파일에 변경 내용을 커밋합니다.

1. *SampleApp_Secrets.jmx* 파일을 편집합니다.

1. `<stringProp name="HTTPSampler.domain">`를 검색합니다.

   파일에 세 개의 `<stringProp name="HTTPSampler.domain">` 인스턴스가 표시됩니다.

1. 값을 세 인스턴스 모두에 있는 샘플 애플리케이션의 App Service URL로 대체합니다. 

   ```xml
   <stringProp name="HTTPSampler.domain">{your-app-name}.azurewebsites.net</stringProp>
   ```

    GitHub Actions 워크플로를 사용하여 샘플 애플리케이션을 Azure App Service 웹앱에 배포합니다. 이전 XML 조각에서 자리 표시자 텍스트를 *`{your-app-name}`* App Service 웹앱의 고유한 이름으로 대체합니다.

    > [!IMPORTANT]
    > `https`샘플 애플리케이션의 URL에 또는 를 포함하지 `http` 마세요.

1. Apache JMeter 스크립트를 저장하고 커밋합니다.

1. **Pipelines** 페이지로 이동하여 파이프라인 정의를 선택한 다음, **편집을** 선택합니다.

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/edit-pipeline.png" alt-text="파이프라인 정의를 편집하는 방법을 보여 주는 스크린샷":::

1. **변수 를** 선택한 다음, **새 변수** 를 선택합니다.

1. **이름(** *mySecret*) 및 **값(** *1797669089*) 정보를 입력한 **다음, 이 값 비밀 유지** 상자를 확인하여 변수를 안전하게 저장합니다. 

    :::image type="content" source="./media/tutorial-cicd-azure-pipelines/new-variable.png" alt-text="새 파이프라인 변수를 만드는 방법을 보여 주는 스크린샷":::

1. **확인을** 선택한 **다음, 저장을** 선택하여 새 변수를 저장합니다.

1. *azure-pipeline.yml* 파일을 편집하여 부하 테스트에 비밀을 전달합니다.

    다음 YAML 조각을 추가하여 Azure 부하 테스트 작업을 편집합니다.

    ```yml
    secrets: |
      [
          {
          "name": "appToken",
          "value": "$(mySecret)"
          }
      ]
    ```

1. 파이프라인을 저장하고 실행합니다.  

    Azure Load Testing 작업은 파이프라인에서 테스트 엔진으로 비밀을 안전하게 전달합니다. 비밀 매개 변수는 부하 테스트를 실행하는 동안에만 사용되며 메모리에서 값이 삭제됩니다.
    
다음 섹션에서 Azure Load Testing 작업의 개요를 살펴보세요.  

## <a name="azure-load-testing-task"></a>Azure 부하 테스트 작업

이 섹션에서는 Azure Pipelines 대한 Azure Load Testing 작업에 대해 설명합니다. 작업은 플랫폼 간이며 Windows, Linux 또는 Mac 에이전트에서 실행됩니다.

다음 매개 변수를 사용하여 Azure 부하 테스트 작업을 구성할 수 있습니다.

|매개 변수  |설명  |
|---------|---------|
|`azureSubscription`     |  **필수** Azure Resource Manager 서비스 연결의 이름입니다.       |
|`loadTestConfigFile`     | **필수** 부하 테스트 YAML 구성 파일의 경로입니다. 경로가 정규화되거나 기본 작업 디렉터리에 상대적입니다.        |
|`resourceGroup`     |  **필수** Azure Load Testing 리소스를 포함하는 리소스 그룹의 이름입니다.       |
|`loadTestResource`     |   **필수** 기존 Azure Load Testing 리소스의 이름입니다.      |
|`secrets`     |   각 비밀의 **이름** 및 **값으로** 구성된 JSON 개체의 배열입니다. 이름은 Apache JMeter 테스트 스크립트에 사용된 비밀 이름과 일치해야 합니다. |
|`env`     |   각 환경 변수의 **이름** 및 **값으로** 구성된 JSON 개체의 배열입니다. 이름은 Apache JMeter 테스트 스크립트에 사용된 변수 이름과 일치해야 합니다. |

다음 YAML 코드 조각에서는 Azure Pipelines CI/CD 워크플로에서 작업을 사용하는 방법을 설명합니다.

```yaml
- task: AzureLoadTest@1
  inputs:
    azureSubscription: '<Azure service connection>'
    loadTestConfigFile: '< YAML File path>'
    loadTestResource: '<name of the load test resource>'
    resourceGroup: '<name of the resource group of your load test resource>' 
    secrets: |
      [
          {
          "name": "<Name of the secret>",
          "value": "$(mySecret1)",
          },
          {
          "name": "<Name of the secret>",
          "value": "$(mySecret1)",
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

이제 부하 테스트를 자동으로 실행하기 위해 Azure Load Testing을 사용하는 Azure Pipelines CI/CD 워크플로를 만들었습니다. 통과/실패 조건을 사용하여 CI/CD 워크플로의 상태를 설정할 수 있습니다. 매개 변수를 사용하면 부하 테스트 실행을 구성할 수 있습니다.

* 부하 테스트 매개 변수화에 대한 자세한 내용은 부하 테스트 [매개 변수화를](./how-to-parameterize-load-tests.md)참조하세요.
* 테스트 통과/실패 조건 정의에 대한 자세한 내용은 [테스트 조건 정의를 참조하세요.](./how-to-define-test-criteria.md)
