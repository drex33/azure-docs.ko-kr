---
title: 노트북 작업을 사용 하 여 Synapse 파이프라인 호출
description: 솔루션 템플릿을 사용 하 여 Azure Data Factory에서 노트북 활동으로 Synapse 파이프라인을 호출 하는 방법을 알아봅니다.
ms.author: chugu
author: chugugrace
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: ''
ms.date: 11/23/2021
ms.openlocfilehash: e256b5a47c06e2fd33ebe1cfc5f90ea49993a69b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133077560"
---
# <a name="call-synapse-pipeline-with-a-notebook-activity"></a>노트북 작업을 사용 하 여 Synapse 파이프라인 호출

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 Azure Data Factory의 **웹**, **Until** 및 **실패** 작업을 포함 하는 종단 간 파이프라인을 만듭니다.

- **웹** 에서 노트북 활동을 사용 하 여 Synapse 파이프라인을 호출 합니다.

- **Until** 은 완료 될 때까지 Synapse 파이프라인 상태를 가져옵니다 (상태 출력은 *성공*, *실패* 또는 *취소 됨*).

- **실패** 하면 작업이 실패 하 고 오류 메시지와 오류 코드를 모두 사용자 지정 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [노트북 활동이 있는 Synapse 파이프라인이](https://docs.microsoft.com/azure/synapse-analytics/synapse-notebook-activity)있습니다.
- RBAC (역할 기반 액세스 제어)를 사용 하 여 Data Factory Synapse 작업 영역 끝점을 호출 하는 권한을 부여 합니다. 자세한 내용은 [관리 되는 id](control-flow-web-activity.md#managed-identity)를 참조 하십시오.
- Synapse 파이프라인 실행/실행 Data Factory에 대 한 권한을 부여 합니다. 자세한 내용은 [SYNAPSE RBAC 역할](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-understand-what-role-you-need#tasks-and-required-roles)을 참조 하세요.

## <a name="how-to-use-this-template"></a>이 템플릿을 사용하는 방법 알아보기

1. **노트북 작업 템플릿을 사용 하 여 Synapse 파이프라인 호출** 로 이동 합니다.

1. **이 템플릿 사용** 을 선택합니다. 만들어진 파이프라인을 볼 수 있습니다.

    :::image type="content" source="media/solution-template-synapse-notebook/pipeline.png" alt-text="새 파이프라인":::

## <a name="pipeline-introduction-and-configuration"></a>파이프라인 소개 및 구성

파이프라인의 구성을 검토하고 필요한 변경 작업을 수행합니다.

1. 파이프라인 매개 변수 필요한 경우 설정을 변경합니다.

    - *SynapseEndpoint*: 작업 영역 개발 끝점 (예: https:// `myworkspace` . dev.azuresynapse.net).
    - *Plname*: 실행할 Synapse 작업 영역의 Synapse 파이프라인 이름입니다.
    - *FailedOrCancelledErrorCode*: Synapse 파이프라인이 실패할 경우 오류 코드를 사용자 지정 합니다.
    
        :::image type="content" source="media/solution-template-synapse-notebook/pipeline-parameters.png" alt-text="파이프라인 매개 변수":::

1. Synapse 파이프라인을 호출 하는 **웹** 작업입니다.

    - *URL*: `@Concat(pipeline().parameters.SynapseEndpoint,'/pipelines/',pipeline().parameters.PLName,'/createRun?api-version=2020-12-01')`. [Synapse REST API를 통해 파이프라인 실행 만들기](https://docs.microsoft.com/rest/api/synapse/data-plane/pipeline/create-pipeline-run)를 참조 하세요.
    - *인증*: **관리 id** 는 인증에 사용 됩니다. 필요한 사용 권한을 부여 하려면 [필수 구성 요소](#prerequisites) 를 참조 하세요.
    - *리소스* : `https://dev.azuresynapse.net/` .
    
        :::image type="content" source="media/solution-template-synapse-notebook/web-call-synapse-pipeline-setting.png" alt-text="Synapse 파이프라인 호출":::

1. 웹 활동이 *성공*, *실패* 또는 *취소 됨* 상태를 반환할 **때까지** **웹** 활동에서 Synapse 파이프라인 상태를 가져옵니다.
    - **웹** *URL*: `@concat(pipeline().parameters.SynapseEndpoint,'/pipelineruns/',activity('Call Synapse Pipeline with Notebook').output.runId,'?api-version=2020-12-01')` [Synapse Get 파이프라인 실행 REST API](https://docs.microsoft.com/rest/api/synapse/data-plane/pipeline-run/get-pipeline-run)을 참조 하세요.
    
        :::image type="content" source="media/solution-template-synapse-notebook/get-pipeline-status.png" alt-text="파이프라인 상태 가져오기":::
    
    - **Until** *식*: `@or(or(equals('Succeeded',activity('Get run status').output.status),equals('Failed',activity('Get run status').output.status)),equals('Cancelled',activity('Get run status').output.status))` .
    
        :::image type="content" source="media/solution-template-synapse-notebook/until-setting.png" alt-text="설정 될 때까지":::

1. **If Condition** 활동은 파이프라인 실행 상태를 평가 합니다.
    - *식*": `@equals('Failed',activity('Get run status').output.status)`

    :::image type="content" source="media/solution-template-synapse-notebook/if-condition.png" alt-text="If 조건":::

1. 파이프라인 **실패 작업입니다** .
    - *실패 메시지*: `@activity('Get run status').output.message`
    - *오류 코드*: `@pipeline().parameters.FailedOrCancelledErrorCode`

        :::image type="content" source="media/solution-template-synapse-notebook/fail-activity.png" alt-text="파이프라인 실패":::

## <a name="next-steps"></a>다음 단계

- [템플릿 개요](solution-templates-introduction.md)