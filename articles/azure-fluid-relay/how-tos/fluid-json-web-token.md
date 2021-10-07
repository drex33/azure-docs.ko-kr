---
title: Azure 유체 Relay 토큰 계약
description: Azure 유체 Relay에서 사용 되는 JSON Web Token에 대 한 이해 향상
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: reference
ms.service: azure-fluid
ms.openlocfilehash: 3fe063a6d5b3c76d300a088372e9dee08d9d79c0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663058"
---
# <a name="azure-fluid-relay-token-contract"></a>Azure 유체 Relay 토큰 계약

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

Azure 유체 Relay에 전송 된 요청은 권한 부여 헤더에 JWT 토큰을 포함 해야 합니다. 이 토큰은 [테 넌 트 키로 서명](../concepts/authentication-authorization.md)해야 합니다.

## <a name="claims"></a>클레임

JWTs (JSON 웹 토큰)는 세 부분으로 나뉩니다. 

- **헤더** -토큰 형식 및 서명 방법에 대 한 정보를 포함 하 여 토큰의 유효성을 검사 하는 방법에 대 한 정보를 제공 합니다. 
- **페이로드** -서비스 호출을 시도 하는 사용자 또는 앱에 대 한 모든 중요 데이터를 포함 합니다. 
- **서명** - 토큰의 유효성을 검사하는 데 사용되는 원시 자료입니다. 

각 부분은 마침표 (.)로 구분 되 고 별도의 b a s e 64로 인코딩됩니다. 

## <a name="header-claims"></a>헤더 클레임

| 클레임 | 형식 | 설명  |
|-------|--------|--------------|
| alg   | string | 토큰에 서명 하는 데 사용 되는 알고리즘입니다. 예: "HS256" |
| p   | string | 이 값은 항상 "JWT" 여야 합니다. |

## <a name="payload-claims"></a>페이로드 클레임

| 클레임      | 형식                   | Description |
|------------|--------------------------|-------------|
| documentId | string                   | FRS에 의해 생성 되는 토큰을 생성 하는 문서를 식별 합니다. |
| scope      | string[]                 | 문서 또는 요약에서 클라이언트에 필요한 사용 권한을 식별 합니다. 모든 범위에 대해 클라이언트에 부여 하려는 권한을 정의할 수 있습니다.  |
| tenantId   | string                   | 테 넌 트를 식별 합니다. |
| 사용자       | JSON                     | *선택 사항* `{ displayName: <display_name>, id: <user_id>, name: <user_name>, }` 응용 프로그램의 사용자를 식별 합니다. 이는 주문 서비스인 Alfred에 의해 응용 프로그램에 다시 전송 됩니다.  응용 프로그램에서 Alfred에서 받은 응답의 사용자를 식별 하는 데 사용할 수 있습니다. Azure 유체 Relay는이 정보의 유효성을 검사 하지 않습니다. |
| iat        | 숫자, UNIX 타임 스탬프 | "발급 시간"은 이 토큰에 대한 인증이 발생한 시간을 나타냅니다. |
| exp        | 숫자, UNIX 타임 스탬프 | "exp"(만료 시간) 클레임은 JWT가 그 이후에는 처리를 허용하지 않아야 하는 만료 시간을 식별합니다. 토큰 수명은 1 시간 보다 많을 수 없습니다. |
| ver        | string                   | 액세스 토큰의 버전을 나타냅니다. 이어야 `1.0` 합니다. |
| jti        | string                   | *선택 사항입니다.*  "jti"(JWT ID) 클레임은 JWT에 대한 고유 식별자를 제공합니다. 식별자 값은 동일한 값이 다른 데이터 개체에 실수로 할당 될 확률이 무시 되도록 하는 방식으로 할당 되어야 합니다. 문서 생성에 동일한 토큰을 사용 하 여 오류를 방지 하기 위해이 클레임을 사용 하는 것이 좋습니다.  |

## <a name="a-sample-azure-fluid-relay-token"></a>샘플 Azure 유체 Relay 토큰

```typescript
{ 
  "alg": "HS256",  
  "typ": "JWT" 
}.{ 
  "documentId": "746c4a6f-f778-4970-83cd-9e21bf88326c", 
  "scopes": [ "doc:read", "doc:write", "summary:write" ],   
  "iat": 1599098963,  
  "exp": 1599098963,  
  "tenantId": "AzureFluidTenantId",  
  "ver": "1.0",
  "jti": "d7cd6602-2179-11ec-9621-0242ac130002"
}.[Signature] 
```

## <a name="how-can-you-generate-an-azure-fluid-relay-token"></a>Azure 유체 Relay 토큰을 생성 하려면 어떻게 해야 하나요? 

[Jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) npm 패키지를 사용 하 고이 메서드를 사용 하 여 토큰에 서명할 수 있습니다.

```typescript
export function getSignedToken(
    tenantId: string,
    documentId: string,
    tokenLifetime: number = 60 * 60,
    ver: string = "1.0") {
        jwt.sign(
            {
                documentId, 
                user: {
                    displayName: "displayName", 
                    id: "userId", 
                    name: "userName" 
                }, 
                scopes: ["doc:read", "doc:write", "summary:write"], 
                iat: Math.round((new Date()).getTime() / 1000), 
                exp: Math.round((new Date()).getTime() / 1000) + tokenLifetime, //set the expiry date based on your needs but max-limit is one hour.
                tenantId, 
                ver,
                jti: uuid(), 
            },
            "<tenant_key>");
    }
```
