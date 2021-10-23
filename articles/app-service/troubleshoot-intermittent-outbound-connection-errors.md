---
title: Azure App Service의 간헐적인 아웃 바운드 연결 오류 문제 해결
description: Azure App Service에서 간헐적 연결 오류 및 관련된 성능 문제 해결
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 1b85dd7bdab820cd66f1ac4f2421191f354a073c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130220380"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Azure App Service의 간헐적인 아웃 바운드 연결 오류 문제 해결

이 문서는 [Azure App Service](./overview.md)에서 간헐적 연결 오류 및 관련된 성능 문제를 해결하는 데 도움이 됩니다. 이 항목에서는 SNAT(원본 주소 네트워크 변환) 포트 소모에 대한 자세한 내용 및 문제 해결 방법을 제공합니다. 이 문서의 어디서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)의 Azure 전문가에게 문의하세요. 또는 Azure 기술 지원 인시던트를 제출하세요. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 **지원 받기** 를 선택합니다.

## <a name="symptoms"></a>증상

Azure App Service에서 호스팅되는 애플리케이션 및 함수에서 다음과 같은 증상 중 하나 이상이 나타날 수 있습니다.

* 서비스 계획의 모든 인스턴스 또는 일부 인스턴스에 대한 응답 시간이 느립니다.
* 간헐적인 5xx 또는 **잘못된 게이트웨이** 오류
* 시간 제한 오류 메시지
* 외부 엔드포인트(예: SQLDB, Service Fabric, 기타 앱 서비스 등)에 연결할 수 없습니다.

## <a name="cause"></a>원인

간헐적 연결 문제는 주로 새로운 아웃바운드 연결을 만드는 동안 한도에 도달하기 때문에 일어납니다. 다음과 같은 제한이 있습니다.

* TCP 연결: 설정할 수 있는 아웃바운드 연결 수에 제한이 있습니다. 아웃바운드 연결에 대한 제한은 사용된 작업자의 크기와 관련됩니다.
* SNAT 포트: [Azure의 아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md)은 SNAT 포트 제한과 이러한 제한 사항이 아웃바운드 연결에 미치는 영향을 설명합니다. Azure는 SNAT(원본 네트워크 주소 변환) 및 부하 분산 장치(고객에게 노출되지 않음)를 사용하여 공용 IP 주소와 통신합니다. Azure App Service의 각 인스턴스에는 처음에 할당된 NAT 포트 **128개** 가 지정됩니다. SNAT 포트 제한은 동일한 주소 및 포트 조합에 대한 연결을 여는 데 영향을 미칩니다. 앱이 여러 가지 주소 및 포트 조합에 대한 연결을 만드는 경우 SNAT 포트를 사용하지 않습니다. SNAT 포트는 동일한 주소 및 포트 조합에 대한 호출이 반복될 때 사용됩니다. 포트를 해제한 후에는 필요에 따라 포트를 재사용할 수 있습니다. Azure 네트워크 부하 분산 장치는 4분 동안 기다린 후에만 닫힌 연결에서 SNAT 포트를 회수합니다.

애플리케이션 또는 함수가 새 연결을 신속하게 열면 미리 할당된 128개 포트를 빠르게 소모할 수 있습니다. 그러면 추가 SNAT 포트를 동적으로 할당하거나 회수된 SNAT 포트를 다시 사용하여 새 SNAT 포트를 사용할 수 있게 될 때까지 차단됩니다. 앱이 SNAT 포트를 모두 실행하는 경우 일시적인 아웃바운드 연결 문제가 발생합니다. 

## <a name="avoiding-the-problem"></a>문제 방지

SNAT 포트 제한을 방지할 수 있는 몇 가지 솔루션이 있습니다. 다음과 같은 변경 내용이 해당됩니다.

* 연결 풀: 연결을 풀링하여 동일한 주소 및 포트 호출에 대한 새 네트워크 연결을 열지 않습니다.
* 서비스 엔드포인트: 서비스 엔드포인트로 보호되는 서비스에 대한 SNAT 포트 제한이 없습니다.
* 프라이빗 엔드포인트: 프라이빗 엔드포인트로 보호되는 서비스에 대한 SNAT 포트 제한이 없습니다.
* NAT 게이트웨이: NAT 게이트웨이를 사용하는 경우 트래픽을 보내는 리소스에서 사용 가능한 64k 아웃바운드 SNAT 포트를 사용할 수 있습니다.

