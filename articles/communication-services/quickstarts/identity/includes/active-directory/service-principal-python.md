---
ms.openlocfilehash: ad95a54a62c863b3c80cc372d57efbdd2adc49dc
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656965"
---
> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/use-managed-Identity)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="setting-up"></a>설치

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir active-directory-authentication-quickstart && cd active-directory-authentication-quickstart
```

### <a name="install-the-sdk-packages"></a>SDK 패키지 설치

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="create-a-new-file"></a>새 파일 만들기
`authentication.py`라는 생성된 폴더에서 새 파일을 열고 저장합니다. 이 파일에 코드를 넣을 것입니다.

### <a name="use-the-sdk-packages"></a>SDK 패키지 사용

파일의 맨 위에 다음 `import` 문을 추가하여 설치한 SDK를 사용합니다.

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient
```

### <a name="create-a-defaultazurecredential"></a>DefaultAzureCredential 만들기

[DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential)을 사용하겠습니다. 이 자격 증명은 프로덕션 환경과 개발 환경에 적합합니다. 이 빠른 시작에서 사용할 것이므로 파일 맨 위에 만들겠습니다.

```python
     credential = DefaultAzureCredential()
```

## <a name="create-an-identity-and-issue-a-token-with-service-principals"></a>ID 만들기 및 서비스 주체로 토큰 발급

이제 생성된 자격 증명을 사용하는 코드를 추가하여 VoIP 액세스 토큰을 발급합니다. 나중에 이 코드를 호출합니다.

```python
def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-service-principals"></a>서비스 주체와 SMS 보내기
서비스 주체를 사용하는 또 다른 예로, 동일한 자격 증명을 사용하여 SMS를 보내는 다음 코드를 추가합니다.

```python
def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

## <a name="write-our-main-code"></a>기본 코드 작성

생성된 함수를 사용하여 이전에 작성한 함수를 호출하는 기본 코드를 작성할 수 있습니다.

```python
# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Service Principals");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Service Principals");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Service Principals");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```

최종 `authentication.py` 파일은 다음과 같습니다.

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient

credential = DefaultAzureCredential()

def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     response = sms_client.send(
          from_=from_phone_number,
          to=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
     return response

# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Service Principals");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Service Principals");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Service Principals");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```
## <a name="run-the-program"></a>프로그램 실행

모든 작업이 완료되면 프로젝트의 디렉터리에서 `python authentication.py`를 입력하여 파일을 실행할 수 있습니다. 모든 것이 순조롭게 진행되면 다음과 같은 페이지가 표시됩니다.

```Bash
    $ python authentication.py
    Retrieving new Access Token, using Service Principals
    Retrieved Access Token: ey...Q
    Sending SMS using using Service Principals
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
