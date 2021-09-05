---
title: 연결 폴링 – 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 클라이언트 데이터베이스 연결 크기 조정
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: d611698bcc72a4a8ceaafb6cfe43f7da362acf71
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536332"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-connection-pooling"></a>Azure Database for PostgreSQL – 하이퍼스케일(Citus) 연결 폴링

새 연결을 설정하려면 시간이 걸립니다. 이는 많은 일시적인 연결을 요청하는 대부분의 애플리케이션에 대해 적용됩니다. 유휴 트랜잭션을 줄이고 기존 연결을 다시 사용하려면 연결 풀러를 사용하는 것이 좋습니다. 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)을 참조하세요.

사용자 고유의 연결 풀러를 실행하거나 Azure에서 관리하는 PgBouncer를 사용할 수 있습니다.

## <a name="managed-pgbouncer"></a>관리되는 PgBouncer

PgBouncer와 같은 연결 풀러를 사용하면 더 많은 클라이언트가 동시에 코디네이터 노드에 연결할 수 있습니다. 애플리케이션은 풀러에 연결하고 풀러는 대상 데이터베이스에 명령을 릴레이합니다.

클라이언트가 PgBouncer를 통해 연결하는 경우 데이터베이스에서 적극적으로 실행될 수 있는 연결 수는 변경되지 않습니다. 대신 PgBouncer는 초과 연결을 큐에 넣고 데이터베이스가 준비되면 실행합니다.

현재 하이퍼스케일(Citus)은 서버 그룹에 대해 PgBouncer의 관리형 인스턴스를 제공합니다. 또한 최대 2,000개의 동시 클라이언트 연결을 지원합니다.  PgBouncer를 통해 연결하려면 다음 단계를 수행합니다.

1. Azure Portal에서 서버 그룹에 대한 **연결 문자열** 페이지로 이동합니다.
2. **PgBouncer 연결 문자열** 확인란을 사용하도록 설정합니다. 나열되는 연결 문자열이 변경됩니다.
3. 새 문자열로 연결하도록 클라이언트 애플리케이션을 업데이트합니다.

## <a name="next-steps"></a>다음 단계

하이퍼스케일(Citus)의 [한도와 제한](concepts-hyperscale-limits.md)에 대해 자세히 알아봅니다.
