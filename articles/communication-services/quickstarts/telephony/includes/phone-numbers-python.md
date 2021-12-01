---
ms.openlocfilehash: 719c0d1c03ed82d69e798df440fb2989d6015662
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133385391"
---
> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/phone-numbers-quickstart)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 이상.
- 배포된 Communication Services 리소스 및 연결 문자열 [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).

## <a name="setting-up"></a>설치

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만든 다음, 해당 디렉터리로 이동합니다.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

텍스트 편집기를 사용하여 phone_numbers_sample.py라고 하는 파일을 프로젝트 루트 디렉터리에 만들고 다음 코드를 추가합니다. 다음 섹션에서 나머지 빠른 시작 코드를 추가합니다.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 `pip install` 명령을 사용하여 Python 패키지용 Azure Communication Services 관리 클라이언트 라이브러리를 설치합니다.

```console
pip install azure-communication-phonenumbers
```

## <a name="authenticate-the-phone-numbers-client"></a>전화 번호 클라이언트 인증

`PhoneNumbersClient`가 Azure Active Directory 인증을 사용하도록 설정되어 있습니다. `DefaultAzureCredential` 개체를 사용하면 가장 손쉽게 Azure Active Directory를 시작할 수 있으며, `pip install` 명령을 사용하여 설치할 수 있습니다.

```console
pip install azure-identity
```

`DefaultAzureCredential` 개체를 만들려면 등록된 Azure AD 애플리케이션에서 `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` 및 `AZURE_TENANT_ID`가 해당 값을 사용하여 환경 변수로 이미 등록되어 있어야 합니다.

환경 변수를 가져오는 방법을 빠르게 확인하려면 [CLI에서 서비스 주체 설정 빠른 시작](../../identity/service-principal-from-cli.md)을 따를 수 있습니다.

`azure-identity` 라이브러리를 설치했으면 클라이언트를 계속 인증할 수 있습니다.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

또는 통신 리소스의 엔드포인트 및 액세스 키를 사용하여 인증할 수도 있습니다.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>Functions

`PhoneNumbersClient`가 인증된 후에는 수행할 수 있는 다른 함수에서 작업을 시작할 수 있습니다.

### <a name="search-for-available-phone-numbers"></a>사용 가능한 전화 번호 검색

전화 번호를 구매하려면 먼저 사용 가능한 전화 번호를 검색해야 합니다. 전화 번호를 검색하려면 지역 번호, 할당 유형, [전화 번호 기능](../../../concepts/telephony/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [전화 번호 유형](../../../concepts/telephony/plan-solution.md#phone-number-types-in-azure-communication-services) 및 수량(기본 수량은 1로 설정됨)을 제공합니다. 수신자 부담 전화 번호 형식의 경우 지역 코드 제공은 선택 사항입니다.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>전화 번호 구매

전화 번호 검색의 결과는 `PhoneNumberSearchResult`입니다. 여기에는 검색 시 숫자를 얻기 위해 번호 구매 API에 전달할 수 있는 `searchId`가 포함됩니다. 전화 번호 구매 API를 호출하면 Azure 계정에 요금이 부과됩니다.

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient,
    PhoneNumberCapabilityType,
    PhoneNumberAssignmentType,
    PhoneNumberType,
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>구매한 전화 번호 가져오기

번호 구매 후에 클라이언트에서 검색할 수 있습니다.

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

구매한 전화 번호를 모두 검색할 수도 있습니다.

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>전화 번호 기능 업데이트

이전에 구매한 전화 번호의 기능을 업데이트할 수 있습니다.
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>전화 번호 해제

구매한 전화 번호를 해제할 수 있습니다.

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>코드 실행

콘솔 프롬프트에서 phone_numbers_sample.py 파일이 포함된 디렉터리로 이동한 다음, 다음 python 명령을 실행하여 앱을 실행합니다.

```console
python phone_numbers_sample.py
```