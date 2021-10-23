---
title: 공유 리소스가 있는 랩 만드는 방법 | Azure Lab Services
description: 학생들 간에 공유되는 리소스가 필요한 랩을 만드는 방법을 알아봅니다.
author: emaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: efd33b95bd1a32c73712c7f094107717b4e5b973
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225778"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>Azure Lab Services에서 공유 리소스가 있는 랩 만드는 방법

경우에 따라 교실 랩을 만들 때 랩에서 모든 학생들 간에 공유해야 하는 리소스가 있을 수 있습니다.  예를 들어 데이터베이스 클래스에 대한 라이선스 서버 또는 SQL Server가 있습니다.  이 문서에서는 랩에서 공유 리소스를 사용하도록 설정하는 단계를 설명합니다.  또한 해당 공유 리소스에 대한 액세스를 제한하는 방법에 대해서도 설명합니다.

## <a name="architecture"></a>Architecture

아래 다이어그램에 표시된 것처럼 랩을 갖춘 랩 계정이 있습니다.  랩 계정에는 VNett 피어링 설정이 있으므로 랩의 가상 네트워크가 공유 리소스의 네트워크에 연결됩니다.  아래 다이어그램에는 IP 범위가 겹치지 않는 두 개의 가상 네트워크가 있습니다.  이러한 IP 범위는 단순한 예제일 뿐입니다.  또한 공유 리소스 가상 네트워크는 랩 계정과 동일한 구독에 있습니다.

![공유 리소스 아키텍처를 사용하는 랩 서비스](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>공유 리소스 설정

랩을 만들기 전에 공유 리소스에 대한 가상 네트워크를 만들어야 합니다.  가상 네트워크를 만드는 방법에 관한 자세한 내용은 [가상 네트워크 만들기](../virtual-network/quick-create-portal.md)를 참조하세요.  가상 네트워크 범위가 랩 컴퓨터의 IP 주소와 겹치지 않도록 계획해야 합니다.  네트워크를 계획하는 방법에 대한 자세한 내용은 [가상 네트워크 계획](../virtual-network/virtual-network-vnet-plan-design-arm.md) 문서를 참조하세요. 이 예제에서 공유 리소스는 범위가 10.2.0.0/16인 가상 네트워크에 있습니다.  아직 만들지 않은 경우 공유 리소스를 보유할 [서브넷을 만듭니다](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).  이 예제에서는 10.2.0.0/24 범위를 사용하지만 네트워크 요구 사항에 따라 범위가 달라질 수 있습니다.

공유 리소스는 가상 머신 또는 Azure 제공 서비스에서 실행되는 소프트웨어일 수 있습니다. 공유 리소스는 개인 IP 주소를 통해 사용할 수 있어야 합니다.  개인 IP를 통해서만 공유 리소스를 사용할 수 있도록 설정하여 해당 공유 리소스에 대한 액세스를 제한합니다.

또한 이 다이어그램은 학생 VM에서 들어오는 트래픽을 제한하는 데 사용할 수 있는 NSG(네트워크 보안 그룹)를 보여 줍니다.  예를 들어 학생 VM의 IP 주소의 트래픽은 하나의 공유 리소스에만 액세스할 수 있고 다른 리소스에는 액세스할 수 없음을 명시하는 보안 규칙을 작성할 수 있습니다.  보안 규칙을 설정하는 방법에 대한 자세한 내용은 [네트워크 보안 그룹 관리](../virtual-network/manage-network-security-group.md#work-with-security-rules)를 참조하세요. 공유 리소스에 대한 액세스를 특정 랩으로 제한하려면 [랩 계정의 랩 설정](manage-labs.md#view-labs-in-a-lab-account)에서 랩의 IP 주소를 가져오고 해당 IP 주소에서 액세스할 경우에만 허용하도록 인바운드 규칙을 설정합니다.  해당 IP 주소에 대해 49152~65535 포트를 허용해야 합니다.  필요에 따라 [가상 머신 풀 페이지](how-to-set-virtual-machine-passwords.md)를 사용하여 학생의 VM에 대한 개인 IP 주소를 찾을 수 있습니다.

공유 리소스가 필요한 소프트웨어를 실행하는 Azure 가상 머신인 경우 가상 머신에 대한 기본 방화벽 규칙을 수정해야 할 수 있습니다.

### <a name="tips-for-shared-resources---license-server"></a>공유 리소스에 대한 팁 - 라이선스 서버
보다 일반적인 공유 리소스 중 하나는 라이선스 서버입니다. 다음은 라이선스 서버를 성공적으로 설정하는 방법에 대한 몇 가지 팁입니다.
#### <a name="server-region"></a>서버 지역
라이선스 서버는 랩으로 피어링된 가상 네트워크에 연결되어야 하므로 랩 계정과 동일한 지역에 있어야 합니다.

#### <a name="static-private-ip-and-mac-address"></a>정적 개인 IP 및 MAC 주소
기본적으로 가상 머신에는 동적 개인 IP가 있습니다. [소프트웨어를 설정하기 전에 개인 IP를 정적으로 설정](../virtual-network/ip-services/virtual-networks-static-private-ip-arm-pportal.md)해야 합니다. 그러면 개인 IP와 MAC 주소가 정적으로 설정됩니다.  

#### <a name="control-access"></a>액세스 제어
라이선스 서버에 대한 액세스 제어가 핵심입니다.  VM을 설정한 후에도 계속해서 유지 관리, 문제 해결 및 업데이트를 위해 액세스해야 합니다.  이를 수행하는 몇 가지 방법이 있습니다.
- [Azure Security Center 내에서 JIT(Just-in-time) 액세스를 설정합니다.](../security-center/security-center-just-in-time.md?tabs=jit-config-asc%252cjit-request-asc)
- [네트워크 보안 그룹을 설정하여 액세스를 제한합니다.](../virtual-network/network-security-groups-overview.md)
- [라이선스 서버에 안전하게 액세스할 수 있도록 하는 방법을 설정합니다.](https://azure.microsoft.com/services/azure-bastion/)

## <a name="lab-account"></a>랩 계정

공유 리소스를 사용하려면 랩 계정이 [피어링된 가상 네트워크](how-to-connect-peer-virtual-network.md)를 사용하도록 설정되어 있어야 합니다.  이 경우 공유 리소스를 보유하는 가상 네트워크로 피어링하게 됩니다.

>[!WARNING]
>랩 계정이 공유 리소스 가상 네트워크에 **피어링되면** 클래스의 랩을 만들어야 합니다.  
템플릿 컴퓨터

랩 계정이 가상 네트워크에 피어링되면 이제 템플릿 컴퓨터에서 공유 리소스에 액세스할 수 있어야 합니다.  액세스 중인 공유 리소스에 따라 방화벽 규칙을 업데이트해야 할 수 있습니다.