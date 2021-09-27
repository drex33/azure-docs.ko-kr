---
title: Azure Functions의 Python 앱에 대한 메모리 프로파일링
description: Python 앱 메모리 사용량을 프로파일링하는 방법과 메모리 병목 상태를 식별하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: f894f369fa1efdb80160761e3c58cad937708b08
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124737437"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>Azure Functions에서 Python 앱 메모리 사용량 프로파일링

개발 중에 또는 Azure에 로컬 Python 함수 앱 프로젝트를 배포한 후에는 함수의 잠재적 메모리 병목 상태에 대해 분석하는 것이 좋습니다. 이러한 병목 상태는 함수의 성능을 저하하고 오류를 일으킬 수 있습니다. 다음 지침에서는 [메모리 프로파일러](https://pypi.org/project/memory-profiler) Python 패키지를 사용하는 방법을 보여 주고 이 패키지는 실행 시 함수에 대한 라인별 메모리 사용량 분석을 제공합니다.

> [!NOTE]
> 메모리 프로파일링은 개발 환경에서 메모리 공간을 분석하는 용도로만 사용됩니다. 메모리 프로파일러를 프로덕션 함수 앱에 적용하지 마세요.

## <a name="prerequisites"></a>사전 요구 사항

Python 함수 앱 개발을 시작하기 전에 다음 요구 사항을 충족해야 합니다.

* [Python 3.6.x 이상](https://www.python.org/downloads/release/python-374/). Azure Functions에서 지원되는 Python 버전의 전체 목록을 확인하려면 [Python 개발자 가이드](functions-reference-python.md#python-version)를 참조하세요.

* [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.x.

* [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 중 하나에 [Visual Studio Code](https://code.visualstudio.com/)가 설치됩니다.

* 활성화된 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>메모리 프로파일링 프로세스

1. requirements.txt에서 `memory-profiler`를 추가하여 패키지가 배포와 함께 제공되도록 합니다. 로컬 머신에서 개발하는 경우 [Python 가상 환경을 활성화](create-first-function-cli-python.md#create-venv)하고 `pip install -r requirements.txt`에서 패키지를 확인할 수 있습니다.

2. 함수 스크립트(일반적으로 \_\_init\_\_.py)에서 `main()` 함수 위에 다음 줄을 추가합니다. 이렇게 하면 루트 로거가 자식 로거 이름을 보고하여 메모리 프로파일링 로그가 접두사 `memory_profiler_logs`로 구분될 수 있습니다.

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=profiler_logstream)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>예제

다음은 각각 “HttpTriggerAsync” 및 “HttpTriggerSync”라는 비동기 및 동기 HTTP 트리거에서 메모리 프로파일링을 수행하는 예제입니다. Microsoft 홈페이지에 GET 요청을 간단하게 보내는 Python 함수 앱을 빌드할 예정입니다.

### <a name="create-a-python-function-app"></a>Python 함수 앱 만들기

Python 함수 앱은 지정된 Azure Functions [폴더 구조](functions-reference-python.md#folder-structure)를 따라야 합니다. 프로젝트를 스캐폴드하려면 다음 명령을 실행하여 Azure Functions Core Tools를 사용하는 것이 좋습니다.

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>파일 콘텐츠 업데이트

requirements.txt는 프로젝트에서 사용되는 패키지를 정의합니다. Azure Functions SDK 및 메모리 프로파일러 외에 `aiohttp` 비동기 HTTP 요청 및 `requests` 동기 HTTP 호출에 대해 소개합니다.

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

또한 비동기 HTTP 트리거 `HttpTriggerAsync/__init__.py`를 다시 작성하고 메모리 프로파일러, 루트 로거 형식, 로거 스트리밍 바인딩을 구성해야 합니다.

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

동기 HTTP 트리거의 경우 다음 `HttpTriggerSync/__init__.py` 코드 섹션을 참조하세요.

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>로컬 개발 환경에서 Python 함수 앱 프로파일링

위의 모든 변경 사항을 수행한 후 Azure Functions 런타임에 대한 Python 가상 환경을 초기화하는 몇 가지 단계가 더 있습니다.

1. Windows PowerShell 또는 Linux 셸 중 하나를 엽니다.
2. `py -m venv .venv`(Windows용) 또는 `python3 -m venv .venv`(Linux용)로 Python 가상 환경을 만듭니다.
3. `.venv\Scripts\Activate.ps1`(Windows PowerShell용) 또는 `source .venv/bin/activate`(Linux 셸용)로 Python 가상 환경을 활성화합니다.
4. `pip install requirements.txt`를 통해 Python 종속성을 복원합니다.
5. Azure Functions Core Tools `func host start`를 이용하여 Azure Functions 런타임을 로컬에서 시작합니다.
6. `https://localhost:7071/api/HttpTriggerAsync` 또는 `https://localhost:7071/api/HttpTriggerSync`에 GET 요청을 보냅니다.
7. Azure Functions Core Tools의 아래 섹션과 유사한 메모리 프로파일링 보고서가 표시되어야 합니다.

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>다음 단계

Azure Functions Python 개발에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions Python 개발자 가이드](functions-reference-python.md)
* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
