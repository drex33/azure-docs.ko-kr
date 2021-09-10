---
title: 버전 지원 정책 - Azure Database for MySQL - 단일 서버 및 유연한 서버(미리 보기)
description: Azure Database for MySQL의 MySQL 주 버전 및 부 버전에 대한 정책을 설명합니다.
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.custom: fasttrack-edit
ms.date: 11/03/2020
ms.openlocfilehash: e60c675c40345ccd9220453cb66125e2fc55582a
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113089434"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Azure Database for MySQL 버전 지원 정책

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

이 페이지에서는 Azure Database for MySQL 버전 관리 정책에 대해 설명하고 Azure Database for MySQL - 단일 서버 및 Azure Database for MySQL - 유연한 서버(미리 보기) 배포 모드에 적용됩니다.

## <a name="supported-mysql-versions"></a>지원되는 MySQL 버전

Azure Database for MySQL은 [MySQL 커뮤니티 버전](https://www.mysql.com/products/community/)에서 InnoDB 스토리지 엔진을 사용하여 개발되었습니다. 이 서비스는 커뮤니티에서 지원되는 현재 주 버전(즉, MySQL 5.6, 5.7 및 8.0)을 모두 지원합니다. MySQL은 X.Y.Z 이름 지정 스키마를 사용합니다. 여기서 X는 주 버전이고 Y는 부 버전이며 Z는 버그 수정 릴리스입니다. 스키마에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)를 참조하세요.

현재 Azure Database for MySQL은 다음과 같은 주 버전 및 부 버전의 MySQL을 지원합니다.

| 버전 | [단일 서버](overview.md) <br/> 현재 부 버전 |[유연한 서버(미리 보기)](./flexible-server/overview.md) <br/> 현재 부 버전  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL 버전 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(사용 중지됨) | 지원되지 않음|
|MySQL 버전 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL 버전 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

> [!NOTE]
> 단일 서버 배포 옵션에서 게이트웨이는 서버 인스턴스에 대한 연결을 리디렉션하는 데 사용됩니다. 연결이 설정되면 MySQL 클라이언트는 MySQL Server 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MySQL 버전을 표시합니다. MySQL Server 인스턴스의 버전을 확인하려면 MySQL 프롬프트에서 `SELECT VERSION();` 명령을 사용합니다. 애플리케이션 요구 사항에 따라 특정 주 버전(예: v5.7 또는 v8.0)에 연결해야 하는 경우 [여기](concepts-supported-versions.md#connect-to-a-gateway-node-that-is-running-a-specific-mysql-version) 설명서에 설명된 대로 서버 연결 문자열의 포트를 변경하여 연결할 수 있습니다.

> [!IMPORTANT]
> MySQL v5.6은 2021년 2월을 기준으로 단일 서버에서 사용이 중지됩니다. 2021년 9월 1일부터는 Azure Database for MySQL - 단일 서버 배포 옵션에서 새 v5.6 서버를 만들 수 없습니다. 그러나 지정 시간 복구를 수행하고 기존 서버에 대한 읽기 복제본을 만들 수 있습니다.

[버전 지원 정책 설명서](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)에서 사용 중지된 버전에 대한 지원 정책을 참조하세요.

## <a name="major-version-support"></a>주 버전 지원

각 주 버전의 MySQL은 [버전 관리 정책](https://www.mysql.com/support/eol-notice.html)에서 제공된 대로 Azure에서 버전 지원을 시작하는 날부터 MySQL 커뮤니티에서 버전을 사용 중지하는 날까지 Azure Database for MySQL에서 지원됩니다.

## <a name="minor-version-support"></a>부 버전 지원

Azure Database for MySQL은 정기적 유지 관리의 일환으로서 Azure 기본 설정 MySQL 버전으로의 부 버전 업그레이드를 자동으로 수행합니다. 

## <a name="major-version-retirement-policy"></a>주 버전 사용 중지 정책

다음 표에서는 MySQL 주 버전의 사용 중지에 대한 자세한 정보를 제공합니다. 날짜는 [MySQL 버전 관리 정책](https://www.mysql.com/support/eol-notice.html)을 따릅니다.

| 버전 | What's New | Azure 지원 시작 날짜 | 사용 중지 날짜|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [기능](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 2018년 3월 20일 | 2021년 2월
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [기능](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 2018년 3월 20일 | 2023년 10월
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [기능](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 2019년 12월 11일 | 2026년 4월

## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>사용 중지된 MySQL 엔진 버전은 Azure Database for MySQL에서 지원되지 않습니다.

각 MySQL 데이터베이스 버전의 사용 중지 날짜 이후에 사용 중지된 버전을 계속 실행하는 경우 다음 제한 사항을 확인하세요.

- 커뮤니티가 추가 버그 수정 또는 보안 수정을 릴리스하지 않으므로 Azure Database for MySQL은 사용 중지된 데이터베이스 엔진을 버그 또는 보안 문제에 대해 패치하지 않으며 사용 중지된 데이터베이스 엔진과 관련된 보안 조치를 취하지 않습니다. 그러나 Azure는 호스트, OS, 컨테이너 및 기타 서비스 관련 구성 요소에 대해 정기적으로 유지 관리 및 패치를 계속 수행합니다.
- MySQL 데이터베이스와 관련된 지원 문제가 발생하는 경우 지원을 제공하지 못할 수도 있습니다. 이러한 경우 지원을 제공받기 위해서는 데이터베이스를 업그레이드해야 합니다.
- 사용 중지된 버전에서는 새 데이터베이스 서버를 만들 수 없습니다. 그러나 지정 시간 복구를 수행하고 기존 서버에 대한 읽기 복제본을 만들 수 있습니다.
- Azure Database for MySQL에서 개발한 새 서비스 기능은 지원되는 데이터베이스 서버 버전에서만 지원될 수 있습니다.
- 작동 시간 SLA는 Azure Database for MySQL 서비스 관련 문제에만 적용되며 데이터베이스 엔진 관련 버그로 인한 가동 중지 시간에는 적용되지 않습니다.  
- 사용 중지된 데이터베이스 버전에서 식별된 MySQL 데이터베이스 엔진 취약성으로 인해 서비스에 심각한 위협이 발생하는 극단적인 경우, Azure는 먼저 서비스를 보호하기 위해 데이터베이스 서버의 컴퓨팅 노드를 중지할 수 있습니다. 서버를 온라인 상태로 만들기 전에 서버를 업그레이드 하라는 메시지가 표시됩니다. 업그레이드 프로세스 중에는 원하는 경우 이전 버전으로 복원하는 데 사용할 수 있는 서비스에서 수행되는 자동 백업을 사용하여 데이터가 항상 보호됩니다. 

## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL - 단일 서버에서 [지원되는 버전](./concepts-supported-versions.md)을 참조하세요.
- Azure Database for MySQL - 유연한 서버(미리 보기)에서 [지원되는 버전](flexible-server/concepts-supported-versions.md)을 참조하세요.
- 업그레이드를 수행하려면 MySQL [덤프 및 복원](./concepts-migrate-dump-restore.md)을 참조하세요.