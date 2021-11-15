---
title: Azure CLI 샘플 - Azure Database for MySQL 유연한 서버
description: 이 문서에서는 Azure Database for MySQL - 유연한 서버와 상호 작용하는 데 사용할 수 있는 Azure CLI 코드 샘플을 나열합니다.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 8ae33838b4dff052006f1124eea3dbec59cc7666
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844273"
---
# <a name="azure-cli-samples-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - 유연한 서버용 Azure CLI 샘플

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

다음 표에는 Azure Database for MySQL - 유연한 서버용 Azure CLI 샘플 스크립트에 대한 링크가 포함되어 있습니다.

| 샘플 링크 | Description  |
|---|---|
|**서버 만들기 및 연결**||
| [서버 만들기 및 퍼블릭 액세스 연결 사용](scripts/sample-cli-create-connect-public-access.md) | Azure Database for MySQL - 유연한 서버를 만들고, 서버 수준 방화벽 규칙(퍼블릭 액세스 연결 방법)을 구성하고, 서버에 연결합니다. |
| [서버 만들기 및 프라이빗 액세스 연결 사용(VNet 통합)](scripts/sample-cli-create-connect-private-access.md) | VNet(프라이빗 액세스 연결 방법)에서 Azure Database for MySQL - 유연한 서버를 만들고, VNet 내의 VM을 통해 서버에 연결합니다. |
|**모니터링 및 크기 조정**||
| [메트릭 모니터링 및 서버 크기 조정](scripts/sample-cli-monitor-and-scale.md) | 단일 Azure Database for MySQL - 유연한 서버를 모니터링하고 스케일 업 또는 스케일 다운하여 성능 요구 사항을 변경합니다. |
|**Backup 및 복원**||
| [서버 복원](scripts/sample-cli-restore-server.md) | 단일 Azure Database for MySQL 유연한 서버를 이전 시점으로 복원합니다. |
|**고가용성**||
| [영역 중복 고가용성 구성](scripts/sample-cli-zone-redundant-ha.md) | Azure Database for MySQL - 유연한 서버를 만드는 동안 영역 중복 고가용성을 사용하도록 설정합니다.|
| [동일한 영역의 고가용성 구성](scripts/sample-cli-same-zone-ha.md) | Azure Database for MySQL - 유연한 서버를 만드는 동안 동일한 영역의 고가용성을 사용하도록 설정합니다.|
|**서버 관리**||
| [서버 다시 시작, 중지, 시작](scripts/sample-cli-restart-stop-start.md)| 단일 Azure Database for MySQL - 유연한 서버에서 다시 시작, 중지 및 시작 작업을 수행합니다. |
| [서버 매개 변수 변경](scripts/sample-cli-change-server-parameters.md) | 단일 Azure Database for MySQL - 유연한 서버의 서버 매개 변수를 변경합니다. |
|**복제**||
| [읽기 복제본 만들기](scripts/sample-cli-read-replicas.md) | 단일 Azure Database for MySQL - 유연한 서버에서 읽기 복제본을 만들고 관리합니다. |
|**로그 구성**||
| [감사 로그 구성](scripts/sample-cli-audit-logs.md) | 단일 Azure Database for MySQL - 유연한 서버에서 감사 로그를 구성합니다. |
| [느린 쿼리 로그 구성](scripts/sample-cli-slow-query-logs.md) | 단일 Azure Database for MySQL - 유연한 서버에서 느린 쿼리 로그를 구성합니다. |

