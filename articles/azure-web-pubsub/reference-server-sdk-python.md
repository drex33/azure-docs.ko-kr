---
title: 참조 - Azure Web PubSub용 Python 서버 SDK
description: 이 참조에서는 Azure Web PubSub 서비스에 대한 Python 서버 SDK를 설명합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 256dbd5e988f95f8e4df7ea6fd1c83b88bc45d46
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487426"
---
# <a name="azure-web-pubsub-service-client-library-for-python"></a>Python용 Azure Web PubSub 서비스 클라이언트 라이브러리

[Azure Web PubSub Service는](https://aka.ms/awps/doc) 개발자가 실시간 기능 및 게시-구독 패턴으로 웹 애플리케이션을 쉽게 빌드할 수 있도록 도와주는 Azure 관리형 서비스입니다. 서버와 클라이언트 간 또는 클라이언트 간에 실시간 게시-구독 메시징이 필요한 시나리오는 Azure Web PubSub 서비스를 사용할 수 있습니다. 서버에서 폴링하거나 HTTP 요청을 제출해야 하는 기존의 실시간 기능에서도 Azure Web PubSub 서비스를 사용할 수 있습니다.

아래 다이어그램과 같이 앱 서버 쪽에서 이 라이브러리를 사용하여 WebSocket 클라이언트 연결을 관리할 수 있습니다.

![오버플로 다이어그램은 서비스 클라이언트 라이브러리를 사용하는 의 오버플로를 보여줍니다.](media/sdk-reference/service-client-overflow.png)

이 라이브러리를 사용하여 다음을 수행합니다.
- 허브 및 그룹에 메시지를 보냅니다.
- 특정 사용자 및 연결에 메시지를 보냅니다.
- 사용자와 연결을 그룹으로 구성합니다.
- 연결 종료
- 기존 연결에 대한 권한 부여, 철회 및 확인

[소스 코드](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/webpubsub/azure-messaging-webpubsubservice)  |  [패키지(Pypi)][package]  |  [API 참조 설명서](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/webpubsub/azure-messaging-webpubsubservice)  |  [제품 설명서][webpubsubservice_docs]

## <a name="_disclaimer_"></a>_고지 사항_

_Python 2.7에 대한 Azure SDK Python 패키지 지원은 2022년 1월 1일에 종료됩니다. 자세한 내용 및 질문은 을 참조하세요. https://github.com/Azure/azure-sdk-for-python/issues/20691_

## <a name="getting-started"></a>시작

### <a name="prerequisites"></a>필수 조건

- 이 패키지를 사용하려면 Python 2.7 또는 3.6 이상이 필요합니다.
- 이 패키지를 사용하려면 [Azure 구독][azure_sub]및 [Azure WebPubSub 서비스 인스턴스가][webpubsubservice_docs] 필요합니다.
- 기존 Azure Web PubSub 서비스 인스턴스.

### <a name="1-install-the-package"></a>1. 패키지 설치

```bash
python -m pip install azure-messaging-webpubsubservice
```

### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. WebPubSubServiceClient 만들기 및 인증

연결 문자열 를 사용하여 를 인증할 수 있습니다. `WebPubSubServiceClient` [][connection_string]

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

>>> service = WebPubSubServiceClient.from_connection_string(connection_string='<connection_string>', hub='hub')
```

또는 서비스 엔드포인트 및 액세스 키를 사용하세요.

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential

>>> service = WebPubSubServiceClient(endpoint='<endpoint>', hub='hub', credential=AzureKeyCredential("<access_key>"))
```

또는 [Azure Active Directory][aad_doc]를 사용하세요.
1. [pip][pip] 설치 [`azure-identity`][azure_identity_pip]
2. 문서에 따라 [Webpubsub 리소스에서 AAD 인증을 사용하도록 설정합니다.][aad_doc]
3. [DefaultAzureCredential을][default_azure_credential] 사용하도록 코드 업데이트

    ```python
    >>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
    >>> from azure.identity import DefaultAzureCredential
    >>> service = WebPubSubServiceClient(endpoint='<endpoint>', hub='hub', credential=DefaultAzureCredential())
    ```

## <a name="key-concepts"></a>주요 개념

### <a name="connection"></a>연결

클라이언트 또는 클라이언트 연결이라고도 하는 연결은 Web PubSub 서비스에 연결된 개별 WebSocket 연결을 나타냅니다. 성공적으로 연결되면 Web PubSub 서비스에서 고유한 연결 ID를 이 연결에 할당합니다.

### <a name="hub"></a>허브

허브는 클라이언트 연결 집합에 대한 논리적 개념입니다. 일반적으로 하나의 목적에 대해 하나의 허브를 사용합니다(예: 채팅 허브 또는 알림 허브). 클라이언트 연결이 만들어지면 허브에 연결되고 수명 동안 해당 허브에 속합니다. 애플리케이션마다 서로 다른 허브 이름을 사용하여 하나의 Azure Web PubSub 서비스를 공유할 수 있습니다.

### <a name="group"></a>그룹

그룹은 허브에 대한 연결의 하위 집합입니다. 그룹에 클라이언트 연결을 추가하거나 원하는 경우 그룹에서 클라이언트 연결을 제거할 수 있습니다. 예를 들어 클라이언트가 채팅방에 참가하거나 채팅방에서 나가면 이 채팅방이 그룹일 수 있습니다. 클라이언트는 여러 그룹에 참가할 수 있으며, 한 그룹에 여러 클라이언트가 포함될 수 있습니다.

### <a name="user"></a>사용자

Web PubSub에 대한 연결은 한 명의 사용자에게 속할 수 있습니다. 단일 사용자가 여러 디바이스 또는 여러 브라우저 탭에서 연결된 경우와 같이 사용자에게 여러 연결이 있을 수 있습니다.

### <a name="message"></a>메시지

클라이언트가 연결되면 업스트림 애플리케이션으로 메시지를 보내거나 WebSocket 연결을 통해 업스트림 애플리케이션에서 메시지를 받을 수 있습니다.

## <a name="examples"></a>예

### <a name="broadcast-messages-in-json-format"></a>JSON 형식으로 메시지 브로드캐스트

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub1')
>>> service.send_to_all(message = {
        'from': 'user1',
        'data': 'Hello world'
    })
```

WebSocket 클라이언트는 JSON 직렬화된 텍스트 를 `{"from": "user1", "data": "Hello world"}` 받습니다.

### <a name="broadcast-messages-in-plain-text-format"></a>일반 텍스트 형식으로 메시지 브로드캐스트

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub1')
>>> service.send_to_all(message = 'Hello world', content_type='text/plain')
```

WebSocket 클라이언트는 텍스트를 `Hello world` 받습니다.

### <a name="broadcast-messages-in-binary-format"></a>이진 형식으로 메시지 브로드캐스트

```python
>>> import io
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub')
>>> service.send_to_all(message=io.StringIO('Hello World'), content_type='application/octet-stream')
```
WebSocket 클라이언트는 이진 텍스트 를 `b'Hello world'` 받습니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="logging"></a>로깅

이 SDK는 Python 표준 로깅 라이브러리를 사용합니다.
로깅을 구성하여 stdout 또는 원하는 위치에 디버깅 정보를 출력할 수 있습니다.

```python
import sys
import logging
from azure.identity import DefaultAzureCredential
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "<endpoint>"
credential = DefaultAzureCredential()

# This WebPubSubServiceClient will log detailed information about its HTTP sessions, at DEBUG level
service = WebPubSubServiceClient(endpoint=endpoint, hub='hub', credential=credential, logging_enable=True)
```

마찬가지로 는 `logging_enable` WebPubSubServiceClient에 대해 사용하도록 설정되지 않은 경우에도 단일 호출에 대해 자세한 로깅을 사용하도록 설정할 수 있습니다.

```python
result = service.send_to_all(..., logging_enable=True)
```

HTTP 요청 및 응답 세부 정보는 이 로깅 구성을 사용하여 stdout에 인쇄됩니다.

## <a name="next-steps"></a>다음 단계

[여기에서 더 많은 샘플을 확인하세요.][samples]

## <a name="contributing"></a>참여

이 프로젝트에 대한 기여와 제안을 환영합니다. 대부분의 경우 기여하려면 권한을 부여하며 실제로 기여를 사용할 권한을 당사에 부여한다고 선언하는 CLA(기여자 라이선스 계약)에 동의해야 합니다.
자세한 내용은 https://cla.microsoft.com 을 참조하세요.

끌어오기 요청을 제출하면 CLA-bot은 CLA를 제공하고 PR을 적절하게 데코레이팅해야 하는지 여부를 자동으로 결정합니다(예: 레이블, 설명). 봇에서 제공하는 지침을 따르기만 하면 됩니다. 이 작업은 CLA를 사용하여 모든 리포지토리에서 한 번만 수행하면 됩니다.

이 프로젝트에는 [Microsoft Open Source Code of Conduct][code_of_conduct](Microsoft 오픈 소스 준수 사항)가 적용됩니다. 자세한 내용은 준수 사항 FAQ를 참조하거나 opencode@microsoft.com 추가 질문 또는 의견을 문의하세요.

<!-- LINKS -->
[webpubsubservice_docs]: https://aka.ms/awps/doc
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[cla]: https://cla.microsoft.com
[code_of_conduct]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[coc_contact]: mailto:opencode@microsoft.com
[azure_identity_credentials]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#credentials
[azure_identity_pip]: https://pypi.org/project/azure-identity/
[default_azure_credential]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#defaultazurecredential
[pip]: https://pypi.org/project/pip/
[enable_aad]: howto-develop-create-instance.md
[api_key]: howto-websocket-connect.md#authorization
[connection_string]: howto-websocket-connect.md#authorization
[azure_portal]: howto-develop-create-instance.md
[azure-key-credential]: https://aka.ms/azsdk-python-core-azurekeycredential
[aad_doc]: howto-authorize-from-application.md
[samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/webpubsub/azure-messaging-webpubsubservice/samples
