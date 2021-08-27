---
title: 지원되는 버전 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 사용 가능한 PostgreSQL 버전
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 299005d8d7c961335decbce46bab8c558d2589a1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536063"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – 하이퍼스케일(Citus)에서 지원되는 데이터베이스 버전

## <a name="postgresql-versions"></a>PostgreSQL 버전

하이퍼스케일(Citus) 서버 그룹에서 실행되는 PostgreSQL 버전은 만드는 동안 사용자 지정이 가능합니다. 하이퍼스케일(Citus)은 현재 다음 주요 버전을 지원합니다.

### <a name="postgresql-version-13"></a>PostgreSQL 버전 13

현재 부 릴리스는 13.3입니다. 이 부 릴리스의 향상된 기능 및 수정사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/13/static/release-13-2.html)를 참조하세요.

### <a name="postgresql-version-12"></a>PostgreSQL 버전 12

현재 부 릴리스는 12.7입니다. 이 부 릴리스의 향상된 기능 및 수정 사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/12/static/release-12-6.html)를 참조하세요.

### <a name="postgresql-version-11"></a>PostgreSQL 버전 11

현재 부 릴리스는 11.12입니다. 이 부 릴리스의 향상된 기능 및 수정 사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/11/static/release-11-11.html)를 참조하세요.

### <a name="postgresql-version-10-and-older"></a>PostgreSQL 버전 10 이상

Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 경우 PostgreSQL 버전 10 및 이전 버전을 지원하지 않습니다.

## <a name="citus-and-other-extension-versions"></a>Citus 및 기타 확장 버전

서버 그룹에서 실행되고 있는 PostgreSQL 버전에 따라 다른 [Postgres 확장 버전](concepts-hyperscale-extensions.md)도 설치됩니다.  특히 Postgres 13은 Citus 10과 함께 제공되며 Postgres 이전 버전은 Citus 9.5와 함께 제공됩니다.

## <a name="next-steps"></a>다음 단계

* 어떤 버전에 어떤 [확장](concepts-hyperscale-extensions.md)이 설치되어 있는지 참조하세요.
* [하이퍼스케일(Citus) 서버 그룹 만들기](quickstart-create-hyperscale-portal.md)에 대해 알아봅니다.
