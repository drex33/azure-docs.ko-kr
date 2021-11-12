---
title: Azure VMware Solution by CloudSimple 2020년 9월 업데이트
description: 이 문서에서는 이 유지 관리 작업 중에 발생하는 상황과 프라이빗 클라우드 변경 내용에 대해 알아봅니다.
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: eea32da3da1748fb1049fb32e6af8dd2467b4f60
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339222"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Azure VMware Solution by CloudSimple 2020년 9월 업데이트

Azure VMware Solution 서비스에 대한 중요 업데이트는 9월에 수행될 예정입니다. 유지 관리의 일환으로 전송되는 메일 알림은 유지 관리의 타임라인을 포함합니다. 이 문서에서는 이 유지 관리 작업 중에 발생하는 상황과 프라이빗 클라우드 변경 내용에 대해 알아봅니다.

> [!NOTE]
> 이것은 무중단 업그레이드입니다. 업그레이드하는 동안 중복 구성 요소 중 하나가 중단된 것을 볼 수 있습니다.

## <a name="vmware-infrastructure-upgrade"></a>VMware 인프라 업그레이드

프라이빗 클라우드의 VMware 인프라가 최신 버전으로 업데이트됩니다. 여기에는 프라이빗 클라우드의 vCenter, ESXi, NSX 및 HCX(하이브리드 클라우드 확장) 구성 요소(배포된 경우)에 대한 업데이트가 포함됩니다.

업그레이드하는 동안 업그레이드 작업을 위해 노드가 유지 관리 모드로 전환되기 전에 새 노드가 프라이빗 클라우드에 추가됩니다. 이를 통해 프라이빗 클라우드의 용량과 프라이빗 클라우드의 가용성이 업그레이드 프로세스 중에 유지됩니다. VMware 구성 요소를 업그레이드하는 동안 vCenter 웹 UI에 경보가 표시되는 것을 볼 수 있습니다. 경보는 서비스 운영 팀에서 수행하는 유지 관리 작업의 일부입니다.

**구성 요소 버전**

- ESXi 6.7U3
- vCenter 6.7U3
- vSAN 6.7
- NSX Data Center 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>데이터 센터 업데이트

이 업데이트에는 데이터 센터 인프라에 대한 업데이트가 포함됩니다. 무중단 업데이트는 유지 관리 기간 동안 수행됩니다. 업데이트 프로세스 동안 중복성이 감소합니다. 프라이빗 클라우드 VMware 인프라, ExpressRoute 회로, GlobalReach 연결 및 링크 가용성 중 하나와 관련된 모든 사이트 간 VPN 디바이스에서 경고가 생성될 수 있습니다. 업데이트의 일부로 구성 요소가 다시 부팅되기 때문에 이것은 정상적인 것입니다.

-   사이트 간 VPN이 단일 인스턴스(비 HA)로 배포된 경우 VPN 연결을 다시 설정해야 할 수 있습니다.

-   지점 및 사이트 간 VPN 연결을 사용하는 경우 VPN 연결을 다시 설정해야 합니다.

## <a name="post-update"></a>사후 업데이트

업데이트가 완료되면 최신 버전의 VMware 구성 요소가 표시됩니다. 문제가 있거나 질문이 있는 경우 [지원 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 열어 지원 팀에 문의하세요.
