---
title: 지능형 튜닝 - Azure Database for PostgreSQL - 유연한 서버
description: 이 문서에서는 Azure Database for PostgreSQL - 유연한 서버의 지능형 튜닝 기능에 대해 설명합니다.
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: d16a1842c4dfed044a8ac865a3dcdcd6cbe05bf3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529105"
---
# <a name="perform-intelligent-tuning-on-your-postgresql-flex-server"></a>PostgreSQL 유연한 서버에서 지능형 튜닝 수행

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

> [!IMPORTANT]
> 지능형 튜닝은 미리 보기 상태입니다.

**적용 대상:**  Azure Database for PostgreSQL - 유연한 서버 버전 11 이상

Azure Database for PostgreSQL 유연한 서버의 지능형 튜닝 기능은 데이터베이스 성능을 자동으로 개선하는 방법을 제공합니다. 당사 서비스를 통해 지능형 튜닝은 사용 패턴 및 값에 따라 Checkpoint_completion_target, min_wal_size 및 max_wal_size를 자동으로 조정합니다. 당사 서비스는 30분마다 데이터베이스에 대한 통계를 쿼리하고 사용자의 상호 작용 없이 성능을 최적화하기 위해 지속적으로 조정합니다.

## <a name="enabling-intelligent-tuning"></a>지능형 튜닝을 사용하도록 설정

지능형 튜닝은 옵트인 기능이므로 서버에서 기본적으로 활성화되지 않습니다. 튜닝은 단일 데이터베이스에 사용할 수 있으며 전역적이지 않으므로 한 데이터베이스에서 사용하도록 설정해도 연결된 모든 데이터베이스에서 사용하도록 설정되지 않습니다.

### <a name="enable-intelligent-tuning-using-the-azure-portal"></a>Azure Portal을 사용하여 지능형 튜닝을 사용하도록 설정

1. Azure Portal에 로그인하고 Azure Database for PostgreSQL 서버를 선택합니다.
2. 메뉴의 설정 섹션에서 서버 매개 변수를 선택합니다.
3. 지능형 튜닝 매개 변수를 검색합니다.
4. 값을 True로 설정하고 저장합니다.

데이터의 첫 번째 배치가 azure_sys 데이터베이스에서 지속되는 데 최대 35분이 걸립니다.

## <a name="information-about-intelligent-tuning"></a>지능형 튜닝에 대한 정보

지능형 튜닝은 지정된 시간 동안 3가지 주요 기능을 기반으로 작동합니다.

* Checkpoint_completion_target
* Min_wal_size
* Max_wal_size

이 세 가지 변수는 주로 다음에 영향을 줍니다.

* 검사점 기간
* 검사점 빈도
* 동기화 기간

지능형 튜닝은 양방향으로 작동하며 높은 워크로드 동안 지속 시간을 줄이고 유휴 세그먼트 동안 지속 시간을 늘리려고 시도합니다. 이러한 규칙은 사용자가 수동 업데이트 없이 어려운 시기에 개인화된 결과를 얻을 수 있도록 이러한 기능을 최적화하려고 시도하는 방법입니다.

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

* 최적화는 특정 범위에서만 실행되며 변경 사항이 없을 가능성이 있습니다.
* 지능형 튜닝 기능은 쿼리에서 삭제된 데이터베이스로 인해 지연될 수 있으며, 이로 인해 향상된 기능 실행 시 약간의 지연이 발생할 수 있습니다.
  
최적화는 현재 스토리지 섹션에서만 이루어지며 다른 다양한 범주로 확장은 미정입니다.
