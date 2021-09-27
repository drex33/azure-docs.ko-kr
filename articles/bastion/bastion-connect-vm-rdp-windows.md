---
title: RDP를 사용하여 Windows VM에 커넥트
titleSuffix: Azure Bastion
description: Azure Bastion 사용하여 RDP를 사용하여 Windows VM에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: 1a1423a0587a64452671e74a210baa9d2ad16ea8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634553"
---
# <a name="create-an-rdp-connection-to-a-windows-vm-using-azure-bastion"></a>Azure Bastion 사용하여 Windows VM에 대한 RDP 연결 만들기

이 문서에서는 Azure Portal 통해 Azure 가상 네트워크에 있는 Windows VM에 대한 RDP 연결을 안전하고 원활하게 만드는 방법을 보여 줍니다. Azure Bastion을 사용하는 경우 VM에는 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다. SSH를 사용하여 Windows VM에 연결할 수도 있습니다. 자세한 내용은 [Windows VM에 대한 SSH 연결 만들기를 참조하세요.](bastion-connect-vm-ssh-windows.md)

Azure Bastion은 프로비저닝된 가상 네트워크의 모든 VM에 대한 안전한 연결을 제공합니다. Azure Bastion을 사용하면 가상 머신에서 RDP/SSH를 사용하여 안전한 액세스 권한을 계속 제공하며 RDP/SSH 포트가 외부 환경에 노출되는 상황으로부터 보호합니다. 자세한 내용은 [Azure Bastion이란?](bastion-overview.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 기준을 충족하는지 확인합니다.

* Bastion 호스트가 있는 VNet이 이미 설치되어 있습니다.

  * VM이 있는 가상 네트워크에 대해 Azure Bastion 호스트를 설정했는지 확인합니다. Bastion 서비스가 가상 네트워크에 프로비저닝되고 배포되면 이를 사용하여 이 가상 네트워크의 모든 VM에 연결할 수 있습니다. 
  * Azure Bastion 호스트를 설정하려면 [베스천 호스트 만들기](tutorial-create-host-portal.md#createhost)를 참조하세요. 사용자 지정 포트 값을 구성하려는 경우 Bastion을 구성할 때 표준 SKU를 선택해야 합니다.

* 가상 네트워크의 Windows 가상 머신

### <a name="required-roles"></a>필요한 역할

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="ports"></a>포트

Windows VM에 연결하려면 Windows VM에서 다음 포트가 열려 있어야 합니다.

*   인바운드 포트: RDP(3389) ***또는***
*   인바운드 포트: 사용자 지정 값(Azure Bastion 통해 VM에 연결할 때 이 사용자 지정 포트를 지정해야 합니다.)

> [!NOTE]
> 사용자 지정 포트 값을 지정하려면 표준 SKU를 사용하여 Azure Bastion 구성해야 합니다. 기본 SKU에서는 사용자 지정 포트를 지정할 수 없습니다. 표준 SKU는 현재 미리 보기 상태입니다.
>

## <a name="connect"></a><a name="rdp"></a>연결

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.
