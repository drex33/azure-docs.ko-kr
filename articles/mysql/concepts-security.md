---
title: 보안 - Azure Database for MySQL
description: Azure Database for MySQL 보안 기능에 대한 개요입니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 1aa2367a4bf9f4bebab6358d60b86ac19b6d3bf7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297471"
---
# <a name="security-in-azure-database-for-mysql"></a>Azure Database for MySQL의 보안

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL 서버의 데이터를 보호하는 데 사용할 수 있는 여러 보안 계층이 있습니다. 이 문서에서는 이러한 보안 옵션을 설명합니다.

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

### <a name="in-transit"></a>전송 중
Azure Database for MySQL은 전송 중인 데이터를 전송 계층 보안으로 암호화하여 데이터를 보호합니다. 암호화(SSL/TLS)는 기본값으로 적용됩니다.

### <a name="at-rest"></a>미사용
Azure Database for MySQL 서비스는 미사용 데이터의 스토리지 암호화를 위해 FIPS 140-2 유효성 검사 암호화 모듈을 사용합니다. 백업을 포함한 데이터는 디스크에서 암호화되며, 쿼리를 실행하는 동안 만든 임시 파일은 제외됩니다. 이 서비스는 Azure 스토리지 암호화에 포함된 AES 256비트 암호화를 사용하며, 키는 시스템에서 관리됩니다. 스토리지 암호화는 항상 켜져 있고 해제할 수 없습니다.


## <a name="network-security"></a>네트워크 보안
그러나 Azure Database for MySQL 서버의 연결은 먼저 공개적으로 액세스할 수 있는 지역 게이트웨이를 통해 라우팅됩니다. 게이트웨이는 공개적으로 액세스할 수 있는 IP를 포함하지만 서버 IP 주소는 보호됩니다. 게이트웨이에 대한 자세한 정보는 [연결 아키텍처 문서](concepts-connectivity-architecture.md)를 참조하세요.  

새로 만든 Azure Database for MySQL 서버에는 모든 외부 연결을 차단하는 방화벽이 있습니다. 이는 게이트웨이에 연결 되지만 서버에 연결할 수는 없습니다. 

### <a name="ip-firewall-rules"></a>IP 방화벽 규칙
IP 방화벽 규칙은 각 요청의 기존 IP 주소를 기준으로 하여 데이터베이스 액세스 권한을 부여합니다. 자세한 내용은 [방화벽 규칙 개요](concepts-firewall-rules.md)를 참조하세요.

### <a name="virtual-network-firewall-rules"></a>Virtual Network 방화벽 규칙
가상 네트워크 서비스 엔드포인트는 Azure 백본을 통해 가상 네트워크 연결을 확장합니다. 가상 네트워크 규칙을 사용하여 Azure Database for MySQL 서버가 선택한 가상 네트워크의 서브넷에서 연결을 허용하도록 설정할 수 있습니다. 자세한 정보는 [가상 네트워크 서비스 엔드포인트 개요](concepts-data-access-and-security-vnet.md)를 참조하세요.

### <a name="private-ip"></a>프라이빗 IP
Private Link를 사용하면 프라이빗 엔드포인트를 통해 Azure에서 Azure Database for MySQL로 연결할 수 있습니다. Azure Private Link는 기본적으로 개인 VNet(Virtual Network) 내에 Azure 서비스를 제공합니다. PaaS 리소스는 VNet의 다른 리소스와 마찬가지로 개인 IP 주소를 사용하여 액세스할 수 있습니다. 자세한 내용은 [프라이빗 링크 개요](concepts-data-access-security-private-link.md)를 참조하세요.

## <a name="access-management"></a>액세스 관리

Azure Database for MySQL 서버를 만드는 동안 관리자 계정 사용자에 대한 자격 증명을 제공합니다. 이 관리자를 사용하여 추가로 MySQL 사용자를 만들 수 있습니다.


## <a name="threat-protection"></a>위협 보호

비정상적이고 잠재적으로 유해한 서버 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 검색하는 [오픈 소스 관계형 데이터베이스용 Microsoft Defender에](../security-center/defender-for-databases-introduction.md) 옵트인할 수 있습니다.

[감사 로깅](concepts-audit-logs.md)은 데이터베이스의 활동을 추적하는 데 사용할 수 있습니다. 


## <a name="next-steps"></a>다음 단계
- [IP](concepts-firewall-rules.md) 또는 [가상 네트워크](concepts-data-access-and-security-vnet.md)에 대한 방화벽 규칙 사용
