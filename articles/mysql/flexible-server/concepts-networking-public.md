---
title: 퍼블릭 네트워크 액세스 개요 - Azure Database for MySQL 유연한 서버
description: Azure Database for MySQL의 유연한 서버 배포 옵션의 퍼블릭 액세스 및 네트워킹 옵션에 대해 알아봅니다.
author: Madhusoodanan
ms.author: dimadhus
ms.service: mysql
ms.topic: conceptual
ms.date: 8/6/2021
ms.openlocfilehash: a8d2e1e6ef3f1aa2dcb736336232b1b149eb7b89
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438206"
---
# <a name="public-network-access-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL 공용 네트워크 액세스 - 유연한 서버

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 문서에서는 서버에 대한 퍼블릭 연결 옵션에 대해 설명합니다. 인터넷을 통해 안전하게 액세스할 수 있는 Azure Database for MySQL 유연한 서버를 만드는 방법에 대해 자세히 설명합니다.

## <a name="public-access-allowed-ip-addresses"></a>퍼블릭 액세스(허용된 IP 주소)

유연한 서버에서 퍼블릭 액세스를 구성하면 퍼블릭 엔드포인트를 통해 서버에 액세스할 수 있습니다. 즉, 인터넷을 통해 서버에 액세스할 수 있습니다. 퍼블릭 엔드포인트는 공개적으로 확인할 수 있는 DNS 주소입니다. "허용되는 IP 주소"라는 말은 선택하는 IP 범위에 서버 액세스 권한을 부여한다는 뜻입니다. 이러한 권한을 방화벽 규칙이라고 합니다.

퍼블릭 액세스 메서드의 특성은 다음과 같습니다.

* 허용하는 IP 주소만 MySQL 유연한 서버에 액세스할 권한이 있습니다. 기본적으로 IP 주소는 허용되지 않습니다. 서버를 만드는 동안 또는 나중에 IP 주소를 추가할 수 있습니다.
* MySQL 서버에는 공개적으로 확인할 수 있는 DNS 이름이 있습니다.
* 유연한 서버는 Azure 가상 네트워크 중 하나에 있지 않습니다.
* 서버에서 들어오고 나가는 네트워크 트래픽은 개인 네트워크를 통해 이동하지 않습니다. 트래픽은 일반적인 인터넷 경로를 사용합니다.

### <a name="firewall-rules"></a>방화벽 규칙

IP 주소에 대한 권한을 부여하는 것을 방화벽 규칙이라고 합니다. 허용되지 않는 IP 주소에서 연결을 시도하면 원래 클라이언트에 오류가 표시됩니다.

### <a name="allowing-all-azure-ip-addresses"></a>모든 Azure IP 주소 허용

고정된 발신 IP 주소를 Azure 서비스에 사용할 수 없는 경우 모든 Azure 데이터 센터 IP 주소에서 연결을 설정하는 것이 좋습니다.

> [!IMPORTANT]
> **Azure 서비스 및 Azure 내의 리소스에서 퍼블릭 액세스 허용** 옵션은 다른 고객 구독의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.

[Azure Portal](how-to-manage-firewall-portal.md) 또는 [Azure CLI](how-to-manage-firewall-cli.md)를 사용하여 퍼블릭 액세스(허용된 IP 주소)를 사용하도록 설정하고 관리하는 방법을 알아봅니다.

### <a name="troubleshooting-public-access-issues"></a>퍼블릭 액세스 문제 해결

Microsoft Azure Database for MySQL 서버 서비스로의 연결이 예상대로 작동되지 않는 경우 다음 사항을 고려하세요.

* **허용 목록에 대한 변경이 아직 적용되지 않음:** Azure Database for MySQL 서버 방화벽 구성에 변경 내용이 적용되려면 최대 5분 정도 걸릴 수 있습니다.

* **인증 실패:** 사용자에게 Azure Database for MySQL 서버에 대한 권한이 없거나 사용한 암호가 틀렸을 경우 Azure Database for MySQL 서버에 대한 연결이 거부됩니다. 방화벽 설정은 클라이언트에게 서버에 연결을 시도할 수 있는 기회를 제공합니다. 각 클라이언트는 꼭 필요한 보안 자격 증명을 제공해야 합니다.

* **동적 클라이언트 IP 주소:** 동적 IP 주소를 통해 인터넷에 연결되어 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도할 수 있습니다.

  * ISP(인터넷 서비스 공급자)는 Azure Database for MySQL 서버에 연결될 클라이언트에 할당된 IP 주소 범위를 요청하고, 방화벽 규칙에 따라 IP 주소 범위를 추가합니다.
  * 클라이언트 컴퓨터 대신 고정 IP 지정을 가져온 다음 고정 IP 주소를 방화벽 규칙으로 추가합니다.

* **IPv6 형식에 방화벽 규칙을 사용할 수 없음:** 방화벽 규칙은 IPv4 형식이어야 합니다. IPv6 형식으로 방화벽 규칙을 지정하는 경우 유효성 검사 오류가 표시됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](how-to-manage-firewall-portal.md) 또는 [Azure CLI](how-to-manage-firewall-cli.md)를 사용하여 퍼블릭 액세스(허용된 IP 주소)를 사용하도록 설정하는 방법 알아보기
* [TLS 사용](how-to-connect-tls-ssl.md) 방법 알아보기
