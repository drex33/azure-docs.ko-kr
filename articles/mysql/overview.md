---
title: 개요 - Azure Database for MySQL
description: MySQL 커뮤니티 버전을 기반으로 하는 Microsoft 클라우드의 관계형 데이터베이스 서비스인 Azure Database for MySQL 서비스에 대해 알아봅니다.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: a2b1eabd332d40d075fd4616d0a79535641dc432
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131429169"
---
# <a name="what-is-azure-database-for-mysql"></a>MySQL용 Azure Database란?

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL은 [MySQL 커뮤니티 버전](https://www.mysql.com/products/community/)(GPLv2 라이선스에서 사용 가능) 데이터베이스 엔진(버전 5.6, 5.7 및 8.0)을 기반으로 하는 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다. MySQL용 Azure Database는 다음과 같은 기능을 제공합니다.

- 기본 제공되는 고가용성
- 최대 35일 동안 자동 백업 및 특정 시점 복원을 사용하여 데이터 보호
- 서비스를 안전하고 최신 상태로 유지하기 위해 기본 하드웨어, 운영 체제 및 데이터베이스 엔진을 자동으로 유지 관리
- 예측 가능한 성능, 종량제 가격 책정 사용
- 몇 초 내에 탄력적 스케일링
- 서버를 중지/시작하는 기능을 사용하여 비용 최적화
- 엔터프라이즈급 보안 및 업계 최고 수준의 규정 준수를 통해 중요한 미사용 데이터 및 사용 중인 데이터를 안전하게 보호
- 모니터링 및 자동화를 통해 대규모 배포의 관리 및 모니터링 간소화
- 업계 최고 수준의 지원 환경

이러한 기능에는 인증이 필요하지 않고 추가 비용 없이 제공됩니다. 그러면 가상 머신과 인프라를 관리하는 데 귀중한 시간과 리소스를 할당하는 대신 빠른 앱 개발에 집중하고 시장 출시 시간을 단축할 수 있습니다. 또한 오픈 소스 도구 및 선택한 플랫폼을 사용하여 애플리케이션을 개발하고 새로운 기술을 배울 필요 없이 비즈니스에서 요구하는 속도 및 효율성을 계속 제공할 수 있습니다.

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="MySQL용 Azure Database 개념 다이어그램":::

## <a name="deployment-models"></a>배포 모델

MySQL 커뮤니티 버전에서 제공하는 Azure Database for MySQL은 두 가지 배포 모드로 사용할 수 있습니다.
- 단일 서버
- 유연한 서버

### <a name="azure-database-for-mysql---single-server"></a>Azure Database for MySQL - 단일 서버

Azure Database for MySQL 단일 서버는 데이터베이스 사용자 지정을 최소화한 완전 관리형 데이터베이스 서비스입니다. 단일 서버 플랫폼은 최소한의 사용자 구성과 제어를 통해 패치, 백업, 고가용성, 보안 등 대부분의 데이터베이스 관리 기능을 처리하도록 설계되었습니다. 이 아키텍처는 단일 가용성 영역에서 99.99%의 가용성을 기본 제공하도록 고가용성에 최적화되었습니다. MySQL 커뮤니티 버전 5.6, 5.7 및 8.0을 지원합니다. 이 서비스는 현재 다양한 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/)에서 일반 공급됩니다.

단일 서버 배포 옵션에서 제공하는 세 가지 가격 책정 계층은 기본, 범용 및 메모리 최적화의 세 가지 가격 책정 계층 중 하나에서 만들 수 있습니다. 각 계층에서는 데이터베이스 작업을 지원하는 다양한 리소스 기능을 제공합니다. 한 달에 몇 달러로 작은 데이터베이스에 첫 번째 앱을 빌드하고 솔루션의 요구에 맞게 규모를 조정할 수 있습니다. 동적 확장성을 사용하면 데이터베이스가 빠르게 변화하는 리소스 요구 사항에 투명하게 대응할 수 있습니다. 필요할 경우 필요한 리소스에 대해서만 요금을 지불합니다. 자세한 내용은 [가격 책정 계층](concepts-pricing-tiers.md)을 참조하세요.

단일 서버는 패치 일정 및 사용자 지정 MySQL 구성 설정에 대한 세부적인 제어 없이 자동화된 패치를 처리하도록 설계된 클라우드 네이티브 애플리케이션에 가장 적합합니다.

단일 서버 배포 모드에 대한 자세한 개요는 [단일 서버 개요](single-server-overview.md)를 참조하세요.

### <a name="azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - 유연한 서버

Azure Database for MySQL 유연한 서버는 데이터베이스 관리 기능 및 구성 설정에 대한 보다 세부적인 제어와 유연성을 제공하도록 설계된 완전 관리형 데이터베이스 서비스입니다. 일반적으로 이 서비스는 사용자 요구 사항에 따라 더 많은 유연성과 사용자 지정을 제공합니다. 유연한 서버 아키텍처는 사용자가 단일 가용성 영역 및 여러 가용성 영역에서 고가용성을 선택할 수 있습니다. 유연한 서버는 서버를 중지/시작하는 기능과 버스트 가능 컴퓨팅 계층을 통해 비용을 최적화할 수 있으므로 전체 컴퓨팅 용량이 지속적으로 필요하지 않는 워크로드에 적합합니다. 이 서비스는 커뮤니티 버전 MySQL 5.7 및 8.0을 지원합니다. 이 서비스는 현재 다양한 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/)에서 사용할 수 있습니다.

유연한 서버는 다음에 가장 적합합니다.
- 더 나은 제어 및 사용자 지정이 필요한 애플리케이션 개발
- 영역 중복 고가용성
- 관리되는 유지 관리 기간

유연한 서버 배포 모드에 대한 자세한 개요는 [유연한 서버 개요](flexible-server/overview.md)를 참조하세요.

## <a name="contacts"></a>연락처
Azure Database for MySQL 작업에 대해 궁금한 점이나 제안하고 싶은 의견이 있으면 Azure Database for MySQL 팀([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com))으로 이메일을 보내주세요. 이 이메일 주소는 기술 지원 별칭이 아닙니다.

또한 문의의 다음 사항을 적절히 고려해 주세요.

- Azure 고객 지원팀에 문의하려면 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
- 계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.
- 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0)를 통해 항목을 만드세요.

## <a name="next-steps"></a>다음 단계

Azure Database for MySQL의 두 가지 배포 모드에 대해 자세히 알아보고 요구 사항에 맞는 옵션을 선택합니다.

- [단일 서버](single-server/index.yml)
- [유연한 서버](flexible-server/index.yml)
- [워크로드에 적합한 MySQL 배포 옵션 선택](select-right-deployment-type.md)
