---
title: 서버 개념 - Azure Database for MySQL 유연한 서버
description: 이 항목에서는 Azure Database for MySQL 유연한 서버를 사용할 때의 고려 사항 및 지침을 제공합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: c368e94d8d2b07e3d1487d1b6e505d2a87a89e91
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994084"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Azure Database for MySQL 유연한 서버의 서버 개념(미리 보기)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT] 
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

이 문서에서는 Azure Database for MySQL 유연한 서버를 사용할 때의 고려 사항 및 지침을 제공합니다.

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL 유연한 서버란?

Azure Database for MySQL 유연한 서버는 MySQL의 커뮤니티 버전을 실행하는 완전 관리형 데이터베이스 서비스입니다. 일반적으로 이 서비스는 사용자 요구 사항에 따라 유연성과 구성 사용자 지정을 제공합니다. 온-프레미스 환경에서도 익숙할 수 있는 동일한 MySQL 서버 구문입니다. 특히 유연한 서버는 관리되며, 기본적으로 제공되는 성능, 향상된 서버 관리 효율성 및 제어 기능을 제공하고, 서버 수준에서 액세스 및 기능을 제공합니다.

Azure Database for MySQL 유연한 서버:

- Azure 구독 내에서 만들어집니다.
- 데이터베이스에 대한 부모 리소스입니다.
- 서버 매개 변수를 통해 MySQL 구성을 제공할 수 있습니다(서버 매개 변수 개념에 대한 링크).
- 자동화된 백업을 수행하고 특정 시점 복원을 지원합니다.
- 데이터베이스에 대한 네임스페이스를 제공합니다.
- 강력한 수명 의미 체계를 가진 컨테이너로서 서버를 삭제하고 포함된 데이터베이스를 삭제합니다.
- 하위 지역에 리소스를 배치합니다.
- 고객 제공 서버 유지 관리 일정에 대한 지원
- 고가용성 향상을 위해 영역 중복 설정에서 유연한 서버를 배포하는 기능
- 데이터베이스 서버 액세스에 대한 가상 네트워크 통합 제공
- 사용하지 않을 때 유연한 서버를 일시 중지하여 비용을 절감하는 방법 제공
- 로그인, 방화벽, 사용자, 역할 구성 등 해당 데이터베이스에 적용되는 관리 정책에 대한 범위를 제공합니다.
- 주 버전 MySQL 5.7 및 MySQL 8.0을 지원합니다. 자세한 내용은 [지원되는 Azure Database for MySQL 엔진 서버](./../concepts-supported-versions.md)를 참조하세요.

Azure Database for MySQL 유연한 서버 내에서 하나 이상의 데이터베이스를 만들 수 있습니다. 서버당 단일 데이터베이스를 만들어 모든 리소스를 활용하도록 하거나 여러 데이터베이스를 만들어 리소스를 공유하도록 할 수 있습니다. 가격은 컴퓨팅 계층, vCore, 스토리지(GB)의 구성에 따라 서버별로 구성됩니다. 자세한 내용은 [컴퓨팅 및 스토리지](./concepts-compute-storage.md)를 참조하세요.

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL 유연한 서버 중지/시작

Azure Database for MySQL 유연한 서버는 사용하지 않을 때 서버를 **중지** 하고 활동을 재개할 때 서버를 **시작** 하는 기능을 제공합니다. 이 기능은 기본적으로 데이터베이스 서버의 비용을 절감하고, 사용 중일 때만 리소스에 대한 비용을 지불하기 위해 수행됩니다. 이 점은 개발-테스트 워크로드의 경우 그리고 하루 중 일부 시간만 서버를 사용하는 경우에 더 중요해집니다. 서버를 중지하면 모든 활성 연결이 삭제됩니다. 나중에 서버를 다시 온라인으로 전환하려면 [Azure Portal](how-to-stop-start-server-portal.md) 또는 CLI를 사용할 수 있습니다.

서버가 **중지됨** 상태이면 서버 컴퓨팅에 요금이 청구되지 않습니다. 그러나 서버가 다시 시작될 때 데이터 파일을 사용할 수 있도록 서버의 스토리지가 그대로 남아 있기 때문에 스토리지 요금이 계속 청구됩니다.

> [!IMPORTANT]
> 서버를 **중지하면** 다음 30일 동안 해당 상태로 유지됩니다. 이 시간 동안 수동으로 **시작하지** 않으면 30일이 지나면 서버가 자동으로 시작됩니다. 서버를 사용하지 않는 경우 다시 **중지** 하도록 선택할 수 있습니다.

서버가 중지된 동안에는 서버에서 관리 작업을 수행할 수 없습니다. 서버에서 구성 설정을 변경하려면 [서버를 시작](how-to-stop-start-server-portal.md)해야 합니다. [중지/시작 제한 사항](./concepts-limitations.md#stopstart-operation)을 참조하세요.

## <a name="how-do-i-manage-a-server"></a>서버는 어떻게 관리해야 하나요?

[Azure Portal](./quickstart-create-server-portal.md) 또는 [Azure CLI](./quickstart-create-server-cli.md)를 사용하여 Azure Database for MySQL 유연한 서버 만들기, 삭제, 서버 매개 변수 구성(my.cnf), 크기 조정, 네트워킹, 보안, 고가용성, 백업 및 복원, 모니터링을 관리할 수 ​​있습니다. 또한 서버에서 SUPER 사용자 권한이 지원되지 않기 때문에 필요한 특정 데이터베이스 관리 작업을 수행하기 위해 Azure Database for MySQL에서 다음 저장 프로시저를 사용할 수 있습니다.

|**저장 프로시저 이름**|**입력 매개 변수**|**출력 매개 변수**|**사용 정보**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|해당 없음|[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) 명령과 동일합니다. 연결이 실행 중인 명령문을 종료한 후 제공된 processlist_id와 관련된 연결을 종료합니다.|
|*mysql.az_kill_query*|processlist_id|해당 없음|[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) 명령과 동일합니다. 연결이 현재 실행 중인 명령문을 종료합니다. 연결 자체를 활성 상태로 유지합니다.|
|*mysql.az_load_timezone*|해당 없음|해당 없음|[표준 시간대 표](../howto-server-parameters.md#working-with-the-time-zone-parameter)를 로드하여 `time_zone` 매개 변수를 명명된 값으로 설정할 수 있습니다(예: “미국/태평양”).|


## <a name="next-steps"></a>다음 단계

-   [서버 만들기](./quickstart-create-server-portal.md)에 대해 알아보기
-   [모니터링 및 경고](./how-to-alert-on-metric.md)에 대해 알아보기

