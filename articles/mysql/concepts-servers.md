---
title: 서버 개념 - Azure Database for MySQL
description: 이 항목에서는 MySQL용 Azure 데이터베이스 서버를 사용할 때의 고려 사항 및 지침을 제공합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7a359c71ab3882c810b8594f4e4e6d73f8124e70
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642200"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스의 서버 개념

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

이 문서에서는 Azure Database for MySQL 서버를 사용할 때의 고려 사항 및 지침을 제공합니다.

## <a name="what-is-an-azure-database-for-mysql-server"></a>MySQL용 Azure 데이터베이스 서버란?

MySQL용 Azure 데이터베이스 서버는 여러 데이터베이스에 대한 중앙 관리 지점입니다. 온-프레미스 환경에서도 익숙할 수 있는 동일한 MySQL 서버 구문입니다. 특히, MySQL용 Azure Database 서비스는 관리되며, 성능 보장을 제공하고, 서버 수준에서 액세스 권한 및 기능을 노출합니다.

MySQL용 Azure 데이터베이스 서버:

- Azure 구독 내에서 만들어집니다.
- 데이터베이스에 대한 부모 리소스입니다.
- 데이터베이스에 대한 네임스페이스를 제공합니다.
- 강력한 수명 의미 체계를 가진 컨테이너로서 서버를 삭제하고 포함된 데이터베이스를 삭제합니다.
- 하위 지역에 리소스를 배치합니다.
- 서버 및 데이터베이스 액세스에 대한 연결 엔드포인트를 제공합니다.
- 로그인, 방화벽, 사용자, 역할 구성 등 해당 데이터베이스에 적용되는 관리 정책에 대한 범위를 제공합니다.
- 여러 버전으로 제공됩니다. 자세한 내용은 [지원되는 MySQL용 Azure 데이터베이스 버전](./concepts-supported-versions.md)을 참조하세요.

Azure Database for MySQL 서버 내에서 하나 이상의 데이터베이스를 만들 수 있습니다. 서버당 단일 데이터베이스를 만들어 모든 리소스를 활용하도록 하거나 여러 데이터베이스를 만들어 리소스를 공유하도록 할 수 있습니다. 가격은 가격 책정 계층, vCore, 스토리지(GB)의 구성에 따라 서버별로 구성됩니다. 자세한 내용은 [가격 책정 계층](./concepts-pricing-tiers.md)을 참조하세요.

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>MySQL용 Azure 데이터베이스 서버에 연결하고 인증을 받으려면 어떻게 해야 하나요?

다음과 같은 요소가 데이터베이스에 안전하게 액세스할 수 있도록 도와줍니다.

| 보안 개념 | Description     |
| :-- | :-- |
| **인증 및 권한 부여** | MySQL용 Azure 데이터베이스 서버는 네이티브 MySQL 인증을 지원합니다. 서버의 관리자 로그인을 사용하여 서버에 연결하고 인증을 받을 수 있습니다. |
| **프로토콜** | 이 서비스는 MySQL에서 사용되는 메시지 기반 프로토콜을 지원합니다. |
| **TCP/IP** | 이 프로토콜은 TCP/IP 및 Unix 도메인 소켓을 통해 지원됩니다. |
| **방화벽** | 데이터를 보호하기 위해, 방화벽 규칙은 사용자가 권한 있는 컴퓨터를 지정하기 전에는 데이터베이스 서버에 대한 모든 액세스를 차단합니다. [MySQL용 Azure 데이터베이스 서버 방화벽 규칙](./concepts-firewall-rules.md)을 참조하세요. |
| **SSL** | 이 서비스는 애플리케이션 및 데이터베이스 서버 간의 SSL 연결 적용을 지원합니다.  [MySQL용 Azure 데이터베이스에 안전하게 연결하기 위한 사용자 애플리케이션의 SSL 연결 구성](./howto-configure-ssl.md)을 참조하세요. |

