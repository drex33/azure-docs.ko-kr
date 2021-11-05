---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 60c534a64bae703de30cd7aaddd1da31d27e750b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461210"
---
## <a name="set-up-prerequisites"></a>필수 조건 설정

- [Python](https://www.python.org/downloads/) 2.7 또는 3.6 이상

## <a name="set-up"></a>설정

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

1. 터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

   ```console
   mkdir communication-access-tokens-quickstart && cd communication-access-tokens-quickstart
   ```

1. 텍스트 편집기를 사용하여 프로젝트 루트 디렉터리에 `exchange-communication-access-tokens.py`라는 파일을 만들고 기본 예외 처리를 비롯한 프로그램의 구조를 추가합니다. 이 빠른 시작의 모든 소스 코드를 다음 섹션의 이 파일에 추가합니다.

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 `pip install` 명령을 사용하여 Python 패키지용 Azure Communication Services ID SDK를 설치합니다.

```console
pip install azure-communication-identity
pip install msal
```

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>1단계: MSAL 라이브러리를 통해 Azure AD 사용자 토큰 받기

토큰 교환 흐름의 첫 번째 단계는 [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md)를 사용하여 Teams 사용자에 대한 토큰을 가져오는 것입니다.

```python
from msal.application import PublicClientApplication

client_id = "<contoso_application_id>"
tenant_id = "<contoso_tenant_id>"
authority = "https://login.microsoftonline.com/%s" % tenant_id

app = PublicClientApplication(client_id, authority=authority)

scope = [ "https://auth.msft.communication.azure.com/VoIP" ]
teams_token_result = app.acquire_token_interactive(scope)
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>2단계: CommunicationIdentityClient 초기화

연결 문자열로 `CommunicationIdentityClient`를 인스턴스화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

다음 코드를 `try` 블록 내에 추가합니다.

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>3단계: Teams 액세스 토큰에 대한 Azure AD 사용자 토큰 교환

`get_token_for_teams_user` 메서드를 사용하여 Azure Communication Services SDK와 함께 사용할 수 있는 Teams 사용자에 대한 액세스 토큰을 발급합니다.

```python
token_result = client.get_token_for_teams_user(teams_token_result['access_token'])
print("Token: " + token_result.token)
```

## <a name="run-the-code"></a>코드 실행

콘솔 프롬프트에서 *exchange-teams-access-tokens.py* 파일이 포함된 디렉터리로 이동한 후, 다음 `python` 명령을 실행하여 앱을 실행합니다.

```console
python ./exchange-communication-access-tokens.py
```
