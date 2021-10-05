---
title: 지능형 튜닝 - Azure Database for PostgreSQL - 유연한 서버
description: 이 문서에서는 Azure Database for PostgreSQL - 유연한 서버의 지능형 튜닝 기능에 대해 설명합니다.
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: ca4dbea3701007d29327b5f8f26e0566f77b0c13
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455913"
---
# <a name="perform-intelligent-tuning-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 지능형 튜닝 수행 - 유연한 서버

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버 및 지능형 튜닝은 미리 보기로 제공됩니다.

**적용된 내용:** Azure Database for PostgreSQL - 유연한 서버 버전 11 이상

Azure Database for PostgreSQL - 유연한 서버의 지능형 튜닝 기능은 데이터베이스의 성능을 자동으로 향상시키는 방법을 제공합니다. 지능형 튜닝은 `checkpoint_completion_target` 사용 패턴 및 값에 따라 , 및 매개 변수를 자동으로 `min_wal_size` `max_wal_size` 조정합니다. 30분마다 데이터베이스에 대한 통계를 쿼리하고 상호 작용 없이 성능을 최적화하도록 지속적으로 조정합니다.

지능형 튜닝은 옵트인 기능이므로 서버에서 기본적으로 활성화되지 않습니다. 단일 데이터베이스에 사용할 수 있으며 전역 데이터베이스가 아닙니다. 하나의 데이터베이스에서 사용하도록 설정해도 연결된 모든 데이터베이스에서 활성화되지는 않습니다.

## <a name="enable-intelligent-tuning-by-using-the-azure-portal"></a>Azure Portal 사용하여 지능형 튜닝 사용

1. Azure Portal에 로그인하고 Azure Database for PostgreSQL 서버를 선택합니다.
2. 메뉴의 **설정** 섹션에서 **서버 매개 변수** 를 선택합니다.
3. 지능형 튜닝 매개 변수를 검색합니다.
4. 값을 **True** 로 설정하고 **저장을** 선택합니다.

데이터의 첫 번째 일괄 처리가 *azure_sys* 데이터베이스에 유지되도록 최대 35분이 걸립니다.

## <a name="information-about-intelligent-tuning"></a>지능형 튜닝에 대한 정보

지능형 튜닝은 지정된 시간에 대한 세 가지 주요 매개 변수, 즉 , 및 에 대해 `checkpoint_completion_target` `min_wal_size` `max_wal_size` 작동합니다.

이러한 세 가지 매개 변수는 대부분 다음과 같은 영향을 미칩니다. 

* 검사점 기간입니다.
* 검사점의 빈도입니다.
* 동기화 기간입니다.

지능형 튜닝은 양방향으로 작동합니다. 높은 워크로드 중 기간을 낮추고 유휴 세그먼트 동안 기간을 늘리려고 시도합니다. 이러한 방식으로 수동 업데이트 없이 어려운 기간 동안 맞춤형 결과를 얻을 수 있습니다.

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

* 지능형 튜닝은 특정 범위에서만 최적화를 만듭니다. 기능이 변경되지 않을 수 있습니다.
* 쿼리에서 데이터베이스가 삭제되면 기능의 향상된 실행이 약간 지연됩니다.
* 현재 이 기능은 스토리지 섹션에서만 최적화를 만듭니다.
