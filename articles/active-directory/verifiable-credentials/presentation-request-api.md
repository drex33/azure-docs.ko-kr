---
title: 요청 서비스 REST API 확인 요청 지정(미리 보기)
titleSuffix: Azure Active Directory Verifiable Credentials
description: 확인 가능한 자격 증명에서 프레젠테이션 요청을 시작하는 방법 알아보기
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: c670f060e7849f844997c0feefd60229b90e5202
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474006"
---
# <a name="request-service-rest-api-presentation-specification-preview"></a>요청 서비스 REST API 프레젠테이션 사양(미리 보기)

Azure AD(Azure Active Directory) 확인 가능한 자격 증명에는 요청 서비스 REST API가 포함됩니다. 이 API를 사용하면 자격 증명을 발급하고 확인할 수 있습니다. 이 문서에서는 프레젠테이션 요청에 대한 요청 서비스 REST API를 지정합니다. 프레젠테이션 요청은 사용자에게 확인 가능한 자격 증명을 제시한 다음, 자격 증명을 확인하도록 요청합니다.

## <a name="http-request"></a>HTTP 요청

요청 서비스 REST API 프레젠테이션 요청은 다음 HTTP 메서드를 지원합니다.

| 메서드 |참고  |
|---------|---------|
|POST | 이 문서에 지정된 JSON 페이로드를 사용합니다. |

요청 서비스 REST API 프레젠테이션 요청에는 다음 HTTP 헤더가 필요합니다.

| 메서드 |값  |
|---------|---------|
|`Authorization`| HTTP 요청의 인증 헤더에 액세스 토큰을 전달자 토큰으로 연결합니다. 예들 들어 `Authorization: Bearer <token>`입니다.|
|`Content-Type`| `Application/json`|

요청 서비스 REST API에 대한 HTTP POST 요청을 생성합니다. `{tenantID}`를 테넌트 ID 또는 테넌트 이름으로 바꿉니다.

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

