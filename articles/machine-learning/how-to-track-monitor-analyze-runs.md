---
title: 실행 추적, 모니터링 및 분석
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Python SDK를 사용하여 기계 학습 실험 실행을 시작, 모니터링 및 추적하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: f0816e7d308eb36f6fb131419bcbcdd384bdc37b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560761"
---
# <a name="start-monitor-and-track-run-history"></a>실행 기록 시작, 모니터링 및 추적

[Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro), [Machine Learning CLI](reference-azure-machine-learning-cli.md) 및 [Azure Machine Learning 스튜디오](https://ml.azure.com)는 학습 및 실험용으로 실행을 모니터링, 구성 및 추적하는 다양한 방법을 제공합니다. ML 실행 기록은 설명 가능하고 반복 가능한 ML 개발 프로세스의 중요한 부분입니다.

이 문서에서는 다음 작업을 수행하는 방법을 보여 줍니다.

* 실행 성능 모니터링하기.
* 실행 표시 이름을 추가합니다. 
* 사용자 지정 보기 만들기. 
* 실행 설명을 추가하기. 
* 태그 및 찾기 실행하기.
* 실행 기록 검색하기. 
* 취소 또는 실패 실행하기.
* 자식 실행 만들기.
* 이메일 알림을 통해 실행 상태를 모니터링하기.
 

> [!TIP]
> Azure Machine Learning Service 및 연결된 Azure 서비스를 모니터링하는 방법에 대한 정보를 찾는다면 [Azure Machine Learning 모니터링 방법](monitor-azure-machine-learning.md)을 참조하세요.
> 웹 서비스로 배포된 모델을 모니터링하는 방법에 대한 정보를 찾고 있는 경우 [모델 데이터 수집](how-to-enable-data-collection.md) 및 [Application Insights로 모니터링](how-to-enable-app-insights.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

다음 항목이 필요합니다.

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

* Python용 Azure Machine Learning SDK(버전 1.0.21 이상) 최신 버전의 SDK를 설치하거나 업데이트하려면 [SDK 설치 또는 업데이트](/python/api/overview/azure/ml/install)를 참조하세요.

    Azure Machine Learning SDK 버전을 확인하려면 다음 코드를 사용합니다.

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) 및 [Azure Machine Learning용 CLI 확장](reference-azure-machine-learning-cli.md)


## <a name="monitor-run-performance"></a>실행 성능 모니터링

* 실행 및 해당 로깅 프로세스 시작

    # <a name="python"></a>[Python](#tab/python)
    
    1. [azureml.core](/python/api/azureml-core/azureml.core) 패키지에서 [작업 영역](/python/api/azureml-core/azureml.core.workspace.workspace), [실험](/python/api/azureml-core/azureml.core.experiment.experiment), [실행](/python/api/azureml-core/azureml.core.run%28class%29) 및 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 클래스를 가져와서 실험을 설정합니다.
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) 메서드를 사용하여 실행 및 해당 로깅 프로세스를 시작합니다.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    실험 실행을 시작하려면 다음 단계를 따릅니다.
    
    1. 셸 또는 명령 프롬프트에서 Azure CLI를 사용하여 Azure 구독에 인증합니다.
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. 학습 스크립트를 포함하는 폴더에 작업 영역 구성을 연결합니다. `myworkspace`를 Azure Machine Learning 작업 영역으로 바꿉니다. `myresourcegroup`을 작업 영역이 포함된 Azure 리소스 그룹으로 바꿉니다.
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        이 명령은 예제 runconfig 및 conda 환경 파일을 포함하는 `.azureml` 하위 디렉터리를 만듭니다. 또한 Azure Machine Learning 작업 영역과 통신하는 데 사용되는 `config.json` 파일을 포함합니다.
    
        자세한 내용은 [az ml folder attach](/cli/azure/ml(v1)/folder#az_ml_folder_attach)를 참조하세요.
    
    2. 다음 명령을 사용하여 실행을 시작합니다. 이 명령을 사용하는 경우 -c 매개 변수에 대해 runconfig 파일의 이름(파일 시스템을 보고 있는 경우 \*.runconfig 앞의 텍스트)을 지정합니다.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > `az ml folder attach` 명령은 두 개의 예제 runconfig 파일을 포함하는 `.azureml` 하위 디렉터리를 만듭니다.
        >
        > 실행 구성 개체를 프로그래밍 방식으로 만드는 Python 스크립트가 있는 경우 [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-)를 사용하여 runconfig 파일로 저장합니다.
        >
        > runconfig 파일 예제에 대한 자세한 정보는 [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/)을 참조하세요.
    
        자세한 내용은 [az ml run submit-script](/cli/azure/ml(v1)/run#az_ml_run_submit-script)를 참조하세요.

    # <a name="studio"></a>[스튜디오](#tab/azure-studio)

    Azure Machine Learning 디자이너에서 모델을 학습하는 방법에 대한 예제는 [자습서: 디자이너를 사용하여 자동차 가격 예측](tutorial-designer-automobile-price-train-score.md)을 참조하세요.

    ---

* 실행 상태 모니터링

    # <a name="python"></a>[Python](#tab/python)
    
    * [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) 메서드를 사용하여 실행 상태를 가져옵니다.
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * 실행에 대한 실행 ID, 실행 시간 및 기타 세부 정보를 가져오려면 [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) 메서드를 사용합니다.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * 실행이 성공적으로 완료되면 [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) 메서드를 사용하여 완료됨으로 표시합니다.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Python의 `with...as` 디자인 패턴을 사용하는 경우 실행이 범위를 벗어나는 경우 자동으로 완료됨으로 표시합니다. 실행을 완료됨으로 표시할 때 수동으로 할 필요가 없습니다.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    * 실험에 대한 실행 목록을 보려면 다음 명령을 입력합니다. `experiment`를 실험 이름으로 바꾸기:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        이 명령은 이 실험의 실행에 대한 정보를 나열하는 JSON 문서를 반환합니다.
    
        자세한 내용은 [az ml experiment list](/cli/azure/ml(v1)/experiment#az_ml_experiment_list)를 참조하세요.
    
    * 특정 실행에 대한 정보를 보려면 다음 명령을 사용합니다. `runid`를 실행 ID로 바꾸기:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        이 명령은 실행 관련 정보를 나열하는 JSON 문서를 반환합니다.
    
        자세한 정보는 [az ml run show](/cli/azure/ml(v1)/run#az_ml_run_show)를 참조하세요.
    
    
    # <a name="studio"></a>[스튜디오](#tab/azure-studio)
    
    ---    
   
## <a name="run-display-name"></a>실행 표시 이름 
실행 표시 이름은 실행을 위해 제공할 수 있는 선택적인 사용자 지정이 가능한 이름입니다. 실행 표시 이름을 편집하려면 다음을 수행합니다.

1. 실행 목록으로 이동합니다. 

2. 실행을 선택하여 실행 세부 정보 페이지에서 표시 이름을 편집합니다.

3. 실행 표시 이름을 **편집** 하려면 편집 단추를 선택합니다. 

:::image type="content" source="media/how-to-track-monitor-analyze-runs/display-name.gif" alt-text="스크린샷: 표시 이름 편집":::

## <a name="custom-view"></a>사용자 지정 보기 
    
스튜디오에서 실행을 보려면 다음을 수행합니다. 
    
1. **실험** 탭으로 이동합니다.
    
1. **모든 실험** 을 선택하여 실험에서 모든 실행을 보거나 **모든 실행** 을 선택하여 작업 영역에서 제출된 모든 실행을 봅니다.
    
**모든 실행’** 페이지에서 태그, 실험, 컴퓨팅 대상 등을 기준으로 실행 목록을 필터링하여 작업을 보다 효율적으로 구성하고 범위를 지정할 수 있습니다.  
    
1. 비교할 실행을 선택하거나 차트를 추가하거나 필터를 적용하여 페이지에 대 한 사용자 지정을 수행합니다. 이러한 변경 내용을 **사용자 지정 보기** 로 저장할 수 있으므로 작업으로 쉽게 돌아갈 수 있습니다. 작업 영역 권한이 있는 사용자는 편집하거나 사용자 지정 보기를 볼 수 있습니다. 또한 **공유 보기** 를 선택하여 향상된 공동 작업을 위해 팀 구성원과 사용자 지정 보기를 공유합니다.   

1. 실행 로그를 보려면 **출력 + 로그** 탭에서 특정 실행을 선택하고 실행에 대 한 진단 및 오류 로그를 확인할 수 있습니다.

:::image type="content" source="media/how-to-track-monitor-analyze-runs/custom-views-2.gif" alt-text="스크린샷: 사용자 지정 보기 만들기":::
    

## <a name="run-description"></a>실행 설명 

실행에 더 많은 컨텍스트 및 정보를 제공하기 위해 실행에 실행 설명을 추가할 수 있습니다. 실행 목록에서 이러한 설명을 검색하고 실행 목록에 열로 실행 설명을 추가할 수도 있습니다. 

실행에 대한 **실행 세부 정보** 페이지로 이동하고 편집이나 연필 아이콘을 선택하여 실행에 대한 설명을 추가, 편집 또는 삭제합니다. 실행 목록의 변경 내용을 유지하려면 기존 사용자 지정 보기나 새 사용자 지정 보기에 대한 변경 내용을 저장합니다. Markdown 형식은 아래와 같이 이미지를 포함하고 딥 링크 설정을 허용하는 실행 설명에서 지원됩니다.

:::image type="content" source="media/how-to-track-monitor-analyze-runs/run-description-2.gif" alt-text="스크린샷: 실행 설명 만들기"::: 

## <a name="tag-and-find-runs"></a>태그 및 찾기 실행

Azure Machine Learning에서는 속성과 태그를 사용하여 실행을 구성하고 중요한 정보에 대한 실행을 쿼리할 수 있습니다.

* 속성 및 태그 추가

    # <a name="python"></a>[Python](#tab/python)
    
    실행에 검색할 수 있는 메타데이터를 추가하려면 [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) 메서드를 사용합니다. 예를 들어 다음 코드는 실행에 `"author"` 속성을 추가합니다.
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    속성은 변경할 수 없으므로 감사를 위해 영구 레코드를 만듭니다. 다음 코드 예제 결과에서는 이전 코드에서 `"author"` 속성 값으로 이미 `"azureml-user"`을 추가했으므로 오류가 발생합니다.
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    속성과 달리 태그는 변경 가능합니다. 실험의 소비자에게 검색 가능하고 의미 있는 정보를 추가하려면 [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) 메서드를 사용합니다.
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    간단한 문자열 태그를 추가할 수도 있습니다. 이러한 태그가 태그 사전에 키로 표시되는 경우, 값은 `None`입니다.
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    > [!NOTE]
    > CLI를 사용하여 태그 추가나 업데이트만 할 수 있습니다.
    
    태그를 추가하거나 업데이트하려면 다음 명령을 사용합니다.
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    자세한 정보는 [az ml run update](/cli/azure/ml(v1)/run#az_ml_run_update)를 참조하세요.
    
    # <a name="studio"></a>[스튜디오](#tab/azure-studio)
    
    스튜디오에서 실행 태그를 추가, 편집 또는 삭제할 수 있습니다. 실행에 대한 **실행 세부 정보** 페이지로 이동하고 편집이나 연필 아이콘을 선택하여 실행에 대한 태그를 추가, 편집 또는 삭제합니다. 실행 목록 페이지에서 이러한 태그를 검색하고 필터링할 수도 있습니다.
    
    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-tags.gif" alt-text="스크린샷: 실행 태그 추가, 편집 또는 삭제":::
    
    ---

* 쿼리 속성 및 태그

    특정 속성 및 태그와 일치하는 실행 목록을 반환하기 위해 실험 내에서 실행을 쿼리할 수 있습니다.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Azure CLI는 속성 및 태그를 기준으로 실행을 필터링하는 데 사용할 수 있는 [JMESPath](http://jmespath.org) 쿼리를 지원합니다. Azure CLI와 함께 JMESPath 쿼리를 사용하려면 `--query` 매개 변수를 사용하여 지정합니다. 다음 예에서는 속성 및 태그를 사용하는 몇 가지 쿼리를 보여 줍니다.
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Azure CLI 결과를 쿼리하는 방법에 대한 자세한 정보는 [Azure CLI 명령 출력 쿼리](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)를 참조하세요.
    
    # <a name="studio"></a>[스튜디오](#tab/azure-studio)
    
    특정 실행을 검색하려면 **모든 실행** 목록으로 이동합니다. 여기에는 다음 두 가지 옵션이 있습니다.
    
    1. **필터 추가** 단추를 사용하고 태그에 필터를 선택하여 실행에 할당된 태그로 실행을 필터링합니다. <br><br>
    OR
    
    1. 실행 상태, 설명, 실험 이름 및 제출자 이름과 같은 실행 메타데이터를 검색하여 실행을 신속하게 찾으려면 검색 표시줄을 사용합니다. 
    
## <a name="cancel-or-fail-runs"></a>취소 또는 실패 실행

오류가 발생하거나 실행을 완료하는 데 시간이 너무 오래 걸리는 경우 실행을 취소할 수 있습니다.

# <a name="python"></a>[Python](#tab/python)

SDK를 사용하여 실행을 취소하려면 [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) 메서드를 사용합니다.

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

실행이 완료되었지만 오류가 포함된 경우(예: 잘못된 학습 스크립트를 사용한 경우) [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) 메서드를 사용하여 실패로 표시할 수 있습니다.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI를 사용하여 실행을 취소하려면 다음 명령을 사용합니다. `runid`를 실행 ID로 바꾸기

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

자세한 정보는 [az ml run cancel](/cli/azure/ml(v1)/run#az_ml_run_cancel)을 참조하세요.

# <a name="studio"></a>[스튜디오](#tab/azure-studio)

스튜디오에서 실행을 취소하려면 다음 단계를 따릅니다.

1. **Experiments** 또는 **Pipelines** 섹션에서 실행 중인 파이프라인으로 이동합니다. 

1. 취소하려는 파이프라인 실행 번호를 선택합니다.

1. 도구 모음에서 **취소** 를 선택합니다.

---

## <a name="create-child-runs"></a>자식 영역 만들기

다른 하이퍼 매개 변수 튜닝 반복의 경우와 같이 관련 실행을 함께 그룹화하는 자식 실행을 만듭니다.

> [!NOTE]
> 자식 실행은 SDK를 사용해야만 만들 수 있습니다.

이 코드 예제에서는 `hello_with_children.py` 스크립트를 사용하여 [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) 메서드를 활용하여 제출된 실행 내에서 5개의 자식 실행 일괄 처리를 만듭니다.

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> 범위 밖으로 이동하면 자식 실행이 완료됨으로 자동 표시됩니다.

많은 자식 실행을 효율적으로 만들려면 [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) 메서드를 사용합니다. 각 생성 시 네트워크 호출이 발생하므로, 실행 일괄 처리를 만드는 것이 하나씩 생성하는 것보다 효율적입니다.

### <a name="submit-child-runs"></a>자식 실행 제출

자식 실행은 부모 실행에서 제출할 수도 있습니다. 이렇게 하면 부모 및 자식 실행의 계층 구조를 만들 수 있습니다. 부모 실행이 없는 자식 실행은 만들 수 없습니다. 부모 실행은 자식 실행을 시작하는 것 말고는 아무 작업도 수행하지 않지만 계층 구조를 만들 때 필요합니다. 모든 실행의 상태는 독립적입니다. 하나 이상의 자식 실행이 취소되거나 실패한 경우에도 부모는 `"Completed"` 성공 상태로 있을 수 있습니다.  

자식 실행에서 부모 실행과 다른 실행 구성을 사용할 수 있습니다. 예를 들어, 부모가 성능이 떨어지는 CPU 기반 구성을 사용하는 동안 자식은 GPU 기반 구성을 사용할 수 있습니다. 또 다른 일반적인 방법은 각 자식에게 다른 인수 및 데이터를 전달하는 것입니다. 자식 실행을 사용자 지정하려면 자식 실행에 대한 `ScriptRunConfig` 개체를 만듭니다. 

> [!IMPORTANT]
> 부모 실행에서 원격 컴퓨팅으로 자식 실행을 제출하려면 먼저 부모 실행 코드의 작업 영역에 로그인해야 합니다. 기본적으로 원격 실행의 실행 컨텍스트 개체는 자식 실행을 제출할 자격 증명을 포함하지 않습니다. 서비스 주체 또는 관리 ID 자격 증명을 사용하여 로그인합니다. 인증에 대한 자세한 정보는 [인증 설정](how-to-setup-authentication.md)을 참조하세요.

다음 코드는

- `"gpu-cluster"`작업 영역에서`ws` 이름이 지정된 컴퓨팅 리소스를 검색합니다.
- 자식 `ScriptRunConfig` 개체에 전달될 다른 인수 값을 반복합니다.
- 사용자 지정 컴퓨팅 리소스 및 인수를 사용하여 새 자식 실행을 만들고 제출합니다.
- 모든 자식 실행이 완료될 때까지 차단합니다.

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

동일한 구성, 인수 및 입력을 사용하여 여러 자식 실행을 효율적으로 만들려면 [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) 메서드를 사용합니다. 각 생성 시 네트워크 호출이 발생하므로, 실행 일괄 처리를 만드는 것이 하나씩 생성하는 것보다 효율적입니다.

자식 실행 내에서 부모 실행 ID를 볼 수 있습니다.

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>자식 실행 쿼리

특정 부모의 자식 실행을 쿼리하려면 [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) 메서드를 사용합니다. ``recursive = True`` 인수를 사용하면 자식 및 손자의 중첩 트리를 쿼리할 수 있습니다.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>부모 또는 루트 실행에 로그

`Run.parent` 필드를 사용하여 현재 자식 실행을 시작한 실행에 액세스할 수 있습니다. `Run.parent`를 사용하는 일반적인 사용 사례는 로그 결과를 한 장소에 결합하는 것입니다. 자식 실행은 비동기적으로 실행되며 자식 실행이 완료될 때까지 부모가 대기하는 능력 이상의 순서나 동기화는 보장되지 않습니다.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="monitor-the-run-status-by-email-notification"></a>이메일 알림으로 실행 상태 모니터링

1. [Azure Portal](https://ms.portal.azure.com/)의 왼쪽 탐색 모음에서 **모니터** 탭을 선택합니다. 

1. **진단 설정** 을 선택하고 **+진단 설정 추가** 를 선택합니다.

    ![이메일 알림에 대한 진단 설정 스크린샷](./media/how-to-track-monitor-analyze-runs/diagnostic-setting.png)

1. 진단 설정에서 
    1. **범주 세부 정보** 아래 **AmlRunStatusChangedEvent** 를 선택합니다. 
    1. **대상 세부 정보** 에서 **Log Analytics 작업 영역으로 보내기** 를 선택하고 **구독** 및 **Log Analytics 작업 영역** 을 지정합니다. 

    > [!NOTE]
    > **Azure Log Analytics 작업 영역** 은 **Azure Machine Learning Service 작업 영역** 과는 다른 유형의 Azure 리소스입니다. 해당 목록에 옵션이 없는 경우 [Log Analytics 작업 영역을 만들](../azure-monitor/logs/quick-create-workspace.md) 수 있습니다. 
    
    ![이메일 알림을 저장할 위치](./media/how-to-track-monitor-analyze-runs/log-location.png)

1. **로그** 탭에서 **새 경고 규칙** 을 추가합니다. 

    ![새 경고 규칙](./media/how-to-track-monitor-analyze-runs/new-alert-rule.png)

1. [Azure Monitor를 사용하여 로그 경고를 만들고 관리하는 방법](../azure-monitor/alerts/alerts-log.md)을 참조하세요.

## <a name="example-notebooks"></a>노트북 예제

이 문서의 개념을 보여 주는 노트북은 다음과 같습니다.

* 로깅 API에 대한 자세한 정보는 [로깅 API 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)을 참조하세요.

* Azure Machine Learning SDK를 사용하여 실행을 관리하는 방법에 대한 자세한 정보는 [실행 노트북 관리](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 실험에 대한 메트릭을 로그하는 방법에 대한 자세한 정보는 [학습 실행 중의 로그 메트릭](how-to-log-view-metrics.md)을 참조하세요.
* Azure Machine Learning에서 리소스와 로그를 모니터링하는 방법을 알아보려면 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.
