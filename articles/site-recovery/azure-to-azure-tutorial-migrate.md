---
title: Azure Site Recovery를 사용하여 Azure VM을 다른 Azure 지역으로 이동
description: Azure Site Recovery를 사용하여 Azure 지역 간에 Azure VM을 이동합니다.
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: 53554e49c9e237f44d73238ca0e78385f66ca875
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019384"
---
# <a name="move-vms-to-another-azure-region"></a>다른 Azure 지역으로 VM 이동

기존 Azure IaaS 가상 머신(VM)을 한 지역에서 다른 지역으로 이동하려는 시나리오가 있습니다. 예를 들어, 기존 VM의 안정성 및 가용성을 개선하고 관리 기능을 개선하거나 거버넌스 이유로 이동하길 원할 수 있습니다. 자세한 내용은 [Azure VM 이동 개요](azure-to-azure-move-overview.md)를 참조하세요. 

[Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure VM을 보조 지역으로 이동할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> 
> * 이동을 위한 필수 조건 확인
> * 원본 VM 및 대상 Azure 지역 준비
> * 데이터를 복사 및 복제 사용
> * 구성 테스트 및 이동 수행
> * 원본 지역에서 리소스 삭제


> [!IMPORTANT]
> Azure VM을 다른 지역으로 이동하려면 이제 [Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md)를 사용하는 것이 좋습니다. Azure Resource Mover는 현재 공개 미리 보기로 제공되며 다음을 제공합니다.
> - 지역 간에 리소스를 이동하기 위한 단일 허브입니다.
> - 이동 시간과 복잡성이 줄어듭니다. 필요한 모든 항목은 단일 위치에 있습니다.
> - 다양한 유형의 Azure 리소스를 이동할 수 있는 간단하고 일관된 환경을 제공합니다.
> - 이동하려는 리소스 간의 종속성을 쉽게 식별할 수 있는 방법입니다. 이를 통해 관련 리소스를 함께 이동함으로써 이동 후 대상 지역에서 모든 것이 예상대로 작동하도록 할 수 있습니다.
> - 이동 후 리소스를 삭제하려는 경우 원본 지역의 리소스를 자동으로 정리합니다.
> - 테스트 이동을 시도한 다음, 전체 이동을 수행하지 않으려면 취소할 수 있습니다.



> [!NOTE]
> 이 자습서에서는 Azure VM을 한 지역에서 다른 지역으로 그대로 이동하는 방법을 보여줍니다. 가용성 집합에 있는 VM을 다른 지역의 영역이 고정된 VM으로 이동하여 가용성을 개선해야 하는 경우에는 [가용성 영역으로 Azure VM 이동 자습서](move-azure-vms-avset-azone.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 이동하려는 Azure 지역에 Azure VM이 있는지 확인합니다.
- 원하는 [원본 지역 - Azure 지역 조합이 지원되는지](./azure-to-azure-support-matrix.md#region-support) 확인하여 충분한 정보를 바탕으로 Azure 지역을 결정합니다.
- [시나리오 아키텍처 및 구성 요소](azure-to-azure-architecture.md)를 이해해야 합니다.
- [제한 사항 및 요구 사항 지원](azure-to-azure-support-matrix.md)을 검토합니다.
- 계정 권한을 확인합니다. Azure 체험 계정을 만든 경우 자신이 구독에 대한 관리자입니다. 구독 관리자가 아닌 경우에는 관리자와 협력하여 필요한 권한을 할당받습니다. VM 복제를 사용하도록 설정하고 기본적으로 Azure Site Recovery를 사용하여 데이터를 복사하려면 다음 권한이 필요합니다.

    - Azure 리소스에서 VM을 만들 수 있는 권한. 기본 제공되는 Virtual Machine 기여자 역할에는 다음을 포함하여 이러한 권한이 있습니다.
    - 선택한 리소스 그룹에서 VM을 만들 수 있는 권한
    - 선택한 가상 네트워크에서 VM을 만들 수 있는 권한
    - 선택한 스토리지 계정에 대한 쓰기 권한
    
    - Azure Site Recovery 작업을 관리할 수 있는 권한 Site Recovery 기여자 역할에는 Recovery Services 자격 증명 모음에서 Site Recovery 작업을 관리하는 데 필요한 모든 권한이 있습니다.

- 이동할 Azure VM에 모든 최신 루트 인증서가 있는지 확인합니다. VM에 최신 루트 인증서가 없는 경우 보안 제약 조건으로 인해 대상 Azure 지역으로 데이터가 복사되지 않습니다.

- Windows VM의 경우 VM에 최신 Windows 업데이트를 모두 설치하여 신뢰할 수 있는 모든 루트 인증서가 컴퓨터에 있도록 합니다. 연결이 끊어진 환경에서 조직의 표준 Windows 업데이트 및 인증서 업데이트 프로세스를 따릅니다.
    
- Linux VM의 경우 Linux 배포자가 제공한 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 인증서 해지 목록을 VM에 가져옵니다.
- 이동하려는 VM의 네트워크 연결을 제어하기 위해 인증 프록시를 사용하지 않도록 합니다.

- 이동하려는 VM이 인터넷에 액세스할 수 없고 방화벽 프록시를 사용하여 아웃바운드 액세스를 제어하는 경우 [요구 사항을 확인](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity)합니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 여기에는 부하 분산 장치, 네트워크 보안 그룹(NSG) 및 공용 IP를 포함하되 국한되지 않습니다.

- Azure 구독에서 재해 복구에 사용되는 대상 Azure 지역에 VM을 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 원본 VM과 동일한 크기의 VM을 지원할 수 있을 만큼 충분한 리소스가 있는지 확인합니다. Site Recovery를 사용하여 대상에 데이터를 복사하는 경우 Site Recovery는 동일한 크기 또는 가장 비슷한 크기를 대상 VM에 선택합니다.

- 원본 네트워킹 레이아웃에서 식별된 모든 구성 요소의 대상 리소스를 만듭니다. 그래야만 VM이 원본 지역서 사용하던 모든 기능을 대상 지역에서 그대로 사용할 수 있으므로 이 단계는 중요합니다.

     > [!NOTE] 
     > 사용자가 원본 VM에 대해 복제를 사용하도록 설정하면 Azure Site Recovery는 가상 네트워크를 자동으로 검색하고 만듭니다. 또한 네트워크를 미리 만들고 복제 사용을 위해 사용자 흐름에서 VM에 할당할 수도 있습니다. 뒷부분에 설명된 것처럼 대상 지역에 다른 리소스를 수동으로 만들어야 합니다.

    원본 VM 구성에 따라 관련성이 있는 가장 일반적으로 사용되는 네트워크 리소스를 만들려면 다음 문서를 참조하세요.
    - [네트워크 보안 그룹](../virtual-network/manage-network-security-group.md)
    - [부하 분산 장치](../load-balancer/index.yml)
    -  [공용 IP](../virtual-network/ip-services/virtual-network-public-ip-address.md)
    - 그 외의 네트워킹 구성 요소는 [네트워킹 설명서](../index.yml?pivot=products&panel=network)를 참조하세요.



## <a name="prepare"></a>준비
다음 단계에서는 Azure Site Recovery를 솔루션으로 사용하여 이동하도록 가상 머신을 준비하는 방법을 보여줍니다. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>원본 지역을 제외한 모든 지역에 자격 증명 모음을 만듭니다.

1. [Azure 포털](https://portal.azure.com)
1. 검색 시 Recovery Services를 입력하고 Recovery Services 자격 증명 모음을 클릭합니다.
1. Recovery Services 자격 증명 모음 메뉴에서 +추가를 클릭합니다.
1. **이름** 에서 **ContosoVMVault** 라는 이름을 지정합니다. 구독이 두 개 이상인 경우 적절한 구독을 선택합니다.
1. **ContosoRG** 리소스 그룹을 만듭니다.
1. Azure 지역을 지정합니다. 지원되는 지역을 확인하려면 [Azure Site Recovery 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하세요.
1. **Recovery Services 자격 증명 모음** 에서 **ContosoVMVault** > **복제된 항목** >  **+복제** 를 선택합니다.
1. 드롭다운에서 **Azure Virtual Machines** 를 선택합니다.
1. **원본 위치** 에서 VM이 현재 실행 중인 원본 Azure 지역을 선택합니다.
1. 리소스 관리자 배포 모델을 선택합니다. 그런 다음, **원본 구독** 및 **원본 리소스 그룹** 을 선택합니다.
1. **확인** 을 선택하여 설정을 저장합니다.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Azure VM에 대한 복제를 사용하도록 설정하고 데이터 복사 시작

Site Recovery는 구독 및 리소스 그룹과 연관된 VM 목록을 검색합니다.

1. 다음 단계에서는 이동하려는 VM을 선택하고 **확인** 을 선택합니다.
1. **설정** 에서 **재해 복구** 를 선택합니다.
1. **재해 복구 구성** > **대상 지역** 에서 복제할 대상 지역을 선택합니다.
1. 이 자습서에서는 다른 기본 설정을 적용합니다.
1. **복제 사용** 을 선택합니다. 이 단계는 VM에 대해 복제를 활성화하는 작업을 시작합니다.



## <a name="move"></a>이동

다음 단계에서는 대상 영역으로 이동하는 방법을 보여줍니다.

1. 자격 증명 모음으로 이동합니다. **설정** > **복제된 항목** 에서 VM을 선택한 후 **장애 조치(failover)** 를 선택합니다.
2. **장애 조치(failover)** 에서 **최신** 을 선택합니다.
3. **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다.** 를 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 원본 VM을 종료하려고 합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
4. 작업이 완료되면 VM이 예상대로 대상 Azure 지역에 표시되는지 확인합니다.


## <a name="discard"></a>취소 

이동된 VM을 확인하고 장애 조치(failover) 지점으로 변경하거나 이전 지점으로 다시 이동해야 하는 경우 **복제된 항목** 에서 마우스 오른쪽 단추로 VM > **복구 지점 변경** 을 선택합니다. 이 단계에서는 다른 복구 지점을 지정하고이에 대한 장애 조치(failover)를 수행할 수 있는 옵션을 제공합니다. 


## <a name="commit"></a>Commit 

이동된 VM을 확인하고 변경 내용을 커밋할 준비가 되면 **복제된 항목** 에서 마우스 오른쪽 단추로 VM > **커밋** 을 선택합니다. 이 단계로, 대상 Azure 지역으로 이동하는 프로세스가 끝났습니다. 커밋 작업이 완료될 때까지 기다립니다.

## <a name="clean-up"></a>정리

다음 단계에서는 원본 지역을 정리하는 방법 및 이동에 사용된 관련 리소스를 안내합니다.

이동에 사용된 모든 리소스의 경우:

- VM으로 이동합니다. **복제 사용 안 함** 을 선택합니다. 이 단계로, VM에 대한 데이터 복사 프로세스가 중지됩니다.

   > [!IMPORTANT]
   > Azure Site Recovery 복제 요금이 청구되지 않도록 이 단계를 수행해야 합니다.

원본 리소스를 다시 사용할 계획이 없다면 다음과 같은 추가 단계를 수행합니다.

1. [필수 구성 요소](#prerequisites)에서 확인한 원본 지역의 모든 관련 네트워크 리소스를 삭제합니다.
1. 원본 Azure 지역에서 해당 스토리지 계정을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure VM을 다른 Azure 지역으로 이동했습니다. 이제 이동한 VM에 대해 재해 복구를 구성할 수 있습니다.

> [!div class="nextstepaction"]
> [마이그레이션 후 재해 복구 설정](azure-to-azure-quickstart.md)