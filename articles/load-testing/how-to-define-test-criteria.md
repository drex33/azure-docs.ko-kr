---
title: 부하 테스트 통과/실패 조건 정의
titleSuffix: Azure Load Testing
description: Azure 부하 테스트를 사용 하 여 부하 테스트에 대 한 통과/실패 조건을 구성 하는 방법을 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: ninallam
author: ninallam
ms.date: 11/30/2021
ms.topic: how-to
ms.openlocfilehash: b5f670a75b738c01af640dab1c4f5289ce7b4859
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133305580"
---
# <a name="define-test-criteria-for-load-tests-with-azure-load-testing-preview"></a>Azure 부하 테스트 미리 보기를 사용 하 여 부하 테스트에 대 한 테스트 조건 정의

이 문서에서는 Azure 부하 테스트 미리 보기를 사용 하 여 부하 테스트에 대 한 통과/실패 조건을 정의 하는 방법을 알아봅니다. 

테스트 조건을 사용 하 여 테스트 중인 응용 프로그램의 성능 요구 사항을 지정할 수 있습니다. Azure 부하 테스트 서비스를 사용 하면 다양 한 테스트 메트릭에 대 한 오류 조건을 설정할 수 있습니다.

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소  

- 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.  
- Azure 부하 테스트 리소스입니다. Azure 부하 테스트 리소스를 만들어야 하는 경우 빠른 시작 [부하 테스트 만들기 및 실행](./quickstart-create-and-run-load-test.md)을 참조 하세요.  

## <a name="load-test-passfail-criteria"></a>부하 테스트 통과/실패 조건

이 섹션에서는 Azure 부하 테스트 통과/실패 조건을 정의 하는 구문에 대해 알아봅니다.

다음 구문을 사용 하 여 Azure 부하 테스트에서 테스트 조건을 정의 `Aggregate_function (client_metric) condition value` 합니다. 조건이 true로 평가 되 면 부하 테스트에서 실패 상태를 가져옵니다.

|매개 변수  |설명  |
|---------|---------|
|`Client metric`     | **(필수)** 조건을 적용 해야 하는 클라이언트 메트릭입니다.  |
|`Aggregate function`     |  **(필수)** 클라이언트 메트릭에 적용할 집계 함수입니다.  |
|`Condition`     | **(필수)** 비교 연산자입니다.        |
|`Threshold`     |  **(필수)** 클라이언트 메트릭과 비교할 숫자 값입니다.<BR>임계값은 집계 된 값을 기준으로 평가 됩니다. |

다음 매개 변수 조합이 지원 됩니다.

|메트릭  |집계 함수  |임계값  |조건  |
|---------|---------|---------|---------|
|*`response_time_ms`*     |  *`avg`* 평균적       | 밀리초 수를 나타내는 정수 값입니다.     |   *`>`* (보다 큼)      |
|*`error`*     |  *`percentage`*       | 백분율을 나타내는 0-100 범위의 숫자 값입니다.      |   *`>`* (보다 큼)      |

## <a name="define-test-passfail-criteria-in-the-azure-portal"></a>Azure Portal에서 테스트 통과/실패 조건을 정의 합니다.

이 섹션에서는 Azure Portal에서 부하 테스트에 대 한 테스트 조건을 구성 하는 방법을 알아봅니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure 부하 테스트 리소스로 이동 합니다.

1. 테스트 **를 선택 하** 여 테스트 목록을 확인 한 다음 테스트를 선택 합니다.

    :::image type="content" source="media/how-to-define-test-criteria/configure-test.png" alt-text="부하 테스트를 구성 하는 방법을 보여 주는 스크린샷":::

1. **테스트 조건** 탭을 선택 합니다.

    :::image type="content" source="media/how-to-define-test-criteria/configure-test-test-criteria.png" alt-text="부하 테스트 조건 탭을 보여 주는 스크린샷":::

1. **메트릭**, **집계 함수**, **조건** 및 **임계값** 을 선택 합니다.

    :::image type="content" source="media/how-to-define-test-criteria/test-creation-criteria.png" alt-text="부하 테스트에 테스트 조건을 추가 하는 방법을 보여 주는 스크린샷":::

    부하 테스트에 대해 최대 10 개의 테스트 조건을 정의할 수 있습니다. 동일한 클라이언트 메트릭에 대해 여러 조건이 있는 경우 임계값이 가장 낮은 조건이 사용 됩니다.

1. **적용** 을 선택하여 변경 내용을 저장합니다.

부하 테스트를 실행 하는 경우 Azure 부하 테스트는 업데이트 된 테스트 구성을 사용 합니다. 테스트 실행 대시보드는 테스트 조건을 표시 하 고 테스트 결과가 조건을 통과 하거나 실패 하는 경우를 나타냅니다.

:::image type="content" source="media/how-to-define-test-criteria/test-criteria-dashboard.png" alt-text="부하 테스트 대시보드의 테스트 조건을 보여 주는 스크린샷":::
 
## <a name="define-test-passfail-criteria-in-cicd-workflows"></a>CI/CD 워크플로에서 테스트 통과/실패 조건을 정의 합니다.

이 섹션에서는 CI/CD 워크플로에 대 한 부하 테스트 통과/실패 조건을 정의 하는 방법을 알아봅니다. CI/CD 워크플로에서 부하 테스트를 실행 하려면 [Yaml 테스트 구성 파일](./reference-test-config-yaml.md)을 사용 합니다. 

1. YAML 테스트 구성 파일을 엽니다.

1. 구성 파일에 테스트 조건을 추가 합니다. YAML 구문에 대 한 자세한 내용은 [테스트 구성 YAML 참조](./reference-test-config-yaml.md)를 참조 하세요.

    ```yml
    failureCriteria: 
        - avg(response_time_ms) > 300
        - percentage(error) > 20
    ```

1. YAML 구성 파일을 저장 합니다.

CI/CD 워크플로가 부하 테스트를 실행 하면 워크플로 상태는 통과/실패 조건의 상태를 반영 합니다. CI/CD 로깅 정보는 각 테스트 조건의 상태를 표시 합니다.

:::image type="content" source="media/how-to-define-test-criteria/azure-pipelines-log.png" alt-text="CI/CD 워크플로 로그의 테스트 조건을 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

- 부하 테스트를 매개 변수화 하는 방법에 대 한 자세한 내용은 [부하 테스트 매개 변수화](./how-to-parameterize-load-tests.md)를 참조 하세요.

- 성능 테스트 자동화에 대 한 자세한 내용은 [자동화 된 성능 테스트 구성](./tutorial-cicd-azure-pipelines.md) 을 참조 하세요.
