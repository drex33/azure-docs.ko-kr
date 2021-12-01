---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: 248ad2602d2b9a17d7b99eb46a049e904b752997
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133385343"
---
Communication Services Python SMS SDK를 사용하여 SMS 메시지를 보내 Azure Communication Services를 시작합니다.

이 빠른 시작을 완료하면 Azure 계정에서 USD 센트 이하의 작은 비용이 발생합니다.

> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/send-sms-quickstart)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 또는 3.6 이상
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- SMS 지원 전화 번호입니다. [전화 번호를 가져옵니다](../../telephony/get-phone-number.md).

### <a name="prerequisite-check"></a>필수 구성 요소 확인

- 터미널 또는 명령 창에서 `python --version` 명령을 실행하여 Python이 설치되어 있는지 확인합니다.
- Communication Services 리소스와 연결된 전화 번호를 보려면 [Azure Portal](https://portal.azure.com/)에 로그인하고 Communication Services 리소스를 찾아 왼쪽 탐색 창에서 **전화 번호** 탭을 엽니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir sms-quickstart && cd sms-quickstart
```

텍스트 편집기를 사용하여 프로젝트 루트 디렉터리에 **send-sms.py** 라는 파일을 만들고 기본 예외 처리를 비롯한 프로그램의 구조를 추가합니다. 이 빠른 시작의 모든 소스 코드를 다음 섹션의 이 파일에 추가합니다.

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 `pip install` 명령을 사용하여 Python 패키지용 Azure Communication Services SMS SDK를 설치합니다.

```console
pip install azure-communication-sms
```

## <a name="object-model"></a>개체 모델

다음 클래스 및 인터페이스는 Python용 Azure Communication Services SMS SDK의 주요 기능 중 일부를 처리합니다.

| Name                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | 이 클래스는 모든 SMS 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 SMS 메시지를 보내는 데 사용합니다.                                                                                                                 |
| SmsSendResult               | 이 클래스는 SMS 서비스의 결과를 포함합니다.                                          |

## <a name="authenticate-the-client"></a>클라이언트 인증

연결 문자열로 **SmsClient** 를 인스턴스화합니다. [리소스의 연결 문자열을 관리](../../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

```python
# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(<connection_string>)
```
간단한 설명을 위해 이 빠른 시작에서 연결 문자열을 사용하지만 프로덕션 환경에서는 [서비스 주체](../../../quickstarts/identity/service-principal.md)를 사용하는 것이 좋습니다.

## <a name="send-a-11-sms-message"></a>1:1 SMS 메시지 보내기

단일 수신자에게 SMS 메시지를 보내려면 단일 수신자 전화 번호로 **SmsClient** 에서 ```send``` 메서드를 호출합니다. 선택적 매개 변수를 전달하여 전달 보고서를 사용하도록 설정할지 여부를 지정하고 사용자 지정 태그를 설정할 수도 있습니다. 다음 코드를 **send-sms.py** 의 `try` 블록 끝에 추가합니다.

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>",
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

`<from-phone-number>`를 Communication Services와 연결된 SMS 지원 전화 번호로 바꾸고, `<to-phone-number>`를 메시지를 보낼 전화 번호로 바꿔야 합니다.

> [!WARNING]
> 전화 번호는 E.164 국제 표준 형식(예: +14255550123)으로 제공되어야 합니다. **발신** 전화번호도 단축 코드일 수 있습니다(예: 23456).

## <a name="send-a-1n-sms-message"></a>1:N SMS 메시지 보내기

SMS 메시지를 수신자 목록에 보내려면 수신자의 전화 번호 목록이 포함된 **SmsClient** 에서 ```send``` 메서드를 호출합니다. 선택적 매개 변수를 전달하여 전달 보고서를 사용하도록 설정할지 여부를 지정하고 사용자 지정 태그를 설정할 수도 있습니다. 다음 코드를 **send-sms.py** 의 `try` 블록 끝에 추가합니다.

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

`<from-phone-number>`를 Communication Services와 연결된 SMS 지원 전화 번호로 바꾸고, `<to-phone-number-1>` `<to-phone-number-2>`를 메시지를 보낼 전화 번호로 바꿔야 합니다.

> [!WARNING]
> 전화 번호는 E.164 국제 표준 형식(예: +14255550123)으로 제공되어야 합니다. **발신** 전화번호도 단축 코드일 수 있습니다(예: 23456).

## <a name="optional-parameters"></a>선택적 매개 변수

`enable_delivery_report` 매개 변수는 전달 보고를 구성하는 데 사용할 수 있는 선택적 매개 변수입니다. 이 기능은 SMS 메시지가 전달될 때 이벤트를 내보내려는 시나리오에 유용합니다. SMS 메시지에 대한 전달 보고를 구성하려면 [SMS 이벤트 처리](../handle-sms-events.md)를 참조하세요.

`tag` 매개 변수는 전달 보고서에 태그를 적용하는 데 사용할 수 있는 선택적 매개 변수입니다.

## <a name="run-the-code"></a>코드 실행
`python` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```console
python send-sms.py
```

전체 Python 스크립트는 다음과 같습니다.

```python

import os
from azure.communication.sms import SmsClient

try:
    # Create the SmsClient object which will be used to send SMS messages
    sms_client = SmsClient.from_connection_string("<connection string>")
    # calling send() with sms values
    sms_responses = sms_client.send(
       from_="<from-phone-number>",
       to="<to-phone-number>",
       message="Hello World via SMS",
       enable_delivery_report=True, # optional property
       tag="custom-tag") # optional property

except Exception as ex:
    print('Exception:')
    print(ex)
```
