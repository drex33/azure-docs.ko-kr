---
title: 버전 관리 정책 - Azure Database for PostgreSQL - 단일 서버 및 유연한 서버(미리 보기)
description: Azure Database for PostgreSQL - 단일 서버의 Postgres 주 버전 및 부 버전 관련 정책을 설명합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/30/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 71290479cc04271f465a37623d3814f71d5b448d
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133267777"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Azure Database for PostgreSQL 버전 관리 정책

이 페이지에서는 Azure Database for PostgreSQL 버전 관리 정책을 설명하고 Azure Database for PostgreSQL - 단일 서버 및 Azure Database for PostgreSQL - 유연한 서버 배포 모드에 적용할 수 있습니다.

## <a name="supported--postgresql-versions"></a>지원되는 PostgreSQL 버전

Azure Database for PostgreSQL는 현재 다음 버전을 지원합니다.

| 버전 | 단일 서버 | 유연한 서버 | 하이퍼스케일(Citus) |
| ----- | :------: | :----: | :----: |
| PostgreSQL 13 |  | X  | X |
| PostgreSQL 12 |  | X  | X |
| PostgreSQL 11 | X | X | X |
| PostgreSQL 10 | X |  |  |
| PostgreSQL 9.6 | X |  |  |
| PostgreSQL 9.5(사용 중지됨) | X |  |  |

## <a name="major-version-support"></a>주 버전 지원
PostgreSQL의 각 주 버전은 Azure가 해당 버전에 대한 지원을 시작하는 날짜부터 [PostgreSQL 커뮤니티 버전 관리 정책](https://www.postgresql.org/support/versioning/)에 규정된 대로 PostgreSQL 커뮤니티에서 해당 버전이 사용 중지될 때까지 Azure Database for PostgreSQL에 의해 지원됩니다.

## <a name="minor-version-support"></a>부 버전 지원
Azure Database for PostgreSQL은 정기적 유지 관리의 일환으로 Azure 기본 PostgreSQL 버전으로 부 버전 업그레이드를 자동으로 수행합니다. 

## <a name="major-version-retirement-policy"></a>주 버전 사용 중지 정책
다음 표에서는 PostgreSQL 주 버전 사용 중지 정보를 제공합니다. 해당 날짜는 [PostgreSQL 커뮤니티 버전 관리 정책](https://www.postgresql.org/support/versioning/)을 따릅니다.

| 버전 | What's New | Azure 지원 시작 날짜 | 사용 중지 날짜|
| ----- | ----- | ------ | ----- |
| [PostgreSQL 9.5(사용 중지됨)](https://www.postgresql.org/about/news/postgresql-132-126-1111-1016-9621-and-9525-released-2165/)| [기능](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 2018년 4월 18일   | 2021년 2월 11일
| [PostgreSQL 9.6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [기능](https://wiki.postgresql.org/wiki/NewIn96) | 2018년 4월 18일  | 2021년 11월 11일
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [기능](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 2018년 6월 4일  | 2022년 11월 10일
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [기능](https://www.postgresql.org/docs/11/release-11.html) | 2019년 7월 24일  | 2023년 11월 9일
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [기능](https://www.postgresql.org/docs/12/release-12.html) | 2020년 9월 22일  | 2024년 11월 14일
| [PostgreSQL 13](https://www.postgresql.org/about/news/postgresql-13-released-2077/) | [기능](https://www.postgresql.org/docs/13/release-13.html) | 2021년 5월 25일   | 2025년 11월 13일

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 지원되지 않는 사용 중지된 PostgreSQL 엔진 버전

Azure Database for PostgreSQL에서 사용 중지된 버전을 계속 실행할 수 있습니다. 그러나 각 PostgreSQL 데이터베이스 버전에 대한 사용 중지 날짜가 경과한 이후에는 다음 제한 사항에 유의하세요.
- 커뮤니티가 더 이상 버그 수정 또는 보안 픽스를 릴리스하지 않으므로 Azure Database for PostgreSQL이 사용 중지된 데이터베이스 엔진에 버그 또는 보안 문제 관련 패치를 적용하거나 사용 중지된 데이터베이스 엔진에 대한 보안 조치를 취하지 않습니다. 따라서 보안 취약성 또는 기타 문제가 발생할 수 있습니다. 그러나 Azure는 호스트, OS, 컨테이너 및 기타 서비스 관련 구성 요소에 대해 정기적으로 유지 관리 및 패치를 계속 수행합니다.
- 커뮤니티에서 더 이상 패치를 제공하지 않기 때문에 PostgreSQL 엔진 자체와 관련하여 발생할 수 있는 지원 문제가 있는 경우 지원을 제공하지 못할 수 있습니다. 이러한 경우 데이터베이스를 지원되는 버전 중 하나로 업그레이드해야 합니다.
- 사용 중지된 버전에서는 새 데이터베이스 서버를 만들 수 없습니다. 그러나 기존 서버에 대해 특정 시점 복구를 수행하고 읽기 복제본을 만들 수 있습니다.
- Azure Database for PostgreSQL에서 개발한 새 서비스 기능은 지원되는 데이터베이스 서버 버전에서만 사용할 수 있습니다.
- 작동 시간 SLA는 Azure Database for PostgreSQL 서비스 관련 문제에만 적용되며 데이터베이스 엔진 관련 버그로 인한 가동 중지 시간에는 적용되지 않습니다.  
- 사용 중지된 데이터베이스 버전에서 식별된 PostgreSQL 데이터베이스 엔진 취약성으로 인해 서비스에 대한 심각한 위협이 발생하는 극단적인 경우에는 Azure에서 서비스를 보호하기 위해 데이터베이스 서버를 중지하도록 선택할 수 있습니다. 이 경우 서버를 온라인 상태로 전환하기 전에 서버를 업그레이드하라는 알림이 표시됩니다.

## <a name="postgresql-version-syntax"></a>PostgreSQL 버전 구문
PostgreSQL 버전 10 이전에는 [PostgreSQL 버전 관리 정책](https://www.postgresql.org/support/versioning/)은 첫 번째 또는 두 번째 숫자의 증가를 '주 버전' 업그레이드로 간주했습니다. 예를 들어 9.5에서 9.6으로 업그레이드는 '주' 버전 업그레이드로 간주되었습니다. 버전 10부터 첫 번째 숫자의 변경만 주 버전 업그레이드로 간주됩니다. 예를 들어 10.0에서 10.1로 업그레이드는 '부' 릴리스 업그레이드입니다. 버전 10에서 버전 11로 업그레이드는 '주' 버전 업그레이드입니다.

## <a name="next-steps"></a>다음 단계
- Azure Database for PostgreSQL - 단일 서버 [지원되는 버전](./concepts-supported-versions.md)을 참조하세요.
- Azure Database for PostgreSQL - 유연한 서버 [지원 버전을](flexible-server/concepts-supported-versions.md) 참조하세요.
- 주 버전 업그레이드를 수행하는 방법에 대한 자세한 내용은 [주 버전 업그레이드](how-to-upgrade-using-dump-and-restore.md) 설명서를 참조하세요.
- 지원되는 PostgreSQL 확장에 대한 자세한 내용은 [확장 문서](concepts-extensions.md)를 참조하세요.
