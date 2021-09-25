---
title: 네트워크 격리
description: Azure DevTest Labs 네트워크 격리에 대해 알아보세요.
ms.topic: how-to
ms.date: 08/25/2020
ms.openlocfilehash: 7da1dbddee6d4807969cda41550972912fc69910
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661757"
---
# <a name="network-isolation-in-devtest-labs"></a>DevTest Labs 네트워크 격리

[Azure 가상 네트워크 ](../virtual-network/virtual-networks-overview.md)는 보안 경계 역할을 하여 Azure 리소스를 공용 인터넷에서 격리합니다. 또한 Azure 가상 네트워크를 온-프레미스 네트워크에 조인하여 온-프레미스 리소스에 안전하게 연결할 수 있습니다. DevTest Labs에서 [모든 랩 가상 머신](devtest-lab-configure-vnet.md) 및 [환경을 네트워크에](connect-environment-lab-virtual-network.md) 격리하여 랩 리소스가 조직 네트워킹 정책을 따르도록 할 수 있습니다. 

또한 랩 소유자는 랩을 완전히 격리하도록 선택할 수 있습니다. 즉, 선택한 네트워크에 가상 머신 및 환경을 격리하는 것과 동시에 랩 스토리지 계정 및 구독에 생성된 키 자격 증명 모음을 격리할 수도 있습니다. 이 기사에서는 네트워크 격리 랩의 생성 과정을 안내합니다. 

또한 다음 문서를 검토하세요.

