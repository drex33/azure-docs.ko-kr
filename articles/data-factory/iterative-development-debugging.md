---
title: Azure Data Factory에서의 반복적인 개발 및 디버깅
titleSuffix: Azure Data Factory & Azure Synapse
description: ADF UX에서 Data Factory 파이프라인을 반복적으로 개발하고 디버그하는 방법을 알아봅니다.
ms.date: 04/21/2021
ms.topic: conceptual
ms.service: data-factory
ms.subservice: authoring
ms.custom: synapse
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: kromerm
ms.author: makromer
ms.openlocfilehash: 39cce38b9b00201ae6fc3e24eff9d5816b69ed69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642266"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Azure Data Factory를 사용한 반복적인 개발 및 디버깅
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory를 사용하면 데이터 통합 솔루션을 개발하는 동안 Data Factory 파이프라인을 반복적으로 개발하고 디버그할 수 있습니다. 해당 기능을 통해 끌어오기 요청을 만들거나 데이터 팩터리 서비스에 게시하기 전에 변경 내용을 테스트할 수 있습니다. 

이 기능에 대한 소개 및 데모는 다음 비디오(8분)를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>파이프라인 디버깅

파이프라인 캔버스를 사용하여 제작하는 경우 **디버그** 기능을 통해 활동을 테스트할 수 있습니다. 테스트 실행을 수행할 때 **디버그** 를 선택하기 전에 데이터 팩터리에 변경 사항을 게시할 필요가 없습니다. 이 기능은 데이터 팩터리 워크플로를 업데이트하기 전에 변경 내용이 예상대로 작동하는지 확인하려는 시나리오에서 유용합니다.

![파이프라인 캔버스의 디버그 기능](media/iterative-development-debugging/iterative-development-1.png)

파이프라인이 실행 중일 때 파이프라인 캔버스의 **출력** 탭에서 각 작업의 결과를 볼 수 있습니다.

파이프라인 캔버스의 **출력** 창에서 테스트 실행 결과를 봅니다.

![파이프라인 캔버스의 출력 창](media/iterative-development-debugging/iterative-development-2.png)

테스트 실행이 성공한 후에는 파이프라인에 작업을 더 추가하고 반복적으로 디버깅을 계속합니다. 테스트 실행을 진행 중에도 **취소** 할 수 있습니다.

> [!IMPORTANT]
> **디버그** 를 선택하면 실제로 파이프라인이 실행됩니다. 예를 들어 파이프라인에 복사 작업이 포함된 경우 테스트 실행은 원본에서 대상으로 데이터를 복사합니다. 따라서 디버깅하는 경우 복사 작업 및 다른 작업에 테스트 폴더를 사용하는 것이 좋습니다. 파이프라인을 디버깅한 후 정상 작업에서 사용할 실제 폴더로 전환하십시오.

### <a name="setting-breakpoints"></a>중단점 설정

Azure Data Factory를 통해 파이프라인 캔버스의 특정 작업에 도달할 때까지 파이프라인을 디버그할 수 있습니다. 테스트하려는 작업까지 중단점을 설정하고 **디버그** 를 선택합니다. Data Factory는 파이프라인 캔버스의 중단점 작업까지만 테스트가 실행되도록 합니다. *Debug Until* 기능은 전체 파이프라인을 테스트하지 않고 파이프라인 내부의 일부 작업만 테스트하려는 경우에 유용합니다.

![파이프라인 캔버스의 중단점](media/iterative-development-debugging/iterative-development-3.png)

중단점을 설정하려면 파이프라인 캔버스에서 요소를 선택합니다. *다음까지 디버그* 옵션이 요소의 오른쪽 위 모서리에 빈 빨간색 원으로 표시됩니다.

![선택된 요소에 중단점을 설정하기 전에](media/iterative-development-debugging/iterative-development-4.png)

*다음까지 디버그* 옵션을 선택하면 중단점이 설정되었음을 나타내기 위해 채워진 빨간색 원으로 바뀝니다.