## <a name="stopstart-an-azure-database-for-mysql"></a>Azure Database for MySQL 중지/시작

Azure Database for MySQL은 사용하지 않을 때 서버를 **중지** 하고 작업을 재개할 때 서버를 **시작** 하는 기능을 제공합니다. 기본적으로 데이터베이스 서버의 비용을 절감하고, 사용 중일 때만 리소스에 대한 비용을 지불하기 위해 수행됩니다. 이 점은 개발-테스트 워크로드의 경우 그리고 하루 중 일부 시간만 서버를 사용하는 경우에 더 중요해집니다. 서버를 중지하면 모든 활성 연결이 삭제됩니다. 나중에 서버를 다시 온라인 상태로 전환하려는 경우 [Azure Portal](how-to-stop-start-server.md) 또는 [CLI](how-to-stop-start-server.md)를 사용하면 됩니다.

서버가 **중지됨** 상태이면 서버 컴퓨팅에 요금이 청구되지 않습니다. 그러나 서버가 다시 시작될 때 데이터 파일을 사용할 수 있도록 서버의 스토리지가 그대로 남아 있기 때문에 스토리지 요금이 계속 청구됩니다.

> [!IMPORTANT]
> 서버를 **중지** 하면 다음 7일 동안 계속 해당 상태가 유지됩니다. 이 시간 동안 수동으로 **시작** 하지 않으면 서버는 7일이 끝나는 시점에 자동으로 시작됩니다. 서버를 사용하지 않는 경우 다시 **중지** 하도록 선택할 수 있습니다.

서버가 중지된 동안에는 서버에서 관리 작업을 수행할 수 없습니다. 서버에서 구성 설정을 변경하려면 [서버를 시작](how-to-stop-start-server.md)해야 합니다.

### <a name="limitations-of-stopstart-operation"></a>중지/시작 작업의 제한 사항
- 읽기 복제본 구성(원본과 복제본 모두)에서는 지원되지 않습니다.

## <a name="how-do-i-manage-a-server"></a>서버는 어떻게 관리해야 하나요?

Azure Portal 또는 Azure CLI를 사용하여 Azure Database for MySQL 서버 만들기, 삭제, 서버 매개 변수 구성(my.cnf), 스케일링, 네트워킹, 보안, 고가용성, 백업 및 복원, 모니터링을 관리할 수 ​​있습니다. 또한 서버에서 SUPER 사용자 권한이 지원되지 않기 때문에 필요한 특정 데이터베이스 관리 작업을 수행하기 위해 Azure Database for MySQL에서 다음 저장 프로시저를 사용할 수 있습니다.

|**저장 프로시저 이름**|**입력 매개 변수**|**출력 매개 변수**|**사용 정보**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|해당 없음|[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) 명령과 동일합니다. 연결이 실행 중인 명령문을 종료한 후 제공된 processlist_id와 관련된 연결을 종료합니다.|
|*mysql.az_kill_query*|processlist_id|해당 없음|[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) 명령과 동일합니다. 연결이 현재 실행 중인 명령문을 종료합니다. 연결 자체를 활성 상태로 유지합니다.|
|*mysql.az_load_timezone*|해당 없음|해당 없음|[표준 시간대 표](howto-server-parameters.md#working-with-the-time-zone-parameter)를 로드하여 `time_zone` 매개 변수를 명명된 값으로 설정할 수 있습니다(예: “미국/태평양”).|

## <a name="next-steps"></a>다음 단계

- 서비스 개요를 보려면 [MySQL용 Azure 데이터베이스 개요](./overview.md)를 참조하세요.
- **가격 책정 계층** 에 따른 특정 리소스 할당량 및 제한 사항에 관한 자세한 내용은 [가격 책정 계층](./concepts-pricing-tiers.md)을 참조하세요.
- 서비스 연결에 대한 자세한 내용은 [MySQL용 Azure 데이터베이스에 대한 연결 라이브러리](./concepts-connection-libraries.md)를 참조하세요.
