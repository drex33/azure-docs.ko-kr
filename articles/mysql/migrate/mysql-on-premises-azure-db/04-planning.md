---
title: 'MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 계획'
description: Azure 랜딩 존은 클라우드 마이그레이션 프로젝트의 최종 휴게소로 정의된 대상 환경입니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: e1ef403583082f1fff631392d7a4467fe253a516
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024186"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-planning"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 계획

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>사전 요구 사항

[평가](03-assessment.md)

## <a name="landing-zone"></a>랜딩 존

[Azure 랜딩 존](/azure/cloud-adoption-framework/ready/landing-zone/)은 클라우드 마이그레이션 프로젝트의 최종 휴게소로 정의된 대상 환경입니다. 대부분의 프로젝트에서 랜딩 존은 초기 설정을 위해 ARM 템플릿을 통해 스크립팅되어야 합니다. 마지막으로 워크로드 요구 사항에 맞게 PowerShell 또는 Azure Portal을 사용하여 사용자 지정해야 합니다.

WWI는 샌프란시스코를 기반으로 하므로 Azure 랜딩 존의 모든 리소스는 `US West 2` 지역에서 만들어졌습니다. 마이그레이션을 지원하기 위해 만들어진 리소스는 다음과 같습니다.

- [Azure Database for MySQL](../../quickstart-create-mysql-server-database-using-azure-portal.md)

- [Azure DMS(Database Migration Service)](../../../dms/quickstart-create-data-migration-service-portal.md)

- [Express 경로](../../../expressroute/expressroute-introduction.md)

- [허브 및 스포크 디자인](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)이 있는 [Azure Virtual Network](../../../virtual-network/quick-create-portal.md)와 해당하는 [가상 네트워크 피어링](../../../virtual-network/virtual-network-peering-overview.md)이 설정됩니다.

- [App Service](../../../app-service/overview.md)

- [Application Gateway](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md?tabs=option-1-create-internal-load-balancer-standard)

- App Services 및 MySQL 인스턴스를 위한 [프라이빗 엔드포인트](../../../private-link/private-endpoint-overview.md)

> [!NOTE]
> 이 가이드의 일부로 MySQL 마이그레이션 프로젝트에 대한 잠재적인 Azure 랜딩 존을 배포하기 위해 두 개의 ARM 템플릿(하나는 프라이빗 엔드포인트가 있고 다른 하나는 포함되지 않음)이 제공되었습니다. 프라이빗 엔드포인트 ARM 템플릿은 보다 안전하고 프로덕션과 같은 시나리오를 제공합니다. 요구 사항에 따라 추가 수동 Azure 랜딩 존 구성이 필요할 수 있습니다.

## <a name="networking"></a>네트워킹

빠르고 최적의 방법으로 원본 시스템에서 Azure Database for MySQL로 데이터를 가져오는 것은 마이그레이션 프로젝트에서 고려해야 할 중요한 구성 요소입니다. 소규모의 신뢰할 수 없는 연결은 관리자가 성공적인 결과를 얻을 때까지 마이그레이션을 여러 번 다시 시작해야 할 수 있습니다. 네트워크 문제로 인해 마이그레이션을 다시 시작하면 노력이 낭비될 수 있습니다.

시간을 들여 원본, 도구 및 대상 환경 간의 네트워크 연결을 이해하고 평가합니다. 경우에 따라 인터넷 연결을 업그레이드하거나 온-프레미스 환경에서 Azure로 ExpressRoute 연결을 구성하는 것이 적절할 수 있습니다. Azure에 대한 온-프레미스 연결이 생성되면 다음 단계는 선택한 마이그레이션 도구가 원본에서 대상으로 연결할 수 있는지 확인하는 것입니다.

마이그레이션 도구 위치는 네트워크 연결 요구 사항을 확인합니다. 아래 표에 표시된 것처럼 선택한 마이그레이션 도구는 온-프레미스 컴퓨터와 Azure에 모두 연결해야 합니다. Azure는 마이그레이션 도구 위치의 네트워크 트래픽만 허용하도록 구성되어야 합니다.

