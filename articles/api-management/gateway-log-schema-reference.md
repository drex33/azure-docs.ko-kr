---
title: 참조 - Azure API Management 리소스 로그
description: Azure API Management GatewayLogs 리소스 로그에 대한 스키마 참조
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: danlep
ms.openlocfilehash: 0da1a897f9abe55e846006136c040d3602882b5a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676507"
---
# <a name="reference-api-management-resource-log-schema"></a>참조: API Management 리소스 로그 스키마

이 문서에서는 Azure API Management GatewayLogs 리소스 로그에 대한 스키마 참조를 제공합니다. 로그 항목에는 [최상위 공통 스키마](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)의 필드도 포함됩니다.

API Management에서 리소스 로그의 컬렉션을 사용하도록 설정하려면 [게시된 API 모니터링](api-management-howto-use-azure-monitor.md#resource-logs)을 참조하세요.

## <a name="gatewaylogs-schema"></a>GatewayLogs 스키마

각 API 요청에 대해 다음 속성이 기록됩니다.

| 속성  | 형식 | Description |
| ------------- | ------------- | ------------- |
| method | 문자열 | 들어오는 요청의 HTTP 메서드 |
| url | 문자열 | 들어오는 요청의 URL |
| responseCode | integer | 클라이언트로 전송된 HTTP 응답의 상태 코드 |
| responseSize | integer | 요청을 처리하는 동안 클라이언트로 전송된 바이트 수 | 
| 캐시 | 문자열 | 요청 처리에서 API Management 캐시 개입 상태(적중, 누락, 없음) | 
| apiId | 문자열 | 현재 요청에 대한 API 엔터티 식별자 | 
| operationId | 문자열 | 현재 요청에 대한 작업 엔터티 식별자 | 
| clientProtocol | 문자열 | 들어오는 요청의 HTTP 프로토콜 버전 |
| clientTime | integer | 전체 클라이언트 I/O(바이트 연결, 송신 및 수신)에 소요된 시간(밀리초) | 
| apiRevision | 문자열 | 현재 요청에 대한 API 수정 버전 | 
| clientTlsVersion| 문자열 | 클라이언트 전송 요청에서 사용하는 TLS 버전 |
| lastError | 개체 | 실패한 요청의 경우 마지막 요청 처리 오류에 대한 세부 정보 | 
| backendMethod | 문자열 | 백 엔드로 전송된 요청의 HTTP 메서드 |
| backendUrl | 문자열 | 백 엔드로 전송된 요청의 URL |
| backendResponseCode | integer | 백 엔드에서 받은 HTTP 응답 코드 |
| backedProtocol | 문자열 | 백 엔드로 전송된 요청의 HTTP 프로토콜 버전 |
| backendTime | integer | 전체 백 엔드 IO(바이트 연결, 송신 및 수신)에 소요된 시간(밀리초) | 


## <a name="next-steps"></a>다음 단계

* API Management의 API 모니터링에 대한 자세한 내용은 [게시된 API 모니터링](api-management-howto-use-azure-monitor.md)을 참조하세요.
* [Azure 리소스 로그에 대한 공통 및 서비스별 스키마](../azure-monitor/essentials/resource-logs-schema.md)에 대해 자세히 알아보세요.

