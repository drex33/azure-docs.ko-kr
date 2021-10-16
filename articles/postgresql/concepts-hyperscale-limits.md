---
title: 한도 및 제한 – 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 하이퍼스케일(Citus) 서버 그룹의 현재 한도
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: e182ea6e5e7e14777614723bc6105c73a94b848e
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073509"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Azure Database for PostgreSQL – 하이퍼스케일(Citus) 한도 및 제한

다음 섹션에서는 하이퍼스케일(Citus) 서비스의 용량 및 기능 한도에 대해 설명합니다.

## <a name="networking"></a>네트워킹

### <a name="maximum-connections"></a>최대 연결 수

모든 PostgreSQL 연결(유휴 연결인 경우에도)은 10MB 이상의 메모리를 사용하므로 동시 연결을 제한하는 것이 중요합니다. 다음은 노드를 정상 상태로 유지하기 위해 선택한 한도입니다.

* 코디네이터 노드
   * 최대 연결 수
       * 0~3개 vCore의 경우 300
       * 4~15개 vCore의 경우 500
       * 16개 이상 vCore의 경우 1,000
   * 최대 사용자 연결
       * 0~3개 vCore의 경우 297
       * 4~15개 vCore의 경우 497
       * 16개 이상 vCore의 경우 997
* 작업자 노드
   * 최대 연결 수
       * 600

이러한 한도를 초과하여 연결하려고 하면 오류가 발생하여 실패합니다. 시스템은 모니터링 노드에 대해 세 개의 연결을 예약하는데, 그 이유는 사용자 쿼리에 사용할 수 있는 연결이 총 연결보다 세 개 더 적기 때문입니다.

#### <a name="connection-pooling"></a>연결 풀링

[연결 풀링](concepts-hyperscale-connection-pool.md)을 사용하여 연결을 더 확장할 수 있습니다. 하이퍼스케일(Citus)은 최대 2,000개의 동시 클라이언트 연결을 위해 구성된 관리형 pgBouncer 연결 풀러를 제공합니다.

### <a name="private-access-preview"></a>프라이빗 액세스(미리 보기)

#### <a name="server-group-name"></a>서버 그룹 이름입니다.

[프라이빗 액세스](concepts-hyperscale-private-access.md)와 호환하려면 하이퍼스케일(Citus) 서버 그룹에 40자 이하의 이름이 있어야 합니다.

#### <a name="regions"></a>영역

프라이빗 액세스 기능은 다음 지역에서만 미리 보기로 제공됩니다.

* 아메리카
    * 미국 동부
    * 미국 동부 2
    * 미국 서부 2
* 아시아 태평양
    * 일본 동부
    * 일본 서부
    * 한국 중부
* 유럽
    * 독일 중서부
    * 영국 남부
    * 서유럽

## <a name="storage"></a>스토리지

### <a name="storage-scaling"></a>스토리지 스케일링

코디네이터 및 작업자 노드의 스토리지는 스케일 업(증가)할 수 있지만 스케일 다운(감소)할 수는 없습니다.

### <a name="storage-size"></a>스토리지 크기

최대 2TiB의 스토리지가 코디네이터 및 작업자 노드에서 지원됩니다. [위](concepts-hyperscale-configuration-options.md#compute-and-storage)의 사용 가능한 스토리지 옵션 및 IOPS 계산에서 노드 및 클러스터 크기를 참조하세요.

## <a name="postgresql"></a>PostgreSQL

### <a name="database-creation"></a>데이터베이스 만들기

Azure Portal은 하이퍼스케일(Citus) 서버 그룹당 정확히 하나의 데이터베이스(`citus` 데이터베이스)에 연결하는 자격 증명을 제공합니다. 현재 다른 데이터베이스를 만들 수 없으며 CREATE DATABASE 명령이 실패하여 오류가 발생합니다.

### <a name="columnar-storage"></a>열 형식 스토리지

현재 하이퍼스케일(Citus) [열 형식 테이블](concepts-hyperscale-columnar.md)에는 다음과 같은 제한이 있습니다.

* 압축이 메모리가 아닌 디스크에 있음
* 추가 전용(UPDATE/DELETE 지원 안 함)
* 공간 재사용 안 함(예: 롤백된 트랜잭션이 디스크 공간을 계속 사용할 수 있음)
* 인덱스 지원, 인덱스 검색 또는 비트맵 인덱스 검색 안 함
* tidscans 없음
* 샘플 검색 안 함
* TOAST 지원 안 함(인라인으로 지원되는 큰 값)
* ON CONFLICT 문 지원 안 함(대상이 지정되지 않은 DO NOTHING 작업 제외).
* 튜플 잠금 지원 안 함(SELECT ... FOR SHARE, SELECT ... FOR UPDATE)
* 직렬화 가능한 격리 수준 지원 안 함
* PostgreSQL 서버 버전 12 이상만 지원
* 외래 키, 고유 제약 조건 또는 제외 제약 조건 지원 안 함
* 논리적 디코딩 지원 안 함
* 노드 내 병렬 검색 지원 안 함
* AFTER ... FOR EACH ROW 트리거 지원 안 함
* UNLOGGED 열 형식 테이블 없음
* TEMPORARY 열 형식 테이블 없음

## <a name="next-steps"></a>다음 단계

* [포털에서 하이퍼스케일(Citus) 서버 그룹 만드는](quickstart-create-hyperscale-portal.md) 방법을 알아봅니다.
* [연결 풀링](concepts-hyperscale-connection-pool.md) 사용 방법을 알아봅니다.
