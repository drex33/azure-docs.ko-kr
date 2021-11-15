---
title: 요청 서비스 REST API 발급 요청 지정(미리 보기)
titleSuffix: Azure Active Directory Verifiable Credentials
description: 발급한 확인 가능한 자격 증명을 발급하는 방법을 알아봅니다.
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 40515c37de50379f84475ad5474fb54fbbfa5f0d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440529"
---
# <a name="request-service-rest-api-issuance-specification-preview"></a>요청 서비스 REST API 발급 사양(미리 보기)

Azure AD(Azure Active Directory) 확인 가능한 자격 증명에는 요청 서비스 REST API가 포함됩니다. 이 API를 사용하면 자격 증명을 발급하고 확인할 수 있습니다. 이 문서에서는 발급 요청에 대한 요청 서비스 REST API를 지정합니다.

## <a name="http-request"></a>HTTP 요청

요청 서비스 REST API 발급 요청은 다음 HTTP 메서드를 지원합니다.

| 메서드 |참고  |
|---------|---------|
|POST | 이 문서에 지정된 JSON 페이로드를 사용합니다. |

요청 서비스 REST API 발급 요청에는 다음 HTTP 헤더가 필요합니다.

| 메서드 |값  |
|---------|---------|
|`Authorization`| HTTP 요청의 인증 헤더에 액세스 토큰을 전달자 토큰으로 연결합니다. 예들 들어 `Authorization: Bearer <token>`입니다.|
|`Content-Type`| `Application/json`|

요청 서비스 REST API에 대한 HTTP POST 요청을 생성합니다. `{tenantID}`를 테넌트 ID 또는 테넌트 이름으로 바꿉니다.

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

