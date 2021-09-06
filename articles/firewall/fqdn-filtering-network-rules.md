---
title: 네트워크 규칙의 Azure Firewall FQDN 필터링
description: 네트워크 규칙에서 Azure Firewall FQDN 필터링을 사용하는 방법
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/11/2021
ms.author: victorh
ms.openlocfilehash: f28092c050e58c6ef90a3faf07034be21198008a
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112017202"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>네트워크 규칙에서 FQDN 필터링 사용

FQDN(정규화된 도메인 이름)은 호스트 또는 IP 주소의 도메인 이름을 나타냅니다. Azure Firewall 및 방화벽 정책에서 DNS 확인을 기반으로 네트워크 규칙의 FQDN을 사용할 수 있습니다. 이 기능을 사용하면 임의의 TCP/UDP 프로토콜(NTP, SSH, RDP 등)을 사용하여 아웃바운드 트래픽을 필터링할 수 있습니다. DNS 프록시에서 네트워크 규칙의 FQDN을 사용하도록 설정해야 합니다. 자세한 내용은 [Azure Firewall DNS 설정](dns-settings.md)을 참조하세요.

> [!NOTE]
> 의도적으로 FQDN 필터링은 와일드카드를 지원하지 않습니다.

## <a name="how-it-works"></a>작동 방법

조직에 필요한 DNS 서버(Azure DNS 또는 고유한 사용자 지정 DNS)를 정의하면 Azure Firewall에서 선택한 DNS 서버를 기반으로 FQDN을 IP 주소로 변환합니다. 변환은 애플리케이션 규칙 처리 및 네트워크 규칙 처리 둘 다에 발생합니다.

새 DNS 확인이 수행되면 새 IP 주소가 방화벽 규칙에 추가됩니다. DNS 서버에서 더 이상 반환되지 않는 이전 IP 주소는 15분 후 만료됩니다. Azure Firewall 규칙은 네트워크 규칙에 있는 FQDN의 DNS 확인에서 15초마다 업데이트됩니다.

### <a name="differences-in-application-rules-vs-network-rules"></a>애플리케이션 규칙과 네트워크 규칙의 차이점

- HTTP/S 및 MSSQL에 대한 애플리케이션 규칙의 FQDN 필터링은 애플리케이션 수준 투명 프록시와 SNI 헤더를 기반으로 합니다. 따라서 동일한 IP 주소로 확인되는 두 FQDN을 구별할 수 있습니다. 네트워크 규칙의 FQDN 필터링을 사용하는 경우에는 구별할 수 없습니다. 

   가능한 경우 항상 애플리케이션 규칙을 사용합니다.
  - 프로토콜이 HTTP/S 또는 MSSQL인 경우 FQDN 필터링에 애플리케이션 규칙을 사용합니다.
  - AzureBackup, HDInsight 등과 같은 서비스의 경우 FQDN 태그가 있는 애플리케이션 규칙을 사용합니다.
  - 다른 프로토콜의 경우 FQDN 필터링에 네트워크 규칙을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Firewall DNS 설정](dns-settings.md)
