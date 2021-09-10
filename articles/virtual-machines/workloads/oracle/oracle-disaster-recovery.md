---
title: Azure 환경의 Oracle 재해 복구 시나리오 개요 | Microsoft Docs
description: Azure 환경의 Oracle Database 12c 데이터베이스에 대한 재해 복구 시나리오입니다.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: b90de3d7f80eb6dcb60d3e44b13aac83e1dec574
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688470"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Azure 환경의 Oracle Database 12c 데이터베이스 재해 복구

**적용 대상:** :heavy_check_mark: Linux VM 

## <a name="assumptions"></a>가정

- Oracle Data Guard 설계 및 Azure 환경에 대해 이해하고 있습니다.


## <a name="goals"></a>목표
- DR(재해 복구) 요구 사항을 충족하는 토폴로지 및 구성을 설계합니다.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>시나리오 1: 기본 사이트 및 DR 사이트가 Azure에 있음

기본 사이트에 설치한 Oracle 데이터베이스가 고객에게 있습니다. DR 사이트는 다른 지역에 있습니다. 고객은 이러한 사이트 간의 빠른 복구를 위해 Oracle Data Guard를 사용합니다. 기본 사이트에는 보고 및 다른 용도를 위한 보조 데이터베이스도 있습니다. 

### <a name="topology"></a>토폴로지

다음은 Azure 설치에 대한 요약입니다.

- 두 사이트(기본 사이트 및 DR 사이트)
- 두 가상 네트워크
- Data Guard가 있는 두 Oracle 데이터베이스(주 및 대기)
- Golden Gate 또는 Data Guard가 있는 두 Oracle 데이터베이스(기본 사이트에만 해당)
- 두 애플리케이션 서비스(기본 사이트 및 DR 사이트 각각에 하나씩)
- *가용성 집합* - 기본 사이트의 데이터베이스 및 애플리케이션 서비스에 사용됩니다.
- Jumpbox(각 사이트에 하나씩) - 사설망에 대한 액세스를 제한하며, 관리자만 로그인할 수 있습니다.
- 별도의 서브넷에 있는 Jumpbox, 애플리케이션 서비스, 데이터베이스 및 VPN 게이트웨이
- 애플리케이션 및 데이터베이스 서브넷에 적용되는 NSG

![Azure의 기본 사이트 및 DR 사이트를 보여 주는 다이어그램](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>시나리오 2: 기본 사이트는 온-프레미스에 있고, DR 사이트는 Azure에 있음

고객이 온-프레미스에 Oracle 데이터베이스를 설치했습니다(기본 사이트). DR 사이트는 Azure에 있습니다. Oracle Data Guard는 이러한 사이트 간의 빠른 복구에 사용됩니다. 기본 사이트에는 보고 및 다른 용도를 위한 보조 데이터베이스도 있습니다. 

이 설치에 대한 접근 방법은 두 가지가 있습니다.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>방법 1: 온-프레미스와 Azure 간에 직접 연결하고, 방화벽에서 열려 있는 TCP 포트가 필요합니다. 

TCP 포트를 외부에 공개하므로 직접 연결은 사용하지 않는 것이 좋습니다.

#### <a name="topology"></a>토폴로지

다음은 Azure 설치에 대한 요약입니다.

- 하나의 DR 사이트 
- 가상 네트워크 1개
- Data Guard가 있는 하나의 Oracle 데이터베이스(활성)
- DR 사이트에 있는 하나의 애플리케이션 서비스
- 하나의 Jumpbox - 사설망에 대한 액세스를 제한하며, 관리자만 로그인할 수 있습니다.
- 별도의 서브넷에 있는 Jumpbox, 애플리케이션 서비스, 데이터베이스 및 VPN 게이트웨이
- 애플리케이션 및 데이터베이스 서브넷에 적용되는 NSG
- 1521 인바운드 TCP 포트(또는 사용자 정의 포트)를 허용하는 NSG 정책/규칙
- IP 주소/온-프레미스(DB 또는 애플리케이션) 주소만 제한하여 가상 네트워크에 액세스하는 NSG 정책/규칙

![방화벽에서 열려 있는 TCP 포트가 필요한 온-프레미스와 Azure 간의 직접 연결을 보여 주는 다이어그램](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>방법 2: 사이트 간 VPN
사이트 간 VPN이 더 나은 방법입니다. VPN 설정에 대한 자세한 내용은 [CLI를 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)를 참조하세요.

#### <a name="topology"></a>토폴로지

다음은 Azure 설치에 대한 요약입니다.

- 하나의 DR 사이트 
- 가상 네트워크 1개 
- Data Guard가 있는 하나의 Oracle 데이터베이스(활성)
- DR 사이트에 있는 하나의 애플리케이션 서비스
- 하나의 Jumpbox - 사설망에 대한 액세스를 제한하며, 관리자만 로그인할 수 있습니다.
- 별도의 서브넷에 있는 Jumpbox, 애플리케이션 서비스, 데이터베이스 및 VPN 게이트웨이
- 애플리케이션 및 데이터베이스 서브넷에 적용되는 NSG
- 온-프레미스와 Azure 간의 사이트 간 VPN 연결

![DR 토폴로지 페이지의 스크린샷](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>추가 자료

- [Azure에서 Oracle 데이터베이스 설계 및 구현](oracle-design.md)
- [Oracle Data Guard 구성](configure-oracle-dataguard.md)
- [Oracle Golden Gate 구성](configure-oracle-golden-gate.md)
- [Oracle 백업 및 복구](./oracle-overview.md)


## <a name="next-steps"></a>다음 단계

- [자습서: 고가용성 VM 만들기](../../linux/create-cli-complete.md)
- [VM 배포 Azure CLI 샘플 탐색](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)