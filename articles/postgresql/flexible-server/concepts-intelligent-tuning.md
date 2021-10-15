---
title: 인텔리전트 튜닝-Azure Database for PostgreSQL 유연한 서버
description: 이 문서에서는 Azure Database for PostgreSQL 유연한 서버에서 지능형 튜닝 기능을 설명 합니다.
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: b354394a393907f47b5c29adf668c8d42e059122
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044898"
---
# <a name="perform-intelligent-tuning-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 유연한 서버에서 지능적인 조정 수행

> [!IMPORTANT]
> Azure Database for PostgreSQL 유연한 서버와 지능적인 조정은 미리 보기 상태입니다.

**적용 대상:** Azure Database for PostgreSQL-유연한 서버 버전 11 이상.

Azure Database for PostgreSQL 유연한 서버에서 지능형 튜닝 기능을 통해 데이터베이스의 성능을 자동으로 향상 시킬 수 있습니다. 인텔리전트 튜닝은 `checkpoint_completion_target` `min_wal_size` `bgwriter_delay` 사용 패턴 및 값에 따라, 및 매개 변수를 자동으로 조정 합니다. 30 분 마다 데이터베이스에 대 한 통계를 쿼리하고 상호 작용 없이 성능을 최적화 하도록 일정 하 게 조정 합니다.

인텔리전트 조정은 옵트인 기능 이므로 서버에서는 기본적으로 활성화 되지 않습니다. 단일 데이터베이스에 사용할 수 있으며 전역이 아닙니다. 데이터베이스 하나에서 사용 하도록 설정 하면 연결 된 모든 데이터베이스에서 사용 하도록 설정 되지 않습니다.

## <a name="enable-intelligent-tuning-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 지능적인 조정 사용

1. Azure Portal에 로그인하고 Azure Database for PostgreSQL 서버를 선택합니다.
2. 메뉴의 **설정** 섹션에서 **서버 매개 변수** 를 선택 합니다.
3. 지능형 튜닝 매개 변수를 검색 합니다.
4. 값을 **True** 로 설정 하 고 **저장** 을 선택 합니다.

*Azure_sys* 데이터베이스에서 데이터의 첫 번째 일괄 처리가 지속 될 때까지 최대 35 분이 소요 됩니다.

## <a name="information-about-intelligent-tuning"></a>지능형 튜닝에 대 한 정보

인텔리전트 조정은 지정 된 시간 (, 및)에 대 한 세 가지 주요 매개 변수 `checkpoint_completion_target` 를 중심으로 작동 `min_wal_size` `bgwriter_delay` 합니다.

이러한 세 매개 변수는 대체로 영향을 줍니다. 

* 검사점 기간입니다.
* 검사점의 빈도입니다.
* 동기화 기간입니다.

지능형 튜닝은 양방향으로 작동 합니다. 높은 작업 중에는 기간을 줄이고 유휴 세그먼트 중에는 기간을 늘려야 합니다. 이러한 방식으로 수동 업데이트 없이 어려운 기간 동안 개인 설정 된 결과를 얻을 수 있습니다.

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

* 인텔리전트 튜닝은 특정 범위 에서만 최적화를 수행 합니다. 기능이 변경 되지 않을 수 있습니다.
* 쿼리에서 삭제 된 데이터베이스는 기능의 향상 된 실행에 약간의 지연이 발생할 수 있습니다.
* 현재이 기능은 저장소 섹션 에서만 최적화를 수행 합니다.
