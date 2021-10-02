---
title: Azure Database for PostgreSQL - 유연한 서버에서 TLS/SSL을 사용한 암호화된 연결
description: Azure Database for PostgreSQL - 유연한 서버에서 TLS/SSL을 사용해 연결하는 방법에 대한 지침 및 정보입니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 065ffe56e2868cce00bd92c612beb987d9372e38
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388796"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버에서 전송 계층 보안을 사용한 암호화된 연결

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

Azure Database for PostgreSQL - 유연한 서버는 이전에 SSL(Secure Sockets Layer)로 알려진 TLS(전송 계층 보안)를 사용한 PostgreSQL 서비스에 대한 클라이언트 애플리케이션 연결을 지원합니다. TLS는 데이터베이스 서버와 클라이언트 애플리케이션 간에 암호화된 네트워크 연결을 보장하여 규정 준수 요구 사항을 준수할 수 있도록 하는 업계 표준 프로토콜입니다.

Azure Database for PostgreSQL - 유연한 서버는 전송 계층 보안(TLS 1.2 이상)을 사용하는 암호화된 연결을 지원하며 TLS 1.0 및 TLS 1.1을 사용하여 들어오는 연결은 모두 거부됩니다. 모든 유연한 서버의 경우 TLS 연결을 적용할 수 있습니다. 

>[!Note]
> 기본적으로 클라이언트와 서버 간의 보안 연결이 적용됩니다. 유연한 서버에 연결하기 위해 TLS/SSL을 사용하지 않도록 설정하려면 서버 매개 변수를 로 변경할 수 `require_secure_transport` `OFF` 있습니다. 

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>TLS/SSL 연결을 위해 인증서 확인이 필요한 애플리케이션
경우에 따라 안전한 연결을 위해 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일에서 생성되는 로컬 인증서 파일이 필요합니다. Azure Database for PostgreSQL - 유연한 서버는 *DigiCert Global Root CA* 를 사용합니다. [DigiCert Global Root CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)에서 SSL을 통해 통신하는 데 필요한 이 인증서를 다운로드하고 인증서 파일을 원하는 위치에 저장합니다. 예를 들어 이 자습서에서는 `c:\ssl`을 사용합니다.


### <a name="connect-using-psql"></a>psql을 사용하여 연결
*프라이빗 액세스(VNet 통합)* 를 사용하여 유연한 서버를 만든 경우 서버와 동일한 VNet 내의 리소스에서 서버에 연결해야 합니다. 가상 머신을 만들고 유연한 서버를 사용하여 만든 VNet에 추가할 수 있습니다.

*퍼블릭 액세스(허용되는 IP 주소)* 를 사용하여 유연한 서버를 만든 경우 서버의 방화벽 규칙 목록에 로컬 IP 주소를 추가할 수 있습니다.

다음 예제에서는 psql 명령줄 인터페이스를 사용하여 서버에 연결하는 방법을 보여줍니다. `sslmode=verify-full` 연결 문자열 설정을 사용하여 TLS/SSL 인증서 확인을 적용합니다. 로컬 인증서 파일 경로를 `sslrootcert` 매개 변수에 전달합니다.

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> `sslrootcert`에 전달된 값이 저장한 인증서의 파일 경로와 일치하는지 확인합니다.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>애플리케이션 또는 프레임워크가 TLS 연결을 지원하는지 확인합니다.

데이터베이스 서비스용 PostgreSQL을 사용하는 일부 애플리케이션 프레임워크는 기본적으로 설치하는 동안 TLS를 사용하도록 설정하지 않습니다. PostgreSQL 서버는 TLS 연결을 적용하지만 애플리케이션이 TLS용으로 구성되지 않은 경우 애플리케이션이 데이터베이스 서버에 연결하지 못할 수 있습니다. 애플리케이션의 설명서를 참조하여 TLS 연결을 활성화하는 방법을 알아보세요.

## <a name="next-steps"></a>다음 단계
- [Azure CLI를 사용하여 Azure Database for PostgreSQL - 유연한 서버 가상 네트워크를 만들고 관리](./how-to-manage-virtual-network-cli.md)합니다.
- [Azure Database for PostgreSQL - 유연한 서버의 네트워킹](./concepts-networking.md)에 대해 자세히 알아보기
- [Azure Database for PostgreSQL - 유연한 서버 방화벽 규칙](./concepts-networking.md#public-access-allowed-ip-addresses)에 대해 자세히 알아보기