- [DevTest Lab의 랩 스토리지 계정 사용 방법](encrypt-storage.md)
- [DevTest Labs의 키 자격 증명 모음 사용 방법](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> 네트워크 격리는 현재 새 랩 생성에만 지원됩니다.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>랩 생성 중 네트워크 격리 활성화 단계

1. 랩을 생성하는 동안 **네트워킹** 탭으로 이동하세요.
1. 랩에서 생성할 **기본값** 네트워크를 선택하거나 드롭다운에서 기존 네트워크를 선택할 수 있습니다. 랩과 동일한 지역 및 구독에 있는 네트워크를 선택할 수 있습니다. 

    > [!div class="mx-imgBorder"]
    > ![랩 만들기](./media/network-isolation/create-lab.png)
1. 서브넷을 선택하세요.

    > [!div class="mx-imgBorder"]
    > ![서브넷 생성](./media/network-isolation/create-lab-subnet.png)
1. 랩 리소스(랩 스토리지 계정 및 키 자격 증명 모음)를 기본 네트워크로 격리하도록 선택하는 경우 추가 작업이 필요하지 않으며 향후의 리소스 격리를 랩에서 처리합니다.
 
    > [!div class="mx-imgBorder"]
    > ![네트워크 격리](./media/network-isolation/isolate-lab-resources.png)
1. 랩 리소스(랩 스토리지 계정 및 키 자격 증명 모음)를 선택한 기존 네트워크에 격리하도록 선택한 경우, 랩이 격리 모드에서 계속 작동하도록 하려면 랩 생성 후 다음 단계를 완료해야 합니다. 
 
    > [!div class="mx-imgBorder"]
    > ![리소스 격리](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > 랩 소유자는 랩에서 리소스를 구성 및/또는 생성하기 전에 이 단계를 완료해야 합니다.

### <a name="steps-to-follow-post-lab-creation"></a>랩 생성 이후 수행할 단계

1. 랩 홈페이지의 **개요** 페이지에서 **리소스 그룹** 을 선택하세요. 랩이 포함된 리소스 그룹의 **리소스 그룹** 페이지가 표시됩니다. 
 
   > [!div class="mx-imgBorder"]
   > ![Contoso 랩](./media/network-isolation/contoso-lab.png)
1. 연구소의 Azure Storage 계정을 선택하세요. 랩 스토리지 계정에 대한 명명 규칙은 a<*labNameWithoutInvalidCharacters*> *<4-digit number*>입니다. 예를 들어, 랩 이름이 contosolab인 경우 스토리지 계정 이름은 contosolab1234일 수 있습니다.
 
   > [!div class="mx-imgBorder"]
   > ![Contoso 테스트](./media/network-isolation/contoso-test.png)
1. 스토리지 계정에서 방화벽 및 가상 네트워크로 이동하여 '스토리지 계정에 대한 신뢰할 수 있는 Microsoft 서비스의 액세스 허용' 확인란이 선택되어 있는지 확인하세요. [DevTest Labs는 신뢰할 수 있는 Microsoft 서비스](../storage/common/storage-network-security.md#trusted-microsoft-services)이므로 이 옵션을 사용하면 네트워크 격리 모드에서 랩이 정상적으로 작동할 수 있습니다. 

   > [!div class="mx-imgBorder"]
   > ![Contoso 랩 방화벽](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. 그런 다음 **기존 가상 네트워크 추가** 를 클릭하고 랩을 생성하는 동안 선택한 가상 네트워크 및 서브넷을 선택한 다음 **사용** 을 클릭합니다. 

   > [!div class="mx-imgBorder"]
   > ![Contoso 나의 vnet](./media/network-isolation/contoso-lab-my-vnet.png)
5.  선택한 가상 네트워크에 대해 서비스 엔드포인트 사용이 성공적으로 설정되면 **추가** 를 클릭하세요. 

   > [!div class="mx-imgBorder"]
   > ![추가](./media/network-isolation/contoso-firewall-add.png)
 
이를 통해 Azure Storage는 추가된 가상 네트워크에서 인바운드 연결을 허용하고 랩이 네트워크 격리 모드에서 성공적으로 작동할 수 있도록 합니다. 

또한 이러한 단계를 자동화하여 여러 랩에 이 설정을 구성할 수 있습니다. 

[PowerShell 및 CLI를 사용하여 Azure Storage의 기본 네트워크 액세스 규칙 관리에 대해 자세히 알아보기](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="network-isolation-for-an-existing-lab"></a>기존 랩에 대한 네트워크 격리

랩 소유자는 기존 랩에 대해 네트워크 격리를 선택할 수 있습니다. [이 샘플 스크립트](https://github.com/Azure/azure-devtestlab/blob/master/Tools/ConvertDtlLabToIsolatedNetwork/Convert-DtlLabToIsolatedNetwork.ps1)는 기존 랩 및 연결된 랩 리소스를 격리된 네트워크 모드로 변환하는 방법을 보여 줍니다. 

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>네트워크 격리 모드에서 랩을 사용하는 동안 기억해야 할 사항

### <a name="accessing-labs-storage-account-outside-the-lab"></a>랩 외부의 랩 스토리지 계정에 액세스 

네트워크 격리 랩 내에서 VHD를 랩의 스토리지 계정에 업로드하여 사용자 지정 이미지를 생성하는 등의 작업을 수행하려면 랩 소유자는 허용된 엔드포인트에서 스토리지 계정에 대한 액세스 사용을 명시적으로 설정해야 합니다. 가상 머신을 생성하여 이 작업을 수행하고 해당 가상 머신에서 랩의 스토리지 계정에 안전하게 액세스할 수 있습니다. 

[가상 머신에서 스토리지 계정에 비공개로 액세스하는 방법에 대해 자세히 알아보기](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>랩에서 사용량 데이터 내보내기 

네트워크 격리 랩 내에서 [랩에 대한 개인 사용량 데이터 내보내기](personal-data-delete-export.md)를 수행하려면 랩 소유자는 명시적으로 스토리지 계정을 제공하고 해당 계정 내에서 데이터를 저장할 BLOB을 생성해야 합니다. 

스토리지 계정이 제공되지 않는다면 고객이 제공한 스토리지 계정이 없는 경우 랩에서 사용할 수 있는 랩의 스토리지 계정에 액세스할 수 없으므로 이 작업은 네트워크 격리 모드에서 실패합니다. 

[지정된 스토리지 계정의 랩 사용량 데이터 내보내기에 대한 자세한 정보 알아보기](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>다음 단계

[Azure Resource Manager 템플릿 및 PowerShell을 사용하여 랩 자동 생성 또는 수정](devtest-lab-use-arm-and-powershell-for-lab-resources.md)
