---
title: 자습서 - Postman을 사용하여 ACS의 SMS API에 서명 및 요청
titleSuffix: An Azure Communication Services tutorial
description: Postman을 사용하여 SMS 메시지를 보내기 위해 ACS에 서명하고 요청하는 방법을 알아봅니다.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: tutorial
ms.service: azure-communication-services
ms.subservice: sms
ms.openlocfilehash: 16f167a66d0a978edf1b102c5a25b26d2868b7ff
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133360350"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>자습서: Postman을 사용하여 서명 및 요청
이 자습서에서는 HTTP를 사용하여 Azure Communication Services에 대해 요청하기 위해 Postman을 설정하고 사용합니다. 이 자습서를 마치면 Communication Services 및 Postman을 사용하여 SMS 메시지를 성공적으로 보낼 수 있습니다. 그런 다음, Postman을 사용하여 Azure Communication Services 내의 다른 API를 탐색할 수 있습니다.

이 자습서에서는 다음 작업을 수행합니다.
> [!div class="checklist"]
> * Postman 다운로드
> * HTTP 요청에 서명하도록 Postman 설정
> * 메시지를 보내도록 Communication Services SMS API에 대해 요청.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요. 체험 계정에는 모든 서비스 조합을 사용해 볼 수 있는 200달러의 Azure 크레딧이 제공됩니다.
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만드는 방법에 대해 자세히 알아보세요](../quickstarts/create-communication-resource.md).
- SMS 메시지를 보낼 수 있는 ACS 전화 번호는 [전화 번호 받기](../quickstarts/telephony/get-phone-number.md)를 참조하세요.

## <a name="downloading-and-installing-postman"></a>Postman 다운로드 및 설치

