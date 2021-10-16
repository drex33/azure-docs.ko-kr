---
title: 공용 액세스-Hyperscale (Citus)-Azure Database for PostgreSQL
description: 이 문서에서는 Citus (Azure Database for PostgreSQL-Hyperscale)에 대 한 공용 액세스를 설명 합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: cb695514fe4fd1b3d0ed72dd70aeb8b5d6ca4253
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072191"
---
# <a name="public-access-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale의 공용 액세스 (Citus)

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

이 페이지에서는 공용 액세스 옵션에 대해 설명 합니다. 개인 액세스는 [여기](concepts-hyperscale-private-access.md)를 참조 하세요.

## <a name="firewall-overview"></a>방화벽 개요

Azure Database for PostgreSQL 서버 방화벽은 사용 권한이 있는 컴퓨터를 지정할 때까지 하이퍼스케일(Citus) 코디네이터 노드에 대한 모든 액세스를 차단합니다. 방화벽은 각 요청이 시작된 IP 주소의 서버에 대한 액세스를 허용합니다.
방화벽을 구성하려면 허용 가능한 IP 주소 범위를 지정하는 방화벽 규칙을 생성해야 합니다. 서버 수준의 방화벽 규칙을 만들 수 있습니다.

**방화벽 규칙:** 이 규칙을 사용하면 클라이언트가 하이퍼스케일(Citus) 코디네이터 노드, 즉 동일한 논리 서버 내의 모든 데이터베이스에 액세스할 수 있습니다. Azure Portal을 사용하여 서버 수준 방화벽 규칙을 구성할 수 있습니다. 서버 수준 방화벽 규칙을 만들려면 구독 소유자 또는 구독 참가자여야 합니다.

코디네이터 노드에 대한 모든 데이터베이스 액세스는 기본적으로 방화벽에서 차단됩니다. 다른 컴퓨터에서 서버를 사용하려면 해당 서버에 대한 액세스를 허용하는 하나 이상의 서버 수준 방화벽 규칙을 지정해야 합니다. 방화벽 규칙을 사용하여 허용할 인터넷에서의 IP 주소 범위를 지정합니다. Azure Portal 웹 사이트 자체에 대한 액세스는 이 방화벽 규칙의 영향을 받지 않습니다.
인터넷과 Azure로부터의 연결 시도는 다음 다이어그램과 같이 PostgreSQL Database에 연결하기 전에 먼저 방화벽을 통과해야 합니다.

:::image type="content" source="media/concepts-hyperscale-firewall-rules/1-firewall-concept.png" alt-text="방화벽 작동 방식을 보여 주는 예제 흐름":::

## <a name="connecting-from-the-internet-and-from-azure"></a>인터넷 및 Azure에서 연결

하이퍼스케일(Citus) 서버 그룹 방화벽은 그룹의 코디네이터 노드에 연결할 수 있는 사용자를 제어합니다. 이 방화벽은 구성 가능한 규칙 목록을 참조하여 액세스 권한을 결정합니다. 각 규칙은 허용되는 IP 주소 또는 주소 범위입니다.

방화벽에서 연결을 차단하는 경우 애플리케이션 오류가 발생할 수 있습니다. 예를 들어 PostgreSQL JDBC 드라이버를 사용하면 다음과 같은 오류가 발생합니다.

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host “123.45.67.890”, user “citus”, database “citus”, SSL

규칙 정의 방법을 알아보려면 [방화벽 규칙 만들기 및 관리](howto-hyperscale-manage-firewall-using-portal.md)를 참조하세요.

## <a name="troubleshooting-the-database-server-firewall"></a>데이터베이스 서버 방화벽 문제 해결
Azure Database for PostgreSQL - 하이퍼스케일(Citus) 서비스Citus(PostgreSQL-Hyperscale) 서비스에 대한 액세스가 예상대로 동작하지 않는 경우 다음 사항을 고려합니다.

* **허용 목록의 변경 내용이 아직 적용되지 않음:** 하이퍼스케일(Citus) 방화벽 구성의 변경 내용이 적용되려면 최대 5분 정도 걸릴 수 있습니다.

* **사용자에게 권한이 없거나 잘못된 암호가 사용됨:** 사용자에게 서버에 대한 사용 권한이 없거나 잘못된 암호가 사용되어 서버 연결이 거부되었습니다. 방화벽 설정을 생성하면 클라이언트에게 서버 연결을 시도할 수 있는 기회만 제공되며 각 클라이언트는 필요한 보안 자격 증명을 제공해야 합니다.

예를 들어 JDBC 클라이언트를 사용하는 경우 다음 오류가 나타날 수 있습니다.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **동적 IP 주소:** 동적 IP 주소를 통해 인터넷에 연결되어 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도할 수 있습니다.

* 하이퍼스케일(Citus) 코디네이터 노드에 액세스하는 클라이언트 컴퓨터에 할당된 IP 주소 범위를 ISP(인터넷 서비스 공급자)에 문의한 다음, 해당 IP 주소 범위를 방화벽 규칙으로 추가합니다.

* 클라이언트 컴퓨터 대신 고정 IP 지정을 가져온 다음 고정 IP 주소를 방화벽 규칙으로 추가합니다.

## <a name="next-steps"></a>다음 단계
서버 수준 및 데이터베이스 수준 방화벽 규칙 만들기에 대한 문서를 보려면 다음을 참조하세요.
* [Azure Portal을 사용한 PostgreSQL용 Azure 데이터베이스 방화벽 규칙 만들기 및 관리](howto-hyperscale-manage-firewall-using-portal.md)
