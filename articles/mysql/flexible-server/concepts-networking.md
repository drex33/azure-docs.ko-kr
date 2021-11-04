---
title: 네트워킹 개요 - Azure Database for MySQL 유연한 서버
description: Azure Database for MySQL의 유연한 서버 배포 옵션의 연결 및 네트워킹 옵션에 대해 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: 2ae111660b2faa6f5bba17e9f94b17caa2b2c642
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438205"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL 유연한 서버에 대 한 연결 및 네트워킹 개념

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 문서에서는 Azure MySQL 유연한 서버와의 연결을 제어하는 개념을 소개합니다. Azure에서 서버를 안전하게 만들어 액세스하기 위해 Azure Database for MySQL 유연한 서버에 대한 네트워킹 개념을 상세히 살펴봅니다.

Azure Database for MySQL - 유연한 서버는 서버에 대한 연결을 구성하는 두 가지 방법을 지원합니다.
> [!NOTE]
> 서버를 만든 후에는 네트워킹 옵션을 변경할 수 없습니다.

   * **프라이빗 액세스(VNet 통합)** [Private access](./concepts-networking-vnet.md) [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 유연한 서버를 배포할 수 있습니다. Azure 가상 네트워크는 프라이빗하고 안전한 네트워크 통신을 제공합니다. 가상 네트워크의 리소스는 개인 IP 주소를 통해 통신할 수 있습니다.
   
   * **퍼블릭 액세스(허용된 IP 주소)** [퍼블릭 액세스](./concepts-networking-public.md) 퍼블릭 엔드포인트를 통해 유연한 서버에 액세스합니다. 퍼블릭 엔드포인트는 공개적으로 확인할 수 있는 DNS 주소입니다. "허용되는 IP 주소"라는 말은 선택하는 IP 범위에 서버 액세스 권한을 부여한다는 뜻입니다. 이러한 권한을 **방화벽 규칙** 이라고 합니다.

## <a name="choosing-a-networking-option"></a>네트워킹 옵션 선택

다음과 같은 기능을 원하는 경우 **프라이빗 액세스(VNet 통합)** 를 선택합니다.
   * 같은 가상 네트워크 또는 [피어 가상 네트워크](../../virtual-network/virtual-network-peering-overview.md)의 Azure 리소스에서 유연한 서버에 연결
   * VPN 또는 ExpressRoute를 사용하여 비 Azure 리소스에서 유연한 서버에 연결
   * 비 퍼블릭 엔드포인트

다음 기능을 사용하려면 **퍼블릭 액세스(허용된 IP 주소)** 방법을 선택합니다.
   * 가상 네트워크를 지원하지 않는 Azure 리소스에서 연결
   * VPN 또는 ExpressRoute로 연결되지 않은 Azure 외부의 리소스에서 연결 
   * 유연한 서버에 퍼블릭 엔드포인트가 있음

다음 특징은 프라이빗 액세스 또는 퍼블릭 액세스 옵션을 사용하도록 선택하는 경우에 적용됩니다.
* 허용되는 IP 주소에서의 연결은 유효한 자격 증명을 사용하여 MySQL 서버에 인증해야 합니다.
* 네트워크 트래픽에 [연결 암호화](#tls-and-ssl)를 사용할 수 있습니다.
* 서버에 FQDN(정규화된 도메인 이름)이 있습니다. 연결 문자열의 호스트 이름 속성에는 IP 주소 대신 FQDN을 사용하는 것이 좋습니다.
* 두 옵션 모두 데이터베이스 수준 또는 테이블 수준이 아닌 서버 수준에서 액세스를 제어합니다. MySQL의 역할 속성을 사용하여 데이터베이스, 테이블 및 기타 개체 액세스를 제어합니다.


### <a name="unsupported-virtual-network-scenarios"></a>지원되지 않는 가상 네트워크 시나리오

* 퍼블릭 엔드포인트(또는 공용 IP 또는 DNS) - 가상 네트워크에 배포된 유연한 서버는 퍼블릭 엔드포인트를 포함할 수 없습니다.
* 유연한 서버가 가상 네트워크 및 서브넷에 배포된 후에는 다른 가상 네트워크 또는 서브넷으로 이동할 수 없습니다. * 유연한 서버를 배포한 후에는 유연한 서버에서 사용하는 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다.
* 서브넷에 리소스가 있으면 서브넷 크기(주소 공간)를 늘릴 수 없습니다.
* 서버를 만든 후에는 퍼블릭에서 프라이빗 액세스로 변경할 수 없습니다. 지정 시간 복구를 사용하는 것이 좋습니다.

[Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)를 사용하여 프라이빗 액세스(VNet 통합)를 사용하도록 설정하는 방법 알아보기

> [!NOTE]
> 사용자 지정 DNS 서버를 사용하는 경우에는 DNS 전달자를 사용하여 Azure Database for MySQL - 유연한 서버의 FQDN을 확인해야 합니다. 자세한 정보는 [자체 DNS 서버를 사용하는 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하세요.

## <a name="hostname"></a>호스트 이름
선택한 네트워킹 옵션과 관계없이 유연한 서버에 연결할 때 연결 문자열에 FQDN(정규화된 도메인 이름) `<servername>.mysql.database.azure.com`을 사용하는 것이 좋습니다. 

## <a name="tls-and-ssl"></a>TLS 및 SSL
Azure Database for MySQL 유연한 서버는 TLS(전송 계층 보안) 암호와 함께 SSL(Secure Sockets Layer)을 사용하여 MySQL 서버에 대한 클라이언트 애플리케이션 연결을 지원합니다. TLS는 데이터베이스 서버와 클라이언트 애플리케이션 간에 암호화된 네트워크 연결을 보장하여 규정 준수 요구 사항을 준수할 수 있도록 하는 업계 표준 프로토콜입니다.

Azure Database for MySQL 유연한 서버는 기본적으로 전송 계층 보안(TLS 1.2)을 사용하는 암호화된 연결을 지원하며 TLS 1.0 및 TLS 1.1을 사용하여 들어오는 연결은 기본적으로 모두 거부됩니다. 유연한 서버에서 암호화된 연결 적용 또는 TLS 버전 구성을 구성하거나 변경할 수 있습니다. 

다음은 유연한 서버에 사용할 수 있는 SSL 및 TLS 설정의 다양한 구성입니다.

| 시나리오   | 서버 매개 변수 설정      | Description                                    |
|------------|--------------------------------|------------------------------------------------|
|SSL 사용 안 함(암호화된 연결) | require_secure_transport = OFF |레거시 애플리케이션이 MySQL 서버에 대한 암호화된 연결을 지원하지 않는 경우 require_secure_transport=OFF를 설정하여 유연한 서버에 암호화된 연결 적용을 사용하지 않도록 설정할 수 있습니다.|
|TLS 버전 < 1.2로 SSL 적용 | require_secure_transport = ON 및 tls_version = TLSV1 또는 TLSV1.1| 레거시 애플리케이션에서 암호화된 연결을 지원하지만 TLS 버전 1.2 미만이 필요한 경우 암호화된 연결을 사용하도록 설정할 수 있지만 애플리케이션에서 지원하는 tls 버전(v1.0 또는 v1.1)과의 연결을 허용하도록 유연한 서버를 구성할 수 있습니다.|
|TLS 버전 = 1.2를 사용하여 SSL 적용(기본 구성)|require_secure_transport = ON 및 tls_version = TLSV1.2| 이는 유연한 서버에 권장되는 기본 구성입니다.|
|TLS 버전 = 1.3으로 SSL 적용(MySQL v8.0 이상에서 지원됨)| require_secure_transport = ON 및 tls_version = TLSV1.3| 이는 새 애플리케이션 개발에 유용하며 권장됩니다.|

> [!Note]
> 유연한 서버에서 SSL 암호화를 변경하는 것은 지원되지 않습니다. tls_version이 TLS 버전 1.2로 설정된 경우 FIPS 암호화 그룹은 기본적으로 적용됩니다. 버전 1.2가 아닌 TLS 버전의 경우 SSL 암호화는 MySQL 커뮤니티 설치와 함께 제공되는 기본 설정으로 설정됩니다.

자세한 정보는 [SSL/TLS를 사용하여 연결](how-to-connect-tls-ssl.md)하는 방법을 검토하세요. 


## <a name="next-steps"></a>다음 단계
* [Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)를 사용하여 프라이빗 액세스(vnet 통합)를 사용하도록 설정하는 방법 알아보기
* [Azure Portal](how-to-manage-firewall-portal.md) 또는 [Azure CLI](how-to-manage-firewall-cli.md)를 사용하여 퍼블릭 액세스(허용된 IP 주소)를 사용하도록 설정하는 방법 알아보기
* [TLS 사용](how-to-connect-tls-ssl.md) 방법 알아보기