다음 HTTP 요청은 요청 서비스 REST API에 대한 프레젠테이션 요청을 보여 줍니다.

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{  
    "includeQRCode": true,  
    "callback": {  
    "url": "https://www.contoso.com/api/verifier/presentationCallbac",  
    "state": "11111111-2222-2222-2222-333333333333",  
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

## <a name="presentation-request-payload"></a>프레젠테이션 요청 페이로드

프레젠테이션 요청 페이로드에는 확인 가능한 자격 증명 프레젠테이션 요청에 대한 정보가 포함됩니다. 다음 예제에서는 특정 발급자의 프레젠테이션 요청을 보여 줍니다. 이 요청의 결과는 프레젠테이션 프로세스를 시작하기 위한 링크가 포함된 QR 코드를 반환합니다.

```json
{
  "includeQRCode": true,
  "callback": {
    "url": "https://www.contoso.com/api/verifier/presentationCallback",
    "state": "92d076dd-450a-4247-aa5b-d2e75a1a5d58",
    "headers": {
      "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
    }
  },
  "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiOiJiRWo5MDY...",
  "registration": {
    "clientName": "Veritable Credential Expert Verifier"
  },
  "presentation": {
    "includeReceipt": true,
    "requestedCredentials": [
      {
        "type": "VerifiedCredentialExpert",
        "purpose": "So we can see that you a veritable credentials expert",
        "acceptedIssuers": [
          "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiO..."
        ]
      }
    ]
  }
}
```

페이로드는 다음 속성을 포함합니다.  

|매개 변수 |형식  | 설명 |
|---------|---------|---------|
| `includeQRCode` |  부울 |   이 요청의 응답에 QR 코드가 포함되는지 여부를 결정합니다. QR 코드를 제공하고 사용자에게 검색하도록 요청합니다. QR 코드를 스캔하면 이 프레젠테이션 요청으로 인증자 앱이 시작됩니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. 값을 `false`로 설정하는 경우 반환 `url` 속성을 사용하여 딥 링크를 렌더링합니다.  |
| `authority` | 문자열|  검증 도구 Azure AD 테넌트의 DID(탈중앙화 식별자)입니다. 자세한 내용은 [샘플 애플리케이션을 설정하기 위한 테넌트 세부 정보 수집](verifiable-credentials-configure-verifier.md#gather-tenant-details-to-set-up-your-sample-application)을 참조하세요.|
| `registration` | [RequestRegistration](#requestregistration-type)|  검증 도구에 대한 정보를 제공합니다. |
| `presentation` | [RequestPresentation](#requestpresentation-type)| 확인 가능한 자격 증명 프레젠테이션 요청에 대한 정보를 제공합니다.  |
|`callback`|  [Callback](#callback-type)| 개발자가 확인 가능한 자격 증명 프레젠테이션 프로세스 중에 UI를 업데이트할 수 있습니다. 사용자가 프로세스를 완료하면 결과가 애플리케이션에 반환된 후 프로세스를 계속합니다.|

### <a name="requestregistration-type"></a>RequestRegistration 형식

`RequestRegistration` 형식은 발급자에 대한 정보 등록을 제공합니다. `RequestRegistration` 형식은 다음 속성을 포함합니다.

|속성 |형식 |Description |
|---------|---------|---------|
| `clientName` | 문자열|  확인 가능한 자격 증명의 발급자 표시 이름입니다. 이 이름은 인증자 앱에서 사용자에게 표시됩니다. |

다음 스크린샷에서는 프레젠테이션 요청의 `clientName` 속성 및 `authority`(검증 도구)의 표시 이름을 보여 줍니다.

![프레젠테이션 요청을 승인하는 방법을 보여 주는 스크린샷](media/presentation-request-api/approve-presentation-request.jpg)

### <a name="requestpresentation-type"></a>RequestPresentation 형식

`RequestPresentation` 형식은 확인 가능한 자격 증명 프레젠테이션에 필요한 정보를 제공합니다. `RequestPresentation`에는 다음 속성이 있습니다.

|속성 |형식 |설명 |
|---------|---------|---------|
| `includeReceipt` |  부울 | 이 요청의 응답에 영수증을 포함해야 하는지 여부를 결정합니다. 가능한 값은 `true` 또는 `false`(기본값)입니다. 영수증에는 인증자에서 확인 가능한 자격 증명 서비스로 전송된 원래 페이로드가 포함됩니다. 영수증은 문제 해결에 유용하며 기본적으로 설정하면 안 됩니다. `OpenId Connect SIOP` 요청에서 영수증에는 원래 요청의 ID 토큰이 포함됩니다. |
| `requestedCredentials` | collection| [RequestCredential](#requestcredential-type) 개체의 컬렉션입니다.|

### <a name="requestcredential-type"></a>RequestCredential 형식

`RequestCredential`은 사용자가 제공해야 하는 요청된 자격 증명에 대한 정보를 제공합니다. `RequestCredential`에는 다음 속성이 있습니다.

|속성 |형식 |Description |
|---------|---------|---------|
| `type`| 문자열| 확인 가능한 자격 증명 형식입니다. `type`은 `issuer` 확인 가능한 자격 증명 매니페스트에 정의된 형식과 일치해야 합니다(예: `VerifiedCredentialExpert`). 발급자 매니페스트를 가져오려면 [샘플 애플리케이션을 설정하기 위한 자격 증명 및 환경 세부 정보 수집](verifiable-credentials-configure-issuer.md)을 참조하세요. **자격 증명 URL 발급** 을 복사하고 웹 브라우저에서 열고 **ID** 속성을 확인합니다. |
| `purpose`| 문자열 | 이 확인 가능한 자격 증명을 요청하는 용도에 대한 정보를 제공합니다. |
| `acceptedIssuers`| 문자열 컬렉션 | 주체가 표시할 수 있는 확인 가능한 자격 증명 형식을 발급할 수 있는 발급자의 DID 컬렉션입니다. 발급자 DID를 얻으려면 [샘플 애플리케이션을 설정하기 위한 자격 증명 및 환경 세부 정보 수집](verifiable-credentials-configure-issuer.md)을 참조하고, **DID(탈중앙화 식별자)** 값을 복사합니다. |

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
    "requestId": "e4ef27ca-eb8c-4b63-823b-3b95140eac11",
    "url": "openid://vc/?request_uri=https://beta.did.msidentity.com/v1.0/87654321-0000-0000-0000-000000000000/verifiablecredentials/request/e4ef27ca-eb8c-4b63-823b-3b95140eac11",
    "expiry": 1633017751,
    "qrCode": "data:image/png;base64,iVBORw0KGgoA<SNIP>"
} 
```

응답에는 다음 속성이 포함됩니다.

|속성 |형식 |Description |
|---------|---------|---------|
| `requestId`| 문자열 | 자동 생성된 상관 관계 ID입니다. [콜백](#callback-events)은 동일한 요청을 사용하므로 프레젠테이션 요청 및 해당 콜백을 추적할 수 있습니다. |
| `url`|  문자열| 인증자 앱을 시작하고 프레젠테이션 프로세스를 시작하는 URL입니다. QR 코드를 스캔할 수 없는 경우 사용자에게 이 URL을 제공할 수 있습니다. |
| `expiry`| 정수| 응답이 만료되는 시기를 나타냅니다. |
| `qrCode`| 문자열 | 사용자가 프레젠테이션 흐름을 시작하기 위해 검색할 수 있는 QR 코드입니다. |

앱에서 응답을 수신하면 앱은 사용자에게 QR 코드를 제공해야 합니다. 사용자가 QR 코드를 검색하면 인증자 앱이 열리고 프레젠테이션 프로세스가 시작됩니다.

## <a name="error-response"></a>오류 응답

오류 응답은 앱에서 적절히 처리할 수 있도록 반환될 수도 있습니다. 다음 JSON은 권한 없는 오류 메시지를 보여 줍니다.


```json
{
    "requestId": "fb888ac646c96083de83b099b2678de0",
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
| `date`| date | 오류가 발생한 시간입니다. |
| `error.code` | 문자열 | 반환 오류 코드입니다. |
| `error.message`| 문자열 | 오류 메시지입니다. |

## <a name="callback-events"></a>콜백 이벤트

콜백 엔드포인트는 사용자가 QR 코드를 검색하거나, 인증자 앱의 딥 링크를 사용하거나, 프레젠테이션 프로세스를 완료할 때 호출됩니다. 

|속성 |형식 |Description |
|---------|---------|---------|
| `requestId`| 문자열 | 페이로드를 확인 가능한 자격 증명 서비스에 게시했을 때 원래 요청에 매핑됩니다.|
| `code` |문자열 |인증자 앱에서 요청을 검색했을 때 반환되는 코드입니다. 가능한 값은 다음과 같습니다. <ul><li>`request_retrieved`: 사용자가 QR 코드를 검색했거나 프레젠테이션 흐름을 시작하는 링크를 선택했습니다.</li><li>`presentation_verified`: 확인 가능한 자격 증명 유효성 검사가 성공적으로 완료되었습니다.</li></ul>    |
| `state` |문자열| 원래 페이로드에 전달된 상태 값을 반환합니다.   |
| `subject`|문자열 | 확인 가능한 자격 증명 사용자 DID입니다.|
| `issuers`| array |요청된 확인 가능한 자격 증명의 배열을 반환합니다. 각 확인 가능한 자격 증명에 대해 다음을 제공합니다. </li><li>확인 가능한 자격 증명 형식입니다.</li><li>검색된 클레임입니다.</li><li>확인 가능한 자격 증명 발급자의 도메인입니다. </li><li>확인 가능한 자격 증명 발급자의 도메인 유효성 검사 상태입니다. </li></ul> |
| `receipt`| 문자열 | (선택 사항) 영수증에는 인증자에서 확인 가능한 자격 증명으로 전송된 원래 페이로드가 포함됩니다.  |

다음 예제에서는 인증자 앱이 프레젠테이션 요청을 시작할 때 콜백 페이로드를 보여 줍니다.

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

다음 예제에서는 확인 가능한 자격 증명 프레젠테이션이 성공적으로 완료된 후의 콜백 페이로드를 보여 줍니다.

```json
{
  "requestId": "87e1cb24-9096-409f-81cb-9e645f23a4ba",
  "code": "presentation_verified",
  "state": "f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
  "subject": "did:ion:EiAlrenrtD3Lsw0GlbzS1O2YFdy3Xtu8yo35W<SNIP>…",
  "issuers": [
    {
      "type": [
        "VerifiableCredential",
        "VerifiedCredentialExpert"
      ],
      "claims": {
        "firstName": "John",
        "lastName": "Doe"
      },
      "domain": "https://contoso.com/",
      "verified": "DNS",
      "issuer": "did:ion:….."
    }
  ],
  "receipt": {
    "id_token": "eyJraWQiOiJkaWQ6aW<SNIP>"
  }
} 
```

## <a name="next-steps"></a>다음 단계

[요청 서비스 REST API를 호출하는 방법](get-started-request-api.md)을 알아봅니다.
