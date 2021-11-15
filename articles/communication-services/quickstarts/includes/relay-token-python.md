---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: rahulva
manager: shahen
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/30/2021
ms.topic: include
ms.custom: include file
ms.author: rahulva
ms.openlocfilehash: 35319f125578143abc2518d8037e0050dd9a6041
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893262"
---
> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/relay-token-quickstart)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="prerequisites-for-python"></a>Python에 대한 필수 구성 요소

 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 또는 3.6 이상
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../create-communication-resource.md).

## <a name="setting-up"></a>설치

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

1. 터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

   ```console
   mkdir access-tokens-quickstart && cd relay-tokens-quickstart
   ```

2. 텍스트 편집기를 사용하여 프로젝트 루트 디렉터리에 **issue-relay-tokens.py** 라는 파일을 만들고 기본 예외 처리를 비롯한 프로그램의 구조체를 추가합니다. 이 빠른 시작의 모든 소스 코드를 다음 섹션의 이 파일에 추가합니다.

   ```python
    import os
    from azure.communication.networktraversal import CommunicationRelayClient
    from azure.identity import DefaultAzureCredential
    from azure.communication.identity import CommunicationIdentityClient
    
    try:
      print("Azure Communication Services - Access Relay Configuration  Quickstart")
      # Quickstart code goes here
    except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 `pip install` 명령을 사용하여 Python 패키지용 Azure Communication Services ID SDK를 설치합니다.

```console
pip install azure-communication-identity
pip install azure.communication.networktraversal
```

## <a name="authenticate-the-client"></a>클라이언트 인증

리소스의 액세스 키 및 엔드포인트를 사용하여 `CommunicationIdentityClient`를 인스턴스화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다. 

다음 코드를 `try` 블록 내에 추가합니다.

```python

# You can find your endpoint and access token from your resource in the Azure Portal
connection_str = "endpoint=ENDPOINT;accessKey=KEY"
endpoint = "https://<RESOURCE_NAME>.communication.azure.com"

# To use Azure Active Directory Authentication (DefaultAzureCredential) make sure to have
# AZURE_TENANT_ID, AZURE_CLIENT_ID and AZURE_CLIENT_SECRET as env variables.
# We also need Identity client to get a User Identifier
identity_client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
relay_client = CommunicationRelayClient(endpoint, DefaultAzureCredential())

#You can also authenticate using your connection string
identity_client = CommunicationIdentityClient.from_connection_string(self.connection_string)
relay_client = CommunicationRelayClient.from_connection_string(self.connection_string)
```

## <a name="create-a-user-from-identity"></a>ID에서 사용자 만들기 

Azure Communication Services는 경량 ID 디렉터리를 유지 관리합니다. `create_user` 메서드를 사용하여 고유한 `Id`가 있는 디렉터리에 새 항목을 만듭니다. 애플리케이션 사용자에게 매핑하여 수신된 ID를 저장합니다. 예를 들어 애플리케이션 서버의 데이터베이스에 저장합니다. ID는 나중에 액세스 토큰을 발급하는 데 필요합니다.

```python
user = identity_client.create_user()
```

## <a name="getting-the-relay-configuration"></a>릴레이 구성 가져오기
Azure Communication 토큰 서비스를 호출하여 TURN 서비스 토큰의 사용자 액세스 토큰을 교환합니다.

```python
relay_configuration = relay_client.get_relay_configuration(user)

for iceServer in config.ice_servers:
    assert iceServer.username is not None
    print('Username: ' + iceServer.username)

    assert iceServer.credential is not None
    print('Credential: ' + iceServer.credential)
    
    assert iceServer.urls is not None
    for url in iceServer.urls:
        print('Url:' + url)
```     

## <a name="run-the-code"></a>코드 실행

콘솔 프롬프트에서 *issue-relay-tokens.py* 파일이 포함된 디렉터리로 이동한 후, 다음 `python` 명령을 실행하여 앱을 실행합니다.

```console
python ./issue-relay-tokens.py
```
