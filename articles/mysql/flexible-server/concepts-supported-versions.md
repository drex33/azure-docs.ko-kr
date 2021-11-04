---
title: 지원되는 버전 - Azure Database for MySQL 유연한 서버
description: Azure Database for MySQL 유연한 서버에서 지원되는 MySQL 서버 버전 알아보기
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 80a849c13344fa202980582b4aec043e82dac86c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468103"
---
# <a name="supported-versions-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - 유연한 서버에 대해 지원되는 버전

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL 유연한 서버는 InnoDB 엔진을 사용하는 [MySQL Community Edition](https://www.mysql.com/products/community/)으로 구동됩니다.

MySQL은 X.Y.Z 명명 체계를 사용합니다. X는 주 버전, Y는 부 버전이며, Z는 버그 수정 릴리스입니다. 스키마에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)를 참조하세요.

> [!NOTE]
> MySQL Server 인스턴스의 버전을 확인하려면 MySQL 프롬프트에서 `SELECT VERSION();` 명령을 사용합니다.

Azure Database for MySQL은 현재 다음 버전을 지원합니다.

## <a name="mysql-version-57"></a>MySQL 버전 5.7

버그 수정 릴리스: 5.7.29

서비스는 기본 하드웨어, OS 및 데이터베이스 엔진의 자동화된 패치를 수행합니다. 패치에는 보안 및 소프트웨어 업데이트가 포함됩니다. MySQL 엔진의 경우 계획된 유지 관리 릴리스에 부 버전 업그레이드도 포함됩니다. 사용자는 시스템 관리를 위해 패치 일정을 구성하거나 사용자 지정 일정을 정의할 수 있습니다. 유지 관리 일정 중에 패치가 적용되며, 업데이트를 완료하기 위해 패치 프로세스 중에 서버를 다시 시작해야 할 수도 있습니다. 사용자 지정 일정을 사용하면 사용자가 패치 주기를 예측 가능하도록 설정하고 비즈니스에 최소한의 영향을 주는 유지 관리 기간을 선택할 수 있습니다. 일반적으로 서비스는 연속 통합 및 릴리스의 일부로 월별 릴리스 일정을 따릅니다.

이 버전의 향상된 기능 및 수정 내용에 대한 자세한 내용은 MySQL [릴리스 정보](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)를 참조하세요.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리
서비스는 버그 수정 버전 업데이트에 대한 패치를 자동으로 관리합니다. 예: 5.7.29~5.7.30

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[MySQL을 사용하여 Windows에서 PHP 앱 빌드](../../app-service/tutorial-php-mysql-app.md)<br/>
>[MySQL을 사용하여 Linux에서 PHP 앱 빌드](../../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%253fpivots%253dplatform-linux)<br/>
>[MySQL을 사용하여 Java 기반 Spring 앱 빌드](/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)<br/>