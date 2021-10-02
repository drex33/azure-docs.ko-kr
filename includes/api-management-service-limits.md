---
title: 포함 파일
description: 포함 파일
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 08/20/2021
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 43358ec8c79e09d0701a098a2bd992f2cee8b23f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378224"
---
| 리소스 | 제한 |
| ---------------------------------------------------------------------- | -------------------------- |
| 최대 배율 단위 수 | 지역당 12개<sup>1</sup> |
| 캐시 크기 | 단위당 5GiB<sup>2</sup> |
| HTTP 기관당 동시 백 엔드 연결 수<sup>3</sup> | 단위당 2,048<sup>4</sup> |
| 캐시된 최대 응답 크기 | 2MiB |
| 최대 정책 문서 크기 | 256KiB<sup>5</sup> |
| 서비스 인스턴스당 최대 사용자 지정 게이트웨이 도메인 수<sup>6</sup> | 20 |
| 서비스 인스턴스당 최대 CA 인증서 수<sup>7</sup> | 10 |
| 구독당 최대 서비스 인스턴스 수<sup>8</sup> | 20 |
| 서비스 인스턴스당 최대 구독 수<sup>8</sup> | 500 |
| 서비스 인스턴스당 최대 클라이언트 인증서 수<sup>8</sup> | 50 |
| 서비스 인스턴스당 최대 API 수<sup>8</sup> | 50 |
| 서비스 인스턴스당 최대 API 관리 작업 수<sup>8</sup> | 1,000 |
| 최대 총 요청 기간<sup>8</sup> | 30초 |
| 최대 요청 페이로드 크기<sup>8</sup> | 1GiB |
| 최대 버퍼링된 페이로드 크기<sup>8</sup> | 2MiB |
| 최대 요청 URL 크기<sup>9</sup> | 16384바이트 |
| URL 경로 세그먼트의 최대 길이<sup>10</sup> | 260자 |
| [유효성 검사 정책](../articles/api-management/validation-policies.md)<sup>10</sup>에서 사용하는 API 스키마의 최대 크기 | 4MB |
| [콘텐츠 유효성 검사 정책](../articles/api-management/validation-policies.md#validate-content)<sup>10</sup>에서 요청 또는 응답 본문의 최대 크기 | 100KB |
| 최대 자체 호스팅 게이트웨이 수<sup>11</sup> | 25 |

<sup>1</sup>크기 조정 제한은 가격 책정 계층에 따라 달라집니다. 가격 책정 계층 및 해당 크기 조정 제한에 대한 자세한 내용은 [API Management 가격](https://azure.microsoft.com/pricing/details/api-management/)을 참조하세요.<br/>
<sup>2</sup>단위당 캐시 크기는 가격 책정 계층에 따라 달라집니다. 가격 책정 계층 및 해당 크기 조정 제한에 대한 자세한 내용은 [API Management 가격](https://azure.microsoft.com/pricing/details/api-management/)을 참조하세요.<br/>
<sup>3</sup>백 엔드에서 명시적으로 닫지 않으면 연결이 풀링되고 다시 사용됩니다.<br/>
<sup>4</sup>이 제한은 기본, 표준 및 프리미엄 계층 단위당입니다. 개발자 계층은 1,024개로 제한됩니다. 이 제한은 소비 계층에 적용되지 않습니다.<br/>
<sup>5</sup>이 제한은 기본, 표준 및 프리미엄 계층에 적용됩니다. 소비 계층에서 정책 문서 크기는 16KiB로 제한됩니다.<br/>
<sup>6</sup>여러 사용자 지정 도메인은 개발자 및 프리미엄 계층에서만 지원됩니다.<br/>
<sup>7</sup>CA 인증서는 소비 계층에서 지원되지 않습니다.<br/>
<sup>8</sup>이 제한은 소비 계층에만 적용됩니다. 이러한 범주에는 다른 계층에 대한 제한이 없습니다.<br/>
<sup>9</sup>소비 계층에만 적용됩니다. 최대 2,048바이트 길이의 쿼리 문자열을 포함합니다.<br/>
<sup>10</sup> 이 제한을 늘리려면 [지원](https://azure.microsoft.com/support/options/)에 문의하세요.<br/>
<sup>11</sup> 자체 호스팅 게이트웨이는 개발자 및 프리미엄 계층에서만 지원됩니다. 이 제한은 [자체 호스팅 게이트웨이 리소스](/rest/api/apimanagement/2021-04-01-preview/gateway)의 수에 적용됩니다. 이 제한을 늘리려면 [지원](https://azure.microsoft.com/support/options/)에 문의하세요. 자체 호스팅 게이트웨이 리소스와 연결된 노드(또는 복제본)의 수는 프리미엄 계층에서 무제한이며, 개발자 계층에서는 단일 노드로 제한됩니다.
