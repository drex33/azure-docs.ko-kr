---
title: 디자이너의 로그 메트릭
titleSuffix: Azure Machine Learning
description: Azure ML 디자이너 실험을 모니터링합니다. Python 스크립트 실행 구성 요소를 사용하여 로깅을 사용하도록 설정하고 스튜디오에서 기록된 결과를 확인합니다.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: ca78f86734c29e0aa6104e43ae759b666df6d290
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560742"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Azure Machine Learning 디자이너 파이프라인에서 로깅 사용


이 문서에서는 디자이너 파이프라인에 로깅 코드를 추가하는 방법에 대해 알아봅니다. 또한 Azure Machine Learning 스튜디오 웹 포털을 사용하여 이러한 로그를 보는 방법을 알아봅니다.

SDK 제작 환경을 사용하여 메트릭을 로깅하는 방법에 대한 자세한 내용은 [Azure ML 실험 실행 및 메트릭 모니터링](how-to-log-view-metrics.md)을 참조하세요.

## <a name="enable-logging-with-execute-python-script"></a>Python 스크립트 실행을 사용하여 로깅을 사용하도록 설정

Python [스크립트 실행](./algorithm-module-reference/execute-python-script.md) 구성 요소를 사용하여 디자이너 파이프라인에서 로깅을 사용하도록 설정합니다. 이 워크플로를 사용하여 모든 값을 기록할 수 있지만 __모델 평가__ 구성 요소의 메트릭을 기록하여 실행 간에 모델 성능을 추적하는 것이 특히 유용합니다.

다음 예제에서는 모델 평가 및 Python 스크립트 실행 구성 요소를 사용하여 학습된 두 모델의 평균 제곱 오차를 기록하는 방법을 보여줍니다.

1. __Python 스크립트 실행__ 구성 요소를 모델 __평가__ 구성 요소의 출력으로 커넥트.

    ![커넥트 Python 스크립트 구성 요소를 실행하여 모델 구성 요소 평가](./media/how-to-log-view-metrics/designer-logging-pipeline.png)

1. 다음 코드를 __Python 스크립트 실행__ 코드 편집기에 붙여넣어 학습된 모델의 평균 절대 오차를 기록합니다. 유사한 패턴을 사용하여 디자이너에서 다른 값을 기록할 수 있습니다.

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model. The following line should be deleted if you only connect one Score component to the` left port of Evaluate Model component.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
이 코드는 Azure Machine Learning Python SDK를 사용하여 값을 기록합니다. Run.get_context()를 사용하여 현재 실행의 컨텍스트를 가져옵니다. 그런 다음, run.parent.log() 메서드를 사용하여 해당 컨텍스트에 값을 기록합니다. 를 사용하여 `parent` 구성 요소 실행이 아닌 부모 파이프라인 실행에 값을 기록합니다.

Python SDK를 사용하여 값을 기록하는 방법에 대한 자세한 내용은 [Azure ML 학습 실행에서 로깅 사용](how-to-log-view-metrics.md)을 참조하세요.

## <a name="view-logs"></a>로그 보기

파이프라인 실행이 완료되면 실험 페이지에서 *Mean_Absolute_Error* 를 확인할 수 있습니다.

1. **실험** 섹션으로 이동합니다.
1. 실험을 선택합니다.
1. 보려는 실험의 실행을 선택합니다.
1. **메트릭** 을 선택합니다.

    ![스튜디오에서 실행 메트릭 보기](./media/how-to-log-view-metrics/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 디자이너에서 로그를 사용하는 방법을 알아보았습니다. 다음 단계에 대해서는 다음의 관련 문서를 참조하세요.


* 디자이너 파이프라인 문제를 해결하는 방법을 알아보려면 [ML 파이프라인 디버그 및 문제 해결](how-to-debug-pipelines.md#azure-machine-learning-designer)을 참조하세요.
* Python SDK를 사용하여 SDK 제작 환경에서 메트릭을 기록하는 방법을 알아보려면 [Azure ML 학습 실행에서 로깅 사용](how-to-log-view-metrics.md)을 참조하세요.
* 디자이너에서 [Python 스크립트 실행](./algorithm-module-reference/execute-python-script.md)을 사용하는 방법을 알아봅니다.
