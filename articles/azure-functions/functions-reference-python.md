---
title: Azure Functions에 대한 Python 개발자 참조
description: Python으로 함수를 개발하는 방법 이해
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: 78351934381ebd76e32041987a4534f64cf151bf
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892744"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 개발자 가이드

이 문서에서는 Python을 사용하여 Azure Functions를 개발하는 방법을 소개합니다. 아래 내용은 [Azure Functions 개발자 가이드](functions-reference.md)를 이미 읽었다고 가정합니다.

Python 개발자라면 다음 문서 중 하나에 관심이 있을 수도 있습니다.

| 시작 | 개념| 시나리오/샘플 |
|--|--|--|
| <ul><li>[Visual Studio Code를 사용하는 Python 함수](./create-first-function-vs-code-python.md)</li><li>[터미널/명령 프롬프트를 사용하는 Python 함수](./create-first-function-cli-python.md)</li></ul> | <ul><li>[개발자 가이드](functions-reference.md)</li><li>[호스팅 옵션](functions-scale.md)</li><li>[성능&nbsp;고려 사항](functions-best-practices.md)</li></ul> | <ul><li>[PyTorch를 사용한 이미지 분류](machine-learning-pytorch.md)</li><li>[Azure 자동화 샘플](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[TensorFlow를 사용한 기계 학습](functions-machine-learning-tensorflow.md)</li><li>[Python 샘플 찾아보기](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> [Windows에서 로컬로 Python 기반 Azure Functions를 개발](create-first-function-vs-code-python.md#run-the-function-locally)할 수 있지만 Python은 Azure에서 실행할 때 Linux 기반 호스팅 계획에서만 지원됩니다. 지원되는 [운영 체제/런타임](functions-scale.md#operating-systemruntime) 조합 목록을 참조하세요.

## <a name="programming-model"></a>프로그래밍 모델

Azure Functions의 함수는 입력을 처리하고 출력을 생성하는 Python 스크립트의 상태 비저장 메서드여야 합니다. 기본적으로 런타임은 메서드가 `__init__.py` 파일에서 `main()`이라는 글로벌 메서드로 구현될 것으로 예상합니다. [대체 진입점을 지정](#alternate-entry-point)할 수도 있습니다.

트리거 및 바인딩의 데이터는 *function.json* 파일에 정의된 `name` 속성을 사용하여 메서드 특성을 통해 함수에 바인딩됩니다. 예를 들어 아래의 _function.json_ 은 `req`라는 HTTP 요청에 의해 트리거되는 단순 함수를 설명합니다.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

이 정의에 따라 함수 코드를 포함하는 `__init__.py` 파일은 다음 예제와 비슷합니다.

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

또한 Python 형식 주석을 사용하여 함수에서 매개 변수 형식 및 반환 형식을 명시적으로 선언할 수 있습니다. 이렇게 하면 여러 Python 코드 편집기에서 제공하는 인텔리센스 및 자동 완성 기능을 사용할 수 있습니다.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

[azure.functions.*](/python/api/azure-functions/azure.functions) 패키지에 포함된 Python 주석을 사용하여 입력 및 출력을 메서드에 바인딩합니다.

## <a name="alternate-entry-point"></a>대체 진입점

필요한 경우 *function.json* 파일에서 `scriptFile` 및 `entryPoint` 속성을 지정하여 함수의 기본 동작을 변경할 수 있습니다. 예를 들어 아래의 _function.json_ 은 _main.py_ 파일의 `customentry()` 메서드를 Azure 함수의 진입점으로 사용하도록 런타임에 지시합니다.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>폴더 구조

Python Functions 프로젝트에 권장하는 폴더 구조는 다음 예제와 같습니다.

```
 <project_root>/
 | - .venv/
 | - .vscode/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function/
 | | - __init__.py
 | | - function.json
 | - shared_code/
 | | - __init__.py
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - tests/
 | | - test_my_second_function.py
 | - .funcignore
 | - host.json
 | - local.settings.json
 | - requirements.txt
 | - Dockerfile
```
기본 프로젝트 폴더(<project_root>)에는 다음 파일이 포함될 수 있습니다.

* *local.settings.json*: 로컬에서 실행될 때 앱 설정과 연결 문자열을 저장하는 데 사용됩니다. 이 파일은 Azure에 게시되지 않습니다. 자세한 내용은 [local.settings.file](functions-develop-local.md#local-settings-file)을 참조하세요.
* *requirements.txt*: Azure에 게시할 때 시스템에서 설치하는 Python 패키지 목록이 포함됩니다.
* *host.json:* 함수 앱 인스턴스의 모든 함수에 영향을 주는 구성 옵션을 포함합니다. 이 파일은 Azure에 게시됩니다. 로컬로 실행할 경우 일부 옵션이 지원되지 않습니다. 자세한 내용은 [host.json](functions-host-json.md)을 참조하세요.
* *.vscode/* : (선택 사항) 저장소 VSCode 구성을 포함합니다. 자세히 알아보려면 [VSCode 설정](https://code.visualstudio.com/docs/getstarted/settings)을 참조하세요.
* *.venv/* : (선택 사항) 로컬 개발에 사용되는 Python 가상 환경을 포함합니다.
* *Dockerfile*: (선택 사항) [사용자 지정 컨테이너](functions-create-function-linux-custom-image.md)에서 프로젝트를 게시할 때 사용됩니다.
* *tests/* : (선택 사항) 함수 앱의 테스트 사례를 포함합니다.
* *.funcignore*: (선택 사항) Azure에 게시하면 안 되는 파일을 선언합니다. 일반적으로 이 파일에는 편집기 설정을 무시하는 `.vscode/`, 로컬 Python 가상 환경을 무시하는 `.venv/`, 테스트 사례를 무시하는 `tests/`, 로컬 앱 설정이 게시되지 않도록 하는 `local.settings.json`이 포함됩니다.

각 함수에는 자체 코드 파일과 바인딩 구성 파일(function.json)이 있습니다.

Azure의 함수 앱에 프로젝트를 배포할 때 기본 프로젝트( *<project_root>* ) 폴더의 전체 콘텐츠가 패키지에 포함되어야 하지만 폴더 자체는 포함되지 않아야 합니다. 즉 `host.json`은 패키지 루트에 있어야 합니다. 다른 함수(이 예에서는 `tests/`)와 함께 폴더에 테스트를 유지 관리하는 것이 좋습니다. 자세한 내용은 [단위 테스트](#unit-testing)를 참조하세요.

## <a name="import-behavior"></a>가져오기 동작

함수 코드에서 절대 참조와 상대 참조를 모두 사용하여 모듈을 가져올 수 있습니다. 위에 표시된 폴더 구조에 따라, 다음 가져오기는 함수 파일 *<project_root>\my\_first\_function\\_\_init\_\_.py* 내부에서 작동합니다.

```python
from shared_code import my_first_helper_function #(absolute)
```

```python
import shared_code.my_second_helper_function #(absolute)
```

```python
from . import example #(relative)
```

> [!NOTE]
>  절대 가져오기 구문을 사용할 때 Python 패키지로 표시하려면 *shared_code/* 폴더에 \_\_init\_\_.py 파일이 있어야 합니다.

다음 \_\_앱\_\_ 가져오기 및 최상위 수준 이상의 상대 가져오기는 정적 형식 검사기에서 지원되지 않고 Python 테스트 프레임워크에서 지원되지 않기 때문에 더 이상 사용되지 않습니다.

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## <a name="triggers-and-inputs"></a>트리거 및 입력

입력은 Azure Functions에서 트리거 입력과 기타 입력의 두 가지 범주로 나뉩니다. `function.json` 파일에서는 두 입력이 서로 다르지만, Python 코드에서 사용법은 동일합니다.  트리거 및 입력 소스의 연결 문자열 또는 비밀은 로컬로 실행할 때에는 `local.settings.json` 파일의 값에 매핑되고, Azure에서 실행할 때에는 애플리케이션 설정에 매핑됩니다.

예를 들어 다음 코드는 둘 사이의 차이점을 보여줍니다.

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

함수가 호출되면 HTTP 요청이 `req`로 함수에 전달됩니다. 항목은 경로 URL의 _ID_ 를 기반으로 Azure Blob Storage에서 검색되고 함수 본문에서 `obj`로 제공됩니다.  여기서 지정된 스토리지 계정은 AzureWebJobsStorage 앱 설정에 있는 연결 문자열로, 함수 앱에서 사용하는 것과 동일한 스토리지 계정입니다.


## <a name="outputs"></a>outputs

출력은 반환 값 및 출력 매개 변수 둘 다로 표현될 수 있습니다. 출력이 하나만 있는 경우 반환 값을 사용하는 것이 좋습니다. 다중 출력의 경우 출력 매개 변수를 사용해야 합니다.

함수의 반환 값을 출력 바인딩의 값으로 사용하려면 바인딩의 `name` 속성을 `function.json`의 `$return`으로 설정해야 합니다.

다중 출력을 생성하려면 [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out) 인터페이스에서 제공하는 `set()` 메서드를 사용하여 바인딩에 값을 할당합니다. 예를 들어 다음 함수는 메시지를 큐로 푸시하고 HTTP 응답도 반환할 수 있습니다.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>로깅

함수 앱의 루트 [`logging`](https://docs.python.org/3/library/logging.html#module-logging) 처리기를 통해 Azure Functions 런타임 로거에 액세스할 수 있습니다. 이 로거는 Application Insights에 연결되며, 함수를 실행하는 동안 발생하는 경고 및 오류에 플래그를 지정할 수 있도록 합니다.

다음 예제는 HTTP 트리거를 통해 함수가 호출될 때 정보 메시지를 기록합니다.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

다양한 추적 수준에서 콘솔에 쓸 수 있는 추가 로깅 메서드가 제공됩니다.

| 메서드                 | Description                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | 루트 로거에 위험 수준의 메시지를 기록합니다.  |
| **`error(_message_)`**   | 루트 로거에 오류 수준의 메시지를 기록합니다.    |
| **`warning(_message_)`**    | 루트 로거에 경고 수준의 메시지를 기록합니다.  |
| **`info(_message_)`**    | 루트 로거에 정보 수준의 메시지를 기록합니다.  |
| **`debug(_message_)`** | 루트 로거에 디버그 수준의 메시지를 기록합니다.  |

로깅에 대한 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

### <a name="log-custom-telemetry"></a>로그 사용자 지정 원격 분석

기본적으로 Functions 런타임은 함수에서 생성된 로그 및 기타 원격 분석 데이터를 수집합니다. 이 원격 분석은 Application Insights 추적으로 종료됩니다. 특정 Azure 서비스에 대한 요청 및 종속성 원격 분석도 트리거 및 바인딩에 의해 기본적으로 [수집됩니다.](functions-triggers-bindings.md#supported-bindings) 바인딩 외부에서 사용자 지정 요청 및 사용자 지정 종속성 원격 분석을 수집하려면 사용자 지정 원격 분석 데이터를 Application Insights 인스턴스로 보내는 [OpenCensus Python 확장](https://github.com/census-ecosystem/opencensus-python-extensions-azure)을 사용할 수 있습니다. [OpenCensus 리포지토리](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib)에서 지원되는 확장 목록을 찾을 수 있습니다.

>[!NOTE]
>OpenCensus Python 확장을 사용하려면 애플리케이션 설정에서 를 로 설정하여 함수 앱에서 [Python 작업자 확장을](#python-worker-extensions) 사용하도록 설정해야 `PYTHON_ENABLE_WORKER_EXTENSIONS` `1` 합니다. [](functions-how-to-use-azure-function-app-settings.md#settings)


```
// requirements.txt
...
opencensus-extension-azure-functions
opencensus-ext-requests
```

```python
import json
import logging

import requests
from opencensus.extension.azure.functions import OpenCensusExtension
from opencensus.trace import config_integration

config_integration.trace_integrations(['requests'])

OpenCensusExtension.configure()

def main(req, context):
    logging.info('Executing HttpTrigger with OpenCensus extension')

    # You must use context.tracer to create spans
    with context.tracer.span("parent"):
        response = requests.get(url='http://example.com')

    return json.dumps({
        'method': req.method,
        'response': response.status_code,
        'ctx_func_name': context.function_name,
        'ctx_func_dir': context.function_directory,
        'ctx_invocation_id': context.invocation_id,
        'ctx_trace_context_Traceparent': context.trace_context.Traceparent,
        'ctx_trace_context_Tracestate': context.trace_context.Tracestate,
        'ctx_retry_context_RetryCount': context.retry_context.retry_count,
        'ctx_retry_context_MaxRetryCount': context.retry_context.max_retry_count,
    })
```

## <a name="http-trigger-and-bindings"></a>HTTP 트리거 및 바인딩

HTTP 트리거는 function.json 파일에 정의됩니다. 바인딩의 `name`은 함수의 명명된 매개 변수와 일치해야 합니다.
이전 예제에서는 바인딩 이름 `req`를 사용합니다. 이 매개 변수는 [HttpRequest] 개체이며 [HttpResponse] 개체가 반환됩니다.

[HttpRequest] 개체에서 요청 헤더, 쿼리 매개 변수, 경로 매개 변수 및 메시지 본문을 가져올 수 있습니다.

다음 예제는 [Python용 HTTP 트리거 템플릿](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)에서 가져온 것입니다.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

이 함수에서 `name` 쿼리 매개 변수의 값은 [HttpRequest] 개체의 `params` 매개 변수에서 가져옵니다. JSON으로 인코딩된 메시지 본문은 `get_json` 메서드를 사용하여 읽습니다.

마찬가지로, 반환된 [HttpResponse] 개체의 응답 메시지에 대한 `status_code` 및 `headers`를 설정할 수 있습니다.

## <a name="scaling-and-performance"></a>크기 조정 및 성능

Python 함수 앱의 스케일링과 성능 모범 사례는 [Python 스케일링 및 성능 문서](python-scale-performance-reference.md)를 참조하세요.

## <a name="context"></a>Context

실행 중에 함수의 호출 컨텍스트를 가져오려면 해당 서명에 [`context`](/python/api/azure-functions/azure.functions.context) 인수를 포함해야 합니다.

다음은 그 예입니다.

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**Context**](/python/api/azure-functions/azure.functions.context) 클래스에는 다음과 같은 문자열 특성이 있습니다.

`function_directory` 함수가 실행되는 디렉터리입니다.

`function_name` 함수의 이름입니다.

`invocation_id` 현재 함수 호출의 ID입니다.

`trace_context` 분산 추적에 대한 컨텍스트입니다. 자세한 내용은  [`Trace Context`](https://www.w3.org/TR/trace-context/) 을 참조하세요.

`retry_context` 함수에 대한 재시도 컨텍스트입니다. 자세한 내용은 [`retry-policies`](./functions-bindings-errors.md#retry-policies-preview) 을 참조하세요.

## <a name="global-variables"></a>글로벌 변수

나중에 실행할 수 있도록 앱 상태가 유지된다는 보장은 없습니다. 그러나 Azure Functions 런타임은 동일한 앱을 여러 차례 실행할 때 종종 동일한 프로세스를 재사용합니다. 비용이 많이 드는 컴퓨팅 결과를 캐시하려면 글로벌 변수로 선언하면 됩니다.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>환경 변수

Functions에서 서비스 연결 문자열 같은 [애플리케이션 설정](functions-app-settings.md)은 실행 중에 환경 변수로 공개됩니다. 코드에서 이러한 설정에 액세스하는 두 가지 주요 방법이 있습니다. 

| 메서드 | 설명 |
| --- | --- |
| **`os.environ["myAppSetting"]`** | 키 이름으로 애플리케이션 설정을 설정하려고 시도하여 실패할 때 오류가 발생합니다.  |
| **`os.getenv("myAppSetting")`** | 키 이름으로 애플리케이션 설정을 구하려고 시도하여 실패하면, null을 반환합니다.  |

이러한 두 가지 방법 모두 `import os`을 선언해야 합니다.

다음 예제에서는 `myAppSetting`라는 키가 있는 [애플리케이션 설정](functions-how-to-use-azure-function-app-settings.md#settings)을 가져오기 위해 `os.environ["myAppSetting"]`을 사용합니다.

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

로컬에서 개발하는 경우 애플리케이션 설정은 [local.settings.json 파일에 유지됩니다](functions-develop-local.md#local-settings-file).

## <a name="python-version"></a>Python 버전

Azure Functions에서 지원하는 Python 버전은 다음과 같습니다.

| Functions 버전 | Python<sup>*</sup> 버전 |
| ----- | ----- |
| 3.x | 3.9(미리 보기) <br/> 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>공식 CPython 배포판

Azure에서 함수 앱을 만들 때 특정 Python 버전을 요청하려면 [`az functionapp create`](/cli/azure/functionapp#az_functionapp_create) 명령의 `--runtime-version` 옵션을 사용합니다. Functions 런타임 버전은 `--functions-version` 옵션을 통해 설정됩니다. Python 버전은 함수 앱을 만들 때 설정되며 변경할 수 없습니다.

로컬로 실행하는 경우 런타임에서는 사용 가능한 Python 버전을 사용합니다.

### <a name="changing-python-version"></a>Python 버전 변경

Python 함수 앱을 특정 언어 버전으로 설정하려면 사이트 구성의 `LinuxFxVersion` 필드에 언어 버전뿐만 아니라 언어를 지정해야 합니다. 예를 들어 Python 3.8을 사용하도록 Python 앱을 변경하려면 `linuxFxVersion`을 `python|3.8`로 설정합니다.

Azure Functions 런타임 지원 정책에 대한 자세한 내용은 이 [문서](./language-support-policy.md)를 참조하세요.

지원되는 Python 버전 함수 앱의 전체 목록을 보려면 이 [문서](./supported-languages.md)를 참조하세요.



# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-linux)

Azure CLI에서 `linuxFxVersion`을 확인하고 설정할 수 있습니다.  

Azure CLI를 사용하여 [az functionapp config show](/cli/azure/functionapp/config) 명령을 사용하여 현재 `linuxFxVersion`을 확인합니다.

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group>
```

이 코드에서 `<function_app>`을 함수 앱 이름으로 바꿉니다. 또한 `<my_resource_group>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. 

다음 출력의 `linuxFxVersion`은 보기 편하도록 잘린 상태입니다.

```output
{
  ...
  "kind": null,
  "limits": null,
  "linuxFxVersion": <LINUX_FX_VERSION>,
  "loadBalancing": "LeastRequests",
  "localMySqlEnabled": false,
  "location": "West US",
  "logsDirectorySizeLimit": 35,
   ...
}
```

[az functionapp config appsettings set](/cli/azure/functionapp/config) 명령을 사용하여 함수 앱의 `linuxFxVersion` 설정을 업데이트할 수 있습니다.

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

`<FUNCTION_APP>`은 함수 앱 이름으로 바꿉니다. 또한 `<RESOURCE_GROUP>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. 또한 `<LINUX_FX_VERSION>`을 사용하려는 python 버전으로 바꾸고 `python|`을 접두사로 사용합니다(예: `python|3.9`).

앞의 코드 샘플에서 **사용해 보세요.** 를 선택하여 [Azure Cloud Shell](../cloud-shell/overview.md)에서 이 명령을 실행할 수 있습니다. 또한 [Azure CLI locally(로컬로 Azure CLI 설치)](/cli/azure/install-azure-cli)를 사용하면 [az login](/cli/azure/reference-index#az-login)을 실행하여 로그인한 후 이 명령을 실행할 수도 있습니다.

사이트 구성이 변경된 후 함수 앱이 다시 시작됩니다.

--- 


## <a name="package-management"></a>패키지 관리

Azure Functions Core Tools 또는 Visual Studio Code를 사용하여 로컬에서 개발하는 경우 필요한 패키지의 이름과 버전을 `requirements.txt` 파일에 추가하고 `pip`를 사용하여 설치합니다.

예를 들어 다음 요구 사항 파일 및 pip 명령을 사용하여 PyPI에서 `requests` 패키지를 설치할 수 있습니다.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Azure에 게시

게시할 준비가 되면 공개적으로 제공되는 모든 종속성이 프로젝트 디렉터리의 루트에 있는 requirements.txt 파일에 나열되는지 확인합니다.

가상 환경 폴더를 비롯하여 게시에서 제외되는 프로젝트 파일과 폴더는 .funcignore 파일에 나열됩니다.

Python 프로젝트를 Azure에 게시하는 데 지원되는 세 가지 빌드 작업은 원격 빌드, 로컬 빌드 및 사용자 지정 종속성을 사용하는 빌드입니다.

또한 Azure Pipelines를 사용하여 종속성을 빌드하고 CD(지속적인 업데이트)를 통해 게시할 수 있습니다. 자세히 알아보려면 [Azure DevOps를 사용하여 지속적인 업데이트](functions-how-to-azure-devops.md)를 참조하세요.

### <a name="remote-build"></a>원격 빌드

원격 빌드를 사용하는 경우 서버에서 복원된 종속성과 기본 종속성은 프로덕션 환경과 일치합니다. 따라서 업로드할 배포 패키지가 작아질 수 있습니다. Windows에서 Python 앱을 개발할 때 원격 빌드를 사용합니다. 프로젝트에 사용자 지정 종속성이 있는 경우 [추가 인덱스 URL로 원격 빌드를 사용](#remote-build-with-extra-index-url)할 수 있습니다.

requirements.txt 파일의 내용에 따라 원격으로 종속성을 가져옵니다. [원격 빌드](functions-deployment-technologies.md#remote-build)는 권장 빌드 방법입니다. 기본적으로 Azure Functions Core Tools는 다음과 같은 [`func azure functionapp publish`](functions-run-local.md#publish) 명령을 사용하여 Python 프로젝트를 Azure에 게시할 때 원격 빌드를 요청합니다.

```bash
func azure functionapp publish <APP_NAME>
```

`<APP_NAME>`을 Azure의 함수 앱 이름으로 바꾸어야 합니다.

[Visual Studio Code용 Azure Functions 확장](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) 역시 기본적으로 원격 빌드를 요청합니다.

### <a name="local-build"></a>로컬 빌드

requirements.txt 파일의 내용에 따라 로컬로 종속성을 가져옵니다. 다음 [`func azure functionapp publish`](functions-run-local.md#publish) 명령을 사용하여 로컬 빌드로 게시하면 원격 빌드를 방지할 수 있습니다.

```command
func azure functionapp publish <APP_NAME> --build local
```

`<APP_NAME>`을 Azure의 함수 앱 이름으로 바꾸어야 합니다.

`--build local` 옵션을 사용하면 requirements.txt 파일에서 프로젝트 종속성을 읽고 해당하는 종속 패키지를 로컬로 다운로드하여 설치합니다. 프로젝트 파일과 종속성은 로컬 컴퓨터에서 Azure로 배포됩니다. 결과적으로 더 큰 배포 패키지가 Azure에 업로드됩니다. 어떤 이유로 Core Tools가 requirements.txt 파일의 종속성이 얻을 수 없는 경우 사용자 지정 종속성 옵션을 사용해야 합니다.

Windows에서 로컬로 개발하는 경우에는 로컬 빌드를 사용하지 않는 것이 좋습니다.

### <a name="custom-dependencies"></a>사용자 지정 종속성

[Python 패키지 인덱스](https://pypi.org/)에 없는 종속성이 프로젝트에 있는 경우 두 가지 방법으로 프로젝트를 빌드할 수 있습니다. 빌드 메서드는 프로젝트를 빌드하는 방법에 따라 달라집니다.

#### <a name="remote-build-with-extra-index-url"></a>추가 인덱스 URL을 사용하여 원격 빌드

액세스할 수 있는 사용자 지정 패키지 인덱스에서 패키지를 사용할 수 있는 경우 원격 빌드를 사용합니다. 게시하기 전에 `PIP_EXTRA_INDEX_URL`이라는 [앱 설정을 만들어야](functions-how-to-use-azure-function-app-settings.md#settings) 합니다. 이 설정값은 사용자 지정 패키지 인덱스의 URL입니다. 이 설정을 사용하면 원격 빌드가 `--extra-index-url` 옵션을 사용하여 `pip install`을 실행하도록 지시합니다. 자세히 알아보려면 [Python pip 설치 설명서](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)를 참조하세요.

추가 패키지 인덱스 URL을 사용하여 기본 인증 자격 증명을 사용할 수도 있습니다. 자세히 알아보려면 Python 설명서의 [기본 인증 자격 증명](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials)을 참조하세요.

#### <a name="install-local-packages"></a>로컬 패키지 설치

당사의 도구에서 공개적으로 제공하지 않는 패키지를 프로젝트에 사용하는 경우 해당 패키지를 \_\_app\_\_/.python_packages 디렉터리에 배치하면 앱에서 사용할 수 있습니다. 게시하기 전에, 다음 명령을 실행하여 종속성을 로컬로 설치합니다.

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

사용자 지정 종속성을 사용하는 경우 프로젝트 폴더에 이미 종속성을 설치했으므로 `--no-build` 게시 옵션을 사용해야 합니다.

```command
func azure functionapp publish <APP_NAME> --no-build
```

`<APP_NAME>`을 Azure의 함수 앱 이름으로 바꾸어야 합니다.

## <a name="unit-testing"></a>단위 테스트

Python으로 작성된 함수는 다른 Python 코드와 마찬가지로 표준 테스트 프레임워크를 사용하여 테스트할 수 있습니다. 대부분의 바인딩에서 `azure.functions` 패키지로 적절한 클래스 인스턴스를 만들어 모의 입력 개체를 만들 수 있습니다. [`azure.functions`](https://pypi.org/project/azure-functions/) 패키지는 즉시 사용할 수 없으므로, 위의 [패키지 관리](#package-management) 섹션에서 설명한 대로 `requirements.txt` 파일을 통해 설치해야 합니다.

*my_second_function* 을 예로 들면 다음은 HTTP 트리거 함수에 대한 모의 테스트입니다.

먼저 *<project_root>/my_second_function/function.json* 파일을 만들고 이 함수를 http 트리거로 정의해야 합니다.

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "main",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

이제 *my_second_function* 및 *shared_code.my_second_helper_function* 을 구현할 수 있습니다.

```python
# <project_root>/my_second_function/__init__.py
import azure.functions as func
import logging

# Use absolute import to resolve shared_code modules
from shared_code import my_second_helper_function

# Define an http trigger which accepts ?value=<int> query parameter
# Double the value and return the result in HttpResponse
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Executing my_second_function.')

    initial_value: int = int(req.params.get('value'))
    doubled_value: int = my_second_helper_function.double(initial_value)

    return func.HttpResponse(
      body=f"{initial_value} * 2 = {doubled_value}",
      status_code=200
    )
```

```python
# <project_root>/shared_code/__init__.py
# Empty __init__.py file marks shared_code folder as a Python package
```

```python
# <project_root>/shared_code/my_second_helper_function.py

def double(value: int) -> int:
  return value * 2
```

http 트리거에 대한 테스트 사례 작성을 시작할 수 있습니다.

```python
# <project_root>/tests/test_my_second_function.py
import unittest

import azure.functions as func
from my_second_function import main

class TestFunction(unittest.TestCase):
    def test_my_second_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/my_second_function',
            params={'value': '21'})

        # Call the function.
        resp = main(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'21 * 2 = 42',
        )
```

`.venv` Python 가상 환경 내에서 즐겨 사용하는 Python 테스트 프레임워크(예: `pip install pytest`)를 설치합니다. 그런 다음, `pytest tests`를 실행하여 테스트 결과를 확인합니다.

## <a name="temporary-files"></a>임시 파일

`tempfile.gettempdir()` 메서드는 임시 폴더를 반환하며, 이 폴더는 Linux에서 `/tmp`입니다. 애플리케이션에서는 함수가 실행 중에 만들어서 사용하는 임시 파일을 이 디렉터리에 저장할 수 있습니다.

> [!IMPORTANT]
> 임시 디렉터리에 기록된 파일은 호출 간에 유지되지 않을 수 있습니다. 스케일 아웃 도중에 임시 파일은 인스턴스 간에 공유되지 않습니다.

다음은 임시 디렉터리(`/tmp`)에 명명된 임시 파일을 만드는 예제입니다.

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

프로젝트 폴더와 다른 별도의 폴더에 테스트를 유지하는 것이 좋습니다. 이렇게 하면 앱에서 테스트 코드를 배포하는 일이 없습니다.

## <a name="preinstalled-libraries"></a>사전 설치된 라이브러리

Python Functions 런타임에는 몇 가지 라이브러리가 함께 제공됩니다.

### <a name="python-standard-library"></a>Python 표준 라이브러리

Python 표준 라이브러리에는 각 Python 배포와 함께 제공되는 기본 제공 Python 모듈 목록이 포함되어 있습니다. 이러한 라이브러리는 대부분 파일 I/O와 같은 시스템 기능에 액세스하는 데 유용합니다. Windows 시스템에서 이러한 라이브러리는 Python과 함께 설치됩니다. Unix 기반 시스템에서는 패키지 컬렉션으로 제공됩니다.

라이브러리 목록의 전체 세부 정보를 보려면 아래 링크를 참조하세요.

* [Python 3.6 표준 라이브러리](https://docs.python.org/3.6/library/)
* [Python 3.7 표준 라이브러리](https://docs.python.org/3.7/library/)
* [Python 3.8 표준 라이브러리](https://docs.python.org/3.8/library/)
* [Python 3.9 표준 라이브러리](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions Python 작업자 종속성

Functions Python 작업자에는 특정 라이브러리 집합이 필요합니다. 함수에서 이러한 라이브러리를 사용할 수도 있지만 Python 표준의 일부가 아닙니다. 함수가 이러한 라이브러리에 의존하는 경우 Azure Functions 외부에서 실행할 때 코드에서 사용하지 못할 수 있습니다. [setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) 파일의 **install\_requires** 섹션에서 세부 종속성 목록을 확인할 수 있습니다.

> [!NOTE]
> 함수 앱의 requirements.txt에 `azure-functions-worker` 항목이 포함되어 있는 경우 해당 항목을 제거합니다. Functions 작업자는 Azure Functions 플랫폼에서 자동으로 관리되며, 새로운 기능 및 버그 수정에 대해 정기적으로 업데이트합니다. requirements.txt에서 이전 버전의 작업자를 수동으로 설치하면 예기치 않은 문제가 발생할 수 있습니다.

> [!NOTE]
>  패키지에 작업자의 종속성과 충돌할 수 있는 특정 라이브러리(예: protobuf, tensorflow, grpcio)가 포함된 경우 애플리케이션이 작업자의 종속성을 참조하지 않도록 앱 설정에서 [`PYTHON_ISOLATE_WORKER_DEPENDENCIES`](functions-app-settings.md#python_isolate_worker_dependencies-preview)을 `1`로 구성합니다. 이 기능은 미리 보기 상태입니다.

### <a name="azure-functions-python-library"></a>Azure Functions Python 라이브러리

모든 Python 작업자 업데이트에는 새 버전의 [Azure Functions Python 라이브러리(azure.functions)](https://github.com/Azure/azure-functions-python-library)가 포함되어 있습니다. 이 접근 방식을 사용하면 각 업데이트가 이전 버전과 호환되므로 더욱 쉽게 Python 함수 앱을 지속적으로 업데이트할 수 있습니다. 이 라이브러리의 릴리스 목록은 [azure-functions PyPi](https://pypi.org/project/azure-functions/#history)에서 찾을 수 있습니다.

런타임 라이브러리 버전은 Azure에서 수정되며 requirements.txt로 재정의할 수 없습니다. requirements.txt의 `azure-functions` 항목은 린팅 및 고객 인식에만 사용됩니다.

런타임에 Python Functions 라이브러리의 실제 버전을 추적하려면 다음 코드를 사용합니다.

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>런타임 시스템 라이브러리

Python 작업자 Docker 이미지에 사전 설치된 시스템 라이브러리 목록을 보려면 아래 링크를 참조하세요.

|  함수 런타임  | Debian 버전 | Python 버전 |
|------------|------------|------------|
| 버전 2.x | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| 버전 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3.9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="python-worker-extensions"></a>Python 작업자 확장  

Azure Functions에서 실행되는 Python 작업자 프로세스를 통해 타사 라이브러리를 함수 앱에 통합할 수 있습니다. 이 확장 라이브러리는 함수 실행 수명 주기 동안 특정 작업을 삽입할 수 있는 미들웨어 역할을 합니다. 

표준 Python 라이브러리 모듈과 마찬가지로 함수 코드에서 확장을 가져옵니다. 확장은 다음 범위에 따라 실행됩니다. 

| 범위 | Description |
| --- | --- |
| **애플리케이션 수준** | 함수 트리거로 가져온 확장은 앱의 모든 함수 실행에 적용됩니다. |
| **함수 수준** | 확장을 가져온 특정 함수 트리거로만 실행이 제한됩니다. |

지정된 확장에 대한 정보를 검토하여 확장이 실행되는 범위에 대해 자세히 알아봅니다. 

확장은 Python 작업자 프로세스에서 함수 실행 수명 주기 동안 확장 코드를 호출할 수 있게 해주는 Python 작업자 확장 인터페이스를 구현합니다. 자세한 내용은 [확장 만들기](#creating-extensions)를 참조하세요.

### <a name="using-extensions"></a>확장 사용 

Python 함수에서 다음과 같은 기본 단계를 수행하여 Python 작업자 확장 라이브러리를 사용할 수 있습니다.

1. 프로젝트의 requirements.txt 파일에 확장 패키지를 추가합니다.
1. 앱에 라이브러리를 설치합니다.
1. 애플리케이션 설정인 `PYTHON_ENABLE_WORKER_EXTENSIONS`를 추가합니다.
    + 로컬: [local.settings.json 파일](functions-develop-local.md#local-settings-file)의 `Values` 섹션에 `"PYTHON_ENABLE_WORKER_EXTENSIONS": "1"`을 추가합니다.
    + Azure: [앱 설정](functions-how-to-use-azure-function-app-settings.md#settings)에 `PYTHON_ENABLE_WORKER_EXTENSIONS=1`을 추가합니다.
1. 확장 모듈을 함수 트리거로 가져옵니다. 
1. 필요한 경우 확장 인스턴스를 구성합니다. 확장 설명서에서 구성 요구 사항을 설명해야 합니다. 

> [!IMPORTANT]
> 타사 Python 작업자 확장 라이브러리는 Microsoft에서 지원하거나 보증하지 않습니다. 함수 앱에서 사용하는 확장을 신뢰할 수 있는지 확인해야 하며, 악의적이거나 잘못 작성된 확장을 사용할 경우의 위험에 대한 모든 책임은 사용자에게 있습니다. 

타사는 함수 앱에서 특정 확장을 설치하고 사용하는 방법에 대한 특정 설명서를 제공해야 합니다. 확장을 사용하는 방법의 기본 예제는 [확장 사용](develop-python-worker-extensions.md#consume-your-extension-locally)을 참조하세요. 

함수 앱에서 확장을 사용하는 방법의 예제는 범위별로 다음과 같습니다.

# <a name="application-level"></a>[애플리케이션 수준](#tab/application-level)

```python
# <project_root>/requirements.txt
application-level-extension==1.0.0
```

```python
# <project_root>/Trigger/__init__.py

from application_level_extension import AppExtension
AppExtension.configure(key=value)

def main(req, context):
  # Use context.app_ext_attributes here
```
# <a name="function-level"></a>[함수 수준](#tab/function-level)
```python
# <project_root>/requirements.txt
function-level-extension==1.0.0
```

```python
# <project_root>/Trigger/__init__.py

from function_level_extension import FuncExtension
func_ext_instance = FuncExtension(__file__)

def main(req, context):
  # Use func_ext_instance.attributes here
```
---

### <a name="creating-extensions"></a>확장 만들기 

확장은 Azure Functions에 통합할 수 있는 기능을 만든 타사 라이브러리 개발자가 만듭니다.  확장 개발자는 함수 실행 컨텍스트에서 실행되도록 특별히 설계된 사용자 지정 논리를 포함하는 Python 패키지를 설계, 구현, 릴리스합니다. PyPI 레지스트리 또는 GitHub 리포지토리에 확장을 게시할 수 있습니다.

Python 작업자 확장 패키지를 생성, 패키지, 게시, 사용하는 방법을 알아보려면 [Azure Functions용 Python 작업자 확장 개발](develop-python-worker-extensions.md)을 참조하세요.

#### <a name="application-level-extensions"></a>애플리케이션 수준 확장

[`AppExtensionBase`](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/app_extension_base.py)에서 상속된 확장은 _애플리케이션_ 범위에서 실행됩니다. 

`AppExtensionBase`는 구현할 수 있도록 다음과 같은 추상 클래스 메서드를 노출합니다.

| 메서드 | Description |
| --- | --- |
| **`init`** | 확장을 가져온 후에 호출됩니다. |
| **`configure`** | 확장을 구성하는 데 필요한 경우 함수 코드에서 호출됩니다. |
| **`post_function_load_app_level`** | 함수가 로드된 후에 바로 호출됩니다. 함수 이름과 함수 디렉터리가 확장에 전달됩니다. 함수 디렉터리는 읽기 전용이므로 이 디렉터리의 로컬 파일에 쓰려고 하면 실패합니다. |
| **`pre_invocation_app_level`** | 함수가 트리거되기 직전에 호출됩니다. 함수 컨텍스트와 함수 호출 인수가 확장에 전달됩니다. 일반적으로 함수 코드에서 사용하기 위해 컨텍스트 개체의 다른 특성을 전달할 수 있습니다. |
| **`post_invocation_app_level`** | 함수 실행이 완료된 후에 바로 호출됩니다. 함수 컨텍스트, 함수 호출 인수, 호출 반환 개체가 확장에 전달됩니다. 이 구현은 수명 주기 후크 실행에 성공했는지 여부를 확인하는 데 적합한 장소입니다. |

#### <a name="function-level-extensions"></a>함수 수준 확장

[FuncExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/func_extension_base.py)에서 상속된 확장은 특정 함수 트리거에서 실행됩니다. 

`FuncExtensionBase`는 구현할 수 있도록 다음과 같은 추상 클래스 메서드를 노출합니다.

| 메서드 | Description |
| --- | --- |
| **`__init__`** | 이 메서드는 확장의 생성자입니다. 특정 함수에서 확장 인스턴스를 초기화할 때 호출됩니다. 이 추상 메서드를 구현하는 경우 `filename` 매개 변수를 허용하고, 적절한 확장 등록을 위해 부모의 `super().__init__(filename)` 메서드에 전달하는 것이 좋습니다. |
| **`post_function_load`** | 함수가 로드된 후에 바로 호출됩니다. 함수 이름과 함수 디렉터리가 확장에 전달됩니다. 함수 디렉터리는 읽기 전용이므로 이 디렉터리의 로컬 파일에 쓰려고 하면 실패합니다. |
| **`pre_invocation`** | 함수가 트리거되기 직전에 호출됩니다. 함수 컨텍스트와 함수 호출 인수가 확장에 전달됩니다. 일반적으로 함수 코드에서 사용하기 위해 컨텍스트 개체의 다른 특성을 전달할 수 있습니다. |
| **`post_invocation`** | 함수 실행이 완료된 후에 바로 호출됩니다. 함수 컨텍스트, 함수 호출 인수, 호출 반환 개체가 확장에 전달됩니다. 이 구현은 수명 주기 후크 실행에 성공했는지 여부를 확인하는 데 적합한 장소입니다. |

## <a name="cross-origin-resource-sharing"></a>크로스-원본 자원 공유

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS는 Python 함수 앱을 완벽하게 지원합니다.

## <a name="known-issues-and-faq"></a>알려진 문제 및 FAQ

다음은 일반적인 문제의 문제 해결 가이드 목록입니다.

* [ModuleNotFoundError 및 ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* ['cygrpc'를 가져올 수 없음](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

모든 알려진 문제 및 기능 요청은 [GitHub 문제](https://github.com/Azure/azure-functions-python-worker/issues) 목록을 사용하여 추적됩니다. 문제가 발생하여 GitHub에서 해당 문제를 찾을 수 없는 경우 새 문제를 열고 해당 문제에 대한 자세한 설명을 제공해 주세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions 패키지 API 설명서](/python/api/azure-functions/azure.functions)
* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Blob Storage 바인딩](functions-bindings-storage-blob.md)
* [HTTP 및 Webhook 바인딩](functions-bindings-http-webhook.md)
* [Queue Storage 바인딩](functions-bindings-storage-queue.md)
* [타이머 트리거](functions-bindings-timer.md)

[문제가 있나요? 알려주세요.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse
