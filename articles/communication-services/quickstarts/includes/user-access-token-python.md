---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/13/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: ea706f98e7da8295305fd0a7cdccfd841e1ce3f3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521578"
---
> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/access-tokens-quickstart)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 또는 3.6 이상
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../create-communication-resource.md).

## <a name="setting-up"></a>설치

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

1. 터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. 텍스트 편집기를 사용하여 프로젝트 루트 디렉터리에 **issue-access-tokens.py** 라는 파일을 만들고 기본 예외 처리를 비롯한 프로그램의 구조를 추가합니다. 이 빠른 시작의 모든 소스 코드를 다음 섹션의 이 파일에 추가합니다.

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
```

## <a name="authenticate-the-client"></a>클라이언트 인증

연결 문자열로 `CommunicationIdentityClient`를 인스턴스화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

다음 코드를 `try` 블록 내에 추가합니다.

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

또는 Azure AD(Active Directory) 애플리케이션이 설정되어 있는 경우 [서비스 주체 사용](../identity/service-principal.md)을 참조하여 AD로 인증할 수도 있습니다.
```python
endpoint = os.environ["COMMUNICATION_SERVICES_ENDPOINT"]
client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
```

## <a name="create-an-identity"></a>ID 만들기

Azure Communication Services는 경량 ID 디렉터리를 유지 관리합니다. `create_user` 메서드를 사용하여 고유한 `Id`가 있는 디렉터리에 새 항목을 만듭니다. 애플리케이션 사용자에게 매핑하여 수신된 ID를 저장합니다. 예를 들어 애플리케이션 서버의 데이터베이스에 저장합니다. ID는 나중에 액세스 토큰을 발급하는 데 필요합니다.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.properties['id'])
```

## <a name="issue-access-tokens"></a>액세스 토큰 발급

`get_token` 메서드를 사용하여 이미 존재하는 Communication Services ID에 대한 액세스 토큰을 발급합니다. 매개 변수 `scopes`는 이 액세스 토큰에 권한을 부여하는 기본 형식 세트를 정의합니다. [지원되는 작업 목록](../../concepts/authentication.md)을 참조하세요. 매개 변수 `CommunicationUserIdentifier`의 새 인스턴스는 Azure Communication Service ID의 문자열 표현에 따라 구성될 수 있습니다.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.get_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

액세스 토큰은 다시 발급해야 하는 단기 자격 증명입니다. 이렇게 하지 않으면 애플리케이션의 사용자 환경이 중단될 수 있습니다. `expires_on` 응답 속성은 액세스 토큰의 수명을 나타냅니다.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>ID를 만들고 동일한 요청 내에서 액세스 토큰을 발급합니다.

`create_user_and_token` 메서드를 사용하여 Communication Services ID를 만들고 이에 대한 액세스 토큰을 발급합니다. 매개 변수 `scopes`는 이 액세스 토큰에 권한을 부여하는 기본 형식 세트를 정의합니다. [지원되는 작업 목록](../../concepts/authentication.md)을 참조하세요.

```python
# Issue an identity and an access token with the "voip" scope for the new identity
identity_token_result = client.create_user_and_token(["voip"])
identity = identity_token_result[0]
token = identity_token_result[1].token
expires_on = identity_token_result[1].expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nCreated an identity with ID: " + identity.properties['id'])
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token)
```

## <a name="refresh-access-tokens"></a>액세스 토큰 새로 고침

액세스 토큰을 새로 고치려면 `CommunicationUserIdentifier` 개체를 사용하여 다시 발급합니다.

```python
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUserIdentifier(existingIdentity)
token_result = client.get_token(identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>액세스 토큰 취소

경우에 따라 액세스 토큰을 명시적으로 취소할 수 있습니다. 예를 들어 애플리케이션의 사용자가 서비스에 인증하는 데 사용하는 암호를 변경하는 경우입니다. 메서드 `revoke_tokens`는 ID에 발급된 모든 활성 액세스 토큰을 무효화합니다.

```python
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.properties['id'])
```

## <a name="delete-an-identity"></a>ID 삭제

ID를 삭제하면 모든 활성 액세스 토큰이 취소되고 ID에 대한 액세스 토큰을 발급할 수 없게 됩니다. 또한 ID와 연결된 모든 지속형 콘텐츠를 제거합니다.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.properties['id'])
```

## <a name="run-the-code"></a>코드 실행

콘솔 프롬프트에서 *issue-access-token.py* 파일이 포함된 디렉터리로 이동한 후, 다음 `python` 명령을 실행하여 앱을 실행합니다.

```console
python ./issue-access-tokens.py
```