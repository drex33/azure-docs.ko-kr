---
title: RDP를 사용 하 여 Linux VM에 커넥트
titleSuffix: Azure Bastion
description: RDP를 사용 하 여 Linux VM에 연결 하는 데 Azure 방호를 사용 하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: 9fbe16756bc178ed012af7f96e937614b0b3a855
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705683"
---
# <a name="create-an-rdp-connection-to-a-linux-vm-using-azure-bastion-preview"></a>Azure 방호 (미리 보기)을 사용 하 여 Linux VM에 대 한 RDP 연결 만들기

이 문서에서는 Azure Portal를 통해 직접 Azure virtual network에 있는 Linux Vm에 대 한 RDP 연결을 안전 하 고 원활 하 게 만드는 방법을 보여 줍니다. Azure Bastion을 사용하는 경우 VM에는 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다. SSH를 사용 하 여 Linux VM에 연결할 수도 있습니다. 자세한 내용은 [LINUX VM에 대 한 SSH 연결 만들기](bastion-connect-vm-ssh-linux.md)를 참조 하세요.

Azure Bastion은 프로비저닝된 가상 네트워크의 모든 VM에 대한 안전한 연결을 제공합니다. Azure Bastion을 사용하면 가상 머신에서 RDP/SSH를 사용하여 안전한 액세스 권한을 계속 제공하며 RDP/SSH 포트가 외부 환경에 노출되는 상황으로부터 보호합니다. 자세한 내용은 [Azure Bastion이란?](bastion-overview.md)을 참조하세요.

> [!NOTE]
> RDP를 사용 하 여 Linux 가상 머신에 연결 하려면 현재 미리 보기 상태인 Azure 방호 표준 SKU가 필요 합니다.
>

RDP를 사용 하 여 Linux 가상 머신에 연결 하기 위해 Azure 방호를 사용 하는 경우 인증에 사용자 이름/암호를 사용 해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 기준을 충족하는지 확인합니다.

VM이 있는 가상 네트워크에 대해 Azure Bastion 호스트를 설정했는지 확인합니다. 자세한 내용은 [Azure Bastion 호스트 만들기](tutorial-create-host-portal.md)를 참조하세요. Bastion 서비스가 가상 네트워크에 프로비전되고 배포되면 이를 사용하여 이 가상 네트워크의 모든 VM에 연결할 수 있습니다.

Linux 가상 머신에 RDP 하려면 Linux 가상 머신에서 xrdp를 설치 하 고 구성 해야 합니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [Linux에서 xrdp 사용](../virtual-machines/linux/use-remote-desktop.md)을 참조 하세요.

### <a name="required-roles"></a>필요한 역할

연결하려면 다음 역할이 필요합니다.
* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="ports"></a>포트

RDP를 통해 Linux VM에 연결 하려면 VM에서 다음 포트를 열어야 합니다.
*   인바운드 포트: RDP (3389) *또는*
*   인바운드 포트: 사용자 지정 값 (Azure 방호를 통해 VM에 연결할 때이 사용자 지정 포트를 지정 해야 합니다.)

### <a name="supported-configurations"></a>지원되는 구성

현재 Azure 방호는 **xrdp** 를 사용 하 여 RDP를 통해 Linux vm에 연결 하는 것만 지원 합니다.

## <a name="connect"></a><a name="rdp"></a>연결

[!INCLUDE [Connect to a Linux VM using RDP](../../includes/bastion-vm-rdp-linux.md)]
 
## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.
