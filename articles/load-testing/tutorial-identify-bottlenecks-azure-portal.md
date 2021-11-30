---
title: '자습서: 부하 테스트를 실행하여 성능 병목 현상 식별'
titleSuffix: Azure Load Testing
description: 이 자습서에서는 Azure Load Testing을 사용하여 대규모 부하 테스트를 실행하여 웹앱에서 성능 병목 현상을 식별하고 모니터링하는 방법을 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: nicktrog
author: ntrogh
ms.date: 11/30/2021
ms.topic: tutorial
ms.openlocfilehash: 1c69c9e722803396cc598ef2a8ab59adfcbed864
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133303912"
---
# <a name="tutorial-run-a-load-test-to-identify-performance-bottlenecks-in-a-web-app"></a>자습서: 부하 테스트를 실행하여 웹앱의 성능 병목 현상 식별

이 자습서에서는 Azure Load Testing 미리 보기를 사용하여 웹 애플리케이션에서 성능 병목 현상을 식별하는 방법을 알아봅니다. 샘플 Node.js 애플리케이션에 대한 부하 테스트를 만듭니다.

샘플 애플리케이션은 NoSQL 데이터베이스와 상호 작용하는 Node.js Web API로 구성됩니다. 웹앱을 Azure App Service 웹 API를 배포하고 Cosmos DB를 데이터베이스로 사용합니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * 샘플 앱을 배포합니다.
> * 부하 테스트를 만들고 실행합니다.
> * 앱의 성능 병목 현상을 식별합니다.
> * 병목 현상을 제거합니다.
> * 부하 테스트를 다시 실행하여 성능 향상을 확인합니다.

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 이 자습서에서는 Azure CLI를 로컬로 실행해야 합니다. Azure CLI 버전 2.2.0이 설치되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치하는 방법을](/cli/azure/install-azure-cli)참조하세요.
* [VS Code](https://code.visualstudio.com/Download)를 다운로드하여 설치합니다.
* [Git](https://git-scm.com/download) 다운로드 및 설치

## <a name="deploy-the-sample-app"></a>샘플 앱 배포

샘플 앱을 부하 테스트하기 전에 실행해야 합니다. Azure CLI 명령, Git 명령 및 PowerShell 명령을 사용하여 이를 만듭니다.

1. Windows PowerShell 열고, Azure에 로그인하고, 구독을 설정합니다.  

   ```powershell
   az login
   az account set --subscription <your-Azure-Subscription-ID>
   ```
    
1. 샘플 애플리케이션의 원본 리포지션을 복제합니다.

   ```powershell
    git clone https://github.com/Azure-Samples/nodejs-appsvc-cosmosdb-bottleneck.git
   ```

    샘플 애플리케이션은 Azure App Service 웹 구성 요소와 Cosmos DB 데이터베이스로 구성된 Node.js 앱입니다. 리포지션에는 샘플 앱을 Azure 구독에 배포하는 PowerShell 스크립트가 포함되어 있습니다. 이후 단계에서 사용할 Apache JMeter 스크립트도 있습니다.

1. Node.js 앱의 디렉터리로 이동하여 PowerShell 스크립트를 사용하여 샘플 앱을 배포합니다.

   ```powershell
    cd nodejs-appsvc-cosmosdb-bottleneck
    .\deploymentscript.ps1
   ```
   
   > [!TIP]
   > [Linux/WSL](/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-7.1#ubuntu-1804&preserve-view=true) 또는 [macOS에](/powershell/scripting/install/installing-powershell-core-on-macos?view=powershell-7.1&preserve-view=true)PowerShell Core 설치할 수 있습니다.  
   >
   > 설치한 후 이전 명령을 로 실행할 수 `pwsh ./deploymentscript.ps1` 있습니다.  

1. 프롬프트에서 다음을 제공합니다.

   * Azure 구독 ID.
   * 웹앱의 고유한 이름입니다.
   * 위치 기본적으로 위치는 `eastus` 입니다. [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 명령을 실행하여 지역 코드를 가져올 수 있습니다.

   > [!IMPORTANT]
   > 웹앱 이름의 경우 소문자와 숫자만 사용합니다. 공백 없음 특수 문자가 없습니다.

1. 배포가 완료되면 브라우저 창에서 를 열어 실행 중인 샘플 `https://<yourappname>.azurewebsites.net` 애플리케이션으로 이동합니다.

1. 애플리케이션의 구성 요소를 보려면 [Azure Portal](https://portal.azure.com) 로그인하고 만든 리소스 그룹으로 이동합니다.  

   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/resource-group.png" alt-text="Azure 리소스 그룹 목록을 보여 주는 스크린샷.":::

이제 애플리케이션을 배포하고 실행했으므로 계속 진행하여 첫 번째 부하 테스트 실행을 시작합니다.

## <a name="configure-and-create-the-load-test"></a>부하 테스트 구성 및 만들기

이 섹션에서는 기존 Apache JMeter 테스트 스크립트를 사용하여 부하 테스트를 만듭니다.

### <a name="configure-the-apache-jmeter-script"></a>Apache JMeter 스크립트 구성

샘플 애플리케이션의 소스 리포지션에는 이라는 Apache JMeter 스크립트가 포함되어 `SampleApp.jmx` 있습니다. 이 스크립트는 각 테스트 반복에 대해 세 가지 API 호출을 수행합니다.  

* `add`- 웹앱의 방문자 수에 대해 Cosmos DB에서 데이터 삽입 작업을 수행합니다.
* `get`- Cosmos DB에서 GET 작업을 수행하여 개수를 검색합니다.
* `lasttimestamp` - 마지막 사용자가 웹 사이트로 간 이후 타임스탬프를 업데이트합니다.

이 섹션에서는 이전 섹션에서 배포한 샘플 웹앱의 URL로 Apache JMeter 스크립트를 업데이트합니다.

시작하기 전에 복제된 샘플 앱의 디렉터리를 VS Code 열어야 합니다.

1. VS Code 복제된 샘플 앱의 디렉터리를 엽니다.

    ```powershell
    cd nodejs-appsvc-cosmosdb-bottleneck
    code .
    ```
 
1. `SampleApp.jmx`를 엽니다.

1. `<stringProp name="HTTPSampler.domain">`를 검색합니다.

   파일에 세 개의 `<stringProp name="HTTPSampler.domain">` 인스턴스가 표시됩니다.

1. 값을 세 인스턴스 모두에 새로 배포된 샘플 애플리케이션의 URL로 대체합니다.

   ```xml
   <stringProp name="HTTPSampler.domain">yourappname.azurewebsites.net</stringProp>
   ```

   세 위치 모두의 값을 업데이트합니다. 접두사 포함 안 함 `https://`  

1. 변경 내용을 저장하고 파일을 닫습니다.

### <a name="create-the-azure-load-testing-resource"></a>Azure Load Testing 리소스 만들기

부하 테스트 리소스는 부하 테스트 활동의 최상위 리소스입니다. 이 리소스는 부하 테스트, 테스트 결과 및 기타 관련 아티팩트 보기 및 관리를 위한 중앙 집중식 위치를 제공합니다.

부하 테스트 리소스가 이미 있는 경우 이 섹션을 건너뛰고 [부하 테스트 만들기를](#create_test)계속 진행합니다.

부하 테스트 리소스가 아직 없는 경우 지금 만듭니다.

[!INCLUDE [azure-load-testing-create-portal](../../includes/azure-load-testing-create-in-portal.md)]

다음 섹션에서는 샘플 앱에 대한 부하 테스트 리소스에서 부하 테스트를 만듭니다.

### <a name="create-a-load-test"></a><a name="create_test"></a> 부하 테스트 만들기

1. 부하 테스트 리소스로 이동하고 명령 모음에서 **새 테스트 만들기를** 선택합니다.

   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/create-test.png" alt-text="새 테스트 만들기를 보여 주는 스크린샷" :::

1. 기본 **사항** 탭에서 **테스트 이름** 및 **테스트 설명** 정보를 입력합니다. 필요에 따라 **만든 후 테스트 실행** 상자를 선택하면 부하 테스트를 만든 후 자동으로 시작할 수 있습니다.

   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/create-new-test-basics.png" alt-text="새 테스트를 만들 때 기본 사항 탭을 보여 주는 스크린샷." :::

1. 테스트 **계획** 탭에서 **JMeter 스크립트** 테스트 메서드를 선택한 다음, 복제된 샘플 애플리케이션 디렉터리에서 *SampleApp.jmx* 테스트 스크립트를 선택합니다. **다음으로, 업로드** 선택하여 Azure에 파일을 업로드하고 부하 테스트를 구성합니다.

   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/create-new-test-test-plan.png" alt-text="테스트 계획 탭 및 Apache JMeter 스크립트를 업로드하는 방법을 보여 주는 스크린샷" :::

    필요에 따라 추가 Apache JMeter 구성 파일을 선택하고 업로드할 수 있습니다.

1. **로드** 탭에서 다음 세부 정보를 구성합니다. 이 자습서의 기본값은 그대로 둘 수 있습니다.

    |설정  |값  |설명  |
    |---------|---------|---------|
    |엔진 인스턴스     |1         |Apache JMeter 스크립트를 실행하는 병렬 테스트 엔진의 수입니다. |
    
   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/create-new-test-load.png" alt-text="새 테스트를 만들 때 로드 탭을 보여 주는 스크린샷." :::

1. **모니터링** 탭에서 리소스 메트릭을 모니터링할 애플리케이션 구성 요소를 지정합니다. **추가/수정을** 선택하여 애플리케이션 구성 요소 목록을 관리합니다.

   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/create-new-test-monitoring.png" alt-text="새 테스트를 만들 때 모니터링 탭을 보여 주는 스크린샷." :::

   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/create-new-test-add-resource.png" alt-text="부하 테스트 중에 모니터링할 Azure 리소스를 추가하는 방법을 보여 주는 스크린샷" :::

   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/create-new-test-added-resources.png" alt-text="모니터링할 Azure 리소스 목록이 있는 모니터링 탭을 보여 주는 스크린샷." :::

1. **검토 + 만들기를** 선택하고 모든 설정을 검토한 다음, **만들기를** 선택합니다.

   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/create-new-test-review.png" alt-text="새 테스트를 만들 때 검토 + 만들기 탭을 보여 주는 스크린샷." :::

## <a name="run-the-load-test-in-the-azure-portal"></a>Azure Portal 부하 테스트 실행

이 섹션에서는 Azure Portal 사용하여 이전에 만든 부하 테스트를 수동으로 시작합니다. **만든 후 테스트 실행** 상자를 선택한 경우 테스트가 이미 실행 중입니다.

1. 테스트 **를 선택 하** 여 테스트 목록을 확인 한 다음, 이전에 만든 테스트를 선택 합니다.

   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/test-list.png" alt-text="테스트 목록을 보여 주는 스크린샷" :::

   >[!TIP]
   > 검색 상자와 **시간 범위** 필터를 사용 하 여 테스트 수를 제한할 수 있습니다.

1. 테스트 실행 페이지에서 **실행** 또는 **테스트 실행** 을 선택 합니다.

    :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/test-runs-run.png" alt-text="테스트를 실행 하는 방법을 보여 주는 스크린샷" :::

1. 실행 요약 페이지에서 **실행** 을 선택 하 여 부하 테스트를 시작 합니다. 그러면 테스트 실행 목록이 표시 됩니다.

    :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/test-run-list.png" alt-text="실행 요약 페이지를 보여 주는 스크린샷" :::

    Azure 부하 테스트는 대시보드에 응용 프로그램의 서버 메트릭을 모니터링 하 고 표시 하기 시작 합니다.
    
    테스트를 실행 하는 동안 스트리밍 클라이언트 쪽 메트릭을 볼 수 있습니다. 기본적으로 5 초 마다 결과가 자동으로 새로 고쳐집니다.

    :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/aggregated-by-percentile.png" alt-text="테스트 결과 대시보드를 보여 주는 스크린샷":::

    여러 필터를 적용 하거나 다양 한 백분위 수 결과를 집계 하 여 차트를 사용자 지정할 수 있습니다.

   > [!TIP]
   > **중지** 를 선택 하 여 언제 든 지 Azure Portal UI에서 부하 테스트를 중지할 수 있습니다.

다음 섹션으로 진행 하기 전에 부하 테스트가 완전히 완료 될 때까지 기다립니다.

## <a name="identify-performance-bottlenecks"></a>성능 병목 상태 식별

이 섹션에서는 부하 테스트 결과를 분석 하 여 응용 프로그램의 성능 병목 상태를 식별 합니다. 클라이언트 쪽 메트릭과 서버 쪽 메트릭을 모두 검사 하 여 문제의 근본 원인을 확인 합니다.

1. 먼저 클라이언트 쪽 메트릭을 살펴보세요. 및 api 요청에 대 한 **응답 시간** 메트릭에 대 한 90 번째 백분위 `add` `get` 수는 api 보다 높은 것을 알 수 있습니다 `lasttimestamp` .

    :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/client-side-metrics.png" alt-text="클라이언트 쪽 메트릭을 보여 주는 스크린샷":::
    
    **오류** 에 대 한 유사한 패턴을 볼 수 있습니다 .이 경우 `lasttimestamp` api의 오류는 다른 api 보다 훨씬 작습니다.
    
    :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/client-side-metrics-errors.png" alt-text="오류 차트를 보여 주는 스크린샷":::

    및 api의 결과 `add` 는 `get` 유사 하지만 `lasttimestamp` api는 다르게 동작 합니다. `add`및 api가 모두 `get` 데이터베이스 액세스를 포함 하기 때문에 데이터베이스와 관련 된 원인이 있을 수 있습니다.

1. 이 문제를 자세히 조사 하려면 **서버 쪽 메트릭** 대시보드 섹션까지 아래로 스크롤합니다.

    서버 쪽 메트릭은 Azure 응용 프로그램 구성 요소에 대 한 자세한 정보를 보여 줍니다. Azure App Service 계획, Azure App Service 웹 앱 및 Azure Cosmos DB.

    :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/app-service-metrics-for-load-testing.png" alt-text="Azure App Service 계획 메트릭을 보여 주는 스크린샷":::

    Azure App Service 계획 메트릭에는 **CPU 비율** 및 **메모리 비율** 메트릭이 허용 가능한 범위 내에 있는 것을 확인할 수 있습니다.

1. 이제 Cosmos DB 서버 쪽 메트릭을 살펴보세요.

    :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/cosmos-db-metrics.png" alt-text="Azure Cosmos DB 메트릭을 보여 주는 스크린샷":::
    
    **정규화** 된 실행 메트릭이 데이터베이스가 100% 리소스 사용률에 빠르게 실행 되 고 있음을 알 수 있습니다. 리소스 사용량이 높으면 데이터베이스 제한 오류가 발생 했을 수 있으며 `add` 및 웹 api에 대 한 응답 시간이 증가 했을 수 있습니다 `get` .
    
    Azure Cosmos DB 인스턴스에 대해 **프로 비전 된 처리량** 메트릭의 최대 처리량이 400 RUs 임을 확인할 수도 있습니다. 데이터베이스의 프로 비전 된 처리량을 높여 성능 문제를 해결할 수 있습니다.

다음 섹션에서는 데이터베이스 프로 비전 된 처리량을 늘리고 응용 프로그램 성능 병목 현상이 해결 되었는지 확인 합니다.

## <a name="increase-the-database-throughput"></a>데이터베이스 처리량 증가

이 섹션에서는 성능 병목 상태를 해결 하기 위해 데이터베이스에 더 많은 리소스를 할당 합니다. 

Azure Cosmos DB의 경우 데이터베이스의 크기 조정 설정을 늘립니다.

1. 샘플 응용 프로그램 배포의 일부로 프로 비전 한 Cosmos DB 리소스로 이동 합니다.

1. **데이터 탐색기** 탭을 선택 합니다.

   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/ru-scaling-for-cosmos-db.png" alt-text="Cosmos DB 크기 조정 설정을 보여 주는 스크린샷":::

1. **Scale & 설정** 옵션을 선택 하 고 처리량 값을 *1200* 으로 업데이트 합니다.
 
   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/1200-ru-scaling-for-cosmos-db.png" alt-text="업데이트 된 Cosmos DB 크기 조정 설정을 보여 주는 스크린샷":::

1. **저장** 을 선택하여 변경 내용을 확인합니다.

## <a name="validate-the-performance-improvements"></a>성능 향상의 유효성을 검사 합니다.

데이터베이스 처리량을 늘린 후 부하 테스트를 다시 실행 하 고 성능 결과가 개선 되었는지 확인 합니다.

1. 테스트 실행 세부 정보 페이지로 돌아가서 **다시 실행** 을 선택한 다음 실행 요약 페이지에서 **실행** 을 선택 합니다.

   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/rerun-test.png" alt-text="부하 테스트를 실행 하는 방법을 보여 주는 스크린샷":::

   **프로 비전**, **실행** 중, **완료** 상태를 순환 하는 상태 열이 포함 된 새 테스트 실행 항목이 표시 됩니다. 언제 든 지 테스트 실행 항목을 선택 하 여 부하 테스트가 진행 되는 방법을 모니터링 합니다.

1. 부하 테스트가 완료 되 면 클라이언트 쪽 메트릭의 **응답 시간** 및 **오류** 를 확인 합니다.

1. 그런 다음 Azure Cosmos DB에 대 한 서버 쪽 메트릭을 확인 하 고 성능이 개선 되었는지 확인 합니다.
 
   :::image type="content" source="./media/tutorial-identify-bottlenecks-azure-portal/cosmos-db-metrics-post-run.png" alt-text="크기 조정 설정 업데이트 후 Cosmos DB 클라이언트 쪽 메트릭을 보여 주는 스크린샷":::

    Cosmos DB **정규화** 된 사용은 이제 100% 미만입니다.

데이터베이스의 크기 조정 설정을 변경한 후에는 다음과 같이 표시 됩니다.

* 및 api에 대 한 응답 시간이 `add` `get` 개선 되었습니다.
* 정규화 된 과도 소비는 최대 한도에 잘 적용 됩니다.

결과적으로 응용 프로그램의 전체 성능이 향상 되었습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [alt-delete-resource-group](../../includes/alt-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

Azure Pipelines 또는 GitHub 작업을 사용 하 여 자동화 된 회귀 테스트 워크플로를 설정 하는 방법을 알아보려면 다음 자습서로 이동 합니다.

> [!div class="nextstepaction"]
> [자동화 된 회귀 테스트 설정](./tutorial-cicd-azure-pipelines.md)
