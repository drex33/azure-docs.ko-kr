---
title: Azure Database for PostgreSQL - 유연한 서버의 방화벽 규칙
description: 이 문서에서는 방화벽 규칙을 통해 퍼블릭 네트워킹 배포 옵션을 사용하여 Azure DB for PostgreSQL - 유연한 서버에 연결하는 방법을 설명합니다.
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: bdda8163bacd596eafab2a9b2d10d914012a3efb
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866871"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 방화벽 규칙
Azure Database for PostgreSQL - 유연한 서버를 실행하는 경우 두 가지 주요 네트워킹 옵션이 있습니다. 하나는 프라이빗 액세스(가상 네트워크 통합)이고 다른 하나는 공용 액세스(허용된 IP 주소)입니다. 

공용 액세스를 사용하면 Azure Database for PostgreSQL 서버에 공용 엔드포인트를 통해 액세스할 수 있습니다. 기본적으로 방화벽은 서버에 대한 모든 액세스를 차단합니다. 서버에 액세스할 수 있는 IP 호스트를 지정하려면 서버 수준 *방화벽 규칙* 을 만듭니다. 방화벽 규칙으로 허용되는 공용 IP 주소 범위를 지정할 수 있습니다. 방화벽은 각 요청이 시작된 IP 주소의 서버에 대한 액세스를 허용합니다.

Azure Portal 또는 Azure CLI 명령을 사용하여 방화벽 규칙을 만들 수 있습니다. 구독 소유자 또는 구독 기여자여야 합니다.

서버 수준 방화벽 규칙은 동일한 Azure Database for PostgreSQL 서버에 있는 모든 데이터베이스에 적용됩니다. 규칙은 Azure Portal 웹 사이트에 대한 액세스에 영향을 주지 않습니다.

다음 다이어그램은 인터넷 및 Azure의 연결 시도가 PostgreSQL 데이터베이스에 도달하기 전에 방화벽을 통과해야 하는 방법을 보여 줍니다.

:::image type="content" source="../media/concepts-firewall-rules/1-firewall-concept.png" alt-text="방화벽 작동 방식의 개요를 보여 주는 다이어그램.":::

## <a name="connect-from-the-internet"></a>인터넷에서 연결
요청된 원본 IP 주소가 서버 수준 방화벽 규칙의 지정된 범위 안에 있을 경우, 연결이 허용됩니다. 그렇지 않으면 거부됩니다. 

예를 들어 애플리케이션이 PostgreSQL용 JDBC(Java Database Connectivity) 드라이버와 연결하는 경우 방화벽이 연결을 차단하고 있기 때문에 이 오류가 발생할 수 있습니다.

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connect-from-azure"></a>Azure에서 연결
애플리케이션 또는 서비스의 발신 IP 주소를 찾고 이러한 개별 IP 주소 또는 범위에 명시적으로 액세스를 허용하는 것이 좋습니다. 예를 들어 Azure App Service 앱의 발신 IP 주소를 찾거나 가상 머신에 연결된 공용 IP 주소를 사용할 수 있습니다. 

고정된 발신 IP 주소를 Azure 서비스에 사용할 수 없는 경우 모든 Azure 데이터 센터의 모든 IP 주소에서 연결을 사용하도록 설정하는 것이 좋습니다.

1. Azure Portal의 **네트워킹** 창에서 **Azure 내의 모든 Azure 서비스에서 이 서버로의 공용 액세스 허용** 확인란을 선택합니다. 
1. **저장** 을 선택합니다. 

> [!IMPORTANT]
> **Azure 내의 모든 Azure 서비스의 이 서버에 대한 공용 액세스 허용** 옵션은 다른 고객 구독의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 사용할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다. 

:::image type="content" source="./media/concepts-firewall-rules/allow-public-access.png" alt-text="포털에서 Azure 서비스에 대한 액세스를 허용하기 위한 선택 항목을 보여 주는 스크린샷.":::

## <a name="programmatically-manage-firewall-rules"></a>프로그래밍 방식으로 방화벽 규칙 관리
Azure Portal을 사용하는 것 외에도 Azure CLI를 사용하여 프로그래밍 방식으로 방화벽 규칙을 관리할 수 있습니다. 

Azure CLI에서 시작 및 종료 주소가 0.0.0.0인 방화벽 규칙 설정은 포털의 **Azure 내의 모든 Azure 서비스에서 이 서버로의 공용 액세스 허용** 옵션과 동일합니다. 방화벽 규칙이 연결 시도를 거부하는 경우 앱은 Azure Database for PostgreSQL 서버에 도달하지 않습니다.

## <a name="troubleshoot-firewall-problems"></a>방화벽 문제 해결
Azure Database for PostgreSQL 서버에 대한 액세스가 예상대로 작동하지 않는 경우 다음 가능성을 고려하세요.

* **허용 목록에 대한 변경 사항이 아직 적용되지 않음**: Azure Database for PostgreSQL 서버의 방화벽 구성을 변경하는 데 최대 5분이 소요될 수 있습니다.

* **로그인이 인증되지 않았거나 잘못된 암호가 사용됨**: Azure Database for PostgreSQL 서버에 대한 로그인 권한이 없거나 암호가 잘못된 경우 서버에 대한 연결이 거부됩니다. 방화벽 설정은 클라이언트에게 서버에 연결을 시도할 수 있는 기회를 제공합니다. 각 클라이언트는 꼭 필요한 보안 자격 증명을 제공해야 합니다.

  예를 들어 JDBC 클라이언트에 대한 인증이 실패하면 다음 오류가 나타날 수 있습니다.

  > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **방화벽이 동적 IP 주소를 허용하지 않음:** 동적 IP 주소 지정을 통해 인터넷에 연결되어 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 솔루션 중 하나를 시도합니다.

  * Azure Database for PostgreSQL 서버에 액세스하는 클라이언트 컴퓨터에 할당된 IP 주소 범위는 ISP(인터넷 서비스 공급자)에 문의하세요. 그런 다음 IP 주소 범위를 방화벽 규칙으로 추가합니다.

  * 클라이언트 컴퓨터 대신 고정 IP 지정을 가져온 다음 고정 IP 주소를 방화벽 규칙으로 추가합니다.

* **IPv6 형식에 방화벽 규칙을 사용할 수 없음:** 방화벽 규칙은 IPv4 형식이어야 합니다. IPv6 형식으로 방화벽 규칙을 지정하면 유효성 검사 오류가 발생합니다.


## <a name="next-steps"></a>다음 단계

* [Azure Portal을 사용한 Azure Database for PostgreSQL 방화벽 규칙 만들기 및 관리](how-to-manage-firewall-portal.md).
* [Azure CLI를 사용한 Azure Database for PostgreSQL 방화벽 규칙 만들기 및 관리](how-to-manage-firewall-cli.md).