![선택된 요소에 중단점을 설정한 후](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>디버그 실행 모니터링

파이프라인 디버그 실행을 실행하면 파이프라인 캔버스의 **출력** 창에 결과가 표시됩니다. 출력 탭에는 현재 브라우저 세션 중에 발생한 가장 최근 실행만이 포함됩니다. 

![파이프라인 캔버스의 출력 창](media/iterative-development-debugging/iterative-development-2.png)

디버그 실행의 기록 보기를 확인하거나 모든 활성 디버그 실행 목록을 보려면 **모니터** 환경으로 이동합니다. 

![활성 디버그 실행 보기 아이콘 선택](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> Azure Data Factory 서비스는 15일간 디버그 실행 기록만 유지합니다. 

## <a name="debugging-mapping-data-flows"></a>매핑 데이터 흐름 디버깅

데이터 흐름을 매핑하면 대규모로 실행되는 코드 없는 데이터 변환 논리를 빌드할 수 있습니다. 논리를 빌드할 때 디버그 세션을 켜고 라이브 Spark 클러스터를 사용하여 대화형으로 데이터 작업을 수행할 수 있습니다. 자세히 알아보려면 [매핑 데이터 흐름 디버그 모드](concepts-data-flow-debug-mode.md)를 참조하세요.

**모니터** 환경에서 팩터리 전체의 활성 데이터 흐름 디버그 세션을 모니터링할 수 있습니다.

![데이터 흐름 디버그 세션 보기](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

데이터 흐름 디자이너의 데이터 미리 보기 및 데이터 흐름의 파이프라인 디버깅은 데이터 샘플의 양이 적어도 가장 잘 작동하도록 설계되었습니다. 그러나 많은 양의 데이터에 대한 파이프라인 또는 데이터 흐름에서 논리를 테스트해야 하는 경우 코어를 더 많이 사용하고 범용 컴퓨팅은 최소한으로 사용하여 디버그 세션에서 사용되는 Azure Integration Runtime의 크기를 늘립니다.
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>데이터 흐름 작업을 사용하여 파이프라인 디버깅

데이터 흐름을 사용하여 디버그 파이프라인을 실행하는 경우 사용할 수 있는 두 가지 컴퓨팅 옵션이 있습니다. 기존 디버그 클러스터를 사용하거나 데이터 흐름에 대한 새로운 Just-In-Time 클러스터를 설정할 수 있습니다.

기존 디버그 세션을 사용하는 경우 클러스터가 이미 실행 중이기 때문에 데이터 흐름 시작 시간이 크게 줄어들지만, 여러 작업이 한 번에 실행되면 실패할 수도 있으므로 복잡한 워크로드나 병렬 워크로드에는 사용하지 않는 것이 좋습니다.

작업 런타임을 사용하면 각 데이터 흐름 작업의 통합 런타임에 지정된 설정을 사용하여 새 클러스터를 만듭니다. 이렇게 하면 각 작업을 격리할 수 있으며 복잡한 워크로드나 성능 테스트에는 이 방법을 사용해야 합니다. 또한 디버깅에 사용되는 클러스터 리소스를 디버깅하는 동안 추가 작업 요청 처리에 계속 사용할 수 있도록 Azure IR의 TTL을 제어할 수 있습니다.

> [!NOTE]
> 병렬로 실행되는 데이터 흐름 또는 대량 데이터 세트를 사용하여 테스트해야 하는 데이터 흐름을 실행하는 파이프라인이 있는 경우 “작업 런타임 사용”을 선택하여 Data Factory가 데이터 흐름 작업에서 선택한 Integration Runtime을 사용할 수 있도록 합니다. 이렇게 하면 데이터 흐름이 여러 클러스터에서 실행될 수 있으며 병렬 데이터 흐름 실행도 수용할 수 있습니다.

![데이터 흐름을 사용하여 파이프라인 실행](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>다음 단계

변경을 테스트한 다음에는 [Azure Data Factory에서 연속 통합 및 배포](continuous-integration-deployment.md)를 사용하여 한층 더 수준 높은 환경으로 승격합니다.
