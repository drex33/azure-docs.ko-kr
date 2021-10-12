---
title: Azure Database for PostgreSQL - 유연한 서버의 보안
description: Azure Database for PostgreSQL에 대한 배포 옵션의 보안에 대해 알아보기
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 10/01/2021
ms.openlocfilehash: 8e474cd0ae6635034607a088aceee47a83c500de
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388777"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 보안

여러 보안 계층을 사용하여 Azure Database for PostgreSQL 서버의 데이터를 보호할 수 있습니다. 이 문서에서는 이러한 보안 옵션에 대해 간략히 설명합니다.

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

Azure Database for PostgreSQL은 다음 두 가지 방법으로 데이터를 암호화합니다.

- **전송 중인 데이터**: Azure Database for PostgreSQL은 SSL/TLS(Secure Sockets Layer 및 전송 계층 보안)를 사용하여 전송 중인 데이터를 암호화합니다. 암호화는 기본적으로 적용됩니다. 자세한 내용은 이 [가이드](how-to-connect-tls-ssl.md)를 참조하세요. 보안을 강화하기 위해 [SCRAM 인증](how-to-connect-scram.md)을 사용하도록 선택할 수 있습니다.

   권장되지는 않지만 필요한 경우 `require_secure_transport` 서버 매개 변수를 OFF로 업데이트하여 Azure Database for PostgreSQL - 유연한 서버에 연결하기 위해 TLS\SSL을 사용하지 않도록 설정하는 옵션이 있습니다. 설정 `ssl_min_protocol_version` 및 `ssl_max_protocol_version` 서버 매개 변수를 설정하여 TLS 버전을 설정할 수도 있습니다.


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

예제:

```SQL
postgres=> create role demouser with password 'password123';
```

서버의 역할 목록을 주기적으로 감사할 수 있습니다. 예를 들어 `psql` 클라이언트를 사용하여 연결하고, 추가 역할 만들기, 데이터베이스 만들기, 복제 등과 같은 권한과 함께 모든 역할을 나열하는 `pg_roles` 테이블을 쿼리할 수 있습니다. 

```SQL
postgres=> \x
Expanded display is on.
postgres=> select * from pg_roles where rolname='demouser';
-[ RECORD 1 ]--+---------
rolname        | demouser
rolsuper       | f
rolinherit     | t
rolcreaterole  | f
rolcreatedb    | f
rolcanlogin    | f
rolreplication | f
rolconnlimit   | -1
rolpassword    | ********
rolvaliduntil  |
rolbypassrls   | f
rolconfig      |
oid            | 24827

```

또한 유연한 서버에서 [감사 로깅](../concepts-audit.md)을 사용하여 데이터베이스의 활동을 추적할 수 있습니다. 

> [!NOTE]
> Azure Database for PostgreSQL - 유연한 서버는 현재 [Azure Defender 보호](../../security-center/azure-defender.md)를 지원하지 않습니다. 

## <a name="updating-passwords"></a>암호 업데이트

보안을 강화하려면 관리자 암호와 데이터베이스 사용자 암호를 주기적으로 회전하는 것이 좋습니다. 대문자와 소문자, 숫자 및 특수 문자를 사용하여 강한 암호를 사용하는 것이 좋습니다.

### <a name="reset-administrator-password"></a>관리자 암호 다시 설정

관리자 암호는 [방법 가이드](./how-to-manage-server-portal.md#reset-admin-password)에 따라 다시 설정합니다.

### <a name="update-database-user-password"></a>데이터베이스 사용자 암호 업데이트

데이터베이스 사용자 암호는 클라이언트 도구를 사용하여 업데이트할 수 있습니다. 예제:
```SQL
postgres=> alter role demouser with password 'Password123!';
ALTER ROLE
```
## <a name="next-steps"></a>다음 단계
- 공용 액세스 네트워킹의 [IP 주소에 대한 방화벽 규칙](concepts-firewall-rules.md)을 사용합니다.
- [Azure Database for PostgreSQL - 유연한 서버를 사용한 프라이빗 액세스 네트워킹](concepts-networking.md)에 대해 알아보기
- Azure Database for PostgreSQL의 [Azure Active Directory 인증](../concepts-aad-authentication.md)에 대해 알아보기