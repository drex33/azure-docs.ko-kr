---
title: 참조 - Azure Web PubSub 서비스에 대한 Python 서버 SDK
description: 참조는 Azure Web PubSub 서비스에 대한 Python 서버 SDK에 대해 설명합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: c394258a49e73055f6848eed87dd15e75bd1c069
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117078"
---
# <a name="python-server-sdk-for-azure-web-pubsub-service"></a>Azure Web PubSub 서비스에 대한 Python 서버 SDK

라이브러리를 사용하여 다음을 수행합니다.

- 허브 및 그룹에 메시지를 보냅니다.
- 특정 사용자 및 연결에 메시지를 보냅니다.
- 사용자와 연결을 그룹으로 구성합니다.
- 연결 종료
- 기존 연결에 대한 권한 부여/철회/확인

[소스 코드](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [패키지(Pypi)][package] | [API 참조 설명서](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [제품 설명서][webpubsubservice_docs] |
[샘플][samples_ref]

## <a name="getting-started"></a>시작

### <a name="installations-the-package"></a>패키지 설치

```bash
python -m pip install azure-messaging-webpubsubservice
```

#### <a name="prerequisites"></a>필수 조건

- 이 패키지를 사용하려면 Python 2.7 또는 3.6 이상이 필요합니다.
- 이 패키지를 사용하려면 [Azure 구독][azure_sub]과 [Azure WebPubSub 서비스 인스턴스][webpubsubservice_docs]가 필요합니다.
- 기존 Azure Web PubSub 서비스 인스턴스.

### <a name="authenticating-the-client"></a>클라이언트 인증

Azure WebPubSub 서비스와 상호 작용하려면 [WebPubSubServiceClient][webpubsubservice_client_class] 클래스의 인스턴스를 만들어야 합니다. 서비스에 대해 인증하려면 엔드포인트 및 액세스 키가 있는 AzureKeyCredential 인스턴스를 전달해야 합니다. 엔드포인트 및 액세스 키는 Azure Portal에서 찾을 수 있습니다.

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> client
<WebPubSubServiceClient endpoint:'<endpoint>'>
```

## <a name="examples"></a>예제

### <a name="sending-a-request"></a>요청 보내기

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> from azure.messaging.webpubsubservice.rest import build_send_to_all_request
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> request = build_send_to_all_request('default', json={ 'Hello':  'webpubsub!' })
>>> request
<HttpRequest [POST], url: '/api/hubs/default/:send?api-version=2020-10-01'>
>>> response = client.send_request(request)
>>> response
<RequestsTransportResponse: 202 Accepted>
>>> response.status_code 
202
>>> with open('file.json', 'r') as f:
>>>    request = build_send_to_all_request('ahub', content=f, content_type='application/json')
>>>    response = client.send_request(request)
>>> print(response)
<RequestsTransportResponse: 202 Accepted>
```

## <a name="key-concepts"></a>주요 개념

[!INCLUDE [Terms](includes/terms.md)]

## <a name="troubleshooting"></a>문제 해결

### <a name="logging"></a>로깅

이 SDK는 Python 표준 로깅 라이브러리를 사용합니다.
stdout 또는 원하는 위치에 로깅 인쇄 디버깅 정보를 구성할 수 있습니다.

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

HTTP 요청 및 응답 세부 정보는 이 로깅 구성을 사용하여 stdout에 인쇄됩니다.

[webpubsubservice_docs]: https://aka.ms/awps/doc
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[webpubsubservice_client_class]: https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice/azure/messaging/webpubsubservice/__init__.py
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]