Postman은 모든 HTTP API에 대해 API 요청을 수행할 수 있는 데스크톱 애플리케이션입니다. 일반적으로 API를 테스트하고 검색하는 데 사용됩니다. [Postman의 웹 사이트에서 최신 데스크톱 버전](https://www.postman.com/downloads/)을 다운로드합니다. Postman에는 Windows, Mac 및 Linux용 버전이 있으므로 운영 체제에 적합한 버전을 다운로드합니다. 다운로드가 완료되면 애플리케이션을 엽니다. 로그인하거나 Postman 계정을 만들도록 요청하는 시작 화면이 표시됩니다. 계정 만들기는 선택 사항이며, "건너뛰고 앱으로 이동" 링크를 클릭하여 건너뛸 수 있습니다. 계정을 만들면 API 요청 설정이 Postman에 저장되므로 다른 컴퓨터에서 요청을 선택할 수 있습니다.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="계정을 만들거나 이를 건너뛰고 앱으로 이동할 수 있는 기능을 보여 주는 Postman의 시작 화면":::

계정을 만들거나 이를 건너뛰면 이제 Postman의 주 창이 표시됩니다.

## <a name="creating-and-configuring-a-postman-collection"></a>Postman 컬렉션 만들기 및 구성

Postman은 여러 가지 방법으로 요청을 구성할 수 있습니다. 이 자습서에서는 Postman 컬렉션을 만듭니다. 이렇게 하려면 애플리케이션의 왼쪽에 있는 컬렉션 단추를 선택합니다.

:::image type="content" source="media/postman/collections-tab.png" alt-text="컬렉션 탭이 강조 표시된 Postman의 주 화면":::

이 단추가 선택되면 "새 컬렉션 만들기"를 클릭하여 컬렉션 만들기 프로세스를 시작합니다. Postman의 가운데 영역에 새 탭이 열립니다. 컬렉션 이름을 원하는 대로 지정합니다. 여기서는 컬렉션 이름이 "ACS"입니다.

:::image type="content" source="media/postman/acs-collection.png" alt-text=" Communication Services 컬렉션이 열려 있고 컬렉션 이름이 강조 표시된 Postman.":::

컬렉션이 만들어지고 이름이 지정되면 구성할 준비가 되었습니다.

### <a name="adding-collection-variables"></a>컬렉션 변수 추가

인증을 처리하고 더 쉽게 요청하기 위해 두 개의 컬렉션 변수를 새로 만든 Communication Services 컬렉션 내에 지정합니다. 이러한 변수는 Communication Services 컬렉션 내의 모든 요청에 사용할 수 있습니다. 변수 만들기를 시작하려면 컬렉션의 변수 탭을 방문하세요.

:::image type="content" source="media/postman/variable-stab.png" alt-text="Communication Services 컬렉션의 변수 탭이 있는 Postman.":::

컬렉션 탭에서 두 개의 변수를 만듭니다.
- key - 이 변수는 Azure Portal 내의 Azure Communication Services 키 페이지에 있는 키 중 하나여야 합니다. 예: `oW...A==`.
- endpoint - 이 변수는 키 페이지의 Azure Communication Services 엔드포인트여야 합니다. **후행 슬래시를 제거해야 합니다**. 예: `https://contoso.communication.azure.com`.

이러한 값을 변수 화면의 "초기 값" 열에 입력합니다. 입력되면 오른쪽 테이블의 바로 위에 있는 "모두 유지" 단추를 누릅니다. 올바르게 구성되면 Postman 화면이 다음과 같이 표시됩니다.

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="Communication Services 컬렉션의 변수가 올바르게 설정된 Postman.":::

변수에 대한 자세한 내용은 [변수에 대한 Postman의 설명서](https://learning.postman.com/docs/sending-requests/variables)를 참조하세요.

### <a name="creating-a-pre-request-script"></a>사전 요청 스크립트 만들기

다음 단계에서는 Postman 내에서 사전 요청 스크립트를 만듭니다. 사전 요청 스크립트는 Postman에서 각 요청 전에 실행되는 스크립트이며, 사용자를 대신하여 요청 매개 변수를 수정하거나 변경할 수 있습니다. 이를 통해 Azure Communication Services에서 권한을 부여할 수 있도록 HTTP 요청에 서명합니다. 서명 요구 사항에 대한 자세한 내용은 [인증 가이드를 참조](/rest/api/communication/authentication)하세요.

컬렉션 내의 모든 요청에서 실행되도록 이 스크립트를 컬렉션 내에 만듭니다. 이렇게 하려면 컬렉션 탭 내에서 "사전 요청 스크립트" 하위 탭을 클릭합니다.

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="Communication Services 컬렉션의 사전 요청 스크립트 하위 탭이 선택된 Postman.":::

사전 요청 스크립트는 이 하위 탭 아래의 텍스트 영역에 입력하여 만들 수 있습니다. 완료되면 붙여넣기 전에 [Visual Studio Code](https://code.visualstudio.com/)와 같은 전체 코드 편집기 내에서 이를 작성하는 것이 더 쉬울 수 있습니다. 이 자습서에서는 스크립트의 각 부분을 살펴봅니다. Postman에 복사하여 시작하려면 끝으로 건너뛸 수 있습니다. 스크립트 작성을 시작해 보겠습니다.

### <a name="writing-the-pre-request-script"></a>사전 요청 스크립트 작성

먼저 UTC(협정 세계시) 문자열을 만들고 이를 "Date" HTTP 헤더에 추가합니다. 또한 나중에 서명할 때 사용할 수 있도록 이 문자열을 변수에 저장합니다.

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

다음으로 SHA 256을 사용하여 요청 본문을 해시하고 `x-ms-content-sha256` 헤더에 배치합니다. Postman에는 전역적으로 해시하고 서명하기 위한 일부 [표준 라이브러리](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries)가 포함되어 있으므로 이를 설치하거나 요구할 필요가 없습니다.

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

이제 이전에 지정한 엔드포인트 변수를 사용하여 HTTP Host 헤더의 값을 식별합니다. 이 스크립트가 처리될 때까지 Host 헤더가 설정되지 않으므로 이 작업을 수행해야 합니다.

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

이 정보가 만들어지면 이제 HTTP 요청에 서명할 문자열을 만들 수 있습니다. 이 문자열은 이전에 만든 몇 가지 값으로 구성됩니다.

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

마지막으로 Communication Services 키를 사용하여 이 문자열에 서명한 다음, `Authorization` 헤더의 요청에 추가해야 합니다.

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>최종 사전 요청 스크립트

최종 사전 요청 스크립트는 다음과 같습니다.

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

이 최종 스크립트를 사전 요청 스크립트 탭 내의 텍스트 영역에 입력하거나 붙여넣습니다.

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="ACS 컬렉션의 사전 요청 스크립트가 입력된 Postman":::

입력한 후 Ctrl+S를 누르거나 저장 단추를 누르면 스크립트가 컬렉션에 저장됩니다. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Postman의 사전 요청 스크립트 저장 단추":::

## <a name="creating-a-request-in-postman"></a>Postman에서 요청 만들기

이제 모든 항목이 설정되었으므로 Postman 내에서 Communication Services 요청을 만들 준비가 되었습니다. 시작하려면 Communication Services 컬렉션 옆에 있는 더하기(+) 아이콘을 클릭합니다.

:::image type="content" source="media/postman/create-request.png" alt-text="Postman의 더하기 단추":::

그러면 Postman 내에서 요청에 대한 새 탭이 만들어집니다. 새 탭이 만들어지면 이를 구성해야 합니다. SMS 보내기 API에 대한 요청을 수행하므로 [도움이 필요하면 이 API의 설명서](/rest/api/communication/sms/send)를 참조하세요. Postman의 요청을 구성해 보겠습니다.

먼저 요청 형식을 `POST`로 설정하고, 요청 URL 필드에서 `{{endpoint}}/sms?api-version=2021-03-07`을 입력합니다. 이 URL은 이전에 만든 `endpoint` 변수를 사용하여 Communication Services 리소스에 자동으로 보냅니다.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="형식이 POST로 설정되고 URL이 올바르게 설정된 Postman 요청":::

이제 요청의 본문 탭을 선택한 다음, 아래의 라디오 단추를 "raw"로 변경합니다. 오른쪽에는 "Text"라는 드롭다운이 있습니다. 이를 JSON으로 변경합니다.

:::image type="content" source="media/postman/postman-json.png" alt-text="요청 본문을 raw 및 JSON으로 설정":::

그러면 JSON 형식으로 정보를 보내고 받도록 요청이 구성됩니다.

아래의 텍스트 영역에서 요청 본문을 입력해야 하며, 다음 형식이어야 합니다.

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

"from" 값의 경우 앞에서 설명한 대로 Azure Communication Services 포털에서 [전화 번호를 가져와야](../quickstarts/telephony/get-phone-number.md) 합니다. 공백 없이 입력하고 국가 코드를 접두사로 사용합니다. 예를 들어 `+15555551234`을 참조하십시오. "message"는 무엇이든 보낼 수 있지만, `Hello from ACS`가 좋은 예입니다. "to" 값은 SMS 메시지를 받을 수 있는 액세스 권한이 있는 전화 번호여야 합니다. 사용자 고유의 모바일을 사용하는 것이 좋습니다.

입력되면 이 요청을 이전에 만든 Communication Services 컬렉션에 저장해야 합니다. 그러면 이전에 만든 변수와 사전 요청 스크립트가 선택됩니다. 이렇게 하려면 요청 영역의 오른쪽 위에 있는 "저장" 단추를 클릭합니다.

:::image type="content" source="media/postman/postman-save.png" alt-text="Postman 요청에 대한 저장 단추":::

그러면 요청을 호출할 대상 및 저장할 위치를 묻는 대화 상자 창이 표시됩니다. 원하는 이름을 지정할 수 있지만, 대화 상자의 아래쪽에서 Communication Services 컬렉션을 선택해야 합니다.

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="Communication Services 컬렉션이 선택된 Postman 저장 요청 대화 상자.":::

## <a name="sending-a-request"></a>요청 보내기

이제 모든 항목이 설정되었으므로 휴대폰에서 요청을 보내고 SMS 메시지를 받을 수 있습니다. 이렇게 하려면 만든 요청이 선택되어 있는지 확인한 다음, 오른쪽의 "보내기" 단추를 누릅니다.

:::image type="content" source="media/postman/postman-send.png" alt-text="보내기 단추가 강조 표시된 Postman 요청":::

모든 항목이 정상적으로 작동하면 이제 Communication Services의 응답이 표시됩니다. 이는 202 상태 코드여야 합니다.

:::image type="content" source="media/postman/postman-202.png" alt-text="202 상태 코드와 함께 성공적으로 보낸 Postman 요청":::

"to" 값에 제공한 번호를 소유하는 휴대폰에서도 SMS 메시지를 받습니다. 이제 Azure Communication Services와 통신하고 SMS 메시지를 보낼 수 있는 기능적인 Postman 구성이 있습니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Communication Services API 살펴보기](/rest/api/communication/)
> [인증에 대해 자세히 알아보기](/rest/api/communication/authentication)
> [Postman에 대해 자세히 알아보기](https://learning.postman.com/)

다음을 수행할 수도 있습니다.

- [앱에 채팅 추가](../quickstarts/chat/get-started.md)
- [사용자 액세스 토큰 만들기](../quickstarts/access-tokens.md)
- [클라이언트 및 서버 아키텍처에 대한 자세한 정보](../concepts/client-and-server-architecture.md)
- [인증에 대한 자세한 정보](../concepts/authentication.md)