| 마이그레이션 도구 | 유형 | 위치 | 인바운드 네트워크 요구 사항 | 아웃바운드 네트워크 요구 사항 |
|----------------|------|----------|------------------------------|-------------------------------|
| **DMS(Database Migration Service)** | 오프라인 | Azure| 외부 IP에서 3306 허용 | Azure MySQL 데이터베이스 인스턴스에 연결할 경로 |
| **가져오기/내보내기(MySQL Workbench, mysqldump)** | 오프라인| 온-프레미스 | 내부 IP에서 3306 허용 | Azure MySQL 데이터베이스 인스턴스에 연결할 경로 |
| **가져오기/내보내기(MySQL Workbench, mysqldump)** | 오프라인| Azure VM | 외부 IP에서 3306 허용 | Azure MySQL 데이터베이스 인스턴스에 연결할 경로 |
| **mydumper/myloader** | 오프라인 | 온-프레미스 | 내부 IP에서 3306 허용 | Azure MySQL 데이터베이스 인스턴스에 연결할 경로 |
| **mydumper/myloader** | 오프라인 | Azure VM | 외부 IP에서 3306 허용 | Azure MySQL 데이터베이스 인스턴스에 연결할 경로 |
| **binlog**  | 오프라인 | 온-프레미스 | 프라이빗 엔드포인트를 통해 외부 IP 또는 개인 IP에서 3306 허용 | 마스터에 대한 각 복제 서버의 경로 |

기타 네트워킹 고려 사항은 다음과 같습니다.

- VNET에 있는 DMS에는 서비스에 [동적 공용 IP](../../../dms/faq.yml)가 할당됩니다. 만들 때 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 또는 [사이트 간 VPN](../../../vpn-gateway/tutorial-site-to-site-portal.md)을 통해 연결된 가상 네트워크 내부에 서비스를 배치할 수 있습니다.

- Azure 가상 머신을 사용하여 마이그레이션 도구를 실행하는 경우 공용 IP 주소를 할당한 다음 온-프레미스 MySQL 인스턴스에만 연결하도록 허용합니다.

- 아웃바운드 방화벽은 Azure Database for MySQL에 대한 아웃바운드 연결을 보장해야 합니다. MySQL 게이트웨이 IP 주소는 [Azure Database for MySQL의 연결 아키텍처](../../concepts-connectivity-architecture.md#azure-database-for-mysql-gateway-ip-addresses) 페이지에서 사용할 수 있습니다.

## <a name="ssltls-connectivity"></a>SSL/TLS 연결

SSL 기반 통신으로 마이그레이션하는 경우의 애플리케이션 영향 외에도 SSL/TLS 연결 유형도 고려해야 할 사항이 있습니다. Azure Database for MySQL 데이터베이스를 만든 후 SSL 설정을 검토하고 [Azure Database for MySQL의 SSL/TLS 연결](../../concepts-ssl-connection-security.md) 문서를 읽고 TLS 설정이 보안 태세에 미치는 영향을 이해합니다.

> [!Important]
> 페이지의 고지 사항에 주의하세요. TLS 버전의 적용은 기본적으로 사용하도록 설정되어 있지 않습니다. TLS가 사용하도록 설정된 경우 사용하지 않도록 설정하는 유일한 방법은 SSL을 다시 사용하도록 설정하는 것입니다.

## <a name="wwi-scenario"></a>WWI 시나리오

WWI 클라우드 팀은 Azure Database for MySQL에 대한 특정 리소스 그룹에 필요한 Azure 랜딩 존 리소스를 만들었습니다. 랜딩 존을 만들기 위해 WWI는 ARM 템플릿을 사용하여 설치 및 배포를 스크립팅하기로 결정했습니다. ARM 템플릿을 사용하여 필요한 경우 환경을 신속하게 해체하고 다시 설정할 수 있습니다.

ARM 템플릿의 일부로 가상 네트워크 간의 모든 연결은 허브 및 스포크 아키텍처에서 피어링을 사용하여 구성됩니다. 데이터베이스와 애플리케이션은 별도의 가상 네트워크에 배치됩니다. Azure App Gateway는 앱 서비스가 인터넷에서 격리될 수 있도록 앱 서비스 앞에 배치됩니다. Azure App Service는 프라이빗 엔드포인트를 사용하여 Azure Database for MySQL에 연결합니다.

WWI는 원래 온라인 마이그레이션을 테스트하려고 했지만 DMS가 온-프레미스 환경에 연결하는 데 필요한 네트워크 설정으로 인해 이를 실행할 수 없었습니다. WWI는 대신 오프라인 마이그레이션을 선택했습니다. MySQL Workbench 도구를 사용하여 온-프레미스 데이터를 내보낸 다음 Azure Database for MySQL 인스턴스로 데이터를 가져오는 데 사용되었습니다.

## <a name="planning-checklist"></a>계획 검사 목록

- Azure 랜딩 존을 준비합니다. 환경을 신속하게 해체하고 다시 빌드해야 하는 경우 ARM 템플릿 배포를 사용하는 것이 좋습니다.

- 네트워킹 설정을 확인합니다. 확인에는 연결, 대역폭, 대기 시간 및 방화벽 구성이 포함되어야 합니다.

- 온라인 또는 오프라인 데이터 마이그레이션 전략을 사용할지 결정합니다.

- SSL 인증서 전략을 결정합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [마이그레이션 방법](./05-migration-methods.md)
