---
title: Azure DevTest Labs 가상 컴퓨터에 대 한 브라우저 연결 사용
description: Azure 방호와 DevTest Labs를 통합 하 여 브라우저를 통해 랩 Vm (가상 머신)에 액세스할 수 있도록 합니다.
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 876a72548c70f3e7717c75973edee802e584fca2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442981"
---
# <a name="enable-browser-connection-to-devtest-labs-vms"></a>DevTest Labs Vm에 대 한 브라우저 연결 사용

Azure DevTest Labs는 [Azure 방호](../bastion/index.yml) 와 통합 되어 브라우저를 통해 랩 vm (가상 머신)에 연결할 수 있습니다. 랩 소유자는 Azure 방호를 통해 모든 랩 Vm에 대 한 브라우저 액세스를 사용 하도록 설정할 수 있습니다.

Azure 방호는 TLS (전송 계층 보안)를 통해 Azure Portal에서 직접 안전 하 고 원활한 RDP (원격 데스크톱 프로토콜) 및 SSH (보안 셸) 연결을 제공 합니다. 브라우저를 통해 랩 Vm에 연결 하는 데 다른 클라이언트, 에이전트 또는 소프트웨어가 필요 하지 않습니다. Vm에 공용 IP 주소가 필요 하지 않습니다.

이 문서에서는 DevTest Labs Vm에 대 한 Azure 방호 브라우저 연결을 사용 하도록 설정 하는 두 가지 다른 방법을 설명 합니다.

