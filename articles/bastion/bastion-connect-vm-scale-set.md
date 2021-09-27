---
title: Azure Bastion을 사용하여 Windows Virtual Machine Scale Sets에 연결
description: Azure Bastion을 사용하여 Azure Virtual Machine Scale Sets에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: e57ee80fa7727a6f5e7e1ec59857746cb4b8d590
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620658"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Azure Bastion을 사용하여 가상 머신 확장 집합에 연결

이 문서에서는 Azure Bastion을 사용하여 Azure 가상 네트워크의 Windows 가상 머신 확장 집합 인스턴스에 안전하고 원활하게 RDP하는 방법을 보여 줍니다. Azure Portal에서 직접 가상 머신 확장 집합 인스턴스에 연결할 수 있습니다. Azure Bastion을 사용하는 경우 VM에는 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다. Azure Bastion에 대한 자세한 내용은 [개요](bastion-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

가상 머신 확장 집합이 상주하는 가상 네트워크에 대해 Azure Bastion 호스트를 설정했는지 확인합니다. 자세한 내용은 [Azure Bastion 호스트 만들기](./tutorial-create-host-portal.md)를 참조하세요. Bastion 서비스가 가상 네트워크에서 프로비저닝되어 배포되면 서비스를 사용하여 이 가상 네트워크의 가상 머신 확장 집합 인스턴스에 연결할 수 있습니다. Bastion은 RDP를 사용하여 Windows 가상 머신 확장 집합에 연결하고 SSH를 사용하여 Linux 가상 머신 확장 집합에 연결한다고 가정합니다. Linux VM에 연결에 대한 자세한 내용은 [VM에 연결 - Linux](bastion-connect-vm-ssh-linux.md)을 참조하세요.

## <a name="connect-using-rdp"></a><a name="rdp"></a>RDP를 사용하여 연결

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결할 가상 머신 확장 집합으로 이동합니다.

   ![이동](./media/bastion-connect-vm-scale-set/1.png)
2. 연결할 가상 머신 확장 집합 인스턴스로 이동한 다음 **연결** 을 선택합니다. RDP 연결을 사용하는 경우 가상 머신 확장 집합은 Windows 가상 머신 확장 집합이어야 합니다.

   ![가상 머신 확장 집합](./media/bastion-connect-vm-scale-set/2.png)
3. **연결** 을 선택하면 세 개의 탭(RDP, SSH, Bastion)이 있는 사이드바가 나타납니다. 사이드바에서 **Bastion** 탭을 선택합니다. 가상 네트워크에 대해 Bastion을 프로비저닝하지 않은 경우 Bastion 구성 링크를 선택할 수 있습니다. 구성 지침은 [Bastion 구성](./tutorial-create-host-portal.md)을 참조하세요.

   ![Bastion 탭](./media/bastion-connect-vm-scale-set/3.png)
4. Bastion 탭에서 가상 머신 확장 집합의 사용자 이름 및 암호를 입력하고 **계속** 을 선택합니다.

   ![연결](./media/bastion-connect-vm-scale-set/4.png)
5. 이 가상 머신에 대한 RDP 연결은 포트 443 및 Bastion 서비스를 사용하여 Azure Portal에서 직접 열립니다(HTML5를 통해).

## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.