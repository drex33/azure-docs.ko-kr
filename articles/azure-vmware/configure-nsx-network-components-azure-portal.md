---
title: Azure VMware 솔루션을 사용 하 여 NSX 네트워크 구성 요소 구성
description: Azure VMware 솔루션을 사용 하 여 NSX-T 네트워크 세그먼트를 구성 하는 방법을 알아봅니다.
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: 190ec1e93cb9fef795e47693f80e5e9296354b83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705677"
---
# <a name="configure-nsx-network-components-using-azure-vmware-solution"></a>Azure VMware 솔루션을 사용 하 여 NSX 네트워크 구성 요소 구성

Azure VMware 솔루션 사설 클라우드는 기본적으로 NSX와 함께 제공 됩니다. 사설 클라우드는 능동/ **능동** 모드로 NSX 게이트웨이를 미리 프로 비전 하 고 활성/대기 모드로 기본 NSX-t 계층-1 게이트웨이를 제공 합니다.  이러한 게이트웨이를 통해 세그먼트(논리적 스위치)를 연결하고 동-서 및 북-남 연결을 제공할 수 있습니다. 

Azure VMware 솔루션을 배포한 후 Azure Portal에서 필요한 NSX 개체를 구성할 수 있습니다.  VMware 관리자는 매일, NSX Manager에 익숙하지 않은 사용자를 대상으로 하는 NSX 작업에 대 한 간단한 보기를 제공 합니다.  

Azure VMware 솔루션 콘솔에서 NSX-T 구성 요소를 구성하는 4가지 옵션이 있습니다.

- **세그먼트** - NSX-T Manager 및 vCenter에 표시되는 세그먼트를 만들 수 있습니다. 자세한 내용은 [Azure Portal를 사용 하 여 NSX 세그먼트 추가](tutorial-nsx-t-network-segment.md#use-azure-portal-to-add-an-nsx-t-segment)를 참조 하세요.

- **DHCP** - DHCP를 사용하려는 경우 DHCP 서버 또는 DHCP 릴레이를 만듭니다.  자세한 내용은 [Azure Portal를 사용 하 여 DHCP 서버 또는 릴레이 만들기](configure-dhcp-azure-vmware-solution.md#use-the-azure-portal-to-create-a-dhcp-server-or-relay)를 참조 하세요.

- **포트 미러링** – 네트워크 문제를 해결하는 데 도움이 되도록 포트 미러링을 만듭니다. 자세한 내용은 [Azure Portal에서 포트 미러링 구성](configure-port-mirroring-azure-vmware-solution.md)을 참조 하세요.

- **DNS** – 확인을 위해 지정된 DNS 서버에 DNS 요청을 보내는 DNS 전달자를 만듭니다.  자세한 내용은 [Azure Portal에서 DNS 전달자 구성](configure-dns-azure-vmware-solution.md)을 참조 하세요.

>[!IMPORTANT]
>설명된 고급 설정 및 기타 NSX-T 기능을 사용할 수 있는 NSX-T Manager 콘솔에는 계속 액세스할 수 있습니다. 

