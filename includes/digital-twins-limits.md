---
author: baanders
description: Azure Digital Twins 제한에 대한 포함 파일
ms.service: digital-twins
ms.topic: include
ms.date: 10/20/2021
ms.author: baanders
ms.openlocfilehash: 9c64606f4816491da803d2c5116ff4ad248ec29a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287360"
---
### <a name="functional-limits"></a>기능 제한

다음 표에는 Azure Digital Twins의 기능 제한이 나와 있습니다. 

> [!TIP]
> 해당 기능 제한 내에서 작동하는 모델링 권장 사항은 [모델링 모범 사례](../articles/digital-twins/concepts-models.md#modeling-best-practices)를 참조하세요.

| 영역형 | 기능 | 기본 제한 | 조정 가능 |
| --- | --- | --- | --- |
| Azure 리소스 | 구독당 지역의 Azure Digital Twins 인스턴스 수 | 10 | 예 |
| Digital Twins | Azure Digital Twins 인스턴스의 트윈 수 | 500,000 | 예 |
| Digital Twins | 단일 쌍으로 들어오는 관계 수 | 5,000 | 아니요 |
| Digital Twins | 단일 쌍에서 나가는 관계 수 | 5,000 | 아니요 |
| Digital Twins | 단일 쌍의 최대 크기(PUT 또는 PATCH 요청의 JSON 본문) | 32KB | 아니요 |
| Digital Twins | 최대 요청 페이로드 크기 | 32KB | 아니요 | 
| Digital Twins | 문자열 속성 값의 최대 크기 (UTF-8) | 4KB | 아니요|
| Digital Twins | 속성이 이름의 최대 크기 | 1KB | 아니요| 
| 라우팅 | 단일 Azure Digital Twins 인스턴스의 엔드포인트 수 | 6 | 아니요 |
| 라우팅 | 단일 Azure Digital Twins 인스턴스의 경로 수 | 6 | 예 |
| 모델 | 단일 Azure Digital Twins 인스턴스 내의 모델 수 | 10000 | 예 |
| 모델 | 단일 API 호출에서 업로드할 수 있는 모델 수 | 250 | 아니요 |
| 모델 | 단일 모델의 최대 크기(PUT 또는 PATCH 요청의 JSON 본문) | 1MB | 아니요 |
| 모델 | 단일 페이지에 반환된 항목 수 | 100 | 아니요 |
| 쿼리 | 단일 페이지에 반환된 항목 수 | 100 | 예 |
| 쿼리 | 쿼리의 `AND` / `OR` 식 수 | 50 | 예 |
| 쿼리 | `IN` / `NOT IN` 절의 배열 항목 수 | 50 | 예 |
| 쿼리 | 쿼리의 문자 수 | 8,000 | 예 |
| 쿼리 | 쿼리의 `JOINS` 수 | 5 | 예 |

### <a name="rate-limits"></a>속도 제한

다음 표에서는 다양한 API의 속도 제한을 반영합니다.

| API | 기능 | 기본 제한 | 조정 가능 |
| --- | --- | --- | --- |
| 모델 API | 초당 요청 수 | 100 | 예 |
| Digital Twins API | 초당 읽기 요청 수 | 1,000 | 예 |
| Digital Twins API | 초당 패치 요청 수 | 1,000 | 예 |
| Digital Twins API | **모든 트윈 및 관계** 에서 초당 생성/삭제 작업 수 | 50 | 예 |
| Digital Twins API | **단일 쌍** 또는 해당 관계에서 초당 생성/업데이트/삭제 작업 수 | 10 | 예 |
| 쿼리 API | 초당 요청 수 | 500 | 예 |
| 쿼리 API | 초당 [쿼리 단위](../articles/digital-twins/concepts-query-units.md) | 4,000 | 예 |
| 이벤트 경로 API | 초당 요청 수 | 100 | 예 |

### <a name="other-limits"></a>기타 제한

Azure Digital Twins 모델의 DTDL 문서 내에 있는 데이터 형식 및 필드에 대한 제한은 GitHub: [DTDL(Digital Twins 정의 언어) - 버전 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)의 사양 설명서 내에서 찾을 수 있습니다.
 
쿼리 대기 시간 세부 정보는 [쿼리 언어](../articles/digital-twins/concepts-query-language.md#considerations-for-querying)에 설명되어 있습니다. 특정 쿼리 언어 기능의 제한 사항은 [쿼리 참조 문서](../articles/digital-twins/concepts-query-language.md#reference-documentation)에서 확인할 수 있습니다.
