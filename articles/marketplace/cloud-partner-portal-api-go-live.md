---
title: 실시간 전송 API - Azure Marketplace
description: Go Live API는 제품 라이브 목록 추가 프로세스를 시작합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: ec036878cf9412bcc612917fa7a104db3a2e8dd0
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112240312"
---
# <a name="go-live-api"></a>Go live API

> [!NOTE]
> Cloud 파트너 포털 API는 파트너 센터와 통합되었으며, 계속 파트너 센터에서 작동합니다. 이 전환으로 인해 몇 가지 사소한 사항이 변경되었습니다. 파트너 센터로 전환한 후에도 코드가 계속 작동하도록 [Cloud 파트너 포털 API 참조](./cloud-partner-portal-api-overview.md)에 나열된 변경 내용을 검토합니다. CPP API는 파트너 센터로 전환하기 전에 이미 통합된 기존 제품에만 사용해야 합니다. 새 제품은 파트너 센터 제출 API를 사용해야 합니다.

이 API는 앱을 프로덕션 환경으로 푸시하는 프로세스를 시작합니다. 이 작업은 대개 오랫동안 실행됩니다. 이 호출에서는 [게시](./cloud-partner-portal-api-publish-offer.md) API 작업의 알림 전자 메일 목록을 사용합니다.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI 매개 변수
--------------

|  **이름**      |   **설명**                                                           | **데이터 형식** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | 검색할 제품의 게시자 식별자입니다(예: `contoso`).       |  String       |
| offerId        | 검색할 제품의 제품 식별자입니다.                                   |  String       |
| api-version    | 최신 버전의 API입니다.                                                   |  Date         |
|  |  |  |

## <a name="header"></a>헤더
------

|  **이름**       |     **값**       |
|  ---------      |     ----------      |
| 콘텐츠 형식    | `application/json`  |
| 권한 부여   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>본문 예제

### <a name="response"></a>응답

#### <a name="migrated-offers"></a>마이그레이션된 제품

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>마이그레이션되지 않은 제품

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>응답 헤더

|  **이름**             |      **값**                                                            |
|  --------             |      ----------                                                           |
| Location    |  이 작업의 상태를 검색할 상대 경로            |
|  |  |

### <a name="response-status-codes"></a>응답 상태 코드

| ‘코드’ |  **설명**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` - 요청이 성공적으로 수락되었습니다. 응답에는 작업 상태 추적을 위한 위치가 포함됩니다. |
|  400     | `Bad/Malformed request` -응답 본문 내에서 추가 오류 정보가 확인되었습니다. |
|  404     |  `Not found` - 지정한 엔터티가 없습니다.                                       |
|  |  |