SNAT 포트 문제를 방지하는 것은 동일한 호스트 및 포트에 대한 새 연결을 반복적으로 만드는 것을 방지하는 것입니다. 연결 풀은 이 문제를 해결하는 보다 분명한 방법 중 하나입니다.

대상이 서비스 엔드포인트를 지원하는 Azure 서비스인 경우 [지역 VNet 통합](./overview-vnet-integration.md) 및 서비스 엔드포인트나 프라이빗 엔드포인트를 사용하여 SNAT 포트 소모 문제를 방지할 수 있습니다. 지역 VNet 통합을 사용하고 통합 서브넷에 서비스 엔드포인트를 추가하는 경우 해당 서비스에 대한 아웃바운드 트래픽에는 아웃바운드 SNAT 포트 제한이 적용되지 않습니다. 마찬가지로, 지역 VNet 통합 및 프라이빗 엔드포인트를 사용하는 경우에는 해당 대상에 대한 아웃바운드 SNAT 포트 문제가 발생하지 않습니다. 

대상이 Azure 외부의 외부 엔드포인트인 경우 [NAT 게이트웨이를 사용](./networking/nat-gateway-integration.md)하면 64k 아웃바운드 SNAT 포트가 제공됩니다. 또한 누구와도 공유하지 않는 전용 아웃바운드 주소도 제공됩니다. 

가능하면 연결 풀을 사용하고 전체 상황을 방지하기 위해 코드를 개선합니다. 이 상황을 완화하기 위해 항상 코드를 빠르게 변경할 수는 없습니다. 제시간에 코드를 변경할 수 없는 경우 다른 솔루션을 활용합니다. 문제에 대한 가장 좋은 해결책은 모든 솔루션을 최대한 활용하는 것입니다. 서비스 엔드포인트와 프라이빗 엔드포인트를 Azure 서비스에 사용하고 나머지에는 NAT 게이트웨이를 사용합니다. 

SNAT 포트 소모를 완화하는 일반적인 전략은 **Azure의 아웃바운드 연결** 설명서의 [문제 해결 섹션](../load-balancer/load-balancer-outbound-connections.md)에 설명되어 있습니다. 이러한 전략 중에서 다음은 Azure App Service에서 호스팅되는 앱 및 함수에 적용할 수 있습니다.

### <a name="modify-the-application-to-use-connection-pooling"></a>연결 풀링을 사용하도록 애플리케이션 수정

