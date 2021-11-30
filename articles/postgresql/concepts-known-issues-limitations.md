---
title: Azure Database for PostgreSQL 대한 알려진 문제 및 제한 사항 - 단일 서버 및 유연한 서버
description: 고객이 알아야 하는 알려진 문제를 나열합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/30/2021
ms.openlocfilehash: 32d04b9545a32008f32fc8ff9c84d764d8f5f9a9
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133269753"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL - 알려진 문제 및 제한 사항

이 페이지에서는 애플리케이션에 영향을 줄 수 있는 Azure Database for PostgreSQL의 알려진 문제 목록을 제공합니다. 또한 문제 해결을 위한 완화 및 권장 사항을 나열합니다.

## <a name="intelligent-performance---query-store"></a>Intelligent Performance - 쿼리 저장소

Azure Database for PostgreSQL - 단일 서버에 해당

| 적용 가능 | 원인 | 수정|
| ----- | ------ | ---- | 
| PostgreSQL 9.6, 10, 11 | 서버 매개 변수 `pg_qs.replace_parameter_placeholders`를 켜면 일부 드문 시나리오에서 서버가 종료될 수 있습니다. | Azure Portal의 서버 매개 변수 섹션에서 `pg_qs.replace_parameter_placeholders` 매개 변수 값을 `OFF`로 설정하고 저장합니다.   | 


## <a name="next-steps"></a>다음 단계
- 쿼리 저장소 [모범 사례](./concepts-query-store-best-practices.md)를 참조하세요.
