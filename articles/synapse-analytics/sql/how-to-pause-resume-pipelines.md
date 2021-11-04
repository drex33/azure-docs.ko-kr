---
title: Synapse 파이프라인을 사용하여 전용 SQL 풀을 일시 중지하고 다시 시작하는 방법
description: Azure Synapse Analytics에서 Synapse 파이프라인을 사용하여 전용 SQL 풀에 대한 일시 중지 및 다시 시작을 자동화하는 방법을 알아봅니다.
author: julieMSFT
ms.author: jrasnick
ms.service: synapse-analytics
ms.reviewer: wiassaf
ms.subservice: sql
ms.topic: how-to
ms.date: 08/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 60425a0915921632874620ef5a1faf0d3ba22115
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013121"
---
# <a name="pause-and-resume-dedicated-sql-pools-with-synapse-pipelines"></a>Synapse 파이프라인을 사용하여 전용 SQL 풀 일시 중지 및 다시 시작

Azure Synapse Analytics에서 Synapse 파이프라인을 사용하여 전용 SQL 풀에 대한 일시 중지 및 다시 시작을 자동화할 수 있습니다. 일시 중지 및 다시 시작을 사용하여 전용 SQL 풀의 비용을 절감할 수 있습니다. 이 솔루션은 기존 데이터 오케스트레이션 프로세스에 쉽게 포함할 수 있습니다. 

다음 단계에서는 자동 일시 중지 및 다시 시작을 설정하는 과정을 안내합니다.

1. 파이프라인을 만듭니다.
1. 파이프라인에서 매개 변수를 설정합니다.
1. Azure Synapse 작업 영역에서 전용 SQL 풀 목록을 식별합니다.
1. 일시 중지하거나 다시 시작하지 않으려는 전용 SQL 풀을 목록에서 필터링합니다. 
1. 각 전용 SQL 풀을 반복하고 다음을 수행합니다.
    1. 전용 SQL 풀의 상태를 확인합니다.
    1. 전용 SQL 풀의 상태를 평가합니다.
    1. 전용 SQL 풀을 일시 중지하거나 다시 시작합니다.

이러한 단계는 Azure Synapse의 간단한 파이프라인에 배치됩니다.

![단순 Synapse 파이프라인](./media/how-to-pause-resume-pipelines/simple-pipeline.png)


사용자 환경의 특성에 따라 여기에 설명된 전체 프로세스는 적용되지 않을 수 있으며 적절한 단계를 선택하는 것이 좋습니다. 여기에 설명된 프로세스를 사용하여 개발, 테스트 또는 PoC 환경의 모든 인스턴스를 일시 중지하거나 다시 시작할 수 있습니다. 프로덕션 환경의 경우 인스턴스에 대해 일시 중지 또는 다시 시작을 예약하는 것이 좋습니다. 그러면 5a~5c단계만 필요합니다.

위의 단계에서는 Azure Synapse 및 Azure SQL에 대한 REST API를 사용합니다.

- [전용 SQL 풀 작업](/rest/api/synapse/sqlpools)
 
- [Azure SQL Database REST API](/rest/api/sql)

