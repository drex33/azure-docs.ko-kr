---
title: Azure Database for PostgreSQL의 쿼리 저장소 모범 사례 - 유연한 서버
description: 이 문서에서는 Azure Database for PostgreSQL의 쿼리 저장소 - 유연한 서버에 관한 모범 사례를 설명합니다.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/30/2021
ms.openlocfilehash: 48ccf6f2332d987543f8c15f551fddcd6f82893f
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133268366"
---
# <a name="best-practices-for-query-store---flexible-server"></a>쿼리 저장소 - 유연한 서버 모범 사례

**적용 대상:** Azure Database for PostgreSQL - 유연한 서버 버전 11, 12

이 문서에서는 Azure Database for PostgreSQL에서 쿼리 저장소를 사용하는 모범 사례를 설명합니다.

## <a name="set-the-optimal-query-capture-mode"></a>최적의 쿼리 캡처 모드 설정
쿼리 저장소를 사용하여 중요한 데이터를 캡처할 수 있습니다. 

|**pg_qs.query_capture_mode** | **시나리오**|
|---|---|
|_모두_  |모든 쿼리 및 쿼리 실행 빈도와 기타 통계 측면에서 워크로드를 철저하게 분석합니다. 작업에서 새 쿼리를 식별합니다. 사용자 또는 자동 매개 변수화 기회를 식별하는 데 임시 쿼리를 사용하는지 검색합니다. _모두_ 는 리소스 사용 비용이 증가합니다. |
|_상위_  |클라이언트에서 실행한 상위 쿼리에 집중합니다.
|_없음_ |없음으로 설정된 경우 쿼리 저장소는 새 쿼리를 캡처하지 않습니다. 조사하려는 쿼리 집합 및 시간 범위를 이미 캡처했으며 다른 쿼리 때문에 발생할 수 있는 방해 요소를 제거하고 싶은 경우에 사용합니다. _없음_ 은 환경 테스트 및 벤치마킹에 적합합니다. _없음_ 을 사용하면 중요한 새 쿼리를 추적하고 최적화하는 기회를 놓칠 수 있으므로 주의해서 사용해야 합니다. |


> [!NOTE] 
> **pg_qs.query_capture_mode** 는 **pgms_wait_sampling.query_capture_mode** 를 대체합니다. pg_qs.query_capture_mode가 _없음_ 인 경우 pgms_wait_sampling.query_capture_mode 설정은 영향을 미치지 않습니다. 


## <a name="keep-the-data-you-need"></a>필요한 데이터 유지
**pg_qs.retention_period_in_days** 매개 변수는 쿼리 저장소의 데이터 보존 기간을 일 단위로 지정합니다. 오래된 쿼리 및 통계 데이터는 삭제됩니다. 기본적으로 쿼리 저장소는 데이터를 7일간 보존하도록 구성됩니다. 사용할 계획이 없는 기록 데이터를 보존하지 마세요. 데이터를 더 오래 보존해야 하는 경우 값을 높이세요.


## <a name="next-steps"></a>다음 단계
- [Azure Portal](howto-configure-server-parameters-using-portal.md) 또는 [Azure CLI](howto-configure-server-parameters-using-cli.md)를 사용하여 매개 변수를 가져오거나 설정하는 방법을 알아봅니다.
