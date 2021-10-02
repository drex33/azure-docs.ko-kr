---
title: 서버 매개 변수 - Azure Database for PostgreSQL - 유연한 서버
description: Azure Database for PostgreSQL - 유연한 서버의 서버 매개 변수에 대해 설명합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 526a06c1ffd110fa02fd3d412ab62882bd74f9fa
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387376"
---
# <a name="server-parameters-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 서버 매개 변수

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

Azure Database for PostgreSQL은 각 서버에 대해 구성 가능한 매개 변수의 하위 집합을 제공합니다. Postgres 매개 변수에 대한 자세한 내용은 [PostgreSQL 문서](https://www.postgresql.org/docs/13/config-setting.html)를 참조하세요.

## <a name="an-overview-of-postgresql-parameters"></a>PostgreSQL 매개 변수 개요 

Azure Database for PostgreSQL 서버는 생성 시 각 매개 변수에 대한 최적의 기본값으로 미리 구성됩니다. 정적 매개 변수는 서버를 다시 시작해야 하며 수퍼유저 액세스가 필요한 매개 변수는 사용자가 구성할 수 없습니다. 

보거나 수정할 수 있는 매개 변수를 검토하려면 Azure Portal 및 서버 매개 변수 페이지로 이동하는 것이 좋습니다. `ALTER DATABASE` 또는 `ALTER ROLE` 명령을 사용하여 사용자 또는 데이터베이스별로 매개 변수를 구성할 수도 있습니다.

>[!NOTE]
> Azure Database for PostgreSQL은 관리형 데이터베이스 서비스이므로 사용자에게 `postgresql.conf`와 같은 구성 파일을 보거나 수정할 수 있는 호스트 또는 OS 액세스 권한이 제공되지 않습니다. 파일 내용은 서버 매개 변수 페이지의 매개 변수 변경 사항에 따라 자동으로 업데이트됩니다.

:::image type="content" source="./media/concepts-server-parameters/server-parameters.png" alt-text="서버 매개 변수 - 포털":::

다음은 일부 매개 변수 목록입니다.

| 매개 변수 이름             | 설명 |
|----------------------|--------|
| **max_connections** | 5,000개 연결로 설정할 수 있는 Postgres 유연한 서버에서 max_connections를 튜닝할 수 있습니다. 자세한 내용은 [제한 설명서](concepts-limits.md)를 참조하세요. | 
| **shared_buffers**    | 'shared_buffers' 설정은 선택한 SKU에 따라 변경됩니다(SKU에서 사용 가능한 메모리 결정). 범용 서버에는 2개의 vCore에 대해 2GB의 shared_buffers가 있습니다. 메모리 최적화 서버에는 2개의 vCore에 대해 4GB의 shared_buffers가 있습니다. shared_buffers 설정은 vCore가 계층에서 증가함에 따라 선형으로(대략) 조정됩니다. | 
| **shared_preload_libraries** | 이 매개 변수는 사전 정의된 지원 확장 세트로 구성에 사용할 수 있습니다. 항상 `azure` 확장(유지 관리 작업에 사용)과 `pg_stat_statements` 확장(pg_stat_statements.track 매개 변수를 사용하여 확장이 활성 상태인지 여부를 제어할 수 있음)을 로드합니다. |
| **connection_throttling** | 잘못된 암호 로그인 실패가 너무 많을 경우 IP당 임시 연결 제한을 사용하도록 설정하거나 사용하지 않도록 설정할 수 있습니다. |
| **require_secure_transport** | 애플리케이션이 서버에 대한 SSL 연결을 지원하지 않는 경우 필요에 따라 이 매개 변수 값을 설정하여 클라이언트에서 보안 전송을 사용하지 않도록 설정할 수 `OFF` 있습니다. |
 
## <a name="next-steps"></a>다음 단계

지원되는 PostgreSQL 확장에 대한 자세한 내용은 [확장 문서](concepts-extensions.md)를 참조하세요.
