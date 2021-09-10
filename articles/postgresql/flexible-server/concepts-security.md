---
title: Azure Database for PostgreSQL - 유연한 서버의 보안
description: Azure Database for PostgreSQL에 대한 배포 옵션의 보안에 대해 알아보기
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 07/26/2021
ms.openlocfilehash: 6fcd772fce8a3f5e869f12e9b20c1219adf8155b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866858"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 보안

여러 보안 계층을 사용하여 Azure Database for PostgreSQL 서버의 데이터를 보호할 수 있습니다. 이 문서에서는 이러한 보안 옵션에 대해 간략히 설명합니다.

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

Azure Database for PostgreSQL은 다음 두 가지 방법으로 데이터를 암호화합니다.

- **전송 중인 데이터**: Azure Database for PostgreSQL은 SSL/TLS(Secure Sockets Layer 및 전송 계층 보안)를 사용하여 전송 중인 데이터를 암호화합니다. 암호화는 기본적으로 적용됩니다.
- **미사용 데이터**: 스토리지 암호화의 경우 Azure Database for PostgreSQL은 FIPS 140-2 유효성 검사 암호화 모듈을 사용합니다. 쿼리가 실행되는 동안 만들어진 백업 및 임시 파일을 포함하여 데이터는 디스크에서 암호화됩니다. 

  이 서비스는 Azure 스토리지 암호화에 포함된 AES 256비트 암호화를 사용하며, 키는 시스템에서 관리됩니다. 이는 SQL Server 또는 Oracle 데이터베이스의 투명한 데이터 암호화와 같은 다른 미사용 암호화 기술과 유사합니다. 스토리지 암호화는 항상 켜져 있고 해제할 수 없습니다.


## <a name="network-security"></a>네트워크 보안

Azure Database for PostgreSQL - 유연한 서버를 실행하는 경우 두 가지 주요 네트워킹 옵션이 있습니다.

- **프라이빗 액세스**: 서버를 Azure 가상 네트워크에 배포할 수 있습니다. Azure Virtual Network는 안전한 프라이빗 네트워크 통신을 제공합니다. 가상 네트워크의 리소스는 개인 IP 주소를 통해 통신할 수 있습니다. 자세한 내용은 [Azure Database for PostgreSQL - 유연한 서버의 네트워킹 개요](concepts-networking.md)를 참조하세요.

  네트워크 보안 그룹의 보안 규칙을 사용하면 가상 네트워크 서브넷 및 네트워크 인터페이스 내외부로 이동할 수 있는 네트워크 트래픽 유형을 필터링할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 개요](../../virtual-network/network-security-groups-overview.md)를 참조하세요.

- **공용 액세스**: 공개 엔드포인트를 통해 서버에 액세스할 수 있습니다. 퍼블릭 엔드포인트는 공개적으로 확인할 수 있는 DNS 주소입니다. 기본적으로 모든 연결을 차단하는 방화벽을 통해 액세스할 수 있습니다. 

  IP 방화벽 규칙은 각 요청의 기존 IP 주소를 기준으로 하여 데이터베이스 액세스 권한을 부여합니다. 자세한 내용은 [방화벽 규칙 개요](concepts-firewall-rules.md)를 참조하세요.

## <a name="access-management"></a>액세스 관리

Azure Database for PostgreSQL 서버를 만드는 동안 관리자 역할에 대한 자격 증명을 제공합니다. 이 관리자 역할은 추가 [PostgreSQL 역할](https://www.postgresql.org/docs/current/user-manag.html)을 만드는 데 사용할 수 있습니다.

[Azure Active Directory 인증](../concepts-aad-authentication.md)을 사용하여 서버에 연결할 수도 있습니다. [감사 로깅](../concepts-audit.md)은 데이터베이스의 활동을 추적하는 데 사용할 수 있습니다. 

> [!NOTE]
> Azure Database for PostgreSQL - 유연한 서버는 현재 [Azure Defender 보호](../../security-center/azure-defender.md)를 지원하지 않습니다. 

## <a name="next-steps"></a>다음 단계
- 공용 액세스 네트워킹의 [IP 주소에 대한 방화벽 규칙](concepts-firewall-rules.md)을 사용합니다.
- [Azure Database for PostgreSQL - 유연한 서버를 사용한 프라이빗 액세스 네트워킹](concepts-networking.md)에 대해 알아보기
- Azure Database for PostgreSQL의 [Azure Active Directory 인증](../concepts-aad-authentication.md)에 대해 알아보기