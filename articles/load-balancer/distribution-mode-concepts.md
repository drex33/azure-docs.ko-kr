---
title: Azure Load Balancer 배포 모드
description: Azure Load Balancer의 다양한 배포 모드에 대한 학습을 시작하세요.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: 71566a843c98d9bf6393d8e344161b0969190185
ms.sourcegitcommit: 8178cd2d9a47a67bb324483bd0879a57591706a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133109988"
---
# <a name="azure-load-balancer-distribution-modes"></a>Azure Load Balancer 배포 모드

Azure Load Balancer는 부하가 분산된 애플리케이션에 대한 연결을 라우팅하기 위한 두 가지 배포 모드를 지원합니다.

* 해시 기반
* 원본 IP 선호도

## <a name="hash-based"></a>해시 기반

Azure Load Balancer의 기본 배포 모드는 5 튜플 해시입니다. 

튜플은 다음으로 구성됩니다.
* **원본 IP**
* **원본 포트**
* **대상 IP**
* **대상 포트**
* **프로토콜 유형**

해시는 사용 가능한 서버에 트래픽을 매핑하는 데 사용됩니다. 알고리즘은 전송 세션 내에서만 고정성을 제공합니다. 동일한 세션에 있는 패킷은 부하가 분산된 엔드포인트 뒤의 동일한 데이터 센터 IP로 보내집니다. 클라이언트가 동일한 원본 IP에서 새 세션을 시작하는 경우 원본 포트가 변경되어 트래픽이 다른 데이터 센터 엔드포인트로 이동합니다.

![5 튜플 해시 기반 배포 모드](./media/distribution-mode-concepts/load-balancer-distribution.png)

해시 기반 모드에는 하나의 구성 유형이 있습니다.

* **없음(해시 기반)** - 동일한 클라이언트의 후속 요청이 모든 가상 머신에서 처리되도록 지정합니다.

## <a name="source-ip-affinity"></a>원본 IP 선호도

이 배포 모드는 세션 선호도 또는 클라이언트 IP 선호도라고도 합니다. 모드는 2 튜플(원본 ID 및 대상 IP) 또는 3 튜플(원본 ID, 대상 IP 프로토콜 유형) 해시를 사용하여 사용 가능한 서버에 트래픽을 매핑합니다. 

원본 IP 선호도를 사용하면 동일한 클라이언트 컴퓨터에서 시작된 연결이 동일한 데이터 센터 엔드포인트로 이동합니다.

다음 그림은 2 튜플 구성을 보여 줍니다. 2 튜플이 부하 분산 장치를 통해 가상 머신 1(VM1)에 도달하는 방식을 확인합니다. 그런 다음 VM1이 VM2와 VM3에 의해 백업됩니다.

![2 튜플 세션 선호도 배포 모드](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

원본 IP 선호도 모드에는 두 가지 구성 유형이 있습니다.

* **클라이언트 IP(원본 IP 선호도 2 튜플)** - 동일한 클라이언트 IP 주소의 연속적인 요청이 동일한 가상 머신에서 처리되도록 지정합니다.
* **클라이언트 IP 및 프로토콜(원본 IP 선호도 3 튜플)** - 동일한 클라이언트 IP 주소 및 프로토콜 조합의 연속적인 요청이 동일한 가상 머신에서 처리되도록 지정합니다.

## <a name="use-cases"></a>사용 사례

클라이언트 IP 및 프로토콜과의 원본 IP 선호도(원본 IP 선호도 3 튜플)는 Azure Load Balancer와 RD 게이트웨이(원격 데스크톱 게이트웨이) 간의 비호환성 문제를 해결합니다. 

또 다른 사용 사례 시나리오는 미디어 업로드입니다. 데이터 업로드는 UDP를 통해 이루어 지지만 제어 평면은 TCP를 통해 이루어집니다.

* 클라이언트는 부하가 분산된 공용 주소에 대한 TCP 세션을 시작하고 특정 DIP로 연결됩니다. 채널은 활성 상태로 유지되어 연결 상태를 모니터링합니다.
* 동일한 클라이언트 컴퓨터의 새 UDP 세션이 부하가 분산된 동일한 공개 엔드포인트로 시작됩니다. 연결은 이전 TCP 연결과 동일한 DIP 엔드포인트로 지정됩니다. TCP를 통해 제어 채널을 유지하면서 미디어 업로드를 높은 처리량으로 실행할 수 있습니다.

> [!NOTE]
> 가상 머신을 제거하거나 추가하여 백 엔드 풀 멤버를 Load Balancer 변경하면 클라이언트 요청의 배포가 다시 처리됩니다. 기존 클라이언트의 새 연결이 동일한 서버로 수행된다고 확신할 수 없습니다. 또한 원본 IP 선호도 배포 모드를 사용하면 트래픽의 균일하지 않은 배포가 발생할 수 있습니다. 프록시 뒤에서 실행되는 클라이언트는 하나의 고유한 클라이언트 애플리케이션으로 보일 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Load Balancer의 배포 모드를 구성하는 방법에 대한 자세한 내용은 [Azure Load Balancer에 대한 배포 모드 구성](load-balancer-distribution-mode.md)을 참조하세요.