Synapse 파이프라인을 사용하면 일시 중지 및 다시 시작을 자동화할 수 있지만 원하는 도구 또는 애플리케이션을 통해 요청 시 이러한 명령을 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 기존 [Azure Synapse 작업 영역](../get-started-create-workspace.md)
- 하나 이상의 [전용 SQL 풀](../get-started-analyze-sql-pool.md)
- 작업 영역에 Azure 기여자 역할이 할당되어야 합니다. [Synapse 관리자에게 작업 영역에 대한 Azure 기여자 역할 부여](../security/how-to-set-up-access-control.md#step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace)를 참조하세요.

## <a name="step-1-create-a-pipeline-in-synapse-studio"></a>1단계: Synapse Studio에서 파이프라인 만들기
1. 작업 영역으로 이동하여 Synapse Studio를 엽니다. 
1. **통합** 아이콘을 선택한 다음 **+** 기호를 선택하여 새 파이프라인을 만듭니다. 
1. 파이프라인 이름을 PauseResume으로 지정합니다.

    ![Synapse Studio 파이프라인 만들기](./media/how-to-pause-resume-pipelines/create-pipeline.png) 

## <a name="step-2-create-pipeline-parameters"></a>2단계: 파이프라인 매개 변수 만들기 

만들 파이프라인은 매개 변수를 구동합니다. 매개 변수를 사용하면 여러 구독, 리소스 그룹 또는 전용 SQL 풀에서 사용할 수 있는 일반 파이프라인을 만들 수 있습니다. 파이프라인 화면 하단 근처에 있는 **매개 변수** 탭을 선택합니다. **+새로 만들기** 를 선택하여 다음 매개 변수를 각각 만듭니다.

    
|이름  |Type  |기본값  |Description|
|---------|---------|---------|-----------|
|ResourceGroup    |문자열        |Synapse          |전용 SQL 풀에 대한 리소스 그룹의 이름|
|구독 ID   |문자열        |`<SubscriptionID>` |리소스 그룹의 구독 ID|
|WorkspaceName    |문자열        |Synapse          |작업 영역의 이름|
|SQLPoolName      |문자열        |SQLPool1         |전용 SQL 풀 보호의 이름|
|PauseorResume    |문자열        |일시 중지            |파이프라인 실행이 끝날 때 원하는 상태|

![Synapse Studio의 파이프라인 매개 변수.](./media/how-to-pause-resume-pipelines/pipeline-parameters.png)

## <a name="step-3-create-list-of-dedicated-sql-pools"></a>3단계: 전용 SQL 풀 목록 만들기
 
 **웹** 작업을 설정하고 이 작업을 사용하여 전용 SQL 풀 - 서버 REST API별 나열 요청을 호출하여 전용 SQL 풀 목록을 만듭니다. 출력은 작업 영역의 전용 SQL 풀 목록을 포함하는 JSON 문자열입니다. JSON 문자열은 다음 작업에 전달됩니다.
1. **작업** > **일반** 에서 파이프라인의 첫 번째 단계로 **웹** 작업을 파이프라인 캔버스로 끌어옵니다.  
1. **일반** 탭에서 이 단계의 이름을 가져오기 목록으로 지정합니다. 
1. **설정** 탭을 선택한 다음 **URL** 입력 공간을 클릭한 다음 **동적 콘텐츠 추가** 를 선택합니다. 아래의 @concat 문자열 함수를 사용하여 매개 변수화된 가져오기 요청을 복사하여 동적 콘텐츠 상자에 붙여넣습니다. **마침** 을 선택합니다.
다음 코드는 간단한 가져오기 요청입니다.

    ```HTTP
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools?api-version=2019-06-01-preview
    ```
    
    @concat 문자열 함수를 사용하여 매개 변수화된 가져오기 요청:
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools?api-version=2019-06-01-preview')
    ``` 
1. **메서드** 드롭다운을 선택하고 **가져오기** 를 선택합니다.  
1. 콘텐츠를 확장하려면 **고급** 을 선택합니다. 인증 유형으로 **MSI** 를 선택합니다. 리소스에 `https://management.azure.com/`을 입력합니다. 
    > [!IMPORTANT]
    > 모든 웹 작업/REST API 웹 호출의 경우 전용 SQL 풀에 대해 Synapse 파이프라인이 인증되었는지 확인해야 합니다. 이러한 REST API 호출을 실행하려면 [관리 ID](../../data-factory/control-flow-web-activity.md#managed-identity)가 필요합니다. 
        
        
    ![전용 SQL 풀의 웹 작업 목록](./media/how-to-pause-resume-pipelines/web-activity-list-sql-pools.png)



## <a name="step-4-filter-dedicated-sql-pools"></a>4단계: 전용 SQL 풀 필터링
일시 중지하거나 다시 시작하지 않으려는 전용 SQL 풀을 제거합니다. 목록 가져오기 작업에서 전달된 값을 필터링하는 필터 작업을 사용합니다.  이 예에서는 이름에 "prod"가 없는 배열에서 레코드를 추출합니다. 필요에 따라 다른 조건을 적용합니다. 예를 들어 Synapse 작업 영역의 sku/이름을 필터링하여 유효한 전용 SQL 풀만 식별되도록 합니다.
1. **필터** 작업을 **반복 및 조건** 을 파이프라인 캔버스에 추가합니다.    
![전용 SQL 풀 필터링](./media/how-to-pause-resume-pipelines/filter-sql-pools.png)    
1. 목록 웹 가져오기 작업을 필터 작업에 연결합니다. 웹 작업에서 녹색 탭을 선택하고 필터 상자로 끕니다.
1. **항목** 에 `@activity('Get list').output.value`를 입력합니다. 여기서 가져오기 목록은 이전 웹 작업의 이름입니다.
1. **조건** 에 `@not(endswith(item().name,'prod'))`를 입력합니다. 그런 다음 배열의 나머지 레코드가 다음 작업으로 전달됩니다.

## <a name="step-5-create-a-foreach-loop"></a>5단계: ForEach 루프 만들기
ForEach 작업을 만들어 각 전용 SQL 풀을 반복합니다. 
1. **ForEach** 작업을 **반복 및 조건** 을 파이프라인 캔버스에 추가합니다.
1. **일반** 탭에서 작업 이름을 지정하고 'ForEach_pool'을 사용했습니다. 
1. 설정 탭에서 **항목** 입력을 선택하고 **동적 콘텐츠 추가** 를 선택합니다. **작업 출력** 으로 스크롤하고 필터 작업에서 출력을 선택합니다. 작업에 `.value`를 추가합니다. 값은 `@activity('Filter_PROD').output.value`와 유사해야 합니다. **마침** 을 선택합니다.    
    ![전용 SQL 풀을 통한 루프](./media/how-to-pause-resume-pipelines/loop-through-sql-pools.png)
1. **작업** 탭을 선택하고 편집 연필을 선택하여 ForEach 루프 캔버스를 엽니다. 

## <a name="step-5a-check-the-state-of-the-dedicated-sql-pools"></a>5a단계: 전용 SQL 풀의 상태 확인    
전용 SQL 풀의 상태를 확인하려면 1단계와 유사한 웹 작업이 필요합니다. 이 작업은 [Azure Synapse용 전용 SQL 풀 상태 REST API 확인](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md#check-database-state)을 호출합니다. 
1. **일반** 아래에서 **웹** 작업을 선택하고 파이프라인 캔버스로 끕니다.    
2. **일반** 탭에서 이 단계의 이름을 CheckState로 지정합니다. 
3. **설정** 탭을 선택합니다.     
4. **URL** 입력 공간을 클릭한 다음 **동적 콘텐츠 추가** 를 선택합니다. 아래에서 @concat 문자열 함수를 사용하여 매개 변수화된 가져오기 요청을 복사하고 동적 콘텐츠 상자에 붙여넣습니다. **마침** 을 선택합니다. 다음 호출을 통해 가져오기 요청을 사용하여 상태를 다시 확인합니다.

    ```HTTP
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}?api-version=2019-06-01-preview HTTP/1.1
    ```
    
    @concat 문자열 함수를 사용하여 가져오기 요청을 매개 변수화합니다.
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',item().name,'?api-version=2019-06-01-preview')
    ```
    
    이 경우 ForEach 루프에서 이 작업에 전달된 1단계의 전용 SQL 풀 이름인 item().name을 사용합니다. 파이프라인을 사용하여 단일 전용 SQL 풀을 제어하는 경우 전용 SQL 풀의 이름을 여기에 포함하거나 파이프라인의 매개 변수를 사용할 수 있습니다. 예를 들어 pipeline().parameters.SQLPoolName을 사용할 수 있습니다.

    출력은 상태(properties.status)를 포함하여 전용 SQL 풀의 세부 정보를 포함하는 JSON 문자열입니다. JSON 문자열은 다음 작업에 전달됩니다. 
1. **메서드** 드롭다운을 선택하고 **가져오기** 를 선택합니다. **고급** 을 선택하여 콘텐츠를 확장합니다. 인증 유형으로 **MSI** 를 선택합니다. 리소스에 `https://management.azure.com/`을 입력합니다. 

![전용 SQL 풀의 상태를 확인합니다.](./media/how-to-pause-resume-pipelines/check-sql-pool-state.png)
    

    
## <a name="step-5b-evaluate-the-state-of-the-dedicated-sql-pools"></a>5b단계: 전용 SQL 풀의 상태 평가
원하는 상태, 일시 중지 또는 다시 시작, 현재 상태, 온라인 또는 일시 중지됨을 평가한 다음 필요에 따라 일시 중지 또는 다시 시작을 시작합니다.

1. **스위치** 작업을 선택하고 **반복 및 조건** 을 파이프라인 캔버스에 추가합니다.    
1. **스위치** 작업을 **CheckState** 작업에 연결합니다. 웹 작업에서 녹색 탭을 선택하고 스위치 상자로 끌어 놓습니다.  
1. **일반** 탭에서 이 단계의 이름을 State-PauseOrResume으로 지정합니다. 

    원하는 상태와 현재 상태에 따라 다음 두 가지 조합만 상태 변경이 필요합니다. 일시 중지됨->다시 시작 또는 온라인->일시 중지. 

1. **작업** 탭에서 아래 코드를 **표현식** 에 복사합니다.

    ```HTTP
    @concat(activity('CheckState').output.properties.status,'-',pipeline().parameters.PauseOrResume)
    ```
    
    여기서 상태 확인은 현재 상태를 정의하는 output.properties.status와 원하는 상태를 나타내는 pipeline().parameters.PauseOrResume이 있는 이전 웹 활동의 이름입니다.
    
    확인 조건은 원하는 상태 및 현재 상태를 확인합니다. 원하는 상태가 다시 시작이고 현재 상태가 일시 중지됨인 경우 다시 시작 작업이 일시 중지됨-다시 시작 사례 내에서 호출됩니다. 원하는 상태가 일시 중지이고 현재 상태가 온라인인 경우 일시 중지 작업이 온라인-일시 중지 사례와 함께 호출됩니다. 원하는 상태의 일시 중지 및 현재 상태의 일시 중지 또는 원하는 상태의 다시 시작 및 현재 상태와 같은 다른 모든 경우에는 작업이 필요하지 않으며 활동이 없는 기본 사례에 의해 처리됩니다.
1. 작업 탭에서 **+사례 추가** 를 선택합니다.  사례 `Paused-Resume` 및 `Online-Pause`를 추가합니다. 
    ![전용 SQL 풀 상태 조건 확인](./media/how-to-pause-resume-pipelines/check-condition.png)

### <a name="step-5c-pause-or-resume-dedicated-sql-pools"></a>5c단계: 전용 SQL 풀 일시 중지 또는 다시 시작     

일부 요구 사항에 대한 마지막이자 유일한 관련 단계는 전용 SQL 풀의 일시 중지 또는 다시 시작을 시작하는 것입니다. 이 단계에서는 다시 웹 작업을 사용하여 [Azure Synapse용 REST API 일시 중지 또는 다시 시작](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md#pause-compute)을 호출합니다. 
1. 작업 편집 연필을 선택하고 State-PauseorResume 캔버스에 **웹** 작업을 추가합니다. 
1. **설정** 탭을 선택한 다음 **URL** 입력 공간을 클릭한 다음 **동적 콘텐츠 추가** 를 선택합니다. 아래의 @concat 문자열 함수를 사용하여 매개 변수화된 POST 요청을 복사하여 동적 콘텐츠 상자에 붙여넣습니다. **마침** 을 선택합니다. 
1. **메서드** 드롭다운을 선택하고 **게시** 를 선택합니다.
1. 본문 섹션에 "일시 중지 및 다시 시작"을 입력합니다.
1. 콘텐츠를 확장하려면 **고급** 을 선택합니다. 인증 유형으로 **MSI** 를 선택합니다. 리소스에 `https://management.azure.com/`을 입력합니다. 
1. 아래 매개 변수가 있는 코드를 사용하여 다시 시작 기능에 대한 두 번째 작업을 추가합니다.

    ![전용 SQL 풀 다시 시작](./media/how-to-pause-resume-pipelines/true-condition-resume.png)
    
    
    여기에 있는 예에서는 다음 호출을 사용하여 POST 요청을 호출하는 전용 SQL 풀을 다시 시작합니다.
    
    ```HTTP
    POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}/resume?api-version=2019-06-01-preview HTTP/1.1
    ```
    
    @concat 문자열 함수를 사용하여 위에서 POST 문을 매개 변수화할 수 있습니다.
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',activity('CheckState').output.name,'/resume?api-version=2019-06-01-preview')
    ```
    
    이 경우 스위치 조건을 통해 이 작업에 전달된 3a단계에서 전용 SQL 풀의 이름과 함께 'Check State'.output.name 작업을 사용합니다. 단일 데이터베이스에 대해 단일 작업을 사용하는 경우 여기에 전용 SQL 풀의 이름을 포함하거나 파이프라인의 매개 변수를 사용할 수 있습니다. 예를 들어 pipeline().parameters.DatabaseName을 사용할 수 있습니다.
    
    전용 SQL 풀을 일시 중지하는 POST 요청은 다음과 같습니다.
    
    ```HTTP
    POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}/pause?api-version=2019-06-01-preview HTTP/1.1    
    ```    
    
    POST 요청은 다음과 같이 @concat 문자열 함수를 사용하여 매개 변수화할 수 있습니다.
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',activity('CheckState').output.name,'/pause?api-version=2019-06-01-preview')
    ```

## <a name="pipeline-run-output"></a>파이프라인 실행 출력

전체 파이프라인이 실행되면 아래에 나열된 출력이 표시됩니다. **디버그** 모드를 선택하거나 **트리거 추가** 를 선택하여 파이프라인을 실행할 수 있습니다. 아래 파이프라인 결과의 경우 "ResourceGroup"이라는 파이프라인 매개 변수가 두 개의 Synapse 작업 영역이 있는 단일 리소스 그룹으로 설정되었습니다. 하나는 testprod라는 이름이 지정되고 필터링되었으며 두 번째는 test1이라는 이름이 지정되었습니다. test1 전용 SQL 풀이 일시 중지되었으므로 작업에서 다시 시작을 시작했습니다.

![파이프라인 실행 출력](./media/how-to-pause-resume-pipelines/pipeline-run-output.png)

## <a name="save-your-pipeline"></a>파이프라인 저장

파이프라인을 저장하려면 파이프라인 위에 있는 **모두 게시** 를 선택합니다.

## <a name="schedule-your-pause-or-resume-pipeline-to-run"></a>일시 중지 또는 다시 시작 파이프라인을 실행하도록 예약합니다.

파이프라인을 예약하려면 파이프라인 상단에서 **트리거 추가** 를 선택합니다. 화면을 따라 지정된 시간에 실행되도록 파이프라인을 예약합니다.

![파이프라인 실행 시간을 설정하려면 트리거를 선택합니다.](./media/how-to-pause-resume-pipelines/trigger.png)

## <a name="next-steps"></a>다음 단계

Azure Synapse의 관리 ID 및 전용 SQL 풀에 관리 ID를 추가하는 방법에 대한 자세한 내용은 여기에서 찾을 수 있습니다.

[Azure Synapse 작업 영역 관리 ID](../security/synapse-workspace-managed-identity.md)

[작업 영역 관리 ID에 권한 부여](../security/how-to-grant-workspace-managed-identity-permissions.md)
