---
title: 지원되는 버전 - Azure Database for MySQL
description: Azure Database for MySQL 서비스에서 지원되는 MySQL 서버 버전을 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.custom: ''
ms.date: 6/3/2020
ms.openlocfilehash: 1dc37ae36703e334ca132b52749edbcdb9b3a602
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164786"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>지원되는 MySQL용 Azure 데이터베이스 서버 버전

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL은 [MySQL 커뮤니티 버전](https://www.mysql.com/products/community/)에서 InnoDB 스토리지 엔진을 사용하여 개발되었습니다. 이 서비스는 커뮤니티에서 지 원하는 모든 현재 주 버전 (MySQL 5.7 및 8.0)을 지원 합니다. MySQL은 X.Y.Z 이름 지정 스키마를 사용합니다. 여기서 X는 주 버전이고 Y는 부 버전이며 Z는 버그 수정 릴리스입니다. 스키마에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)를 참조하세요.

## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>특정 MySQL 버전을 실행하는 게이트웨이 노드에 연결

단일 서버 배포 옵션에서 게이트웨이는 서버 인스턴스에 대한 연결을 리디렉션하는 데 사용됩니다. 연결이 설정되면 MySQL 클라이언트는 MySQL Server 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MySQL 버전을 표시합니다. MySQL Server 인스턴스의 버전을 확인하려면 MySQL 프롬프트에서 `SELECT VERSION();` 명령을 사용합니다. [연결 아키텍처](./concepts-connectivity-architecture.md#connectivity-architecture)를 검토하여 Azure Database for MySQL 서비스 아키텍처의 게이트웨이에 대한 자세한 정보를 알아봅니다.

Azure Database for MySQL에서 주 버전 v 5.7 및 v 8.0을 지원 하므로 Azure Database for MySQL에 연결 하는 기본 포트 3306는 MySQL 클라이언트 버전 5.7 (최소 공통 분모)을 실행 하 여 지원 되는 모든 주 버전의 서버에 대 한 연결을 지원 합니다. 그러나 애플리케이션 요구 사항에 따라 특정 주 버전(예: v5.7 또는 v8.0)에 연결해야 하는 경우 서버 연결 문자열의 포트를 변경하여 연결할 수 있습니다.

Azure Database for MySQL 서비스에서 게이트웨이 노드는 v5.7 클라이언트의 경우 포트 3308에서, v8.0 클라이언트의 경우 포트 3309에서 수신 대기합니다. 즉, v5.7 게이트웨이 클라이언트에 연결하려면 정규화된 서버 이름 및 포트 3308을 사용하여 클라이언트 애플리케이션에서 서버에 연결해야 합니다. 마찬가지로, v8.0 게이트웨이 클라이언트에 연결하려면 정규화된 서버 이름 및 포트 3309를 사용하여 서버에 연결할 수 있습니다. 보다 명확한 이해를 위해 다음 예제를 확인합니다.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="다른 게이트웨이 mysql 버전을 통해 연결하는 예제":::

> [!NOTE]
> 포트 3308 및 3309를 통해 Azure Database for MySQL에 연결하는 것은 공용 연결에 대해서만 지원되고, Private Link 및 VNet 서비스 엔드포인트는 포트 3306에서만 사용할 수 있습니다.

## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>현재 Azure Database for MySQL은 다음과 같은 주 버전 및 부 버전의 MySQL을 지원합니다.

| 버전 | [단일 서버](overview.md) <br/> 현재 부 버전 |[유연한 서버(미리 보기)](./flexible-server/overview.md) <br/> 현재 부 버전  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL 버전 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(사용 중지됨) | 지원되지 않음|
|MySQL 버전 5.7 | [5.7.32](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-32.html) | [5.7.32](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-32.html)|
|MySQL 버전 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

[버전 지원 정책 설명서](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)에서 사용 중지된 버전에 대한 지원 정책을 참조하세요.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리

서비스는 버그 수정 버전 업데이트에 대한 패치를 자동으로 관리합니다. 예: 5.7.20~5.7.21  

주 버전 업그레이드는 현재 서비스에서 MySQL v5.6에서 v5.7로의 업그레이드를 지원합니다. 자세한 내용은 [주 버전 업그레이드를 수행하는 방법](how-to-major-version-upgrade.md)을 참조하세요. 5\.7에서 8.0로 업그레이드하려는 경우 새 엔진 버전으로 만든 서버에 [덤프 및 복원](./concepts-migrate-dump-restore.md)을 수행하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL 버전 관리 정책에 대한 자세한 내용은 [이 문서](concepts-version-policy.md)를 참조하세요.
- **서비스 계층** 에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층](./concepts-pricing-tiers.md)을 참조하세요.
