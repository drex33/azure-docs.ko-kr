---
title: Azure CDN에 대한 표준 규칙 엔진의 동작 | Microsoft Docs
description: Azure CDN(Azure Content Delivery Network)에 대한 표준 규칙 엔진의 동작을 살펴보는 참조 설명서입니다.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: duau
ms.openlocfilehash: 32c0b0a245f864601110505af0ce81ad4e9b0b0e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461383"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN에 대한 표준 규칙 엔진의 동작

Azure CDN(Azure Content Delivery Network)에 대한 [표준 규칙 엔진](cdn-standard-rules-engine.md)에서 규칙은 하나 이상의 일치 조건 및 동작으로 구성됩니다. 이 문서에서는 Azure CDN에 대한 표준 규칙 엔진에서 사용할 수 있는 동작에 대해 자세히 설명합니다.

규칙의 두 번째 부분은 동작입니다. 작업은 일치 조건 또는 일치 조건 세트에서 식별하는 요청 유형에 적용되는 동작을 정의합니다.

## <a name="actions"></a>동작

Azure CDN에 대한 표준 규칙 엔진에서 사용할 수 있는 동작은 다음과 같습니다. 

### <a name="cache-expiration"></a>캐시 만료

규칙 일치 조건에 지정된 요청의 엔드포인트 TTL(Time to Live) 값을 덮어쓰려면 이 동작을 사용합니다.

#### <a name="required-fields"></a>Required fields

캐시 동작 |  Description              
---------------|----------------
바이패스 캐시 | 이 옵션이 선택되어 있고 규칙이 일치하면 콘텐츠가 캐시되지 않습니다.
재정의 | 이 옵션이 선택되어 있고 규칙이 일치하면 원본에서 반환된 TTL 값을 이 동작에 지정된 값으로 덮어씁니다. 이 동작은 응답을 캐시할 수 있는 경우에만 적용됩니다. cache-control 응답 헤더 값이 “no-cache”, “private”, “no-store”이면 이 동작을 적용할 수 없습니다.
누락된 경우 설정 | 이 옵션이 선택되어 있고 규칙이 일치하면 원본에서 TTL 값이 반환되지 않은 경우 규칙이 TTL을 이 동작에 지정된 값으로 설정합니다. 이 동작은 응답을 캐시할 수 있는 경우에만 적용됩니다. cache-control 응답 헤더 값이 “no-cache”, “private”, “no-store”이면 이 동작을 적용할 수 없습니다.

#### <a name="additional-fields"></a>추가 필드

일 | 시간 | 분 | 초
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>캐시 키 쿼리 문자열

쿼리 문자열에 따라 캐시 키를 수정하려면 이 동작을 사용합니다.

#### <a name="required-fields"></a>Required fields

동작 | 설명
---------|------------
포함 | 이 옵션이 선택되어 있고 규칙이 일치하면 캐시 키가 생성되는 경우 매개 변수에 지정된 쿼리 문자열이 포함됩니다. 
모든 고유한 URL 캐시 | 이 옵션이 선택되어 있고 규칙이 일치하면 고유한 각 URL에 자체 캐시 키가 포함됩니다. 
제외 | 이 옵션이 선택되어 있고 규칙이 일치하면 캐시 키가 생성되는 경우 매개 변수에 지정된 쿼리 문자열이 제외됩니다.
쿼리 문자열 무시 | 이 옵션이 선택되어 있고 규칙이 일치하면 캐시 키가 생성되는 경우 쿼리 문자열을 고려하지 않습니다. 

### <a name="modify-request-header"></a>요청 헤더 수정

원본으로 보낸 요청에 있는 헤더를 수정하려면 다음 작업을 사용합니다.

#### <a name="required-fields"></a>Required fields

