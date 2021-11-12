---
title: Azure Database for PostgreSQL - 단일 서버 및 유연한 서버 비교
description: Azure Database for PostgreSQL 단일 서버와 유연한 서버 간의 기능에 대한 자세한 비교
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: c5b4f6611dc5ad5a514eaf1affd0517c14fcd353
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337115"
---
# <a name="comparison-chart---azure-database-for-postgresql-single-server-and-flexible-server"></a>비교 차트 - Azure Database for PostgreSQL 단일 서버 및 유연한 서버

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

다음 표는 단일 서버와 유연한 서버 간의 상위 수준 특징 및 기능 비교를 제공합니다.

| **특징/기능** | **단일 서버** | **유연한 서버** |
| ---- | ---- | ---- |
| **일반**  | | |
| 일반 공급 | 2018년 이후 GA | 공개 미리 보기 |
| PostgreSQL | 커뮤니티 | 커뮤니티 |
| 버전 | 9.6, 10, 11 | 11, 12, 13 |
| 기본 운영 체제 | Windows | Linux  |
| 애플리케이션 공동 배치를 위한 AZ 선택 | 아니요 | 예 |
| 기본 제공 연결 풀 | No | 예(PgBouncer)|
| **연결** | | |
| 연결 문자열의 사용자 이름 | `<user_name>@server_name`. 예를 들어 `pgadmusr@mypgServer` | 사용자 이름. 예를 들어 `pgadmusr` | 
| lc_collate  | English_United States.1252 | en_US.utf8 |
| lc_ctype    | English_United States.1252 |en_US.utf8 |
|lc_messages | English_United States.1252 |en_US.utf8|
| lc_monetary | English_United States.1252 |  en_US.utf-8 |
| lc_numeric  | English_United States.1252 |  en_US.utf-8 |
| lc_time     | English_United States.1252 | en_US.utf8 |
| 연결 포트 | 5432 | 5432(DB), 6432(PgBouncer) |
| 최대 connections | 1982 | 5,000 |
| 연결 제한 구성 가능? | No | 예(`max_connections` 매개 변수) |
| **컴퓨팅 및 스토리지** | | |
| 컴퓨팅 계층 | 기본, 범용 및 메모리 최적화 | 버스트 가능, 범용 및 메모리 최적화 |
| 버스트 가능한 SKU | 아니요 | 예 |
| 컴퓨팅 계층 간 크기 조정 기능 | 기본 계층을 크기 조정할 수 없음 | 예. 계층 간에 크기 조정 가능 |
| 중지/시작 | No | 예(모든 컴퓨팅 SKU에 대해). 컴퓨팅만 중지/시작됨 |
| 최대 스토리지 크기 | 1TB(기본), 4TB 또는 16TB(GP,MO) 참고: 일부 지역에서는 16TB를 지원하지 않습니다. | 16TB |
| 최소 스토리지 크기 | 5GB(기본), 100GB(GP, MO) | 32GB |
| 스토리지 자동 증가 | 예(1GB 증분) | No |
| 최대 IOPS | 기본 - 변수. GP/MO: 최대 20K  | 최대 20K |
| **네트워킹/보안** | | |
| 지원되는 네트워킹 | 가상 네트워크, 프라이빗 링크, 공용 액세스 | 공용 액세스(위임된 서브넷의 VNET 주입), 공용 액세스) |
| 공용 액세스 제어 | 방화벽 | 방화벽 |
| 프라이빗 링크 지원 | 예 |아니요|
| 프라이빗 DNS 영역 지원 | 아니요 | 예 |
| 프라이빗 액세스와 공용 액세스 사이를 이동할 수 있는 기능 | 아니요 | 아니요 |
| TLS 지원 | TLS 1.2 | TLS 1.2, 1.3 적용됨|
| SSL을 끌 수 있음 | 예 | 아니요 |
| SCRAM 인증 | 아니요 | 예 |
| **고가용성** | | |
| 영역 중복 HA | No | 예(지역 내의 다른 영역에 동기 대기가 설정됨) |
| HA 구성 | 영역에 고정된 스토리지가 기본 제공됨. 컴퓨팅은 여러 지역에 걸쳐 부동 상태일 수 있음 | 물리적으로 분리된 컴퓨팅 및 두 영역에 걸쳐 프로비저닝된 스토리지 |
| 비용 | 1x | 2x(컴퓨팅 + 스토리지) |
| 비HA 구성의 가용성 | 자동 재시작, 컴퓨팅 재배치 | 자동 재시작, 컴퓨팅 재배치)
| 영역 오류로부터 보호 | 컴퓨팅 - 예. 스토리지 - 아니요 | 컴퓨팅 및 스토리지 - 예 |
| 지역 오류로부터 보호 | 아니요 | 아니요 |
| HA 복제 모드 | 해당 없음 | SYNC 모드에서 Postgres 물리적 스트리밍 복제)
| 읽기 목적으로 대기를 사용할 수 있음 | 해당 없음 | 아니요 |
| 애플리케이션 성능 영향 | 아니요(복제하지 않음) | 예(동기화 복제로 인해. 워크로드에 따라 다름) |
| 자동 장애 조치(automatic failover) | 예(다른 서버 회전)| Yes |
| 장애 조치(failover) 후 애플리케이션 연결 문자열 | 변경 내용 없음 | 변경 내용 없음 |
| **논리적 복제** | | |
| 논리적 디코딩 지원 | 예 | 예 |
| 기본 논리적 복제 지원 | 아니요 | 예 |
| PgLogical 확장 지원 | 아니요 | 예 |
| HA로 논리적 복제 지원 | 해당 없음 | 제한됨 |
| **재해 복구** | | |
| 지역 간 DR | 읽기 복제본, 지역 중복 백업 사용 | 해당 없음 |
| 복제본을 사용한 DR | 비동기 물리적 복제 사용 | 해당 없음 |
| 자동 장애 조치(automatic failover) | 예 | 해당 없음 |
| 동일한 r/w 엔드포인트를 사용할 수 있음 | 아니요 | 해당 없음 |
| **백업 및 복구** | | |
| 자동화된 백업 | 예 | 예 |
| 백업 보존 | 7-35일 | 7-35일 |
| 보존 기간 내 언제든지 PITR 기능 | 예 | 예
| 다른 영역에서 복원하는 기능 | 해당 없음 | 예 |
| 다른 VNET으로 복원하는 기능 | 아니요 | 예 |
| 다른 지역으로 복원하는 기능 | 예(지역 중복) | No |
| 삭제된 서버를 복원하는 기능 | API를 통해 제한됨 | No |
| **읽기 복제본** | | |
| 읽기 복제본 지원 | 예 | 아니요 |
| 읽기 복제본 수 | 5 | 해당 없음 |
| 복제 모드 | Async | 해당 없음 |
| 지역 간 지원 | 예 | 해당 없음 |
| **유지 관리 기간** | | |
| 시스템 예약 기간 | 예 | 예 |
| 고객 예약 기간| No | 예(요일과 상관없이 1시간을 선택할 수 있음) |
| 공지 기간 | 3일 | 5일 |
| 유지 보수 기간 | 15시간 이내 언제든지 | 1시간 | 
| **메트릭** | | |
| 오류 | 실패한 연결 | 실패한 연결|
| 대기 시간 | 복제본 간의 최대 지연, 복제본 지연 | 해당 없음 |
| 채도 | 백업 스토리지 사용, CPU %, IO %, 메모리 %, 서버 로그 스토리지 제한, 서버 로그 스토리지 %, 사용된 서버 로그 스토리지, 스토리지 제한, 스토리지 %, 사용된 스토리지 | 사용된 백업 스토리지, 사용된 CPU 크레딧, 남은 CPU 크레딧, CPU %, 디스크 큐 깊이, IOPS, 메모리 %, 읽기 IOPS, 읽기 처리량 바이트/초, 사용 가능한 스토리지, 스토리지 %, 사용된 스토리지, 사용된 트랜잭션 로그 스토리지, 쓰기 IOPS, 쓰기 처리량 바이트/초 |
| 트래픽 | 활성 연결, 네트워크 입력, 네트워크 출력 | 활성 연결, 최대 사용된 트랜잭션 ID, 네트워크 입력, 네트워크 출력, 성공한 연결 |
| **확장** | | (최신 버전 제공)|
| TimescaleDB, orafce, plv8 | 예 | 아니요 |
| PgCron, lo, pglogical | 아니요 | 예 |
| pgAudit | 미리 보기 | 예 |
| **기타 기능** | | |
| 고객 관리형 키(BYOK) | 예 | 아니요 |
| 경고 | 예 | 예 |
| Microsoft Defender for Cloud | Yes | 아니요 |
| 리소스 상태 | 예 | 아니요 |
| 서비스 상태 | 예 | 예 |
| 성능 인사이트(iPerf) | Yes | 예(미리 보기) |
| 주 버전 업그레이드 지원 | 아니요 | 아니요 |
| 부 버전 업그레이드 | 예. 유지 관리 기간 동안 자동 | 예. 유지 관리 기간 동안 자동 |


## <a name="next-steps"></a>다음 단계

- [컴퓨팅 및 스토리지 옵션에 사용할 수 있는 기능 - 유연한 서버](concepts-compute-storage.md) 이해
- [유연한 서버에서 지원되는 PostgreSQL 데이터베이스 버전](concepts-supported-versions.md)에 대해 알아보기
- [유연한 서버의 현재 제한사항](concepts-limits.md)에 대해 알아보기
