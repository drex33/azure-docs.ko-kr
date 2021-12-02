---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: jbeauregardb
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: jbeauregardb
ms.openlocfilehash: b4e5bc39bfbd3faf0bee231d75be54f6e8ffd70d
ms.sourcegitcommit: 9567c42d1e5270af16a1a8090f11a3b12131010d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133444950"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 또는 3.6 이상
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../create-communication-resource.md).
- [Azure Portal](../../../azure-monitor/app/create-new-resource.md)에서 Application Insights 리소스를 만듭니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

1. 터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

   ```console
   mkdir application-insights-quickstart && cd application-insights-quickstart
   ```

1. 텍스트 편집기를 사용하여 프로젝트 루트 디렉터리에 **application-insights-quickstart.py** 라는 파일을 만들고 기본 예외 처리를 비롯한 프로그램의 구조를 추가합니다. 이 빠른 시작의 모든 소스 코드를 다음 섹션의 이 파일에 추가합니다.

   ```python
    import os
    from opentelemetry import trace
    from opentelemetry.sdk.trace import TracerProvider
    from opentelemetry.sdk.trace.export import BatchSpanProcessor
    from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

    from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 Python 패키지용 Azure Communication Services ID SDK 및 Azure Monitor용 Microsoft Opentelemetry Exporter를 설치합니다.

```console
pip install azure-communication-identity
pip install azure-monitor-opentelemetry-exporter --pre
```

## <a name="setting-up-the-telemetry-tracer-with-communication-identity-sdk-calls"></a>통신 ID SDK 호출을 사용하여 원격 분석 추적 프로그램 설정

연결 문자열로 `CommunicationIdentityClient`를 인스턴스화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

다음 코드를 `try` 블록 내에 추가합니다.

```python
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]
identity_client = CommunicationIdentityClient.from_connection_string(connection_string)
```

먼저 Azure Monitor에서 요청을 추적할 수 있는 범위를 만들기 위해 `AzureMonitorTraceExporter` 개체의 인스턴스를 만들어야 합니다. Application Insights 리소스에서 연결 문자열을 제공해야 합니다.

```python
exporter = AzureMonitorTraceExporter.from_connection_string(
    "<APPLICATION-INSIGHTS-RESOURCE-CONNECTION-STRING>"
)
```

그러면 이 내보내기를 통해 요청 추적을 가능하게 하는 다음 인스턴스를 만들 수 있습니다. `AzureMonitorTraceExporter`를 만든 후 다음 코드를 추가합니다.

```python
    trace.set_tracer_provider(TracerProvider())
    tracer = trace.get_tracer(__name__)
    span_processor = BatchSpanProcessor(exporter)
    trace.get_tracer_provider().add_span_processor(span_processor)
```
추적 프로그램이 초기화되면 요청 추적을 담당하는 범위를 만들 수 있습니다.

```python
with tracer.start_as_current_span(name="MyIdentityApplication"):
    user = identity_client.create_user()
```

## <a name="run-the-code"></a>코드 실행

콘솔 프롬프트에서 **application-insights-quickstart.py** 파일이 포함된 디렉터리로 이동한 후 다음 `python` 명령을 실행하여 앱을 실행합니다.

```console
python application-insights-quickstart.py
```
