---
title: 지원되는 버전 - Azure Database for PostgreSQL - 단일 서버
description: Azure Database for PostgreSQL - 단일 서버의 지원되는 Postgres 주 버전 및 부 버전을 설명합니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/01/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: c20eb75fbb248ff67fb244fde1355aae9c726d7a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529112"
---
# <a name="supported-postgresql-major-versions"></a>지원되는 PostgreSQL 주 버전

지원 정책에 대한 자세한 내용은 [Azure Database for PostgreSQL 버전 관리 정책](concepts-version-policy.md)을 참조하세요.

PostgreSQL용 Azure 데이터베이스는 현재 다음 주 버전을 지원합니다.

## <a name="postgresql-version-11"></a>PostgreSQL 버전 11
현재 부 릴리스는 11.11입니다. 이 부 릴리스의 향상된 기능 및 수정사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/11/static/release-11-11.html)를 참조하세요.

## <a name="postgresql-version-10"></a>PostgreSQL 버전 10
현재 부 릴리스는 10.16입니다. 이 부 릴리스의 향상된 기능 및 수정사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/10/static/release-10-16.html)를 참조하세요.

## <a name="postgresql-version-96"></a>PostgreSQL 버전 9.6
현재 부 릴리스는 9.6.21입니다. 이 부 릴리스의 향상된 기능 및 수정사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/9.6/static/release-9-6-21.html)를 참조하세요.

## <a name="postgresql-version-95-retired"></a>PostgreSQL 버전 9.5(사용 중지)
Postgres 커뮤니티의 [버전 정책](https://www.postgresql.org/support/versioning/)에 맞춰 2021년 2월 11일부로 Azure Database for PostgreSQL에서 Postgres 버전 9.5가 사용 중지되었습니다. 자세한 내용과 제한 사항은 [Azure Database for PostgreSQL 버전 정책](concepts-version-policy.md)을 참조하세요. 이 주 버전을 실행하는 경우 가능한 한 빨리 더 높은 버전(PostgreSQL 11 권장)으로 업드레이드하십시오.

## <a name="managing-upgrades"></a>업그레이드 관리
PostgreSQL 프로젝트는 보고된 버그를 수정하기 위해 부 릴리스를 정기적으로 제공합니다. Azure Database for PostgreSQL은 서비스의 월별 배포 중에 부 릴리스로 서버를 자동으로 패치합니다. 

주 버전에 대한 자동 현재 위치 업그레이드는 지원되지 않습니다. 다음과 같은 방법을 통해 더 높은 주 버전으로 업그레이드할 수 있습니다. 
   * [덤프 및 복원을 사용하여 주 버전 업그레이드](./how-to-upgrade-using-dump-and-restore.md)에 설명된 방법 중 하나를 사용합니다.
   * [pg_dump 및 pg_restore](./howto-migrate-using-dump-and-restore.md)를 사용하여 새 엔진 버전을 사용하도록 만든 서버로 데이터베이스를 이동합니다.
   * [Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)를 사용하여 온라인 업그레이드를 수행합니다.

### <a name="version-syntax"></a>버전 구문
PostgreSQL 버전 10 이전에는 [PostgreSQL 버전 관리 정책](https://www.postgresql.org/support/versioning/)은 첫 번째 _또는_ 두 번째 숫자의 증가를 _주 버전_ 업그레이드로 간주했습니다. 예를 들어 9.5에서 9.6으로 업그레이드는 '주' 버전 업그레이드로 간주되었습니다. 버전 10부터 첫 번째 숫자의 변경만 주 버전 업그레이드로 간주됩니다. 예를 들어 10.0에서 10.1로 업그레이드는 '부' 릴리스 업그레이드입니다. 버전 10에서 버전 11로 업그레이드는 '주' 버전 업그레이드입니다.

## <a name="next-steps"></a>다음 단계
지원되는 PostgreSQL 확장에 대한 자세한 내용은 [확장 문서](concepts-extensions.md)를 참조하세요.