- 랩 및 해당 Vm에 대 한 새 Azure 방호 지원 가상 네트워크를 만들 수 있습니다.
- 기존 랩 가상 네트워크에 Azure 방호를 배포할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- DevTest Labs에서 [랩을 만들거나 만드세요](tutorial-create-custom-lab.md#create-a-lab) .
- Azure 방호 브라우저 액세스를 사용 하려면 랩 사용자에 게 azure 방호 호스트 및 Azure 방호가 구성 된 랩 가상 네트워크에 대 한 **독자** 역할이 있어야 합니다.

## <a name="option-1-connect-a-lab-to-an-azure-bastion-enabled-virtual-network"></a>옵션 1: Azure 방호 지원 가상 네트워크에 랩 커넥트

먼저, Azure 방호 서브넷과 그 안에 있는 다른 서브넷을 사용 하 여 새 가상 네트워크를 만듭니다. Azure 방호 서브넷은 Azure가 아닌 리소스를 만들도록 허용 하지 않으므로 랩 Vm을 만들기 위한 다른 서브넷이 필요 합니다.

1. Azure Portal에서 **가상 네트워크** 를 검색 하 고 선택 합니다.
1. **가상 네트워크** 페이지 맨 위에 있는 **+ 만들기** 를 선택 합니다.
1. **가상 네트워크 만들기** 화면에서 새 가상 네트워크의 **이름을** 입력 하 고 랩과 동일한 **구독**, **리소스 그룹** 및 **지역을** 선택 합니다.
1. 완료되면 **다음: IP 주소** 를 선택합니다.
1. **IP 주소** 탭에는 이미 하나의 서브넷 ( **기본값**)이 있습니다. **서브넷 추가** 를 선택합니다.
1. **서브넷 추가** 창에서 **이름** 아래에 *AzureBastionSubnet* 을 입력 합니다.
1. **서브넷 주소 범위** 에서 가상 네트워크의 주소 공간 내에 있지만 기본 서브넷과 겹치지 않는 주소 범위를 입력 합니다. 필요한 경우 **가상 네트워크 만들기** 페이지의 빈 필드에 새 주소 공간을 추가할 수 있습니다.
1. **추가** 를 선택합니다.

   ![AzureBastionSubnet 서브넷 만들기를 보여 주는 스크린샷](media/enable-browser-connection-lab-virtual-machines/create-subnet.png)

1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과하면 **만들기** 를 선택합니다.
1. 새 가상 네트워크를 만든 후에는 해당 페이지로 이동 하 고, 왼쪽 탐색 영역에서 **서브넷** 을 선택 하 고, **기본** 및 **AzureBastionSubnet** 두 개의 서브넷이 있는지 확인 합니다.

   ![Azure 방호 가상 네트워크에서 두 개의 서브넷을 보여 주는 스크린샷](media/enable-browser-connection-lab-virtual-machines/second-subnet.png)

다음으로 랩을 새 가상 네트워크에 연결 합니다.

1. 랩 **개요** 페이지의 왼쪽 탐색 영역에서 **구성 및 정책** 을 선택 합니다.
1. **구성 및 정책** 페이지의 왼쪽 탐색 영역에서 **외부 리소스** 아래에 있는 **가상 네트워크** 를 선택 합니다.
1. **구성 및 정책에서 | 가상 네트워크** 페이지 위쪽에서 **추가** 를 선택 합니다.
1. **가상 네트워크** 페이지에서 **가상 네트워크 선택** 을 선택 합니다.
1. **가상 네트워크 선택** 페이지에서 방금 만든 Azure 방호 사용 가상 네트워크를 선택 합니다.
1. **가상 네트워크** 페이지의 위쪽에서 **저장** 을 선택 합니다.
1. **구성 및 정책에서 | 가상 네트워크** 페이지에서 랩에서 이전 가상 네트워크를 제거 합니다. 가상 **네트워크 옆의 ...를** 선택 하 고 **삭제** 를 선택한 다음 **예** 를 선택 합니다. 

   ![이전 랩 가상 네트워크를 삭제 하는 방법을 보여 주는 스크린샷](media/enable-browser-connection-lab-virtual-machines/add-virtual-network.png)

비 Azure 방호 서브넷에서 VM 만들기를 사용 하도록 설정 합니다.

1. **구성 및 정책에서 | 가상 네트워크** 페이지에서 Azure 방호 사용 가상 네트워크를 선택 합니다.
1. **가상 네트워크** 페이지에서 **AzureBastionSubnet** 서브넷과 **기본** 서브넷이 모두 표시 되는지 확인 합니다. 두 서브넷이 모두 표시 되지 않으면 페이지를 닫았다가 다시 엽니다.
1. **기본** 서브넷을 선택 합니다.
1. **랩 서브넷** 화면의 **가상 컴퓨터를 만드는 데 사용** 에서 **예** 를 선택 하 고 **저장** 을 선택 합니다. 이제 랩 가상 네트워크의 기본 서브넷에서 Vm을 만들 수 있습니다.

   ![기본 서브넷에서 V M 생성을 사용 하도록 설정 하는 것을 보여 주는 스크린샷](./media/enable-browser-connection-lab-virtual-machines/enable-vm-creation-subnet.png)

## <a name="option-2-deploy-azure-bastion-in-a-labs-existing-virtual-network"></a>옵션 2: 랩의 기존 가상 네트워크에 Azure 방호 배포

먼저 랩의 기존 가상 네트워크에서 새 Azure 방호 서브넷을 만듭니다.

1. Azure Portal에서 **가상 네트워크** 를 검색 하 고 선택 합니다.
1. **가상 네트워크** 페이지의 목록에서 랩의 기존 가상 네트워크를 선택 합니다.
1. 가상 네트워크 페이지의 왼쪽 탐색 영역에서 **서브넷** 을 선택 합니다.
1. **서브넷** 페이지의 상단 메뉴에서 **+ 서브넷** 을 선택 합니다.
1. **서브넷 추가 화면** 에서 **이름** 아래에 *AzureBastionSubnet* 을 입력 합니다.
1. **서브넷 주소 범위** 에서 가상 네트워크의 주소 공간 내에 있지만 기존 랩 서브넷과 겹치지 않는 주소 범위를 입력 합니다.
   >[!TIP]
   >이 대화 상자를 취소 하 고, 가상 네트워크의 왼쪽 탐색 **영역에서 주소 공간** 을 선택 하 고, 서브넷에 대 한 새 주소 공간을 만들어야 할 수도 있습니다.
1. **저장** 을 선택합니다.

   ![기존 가상 네트워크에서 서브넷을 추가 하는 방법을 보여 주는 스크린샷](./media/enable-browser-connection-lab-virtual-machines/add-subnet.png)

다음으로 새 Azure 방호 서브넷에 Azure 방호 호스트를 배포 합니다.

1. Azure Portal에서 **베스천** 을 검색하거나 선택합니다.
1. **Bastions** 페이지의 맨 위에서 **+ 만들기** 를 선택 합니다.
1. **요새 만들기** 페이지에서 **이름을** 입력 하 고 랩으로 동일한 **구독**, **리소스 그룹** 및 **지역을** 선택 합니다.
1. **가상 네트워크** 아래의 드롭다운 목록에서 랩의 가상 네트워크를 선택 하 고 **서브넷** 아래에서 **AzureBastionSubnet** 이 선택 되어 있는지 확인 합니다.
1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과하면 **만들기** 를 선택합니다.

   ![기존 가상 네트워크에 Azure 방호를 배포 하는 방법을 보여 주는 스크린샷](./media/enable-browser-connection-lab-virtual-machines/create-bastion.png)

## <a name="connect-to-lab-vms-through-azure-bastion"></a>Azure 방호를 통해 랩 Vm에 커넥트

랩 가상 네트워크에 Azure 방호를 배포한 후 랩에 대 한 브라우저 연결을 사용 하도록 설정 합니다.

1. 랩 **개요** 페이지에서 **구성 및 정책** 을 선택한 다음 **설정** 에서 **브라우저 연결** 을 선택 합니다.
1. **브라우저 연결** 페이지에서 **켜기** 를 선택 합니다.
1. 페이지 위쪽에서 **저장** 을 선택합니다.

   ![브라우저 연결을 사용 하도록 설정 하는 방법을 보여 주는 스크린샷](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

Azure 방호를 통해 랩 VM에 연결 하려면 다음을 수행 합니다.

1. 랩 **개요** 페이지의 **내 가상 컴퓨터** 에서 랩 VM을 선택 합니다.
1. VM 페이지의 맨 위에서 **브라우저 연결** 을 선택 합니다.
1. **브라우저 연결** 창에서 VM의 사용자 이름 및 암호를 입력 하 고 **커넥트** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계
- [Azure Bastion 정보](../bastion/bastion-overview.md)
- [랩에 VM 추가](devtest-lab-add-vm.md)
