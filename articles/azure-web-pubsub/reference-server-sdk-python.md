---
title: 참조-Azure 웹 PubSub 용 Python 서버 SDK
description: 이 참조는 Azure 웹 PubSub 서비스용 Python 서버 SDK에 대해 설명 합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: f28ab302aa6899e35c080821ccddf01d5e7cb76e
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579529"
---
# <a name="python-server-sdk-for-azure-web-pubsub"></a>Azure 웹 PubSub 용 Python 서버 SDK

이 라이브러리를 사용 하 여 다음을 수행할 수 있습니다.

- 허브 및 그룹에 메시지를 보냅니다. 
- 특정 사용자 및 연결에 메시지를 보냅니다.
- 사용자와 연결을 그룹으로 구성합니다.
- 연결을 닫습니다.
- 기존 연결에 대 한 권한을 부여, 취소 및 확인 합니다.

[소스 코드](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [패키지(Pypi)][package] | [API 참조 설명서](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [제품 설명서][webpubsubservice_docs] |
[샘플][samples_ref]

## <a name="get-started"></a>시작

다음과 같이 패키지를 설치 합니다.

```bash
python -m pip install azure-messaging-webpubsubservice==1.0.0b1
```

### <a name="prerequisites"></a>필수 조건

- 이 패키지를 사용 하려면 Python 2.7 또는 3.6 이상이 필요 합니다.
- [Azure 구독][azure_sub].
- 기존 Azure Web PubSub 서비스 인스턴스.

### <a name="authenticate-the-client"></a>클라이언트 인증

Azure WebPubSub 서비스와 상호 작용 하려면 클래스의 인스턴스를 만들어야 [`WebPubSubServiceClient`][webpubsubservice_client_class] 합니다. 서비스에 대해 인증 하려면 끝점 및 액세스 키를 사용 하 여 ' AzureKeyCredential ' 인스턴스를 전달 해야 합니다. Azure Portal에서 끝점 및 액세스 키를 찾을 수 있습니다.

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

이 SDK는 Python의 표준 로깅 라이브러리를 사용 합니다. 또는 원하는 모든 위치에 대 한 로깅 인쇄 디버깅 정보를 구성할 수 있습니다 `stdout` .

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

HTTP 요청 및 응답 세부 정보는 `stdout` 이 로깅 구성으로 출력 됩니다.

[webpubsubservice_docs]: ./index.yml
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[webpubsubservice_client_class]: https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice/azure/messaging/webpubsubservice/__init__.py
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]