다음 HTTP 요청은 요청 서비스 REST API에 대한 요청을 보여 줍니다.

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
    "includeQRCode": true, 
        "callback": {  
            "url": "https://wwww.contoso.com/vc/callback",  
            "state": "Aaaabbbb11112222", 
            "headers": { 
                "api-key": "an-api-key-can-go-here" 
              }  
        }, 
    ... 
}
```  

요청 서비스 REST API를 호출하려면 다음 권한이 필요합니다. 자세한 내용은 [액세스 토큰을 얻을 수 있는 권한 부여](verifiable-credentials-configure-tenant.md#grant-permissions-to-get-access-tokens)를 참조하세요.

| 사용 권한 유형 | 사용 권한  |
|---------|---------|
| 애플리케이션 | bbb94529-53a3-4be5-a069-7eaf2712b826/.default|

## <a name="issuance-request-payload"></a>발급 요청 페이로드

발급 요청 페이로드에는 확인 가능한 자격 증명 발급 요청에 대한 정보가 포함되어 있습니다. 다음 예제에서는 이름 및 성 같은 사용자 클레임으로 PIN 코드 흐름을 사용하여 발급 요청을 보여 줍니다. 이 요청의 결과는 발급 프로세스를 시작하기 위한 링크가 포함된 QR 코드를 반환합니다.

```json
{
    "includeQRCode": true,
    "callback": {
        "url": "https://www.contoso.com/api/issuer/issuanceCallback",
        "state": "de19cb6b-36c1-45fe-9409-909a51292a9c",
        "headers": {
            "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
        }
    },
    "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDek...",
    "registration": {
        "clientName": "Verifiable Credential Expert Sample"
    },
    "issuance": {
        "type": "VerifiedCredentialExpert",
        "manifest": "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert",
        "pin": {
            "value": "3539",
            "length": 4
        },
        "claims": {
            "given_name": "Megan",
            "family_name": "Bowen"
        }
    }
}
```

페이로드는 다음 속성을 포함합니다.  

|매개 변수 |형식  | 설명 |
|---------|---------|---------|
| `includeQRCode` |  부울 |   이 요청의 응답에 QR 코드가 포함되는지 여부를 결정합니다. QR 코드를 제공하고 사용자에게 검색하도록 요청합니다. QR 코드를 스캔하면 이 발급 요청으로 인증자 앱이 시작됩니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. 값을 `false`로 설정하는 경우 반환 `url` 속성을 사용하여 딥 링크를 렌더링합니다.  |
| `authority` | 문자열|  발급자의 DID(분산된 식별자)입니다. 자세한 내용은 [샘플 애플리케이션을 설정하기 위한 자격 증명 및 환경 세부 정보 수집](verifiable-credentials-configure-issuer.md)을 참조하세요.|
| `registration` | [RequestRegistration](#requestregistration-type)|  인증자 앱에 표시될 수 있는 발급자에 대한 정보를 제공합니다. |
| `issuance` | [RequestIssuance](#requestissuance-type)| 발급 요청에 대한 정보를 제공합니다.  |
|`callback`|  [Callback](#callback-type)| 개발자가 확인 가능한 자격 증명 발급 프로세스 중에 흐름에 대한 정보를 비동기식으로 가져올 수 있도록 합니다. 예를 들어 사용자가 QR 코드를 스캔할 때 개발자가 전화를 걸 수 있습니다.|

### <a name="requestregistration-type"></a>RequestRegistration 형식

`RequestRegistration` 형식은 발급자에 대한 정보 등록을 제공합니다. `RequestRegistration` 형식은 다음 속성을 포함합니다.

|속성 |형식 |Description |
|---------|---------|---------|
| `clientName` | 문자열|  확인 가능한 자격 증명의 발급자 표시 이름입니다.  |
| `logoUrl` |  문자열 |  (선택 사항) 발급자 로고에 대한 URL입니다.  |
| `termsOfServiceUrl` |  문자열 | (선택 사항) 발급하는 확인 가능한 자격 증명의 사용 약관에 대한 URL입니다.  |

> [!NOTE]
> 이번에는 Microsoft Authenticator 앱에서 발급하는 동안 `RequestRegistration` 정보가 제공되지 않습니다. 그러나 이 정보는 페이로드에 사용될 수 있습니다.

### <a name="requestissuance-type"></a>RequestIssuance 형식

`RequestIssuance` 형식은 확인 가능한 자격 증명 발급에 필요한 정보를 제공합니다. 현재 `RequestIssuance`에서 전송할 수 있는 세 가지 입력 형식이 있습니다. Azure AD 확인 가능한 자격 증명은 이러한 형식을 사용하여 클레임을 확인 가능한 자격 증명에 삽입하고 발급자의 DID를 사용하여 해당 정보를 증명합니다. 다음은 세 가지 형식입니다.

- ID 토큰
- 확인 가능한 프레젠테이션을 통해 확인 가능한 자격 증명
- 자체 증명 클레임

[확인 가능한 자격 증명 사용자 지정](credential-design.md)에서 입력 형식에 대한 자세한 정보를 찾을 수 있습니다. 

`RequestIssuance` 형식은 다음 속성을 포함합니다.

|속성 |형식 |Description |
|---------|---------|---------|
| `type` |  문자열 |  확인 가능한 자격 증명 형식입니다. 확인 가능한 자격 증명 매니페스트에 정의된 형식과 일치해야 합니다. 예: `VerifiedCredentialExpert` 자세한 내용은 [Azure에서 확인된 자격 증명 전문가 카드 만들기](verifiable-credentials-configure-issuer.md)를 참조하세요. |
| `manifest` | 문자열| 확인 가능한 자격 증명 매니페스트 문서의 URL입니다. 자세한 내용은 [샘플 애플리케이션을 설정하기 위한 자격 증명 및 환경 세부 정보 수집](verifiable-credentials-configure-issuer.md)을 참조하세요.|
| `claims` | 문자열| (선택 사항) 확인 가능한 자격 증명에서 주체에 대해 만든 어설션의 컬렉션을 포함합니다. PIN 코드 흐름의 경우 사용자의 이름과 성을 입력하는 것이 중요합니다. 자세한 내용은 [확인 가능한 자격 증명 이름](verifiable-credentials-configure-issuer.md#verifiable-credential-names)을 참조하세요. |
| `pin` | [PIN](#pin-type)| 선택 사항입니다. 발급 중에 추가 보안을 제공하는 PIN 번호입니다. PIN 코드 흐름의 경우 이 속성이 필요합니다. PIN 코드를 생성하고 앱의 사용자에게 제공합니다. 사용자는 생성된 PIN 코드를 제공해야 합니다. |

### <a name="pin-type"></a>pin 형식

`pin` 형식은 발급의 일부로 표시될 수 있는 PIN 코드를 정의합니다. `pin`은 선택 사항이며, 사용되는 경우 항상 대역 외로 전송되어야 합니다. 해시 PIN 코드를 사용하는 경우 `salt`, `alg` 및 `iterations` 속성을 정의해야 합니다. `pin`에는 다음 속성이 있습니다.

|속성 |형식 |Description |
|---------|---------|---------|
| `value` | 문자열| PIN 값을 일반 텍스트로 포함합니다. 해시된 PIN을 사용하는 경우 값 속성에는 base64로 인코딩된 솔트된 해시가 포함됩니다.|
| `type` | 문자열|  PIN 코드의 형식입니다. 가능한 값: `numeric`(기본값) |
| `length` | 정수|  PIN 코드의 길이입니다. 기본 길이는 6, 최솟값은 4, 최댓값은 16입니다.|
| `salt` | 문자열|  해시된 PIN 코드의 솔트입니다. 해시를 계산하는 동안 솔트가 앞에 붙습니다. 인코딩 = UTF-8 |
| `alg` | 문자열|  해시된 PIN에 대한 해시 알고리즘입니다. 지원되는 알고리즘: `sha256` |
| `iterations` | 정수| 해시 반복 횟수입니다. 가능한 값: `1`.|

### <a name="callback-type"></a>콜백 유형

요청 서비스 REST API는 콜백 엔드포인트에 여러 이벤트를 생성합니다. 이러한 이벤트를 통해 UI를 업데이트하고 결과를 애플리케이션에 반환한 후에 프로세스를 계속할 수 있습니다. `Callback` 형식은 다음 속성을 포함합니다.

|속성 |형식 |Description |
|---------|---------|---------|
| `url` | 문자열| 애플리케이션의 콜백 엔드포인트에 대한 URI입니다. |
| `state` | 문자열| 원본 페이로드에 전달된 상태와 연결합니다. |
| `headers` | 문자열| (선택 사항) POST 메시지의 수신 끝에 필요한 HTTP 헤더 컬렉션을 포함할 수 있습니다. 헤더는 권한 부여에 필요한 `api-key` 또는 헤더를 포함해야 합니다.|

## <a name="successful-response"></a>성공적인 응답

성공하면 이 메서드는 응답 코드(*HTTP 201 Created*)와 응답 본문에 있는 이벤트 개체의 컬렉션을 반환합니다. 다음 JSON은 성공적인 응답을 보여 줍니다.

```json
{  
    "requestId": :"799f23ea-5241-45af-99ad-cf8e5018814e",  
    "url": "openid://vc?request_uri=https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiablecredentials/request/178319f7-20be-4945-80fb-7d52d47ae82e",  
    "expiry": 1622227690,  
    "qrCode": "data:image/png;base64,iVBORw0KggoA<SNIP>"  
} 
```

응답에는 다음 속성이 포함됩니다.

|속성 |형식 |Description |
|---------|---------|---------|
| `requestId`| 문자열 | 자동 생성된 상관 관계 ID입니다. [콜백](#callback-events)은 동일한 요청을 사용하므로 발급 요청 및 해당 콜백을 추적할 수 있습니다. |
| `url`|  문자열| 인증자 앱을 시작하고 발급 프로세스를 시작하는 URL입니다. QR 코드를 스캔할 수 없는 경우 사용자에게 이 URL을 제공할 수 있습니다. |
| `expiry`| 정수| 응답이 만료되는 시기를 나타냅니다. |
| `qrCode`| 문자열 | 사용자가 발급 흐름을 시작하기 위해 검색할 수 있는 QR 코드입니다. |

앱에서 응답을 수신하면 앱은 사용자에게 QR 코드를 제공해야 합니다. 사용자가 QR 코드를 검색하면 인증자 앱이 열리고 발급 프로세스가 시작됩니다.

## <a name="error-response"></a>오류 응답

오류 응답은 앱에서 적절히 처리할 수 있도록 반환될 수도 있습니다. 다음 JSON은 권한 없는 오류 메시지를 보여 줍니다.


```json
{
    "requestId": "d60b068e7fbd975896e179b99347866a",
    "date": "Wed, 29 Sep 2021 21:49:00 GMT",
    "error": {
        "code": "unauthorized",
        "message": "Failed to authenticate the request."
    }
}
```

응답에는 다음 속성이 포함됩니다.

|속성 |형식 |Description |
|---------|---------|---------|
| `requestId`| 문자열 | 자동 생성된 요청 ID입니다.|
| `date`| date| 오류가 발생한 시간입니다. |
| `error.code` | 문자열| 반환 오류 코드입니다. |
| `error.message`| 문자열| 오류 메시지입니다. |

## <a name="callback-events"></a>콜백 이벤트

콜백 엔드포인트는 사용자가 QR 코드를 검색하거나, 인증자 앱의 딥 링크를 사용하거나, 발급 프로세스를 완료할 때 호출됩니다. 

|속성 |형식 |Description |
|---------|---------|---------|
| `requestId`| 문자열 | 페이로드를 확인 가능한 자격 증명 서비스에 게시했을 때 원래 요청에 매핑됩니다.|
| `code` |문자열 |인증자 앱에서 요청을 검색했을 때 반환되는 코드입니다. 가능한 값은 다음과 같습니다. <ul><li>`request_retrieved`: 사용자가 QR 코드를 검색했거나 발급 흐름을 시작하는 링크를 선택했습니다.</li><li>`issuance_successful`: 확인 가능한 자격 증명을 성공적으로 발급했습니다.</li><li>`Issuance_error`: 발급 중에 오류가 발생했습니다. 자세한 내용은 `error` 속성을 참조하십시오.</li></ul>    |
| `state` |문자열| 원래 페이로드에 전달된 상태 값을 반환합니다.   |
| `error`| error | `code` 속성 값이 `Issuance_error`이면 이 속성은 오류에 대한 정보를 포함합니다.| 
| `error.code` | 문자열| 반환 오류 코드입니다. |
| `error.message`| 문자열| 오류 메시지입니다. |

다음 예제에서는 인증자 앱이 발급 요청을 시작할 때 콜백 페이로드를 보여 줍니다.

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

다음 예제에서는 사용자가 발급 프로세스를 성공적으로 완료한 후의 콜백 페이로드를 보여 줍니다.

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",
    "code":"issuance_successful",
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
} 
```

