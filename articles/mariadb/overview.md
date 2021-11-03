---
title: 개요 - Azure Database for MariaDB
description: MySQL 커뮤니티 버전을 기반으로 하는 Microsoft 클라우드의 관계형 데이터베이스 서비스인 Azure Database for MariaDB 서비스에 대해 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 3/18/2020
ms.openlocfilehash: 4b1631fd24575c20bdc088749983407b74284bb4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442505"
---
# <a name="what-is-azure-database-for-mariadb"></a>Azure Database for MariaDB란?

Azure Database for MariaDB는 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다. Azure Database for MariaDB는 [MariaDB 커뮤니티 버전](https://mariadb.org/download/)(GPLv2 라이선스에 따라 사용 가능) 데이터베이스 엔진 버전 10.2 및 10.3을 기반으로 합니다.

Azure Database for MariaDB는 다음을 제공합니다.

- 추가 비용 없이 기본 제공되는 고가용성
- 예측 가능한 성능, 종량제 가격 책정 사용
- 필요에 따라 몇 초 이내에 스케일링.
- 미사용 및 동작 중인 중요한 데이터를 안전하게 보호.
- 최대 35일 동안 자동 백업 및 지정 시간 복원
- 엔터프라이즈급 보안 및 규정 준수

이러한 기능에는 인증이 필요합니다. 추가 비용 없이 제공 됩니다. Azure Database for MariaDB를 사용하면 앱을 신속히 개발하고 출시 기간을 단축할 수 있습니다. 소중한 시간과 리소스를 가상 머신 및 인프라 관리에 할애할 필요가 없습니다. 원하는 오픈 소스 도구와 플랫폼을 사용해서도 애플리케이션을 개발할 수 있습니다. 새로운 기술 습득 없이도 비즈니스에 필요한 속도와 효율성을 제공합니다.

성능, 확장성, 관리 효율성 등을 포함한 Azure Database for MariaDB의 핵심 개념과 특징에 대한 정보는 다음 빠른 시작을 참조하세요.

- [Azure Portal을 사용하여 Azure Database for MariaDB 서버 만들기](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Azure CLI를 사용하여 Azure Database for MariaDB 서버 만들기](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>몇 초 이내 성능 및 규모 조정

MariaDB용 서비스는 기본, 범용 및 메모리 최적화와 같은 몇 가지 서비스 계층을 제공합니다. 각 계층은 경량급에서 중량급까지 데이터베이스 워크로드를 지원하기 위해 다양한 성능 및 기능을 제공합니다. 한 달에 몇 달러로 작은 데이터베이스에 첫 번째 앱을 빌드하고 솔루션의 요구에 맞게 규모를 조정할 수 있습니다. 동적 확장성을 사용하면 데이터베이스가 빠르게 변화하는 리소스 요구 사항에 투명하게 대응할 수 있습니다. 필요할 경우 필요한 리소스에 대해서만 요금을 지불합니다. 자세한 내용은 [가격 책정 계층](concepts-pricing-tiers.md)을 참조하세요.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고

기능을 강화하거나 축소하는 시기는 어떻게 결정하나요? vCore 수에 따른 성능 등급과 연계하여 Azure Database for MariaDB 기본 제공 성능 모니터링 및 경고 기능을 사용할 수 있습니다. 이러한 도구를 사용하면 현재 또는 예상되는 성능 요구 사항에 따라 vCore 수를 확장 또는 축소함으로써 발생하는 효과를 신속하게 평가할 수 있습니다. 자세한 내용은 [경고](howto-alert-metric.md)를 참조하세요.

## <a name="keep-your-app-and-business-running"></a>앱 및 비즈니스 운영 유지

Azure에서는 Microsoft에서 관리하는 데이터 센터로 구성된 글로벌 네트워크를 통해 업계 최고 수준을 자랑하는 99.99% 가용성 SLA가 보장됩니다. 네트워크는 앱을 24/7로 실행하는 데 도움이 됩니다. Azure Database for MariaDB의 기본 제공 보안, 내결함성 및 데이터 보호를 활용할 수 있습니다. Azure Database for MariaDB를 사용하면 특정 시점 복원 기능을 통해 최대 35일 전의 상태로 서버를 복구할 수 있습니다.

## <a name="secure-your-data"></a>데이터 보호

Azure 데이터베이스는 Azure Database for MariaDB가 가지고 있는 데이터 보안의 전통을 이어갑니다. Azure Database for MariaDB는 액세스를 제한하고, 미사용 및 이동 중인 데이터를 보호하며, 모니터 작업을 지원하는 기능을 제공합니다. Azure의 플랫폼 보안에 대한 자세한 내용을 보려면 [Azure 보안 센터](https://www.microsoft.com/trustcenter/security)를 방문하세요. Azure Database for MariaDB 보안 기능에 대한 자세한 내용은 [보안 개요](concepts-security.md)를 참조하세요.

## <a name="contacts"></a>연락처

Azure Database for MariaDB 작업과 관련하여 궁금한 점이나 제안하고 싶은 의견이 있으면 [Azure Database for MariaDB 팀](mailto:AskAzureDBforMariaDB@service.microsoft.com)(기술 지원팀 별칭 아님)으로 문의하세요.

다음 연락 방법을 사용할 수도 있습니다.
- Azure 지원에 문의하려면 Azure Portal에서 [지원 요청을 엽니다](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- 계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 엽니다.
- 피드백을 제공하거나 새 기능을 요청하려면 [Azure 피드백 포럼](https://feedback.azure.com/d365community/forum/8f6502f1-ed24-ec11-b6e6-000d3a4f0da0)에서 항목을 만듭니다.

## <a name="next-steps"></a>다음 단계

Azure Database for MariaDB에 대해 개략적으로 알아보았으므로 다음에 대한 준비가 되었습니다.
- 비용 비교 및 계산기는 [가격 책정](https://azure.microsoft.com/pricing/details/mariadb/) 페이지를 참조하세요. 
- [첫 번째 서버를 만들어서](quickstart-create-mariadb-server-database-using-azure-portal.md) 시작합니다.

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