작업 | HTTP 헤더 이름 | 값
-------|------------------|------
추가 | 이 옵션이 선택되고 규칙이 일치하면 지정된 값을 사용하여 **헤더 이름** 에 지정된 헤더가 요청에 추가됩니다. 헤더가 이미 있으면 값이 기존 값에 추가됩니다. | String
Overwrite | 이 옵션이 선택되고 규칙이 일치하면 지정된 값을 사용하여 **헤더 이름** 에 지정된 헤더가 요청에 추가됩니다. 헤더가 이미 있으면 지정한 값이 기존 값을 덮어씁니다. | String
DELETE | 이 옵션이 선택되고 규칙이 일치하고 규칙에 지정된 헤더가 있으면 요청에서 헤더가 삭제됩니다. | String

### <a name="modify-response-header"></a>응답 헤더 수정

클라이언트로 반환된 응답에 있는 헤더를 수정하려면 다음 작업을 사용합니다.

#### <a name="required-fields"></a>Required fields

작업 | HTTP 헤더 이름 | 값
-------|------------------|------
추가 | 이 옵션이 선택되고 규칙이 일치하면 지정된 **값** 을 사용하여 **헤더 이름** 에 지정된 헤더가 응답에 추가됩니다. 헤더가 이미 있으면 **값** 이 기존 값에 추가됩니다. | String
Overwrite | 이 옵션이 선택되고 규칙이 일치하면 지정된 **값** 을 사용하여 **헤더 이름** 에 지정된 헤더가 응답에 추가됩니다. 헤더가 이미 있으면 **값** 이 기존 값을 덮어씁니다. | String
DELETE | 이 옵션이 선택되고 규칙이 일치하고 규칙에 지정된 헤더가 있으면 응답에서 헤더가 삭제됩니다. | String

### <a name="url-redirect"></a>URL 리디렉션

클라이언트를 새 URL로 리디렉션하려면 다음 작업을 사용합니다. 

#### <a name="required-fields"></a>Required fields

필드 | Description 
------|------------
Type | 요청자에게 반환할 응답 형식(찾음(302), 이동됨(301), 임시 리디렉션(307) 및 영구 리디렉션(308))을 선택합니다.
프로토콜 | 요청, HTTP, HTTPS를 일치시킵니다.
호스트 이름 | 요청을 리디렉션할 호스트 이름을 선택합니다. 들어오는 호스트를 유지하려면 비워 둡니다.
경로 | 리디렉션에 사용할 경로를 정의합니다. 들어오는 경로를 유지하려면 비워 둡니다.  
쿼리 문자열 | 리디렉션에 사용되는 쿼리 문자열을 정의합니다. 들어오는 쿼리 문자열을 유지하려면 비워 둡니다. 
Fragment | 리디렉션에 사용할 조각을 정의합니다. 들어오는 조각을 유지하려면 비워 둡니다. 

절대 URL을 사용하는 것이 좋습니다. 상대 URL을 사용하면 Azure CDN URL을 잘못된 경로로 리디렉션할 수 있습니다. 

### <a name="url-rewrite"></a>URL 다시 쓰기

원본으로 라우팅하는 요청의 경로를 다시 작성하려면 다음 작업을 사용합니다.

#### <a name="required-fields"></a>Required fields

필드 | 설명 
------|------------
원본 패턴 | 바꿀 URL 경로에 원본 패턴을 정의합니다. 현재 원본 패턴은 접두사 기반 일치를 사용합니다. 모든 URL 경로를 일치시키려면 슬래시( **/** )를 원본 패턴 값으로 사용합니다.
대상 | 다시 쓰기에 사용할 대상 경로를 정의합니다. 대상 경로는 원본 패턴을 덮어씁니다.
불일치한 경로 유지 | **예** 로 설정된 경우 원본 패턴 뒤의 나머지 경로가 새 대상 경로에 추가됩니다. 

## <a name="next-steps"></a>다음 단계

- [Azure CDN 개요](cdn-overview.md)
- [표준 규칙 엔진 참조](cdn-standard-rules-engine-reference.md)
- [표준 규칙 엔진의 일치 조건](cdn-standard-rules-engine-match-conditions.md)
- [표준 규칙 엔진을 사용하여 HTTPS 적용](cdn-standard-rules-engine.md)
