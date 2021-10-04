---
title: 지원되는 버전 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 사용 가능한 PostgreSQL 버전
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 63f9cf024101fee3049eddc5f91497ab978d2821
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399691"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – 하이퍼스케일(Citus)에서 지원되는 데이터베이스 버전

## <a name="postgresql-versions"></a>PostgreSQL 버전

하이퍼스케일(Citus) 서버 그룹에서 실행되는 PostgreSQL 버전은 만드는 동안 사용자 지정이 가능합니다. 하이퍼스케일(Citus)은 현재 다음 주요 버전을 지원합니다.

### <a name="postgresql-version-14"></a>PostgreSQL 버전 14

현재 부 릴리스는 14.0입니다. 이 부 릴리스의 향상된 기능 및 수정사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/14/release-14.html)를 참조하세요.

### <a name="postgresql-version-13"></a>PostgreSQL 버전 13

현재 부 릴리스는 13.4입니다. 이 부 릴리스의 향상된 기능 및 수정사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/13/release-13-4.html)를 참조하세요.

### <a name="postgresql-version-12"></a>PostgreSQL 버전 12

현재 부 릴리스는 12.8입니다. 이 부 릴리스의 향상된 기능 및 수정 사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/12/release-12-8.html)를 참조하세요.

### <a name="postgresql-version-11"></a>PostgreSQL 버전 11

현재 부 릴리스는 11.13입니다. 이 부 릴리스의 향상된 기능 및 수정 사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/11/release-11-13.html)를 참조하세요.

### <a name="postgresql-version-10-and-older"></a>PostgreSQL 버전 10 이상

Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 경우 PostgreSQL 버전 10 및 이전 버전을 지원하지 않습니다.

## <a name="citus-and-other-extension-versions"></a>Citus 및 기타 확장 버전

서버 그룹에서 실행되고 있는 PostgreSQL 버전에 따라 다른 [Postgres 확장 버전](concepts-hyperscale-extensions.md)도 설치됩니다. 특히 Postgres 버전 12-14은 Citus 10과 함께 제공 되며 이전 Postgres 버전은 Citus 9.5과 함께 제공 됩니다.

## <a name="next-steps"></a>다음 단계

* 어떤 버전에 어떤 [확장](concepts-hyperscale-extensions.md)이 설치되어 있는지 참조하세요.
* [하이퍼스케일(Citus) 서버 그룹 만들기](quickstart-create-hyperscale-portal.md)에 대해 알아봅니다.