* 풀링 HTTP 연결의 경우 [HttpClientFactory를 사용하여 풀 http 연결](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory)을 검토합니다.
* SQL Server 연결 풀링 방법에 대한 자세한 내용은 [SQL Server 연결 풀링(ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)을 검토합니다.
* 엔터티 프레임워크 애플리케이션을 사용하여 풀링을 구현하려면 [DbContext 풀링](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)을 검토합니다.

다음은 서로 다른 솔루션 스택에 의해 연결 풀링을 구현하기 위한 링크 컬렉션입니다.

#### <a name="node"></a>노드

기본적으로 NodeJS에 대한 연결은 활성 상태로 유지되지 않습니다. 다음은 연결 풀링을 위한 인기 있는 데이터베이스 및 패키지입니다(구현 방법에 대한 예제 포함).

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP 연결 유지

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v13.9.0 설명서](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

다음은 JDBC 연결 풀링에 널리 사용되는 라이브러리입니다(구현 방법에 대한 예 포함: JDBC 연결 풀링).

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP 연결 풀링

* [Apache 연결 관리](https://hc.apache.org/httpcomponents-client-5.0.x/)
* [클래스 PoolingHttpClientConnectionManager](https://hc.apache.org/httpcomponents-client-5.0.x/)

#### <a name="php"></a>PHP

PHP는 연결 풀링을 지원하지 않지만 백 엔드 서버에 대한 영구 데이터베이스 연결을 사용해 볼 수 있습니다.
 
* MySQL Server

   * 최신 버전에 대한 [MySQLi 연결](https://www.php.net/manual/mysqli.quickstart.connections.php)
   * 이전 버전의 PHP에 대한 [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php)

* 다른 데이터 원본

   * [PHP 연결 관리](https://www.php.net/manual/en/pdo.connections.php)

### <a name="modify-the-application-to-reuse-connections"></a>연결을 다시 사용하도록 애플리케이션 수정

*  [Azure Functions에서 연결 관리](../azure-functions/manage-connections.md)에서 Azure 함수에서 연결 관리에 대한 추가 포인터 및 예를 검토합니다.

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>덜 적극적인 재시도 논리를 사용하도록 애플리케이션 수정

* [다시 시도 패턴](/azure/architecture/patterns/retry)을 검토하여 추가 지침과 예를 확인합니다.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>keepalive를 사용하여 아웃바운드 유휴 시간 제한 다시 설정

* Node.js 앱에 대한 keepalive를 구현하는 경우 [내 노드 애플리케이션에서 과도한 아웃바운드 호출](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls)을 검토합니다.

### <a name="additional-guidance-specific-to-app-service"></a>App Service 관련 추가 지침:

* [부하 테스트](/azure/devops/test/load-test/app-service-web-app-performance-test)는 안정적인 공급 속도로 실제 데이터를 시뮬레이션합니다. 실제 스트레스 상태에서 앱 및 함수를 테스트하면 SNAT 포트 소모 문제를 미리 파악하고 해결할 수 있습니다.
* 백 엔드 서비스에서 응답을 신속하게 반환할 수 있는지 확인합니다. Azure SQL Database의 성능 문제 해결에 대한 자세한 내용은 [Intelligent Insights를 사용한 Azure SQL Database 성능 문제 해결](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow)을 참조하세요.
* App Service 계획을 더 많은 인스턴스로 확장합니다. 크기 조정에 대한 자세한 내용은 [Azure App Service에서 앱 크기 조정](./manage-scale-up.md)을 참조하세요. App Service 계획의 각 작업자 인스턴스에 여러 SNAT 포트가 할당됩니다. 더 많은 인스턴스를 사용하여 사용량을 분산하는 경우 인스턴스당 SNAT 포트 사용량이 고유한 원격 엔드포인트당 아웃바운드 연결의 권장 한도인 100보다 낮을 수 있습니다.
* 단일 아웃바운드 IP 주소를 할당하는 [ASE(App Service Environment)](./environment/using-an-ase.md)로 이동하는 것이 좋습니다. 이 경우 연결 및 SNAT 포트의 한도가 훨씬 더 높습니다. ASE에서 인스턴스당 SNAT 포트 수는 [Azure 부하 분산 장치 미리 할당 테이블](../load-balancer/load-balancer-outbound-connections.md#snatporttable)을 기반으로 합니다. 예를 들어, 1-50개의 작업자 인스턴스가 있는 ASE에는 인스턴스당 1024개의 포트가 미리 할당되어 있으며 51-100개의 작업자 인스턴스가 있는 ASE에는 인스턴스당 512개의 포트가 미리 할당되어 있습니다.

작업자의 크기에 따라 제한이 설정되므로 아웃바운드 TCP 제한 방지는 쉽게 해결할 수 있습니다. [샌드박스 VM 간 숫자 제한 - TCP 연결](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)에서 제한을 확인할 수 있습니다.

|제한 이름|Description|작음(A1)|보통(A2)|큼(A3)|격리 계층(ASE)|
|---|---|---|---|---|---|
|Connections|전체 VM의 연결 수|1920|3968|8064|16,000|

아웃바운드 TCP 제한을 방지하기 위해 작업자의 크기를 늘리거나 가로로 스케일 아웃할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

두 가지 유형의 아웃바운드 연결 제한과 앱에서 수행하는 작업을 알면 문제를 보다 쉽게 해결할 수 있습니다. 앱이 동일한 스토리지 계정에 대해 호출을 여러 번 수행하는 경우 SNAT 한도를 의심할 수 있습니다. 앱이 인터넷을 통해 엔드포인트를 매우 많이 호출하는 경우 VM 한도에 도달한 것으로 의심됩니다.

원인을 신속하게 확인할 정도로 애플리케이션 동작에 대해 잘 알지 못하는 경우 App Service에서 이러한 결정에 도움이 되는 몇 가지 도구와 기술을 사용할 수 있습니다.

### <a name="find-snat-port-allocation-information"></a>SNAT 포트 할당 정보 찾기

[App Service 진단](./overview-diagnostics.md)을 사용하여 SNAT 포트 할당 정보를 찾고 App Service 사이트의 SNAT 포트 할당 메트릭을 확인할 수 있습니다. SNAT 포트 할당 정보를 찾으려면 다음 단계를 수행합니다.

1. App Service 진단에 액세스하려면 [Azure Portal](https://portal.azure.com/)의 App Service 웹앱 또는 App Service Environment로 이동합니다. 왼쪽 탐색 메뉴에서 **문제 진단 및 해결** 을 선택합니다.
2. 가용성 및 성능 범주를 선택합니다.
3. 범주 아래의 사용 가능한 타일 목록에서 SNAT 포트 소모 타일을 선택합니다. 128 미만으로 유지하는 것이 좋습니다.
필요한 경우 지원 티켓을 열 수 있습니다. 그러면 지원 엔지니어가 백 엔드에서 메트릭을 가져오게 됩니다.

SNAT 포트 사용은 메트릭으로 사용할 수 없으므로 SNAT 포트 사용량이나 SNAT 포트 할당 메트릭에 따라 자동 크기 조정을 구성할 수 없습니다.

### <a name="tcp-connections-and-snat-ports"></a>TCP 연결 및 SNAT 포트

TCP 연결 및 SNAT 포트는 직접적인 관련성이 없습니다. TCP 연결 사용 탐지기는 모든 App Service 사이트의 문제 진단 및 해결 블레이드에 포함되어 있습니다. "TCP 연결" 구를 검색하여 찾습니다.

* SNAT 포트는 외부 네트워크 흐름에 대해서만 사용되지만 총 TCP 연결은 로컬 루프백 연결을 포함합니다.
* 프로토콜, IP 주소 또는 포트에서 흐름이 서로 다른 경우 SNAT 포트는 다른 흐름에서 공유할 수 있습니다. TCP 연결 메트릭은 모든 TCP 연결을 계산합니다.
* TCP 연결 제한은 작업자 인스턴스 수준에서 발생합니다. Azure 네트워크 아웃바운드 부하 분산에서는 SNAT 포트 제한에 TCP 연결 메트릭을 사용하지 않습니다.
* TCP 연결 제한은 [샌드박스 VM 간 숫자 제한 - TCP 연결](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)에 설명되어 있습니다.
* Azure App Service 원본 포트에서 새 아웃 바운드 TCP 세션을 실행 하면 기존 TCP 세션이 실패 합니다. 단일 IP를 사용 하거나 백 엔드 풀 멤버를 다시 구성 하 여 충돌을 방지할 수 있습니다.

|제한 이름|Description|작음(A1)|보통(A2)|큼(A3)|격리 계층(ASE)|
|---|---|---|---|---|---|
|Connections|전체 VM의 연결 수|1920|3968|8064|16,000|

### <a name="webjobs-and-database-connections"></a>WebJob 및 데이터베이스 연결
 
SNAT 포트가 소진되고 WebJob이 SQL Database에 연결할 수 없는 경우 각 개별 웹 애플리케이션 프로세스에서 열린 연결 수를 보여 주는 메트릭이 없습니다. 문제가 있는 WebJob을 찾으려면 몇 가지 WebJob을 다른 App Service 계획으로 이동하여 상황을 개선하거나 계획 중 하나에 문제가 남아 있는지 확인합니다. 문제가 있는 WebJob을 찾을 때까지 프로세스를 반복합니다.

## <a name="additional-information"></a>추가 정보

* [App Service를 사용한 SNAT](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Azure App Service에서 느린 앱 성능 문제 해결](./troubleshoot-performance-degradation.md)