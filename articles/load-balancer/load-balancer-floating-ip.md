---
title: Azure Load Balancer 부동 IP 구성
description: Azure Load Balancer 부동 IP의 개요
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/2/2021
ms.author: allensu
ms.openlocfilehash: 107037e54f62618e6ce7623f6170b402f57401b4
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133569645"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer 부동 IP 구성

부하 분산 장치는 몇 가지 기능을 UDP 및 TCP 애플리케이션 모두에 제공합니다.

## <a name="floating-ip"></a>부동 IP

일부 애플리케이션 시나리오는 백 엔드 풀의 단일 VM에서 여러 애플리케이션 인스턴스가 동일한 포트를 사용하는 것을 선호하거나 필요로 합니다. 포트 재사용의 일반적인 예는 다음과 같습니다. 
- 고가용성을 위한 클러스터링
- 네트워크 가상 어플라이언스
- 다시 암호화하지 않고 여러 TLS 엔드포인트 노출 

여러 규칙 전반에 백 엔드 포트를 재사용하려면 규칙 정의에 부동 IP를 사용하도록 설정해야 합니다.

부동 IP를 사용하도록 설정하면 Azure는 IP 주소 매핑을 백 엔드 인스턴스의 IP 대신 Load Balancer 프런트 엔드의 프런트 엔드 IP 주소로 변경합니다. 

부동 IP가 없으면 Azure는 VM 인스턴스의 IP를 노출합니다. 부동 IP를 사용하도록 설정하면 Load Balancer의 프런트 엔드 IP에 매핑되는 IP 주소가 변경되어 추가적인 유연성이 제공됩니다. [여기](load-balancer-multivip-overview.md)를 참조하세요.

부동 IP는 Azure Portal, REST API, CLI, PowerShell 또는 기타 클라이언트를 통해 Load Balancer 규칙에서 구성할 수 있습니다. 규칙 구성 외에도 부동 IP를 활용하려면 가상 머신의 게스트 OS도 구성해야 합니다.

## <a name="floating-ip-guest-os-configuration"></a>부동 IP 게스트 OS 구성
백 엔드 풀의 각 VM에 대해, Windows 명령 프롬프트에서 다음 명령을 실행합니다.

VM에 있는 인터페이스 이름 목록을 가져오려면 다음 명령을 입력합니다.

```console
netsh interface show interface 
```

VM NIC (Azure 관리형)의 경우, 다음 명령을 입력합니다.

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
```

(interfacename을 이 인터페이스의 이름으로 바꿉니다)

추가한 각 루프백 인터페이스에 대해 다음 명령을 반복합니다.

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
```

(interfacename을 이 루프백 인터페이스의 이름으로 바꿉니다)

```console
netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
```

(interfacename을 이 루프백 인터페이스의 이름으로 바꿉니다)

> [!IMPORTANT]
> 루프백 인터페이스에 대한 구성은 게스트 OS 내에서 수행됩니다. 이 구성은 Azure에서 수행하거나 관리하지 않습니다. 이 구성 없이는 규칙이 작동하지 않습니다.

## <a name="limitations"></a><a name = "limitations"></a>제한 사항

- 부동 IP는 현재 부하 분산 시나리오의 보조 IP 구성에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- Load Balancer를 사용하여 시작하려면 [공용 표준 Load Balancer 만들기](quickstart-load-balancer-standard-public-portal.md)를 참조하세요.
- [Azure Load Balancer 아웃바운드 연결](load-balancer-outbound-connections.md)에 대해 자세히 알아보세요.
- [Azure Load Balancer에 대해 자세히 알아보세요](load-balancer-overview.md).
- [상태 프로브](load-balancer-custom-probe-overview.md)에 대해 자세히 알아보세요.
- [표준 Load Balancer 진단](load-balancer-standard-diagnostics.md)에 대해 자세히 알아보세요.
- [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)에 대해 자세히 알아보세요.
