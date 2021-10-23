---
title: Azure SQL Managed Instance 퍼블릭 엔드포인트 보호
description: Azure SQL Managed Instance에서 안전하게 퍼블릭 엔드포인트 사용
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 05/08/2019
ms.openlocfilehash: 2718816adfeaa8fa98c93ea1ed394c6634b5cb7b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250448"
---
# <a name="use-azure-sql-managed-instance-securely-with-public-endpoints"></a>퍼블릭 엔드포인트로 안전하게 Azure SQL Managed Instance 사용
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance는 [퍼블릭 엔드포인트](public-endpoint-configure.md)를 통해 사용자 연결을 제공할 수 있습니다. 이 문서에서는 이 구성을 더 안전하게 보호하는 방법을 설명합니다.

## <a name="scenarios"></a>시나리오

Azure SQL Managed Instance는 가상 네트워크 내부에서 연결할 수 있도록 프라이빗 엔드포인트를 제공합니다. 기본 옵션은 최대 격리를 제공하는 것입니다. 그러나 퍼블릭 엔드포인트 연결을 제공해야 하는 다음과 같은 시나리오도 있습니다.

- 관리형 인스턴스가 다중 테넌트 전용 PaaS(Platform as a Service) 제품과 통합되어야 합니다.
- VPN을 사용하는 경우에 가능한 것보다 높은 처리량의 데이터 교환이 필요합니다.
- 회사 정책에 따라 회사 네트워크 내에서 PaaS를 사용할 수 없습니다.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>퍼블릭 엔드포인트 액세스를 위한 관리형 인스턴스 배포

필수는 아니지만 퍼블릭 엔드포인트 액세스 권한이 있는 관리형 인스턴스의 일반적인 배포 모델은 격리된 전용 가상 네트워크에서 인스턴스를 만드는 것입니다. 이 구성에서 가상 네트워크는 가상 클러스터 격리에만 사용됩니다. 관리형 인스턴스의 IP 주소 공간이 회사 네트워크의 IP 주소 공간과 겹쳐도 됩니다.

## <a name="secure-data-in-motion"></a>사용 중인 데이터 보호

클라이언트 드라이버에서 암호화를 지원하는 경우 SQL Managed Instance 데이터 트래픽은 항상 암호화됩니다. 관리형 인스턴스와 다른 Azure 가상 머신 또는 Azure 서비스 간에 전송되는 데이터는 Azure의 백본을 나가지 않습니다. 관리형 인스턴스와 온-프레미스 네트워크 간 연결이 있는 경우 Azure ExpressRoute를 사용하는 것이 좋습니다. ExpressRoute를 사용하면 퍼블릭 인터넷을 통해 데이터를 이동하지 않아도 됩니다. 관리형 인스턴스 프라이빗 연결의 경우 프라이빗 피어링만 사용할 수 있습니다.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>인바운드 및 아웃바운드 연결 잠금

다음 다이어그램은 권장되는 보안 구성을 보여 줍니다.

![인바운드 및 아웃바운드 연결을 잠그기 위한 보안 구성](./media/public-endpoint-overview/managed-instance-vnet.png)

관리되는 인스턴스에는 고객 전용의 퍼블릭 엔드포인트 주소가 있습니다. 이 엔드포인트는 [관리 엔드포인트](management-endpoint-find-ip-address.md)와 IP를 공유하지만 다른 포트를 사용합니다. 클라이언트 쪽 아웃바운드 방화벽과 네트워크 보안 그룹 규칙에서 이 퍼블릭 엔드포인트 IP 주소를 설정하여 아웃바운드 연결을 제한합니다.

관리형 인스턴스에 대한 트래픽이 신뢰할 수 있는 원본에서 들어오는지 확인하려면 잘 알려진 IP 주소를 가진 원본에서 연결하는 것이 좋습니다. 네트워크 보안 그룹을 사용하여 포트 3342에서 관리형 인스턴스 퍼블릭 엔드포인트에 대한 액세스를 제한합니다.

클라이언트가 온-프레미스 네트워크에서 연결을 시작해야 하는 경우 원래 주소가 잘 알려진 IP 주소 집합으로 변환되었는지 확인합니다. 해당 작업을 수행할 수 없는 경우(예: 모바일 인력이 일반적인 시나리오인 경우) [지점 및 사이트 간 VPN 연결과 프라이빗 엔드포인트](point-to-site-p2s-configure.md)를 사용하는 것이 좋습니다.

Azure에서 연결을 시작한 경우 잘 알려진 할당된 [가상 IP 주소](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip)(예: 가상 머신)에서 트래픽이 들어오는 것이 좋습니다. VIP(가상 IP) 주소 관리가 용이하도록 [공용 IP 주소 접두사](../../virtual-network/ip-services/public-ip-address-prefix.md)를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [퍼블릭 엔드포인트 구성](public-endpoint-configure.md)에서 관리형 인스턴스에 대해 퍼블릭 엔드포인트를 구성하는 방법을 알아봅니다.