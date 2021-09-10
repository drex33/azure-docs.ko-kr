---
title: 최소 가동 중지 시간 마이그레이션 - Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL로 MySQL 데이터베이스의 최소 가동 중지 시간 마이그레이션을 수행하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: ''
ms.date: 6/19/2021
ms.openlocfilehash: 728c1d76539eb34efe5e5fc475837810ea5a5111
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113088587"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Azure Database for MySQL로 최소 가동 중지 시간 마이그레이션

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

애플리케이션에서 발생하는 가동 중지 시간을 제한하는 데이터 내 복제를 사용하여 가동 중지 시간을 최소화하면서 Azure Database for MySQL 마이그레이션을 수행할 수 있습니다.

데이터베이스를 Azure Database for MySQL로 마이그레이션하는 방법에 대한 자세한 내용 및 사용 사례는 [데이터베이스 마이그레이션 가이드](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)를 참조할 수도 있습니다. 이 가이드는 Azure로의 MySQL 마이그레이션의 계획 및 실행을 안내하는 참고 자료를 제공합니다.

## <a name="overview"></a>개요

데이터 내 복제를 사용하여 원본을 주 복제본으로 구성하고 대상을 복제본으로 구성하여 애플리케이션이 계속 실행되는 동안 새 트랜잭션을 Azure에 지속적으로 동기화할 수 있습니다. 데이터가 Azure 쪽 대상을 처리한 후 잠시(최소 가동 중지 시간) 동안 애플리케이션을 중지하고, 대상을 처리하기 위해 데이터의 마지막 일괄 처리(애플리케이션을 중지한 때부터 애플리케이션이 모든 새 트래픽을 효과적으로 사용할 수 없을 때까지)를 기다린 다음, Azure를 가리키도록 연결 문자열을 업데이트합니다. 완료하면 애플리케이션이 Azure에 게시됩니다!

## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL로 데이터베이스 마이그레이션에 대한 자세한 내용은 [데이터베이스 마이그레이션 가이드](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)을 참조합니다.
