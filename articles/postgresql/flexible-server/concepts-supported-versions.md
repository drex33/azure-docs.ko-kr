---
title: 지원 버전 - Azure Database for PostgreSQL - 유연한 서버
description: Azure Database for PostgreSQL - 유연한 서버에서 지원되는 PostgreSQL 주 버전 및 부 버전을 설명합니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/30/2021
ms.openlocfilehash: 29b7f440f182f01c457c9867d571dc4205f4d83e
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133269449"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버에서 지원되는 PostgreSQL 주 버전



Azure Database for PostgreSQL - 유연한 서버는 현재 다음 주 버전을 지원합니다.

## <a name="postgresql-version-13"></a>PostgreSQL 버전 13

현재 부 릴리스는 **13.4** 입니다. 이 부 릴리스의 향상된 기능 및 수정 사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/13/static/release-13-4.html)를 참조하세요. 이 부 버전을 사용하여 새 서버를 만듭니다. 

## <a name="postgresql-version-12"></a>PostgreSQL 버전 12

현재 부 릴리스는 **12.8** 입니다. 이 부 릴리스의 향상된 기능 및 수정 사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/12/static/release-12-8.html)를 참조하세요. 이 부 버전을 사용하여 새 서버를 만듭니다. 추후 예약된 유지 관리 기간에는 기존 서버가 지원하는 최신 부 버전으로 자동 업그레이드됩니다.

## <a name="postgresql-version-11"></a>PostgreSQL 버전 11

현재 부 릴리스는 **11.13** 입니다. 이 부 릴리스의 향상된 기능 및 수정 사항에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/11/static/release-11-13.html)를 참조하세요. 이 부 버전을 사용하여 새 서버를 만듭니다. 추후 예약된 유지 관리 기간에는 기존 서버가 지원하는 최신 부 버전으로 자동 업그레이드됩니다.

## <a name="postgresql-version-10-and-older"></a>PostgreSQL 버전 10 이상

Azure Database for PostgreSQL - 유연한 서버의 경우 PostgreSQL 버전 10과 그 이전 버전을 지원하지 않습니다. 이전 버전이 필요한 경우에는 [단일 서버](../concepts-supported-versions.md) 배포 옵션을 사용하세요.

## <a name="managing-upgrades"></a>업그레이드 관리

PostgreSQL 프로젝트는 보고된 버그를 수정하기 위해 부 릴리스를 정기적으로 제공합니다. Azure Database for PostgreSQL은 서비스의 월별 배포 중에 부 릴리스로 서버를 자동으로 패치합니다.

주 버전 업그레이드에 대한 자동화는 아직 지원되지 않습니다. 예를 들어, 현재 PostgreSQL 11에서 PostgreSQL 12로 자동 업그레이드되지 않습니다.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->