### <a name="callback-errors"></a>콜백 오류  

콜백 엔드포인트는 오류 메시지를 사용하여 호출될 수 있습니다. 다음 표에서는 오류 코드를 나열합니다.

|메시지  |정의    |
|---------|---------|
| `fetch_contract_error*`| 확인 가능한 자격 증명 계약을 가져올 수 없습니다. 이 오류는 일반적으로 API가 요청 페이로드 [RequestIssuance 개체](#requestissuance-type)에 지정한 매니페스트를 가져올 수 없을 때 발생합니다.|
| `issuance_service_error*` | 확인 가능한 자격 증명 서비스가 요구 사항의 유효성을 검사할 수 없거나 확인 가능한 자격 증명에 문제가 있습니다.|
| `unspecified_error`| 이 오류는 일반적이지 않지만 조사할 가치가 있습니다. |

다음 예제에서는 오류가 발생했을 때의 콜백 페이로드를 보여 줍니다.

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",  
    "code": "issuance_error",  
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",  
    "error": { 
      "code":"IssuanceFlowFailed", 
      "message":"issuance_service_error”, 
    } 
} 
``` 

## <a name="next-steps"></a>다음 단계

[요청 서비스 REST API를 호출하는 방법](get-started-request-api.md)을 알아봅니다.
