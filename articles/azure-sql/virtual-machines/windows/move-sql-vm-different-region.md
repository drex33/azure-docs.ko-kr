---
title: 가상 머신을 다른 지역으로 이동(Azure Site Recovery)
description: Azure 내의 한 지역에서 다른 지역으로 SQL Server 가상 머신을 마이그레이션하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: migration
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 599546679039005831a72440ff0d095d7e62c257
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047307"
---
# <a name="move-a-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery"></a>Azure Site Recovery를 사용하여 Azure 내의 다른 지역으로 SQL Server VM 이동
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure Site Recovery를 사용하여 Azure 내의 한 지역에서 다른 지역으로 SQL Server VM(가상 머신)을 마이그레이션하는 방법을 설명합니다. 

SQL Server VM을 다른 지역으로 이동하려면 다음 단계를 수행해야 합니다.
1. [준비](#prepare-to-move): 원본 SQL Server VM과 대상 지역이 이동에 적절하게 준비되었는지 확인합니다. 
1. [구성](#configure-azure-site-recovery-vault): SQL Server VM을 이동하려면 해당 VM이 Azure Site Recovery 자격 증명 모음 내의 복제된 개체여야 합니다. Azure Site Recovery 자격 증명 모음에 SQL Server VM을 추가해야 합니다. 
1. [테스트](#test-move-process): SQL Server VM을 마이그레이션하려면 원본 지역에서 복제된 대상 지역으로 VM을 장애 조치(failover)해야 합니다. 이동 프로세스가 성공하려면 먼저 SQL Server VM을 대상 지역으로 장애 조치(failover)할 수 있는지 테스트해야 합니다. 이 테스트를 통해 문제가 노출되므로 실제 이동을 수행할 때 이를 방지할 수 있습니다. 
1. [이동](#move-the-sql-server-vm): 테스트 장애 조치(failover)가 성공하고 SQL Server VM을 마이그레이션해도 안전하다는 판단이 서면 VM을 대상 지역으로 이동할 수 있습니다. 
1. [정리](#clean-up-source-resources): 요금 청구를 방지하려면 자격 증명 모음에서 SQL Server VM을 제거하고 리소스 그룹에 남은 불필요한 리소스를 제거합니다. 

## <a name="verify-prerequisites"></a>필수 조건 확인 

- 원본 지역에서 대상 지역으로 이동이 [지원](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)되는지 확인합니다.  
- [시나리오 아키텍처 및 구성 요소](../../../site-recovery/azure-to-azure-architecture.md)뿐 아니라 [지원 제한 사항 및 요구 사항](../../../site-recovery/azure-to-azure-support-matrix.md)도 검토하세요. 
- 계정 권한을 확인합니다. Azure 체험 계정을 만든 경우 자신이 구독에 대한 관리자입니다. 구독 관리자가 아닌 경우에는 관리자와 협력하여 필요한 권한을 할당받습니다. VM 복제를 사용하도록 설정하고 Azure Site Recovery를 사용하여 데이터를 복사하려면 다음 권한이 필요합니다. 
    - VM을 만들 수 있는 권한 기본 제공되는 *Virtual Machine 참가자* 역할에는 다음을 포함하여 이러한 권한이 있습니다. 
        - 선택한 리소스 그룹에서 VM을 만들 수 있는 권한 
        - 선택한 가상 네트워크에서 VM을 만들 수 있는 권한 
        - 선택한 스토리지 계정에 대한 쓰기 권한 
      - Azure Site Recovery 작업을 관리할 수 있는 권한 Site Recovery 기여자 역할에는 Recovery Services 자격 증명 모음에서 Site Recovery 작업을 관리하는 데 필요한 모든 권한이 있습니다.  

## <a name="prepare-to-move"></a>이동 준비
원본 SQL Server VM과 대상 지역을 모두에서 이동을 준비합니다. 

### <a name="prepare-the-source-sql-server-vm"></a>원본 SQL Server VM 준비

- 이동할 SQL Server VM에 모든 최신 루트 인증서가 있는지 확인합니다. 최신 루트 인증서가 없는 경우 보안 제약 조건으로 인해 대상 지역으로 데이터가 복사되지 않습니다. 
- Windows VM의 경우 VM에 최신 Windows 업데이트를 모두 설치하여 신뢰할 수 있는 모든 루트 인증서가 컴퓨터에 있도록 합니다. 연결이 끊어진 환경에서는 조직의 표준 Windows 업데이트 및 인증서 업데이트 프로세스를 따릅니다. 
- Linux VM의 경우 Linux 배포자가 제공한 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 인증서 해지 목록을 VM에 가져옵니다. 
- 이동하려는 VM의 네트워크 연결을 제어하기 위해 인증 프록시를 사용하지 않도록 합니다. 
- 이동하려는 VM이 인터넷에 액세스할 수 없고 방화벽 프록시를 사용하여 아웃바운드 액세스를 제어하는 경우 요구 사항을 확인합니다. 
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 여기에는 부하 분산 장치, 네트워크 보안 그룹(NSG) 및 공용 IP를 포함하되 국한되지 않습니다. 

### <a name="prepare-the-target-region"></a>대상 Azure 지역 준비

- Azure 구독에서 재해 복구에 사용되는 대상 Azure 지역에 VM을 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요. 
- 구독에 원본 VM과 동일한 크기의 VM을 지원할 수 있을 만큼 충분한 리소스가 있는지 확인합니다. Site Recovery를 사용하여 대상에 데이터를 복사하는 경우 Site Recovery는 동일한 크기 또는 가장 비슷한 크기를 대상 VM에 선택합니다. 
- 원본 네트워킹 레이아웃에서 식별된 모든 구성 요소의 대상 리소스를 만듭니다. 그래야만 VM이 원본 지역서 사용하던 모든 기능을 대상 지역에서 그대로 사용할 수 있으므로 이 단계는 중요합니다. 
    - 사용자가 원본 VM에 대해 복제를 사용하도록 설정하면 Azure Site Recovery는 가상 네트워크를 자동으로 검색하고 만듭니다. 또한 네트워크를 미리 만들고 복제를 사용하기 위한 사용자 흐름에서 VM에 할당할 수도 있습니다. 다른 모든 리소스는 대상 지역에서 수동으로 만들어야 합니다.
- 원본 VM 구성에 따라 관련성이 있는 가장 일반적으로 사용되는 네트워크 리소스를 만들려면 다음 문서를 참조하세요. 
    - [네트워크 보안 그룹](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [부하 분산 장치](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md)
    - [공용 IP 주소](../../../virtual-network/virtual-network-public-ip-address.md)
    - 기타 네트워킹 구성 요소는 [네트워킹 설명서](../../../virtual-network/virtual-networks-overview.md)를 참조하세요.
- 대상 Azure 지역으로 최종 이동을 수행하기 전에 구성을 테스트하려면 수동으로 대상 Azure 지역에 비-프로덕션 네트워크를 만듭니다. 이 단계는 프로덕션 네트워크를 최소로만 중단하므로 권장됩니다. 

## <a name="configure-azure-site-recovery-vault"></a>Azure Site Recovery 자격 증명 모음 구성

다음 단계에서는 Azure Site Recovery를 사용하여 대상 Azure 지역에 데이터를 복사하는 방법을 보여줍니다. 원본 지역이 아닌 임의의 지역에 Recovery Services 자격 증명 모음을 만듭니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. 탐색 창의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다. 
1. **IT 및 관리 도구** 를 선택한 다음 **Backup 및 Site Recovery** 를 선택합니다. 
1. **기본 사항** 탭의 **프로젝트 세부 정보** 에서 대상 지역에 새 리소스 그룹을 만들거나 대상 지역에서 기존 리소스 그룹을 선택합니다. 
1. **인스턴스 세부 정보** 에서 자격 증명 모음의 이름을 지정하고 드롭다운에서 대상 **지역** 을 선택합니다. 
1. **검토 + 만들기** 를 클릭하여 Recovery Services 자격 증명 모음을 생성합니다. 
1. 탐색 창의 왼쪽 위 모서리에서 **모든 서비스** 를 선택하고 검색 상자에 `recovery services`를 입력합니다. 
1. (선택 사항) **Recovery Services 자격 증명 모음** 옆의 별표를 선택하여 빠른 탐색 모음에 추가합니다. 
1. **Recovery services 자격 증명 모음** 을 선택한 다음, 만든 Recovery Services 자격 증명 모음을 선택합니다. 
1. **개요** 창에서 **복제** 를 선택합니다. 

   ![복제 구성](./media/move-sql-vm-different-region/configure-replication.png)

1. **원본** 을 선택한 다음 원본으로 **Azure** 를 선택합니다. 원본 VM의 위치와 같은 다른 드롭다운 필드에서 적절한 값을 선택합니다. **원본 위치** 영역에 있는 리소스 그룹만 **원본 리소스 그룹** 필드에 표시됩니다. 
1. **가상 머신** 을 선택한 다음 마이그레이션하려는 가상 머신을 선택합니다. **확인** 을 선택하여 VM 선택 항목을 저장합니다. 
1. **설정** 을 선택한 다음 드롭다운에서 **대상 위치** 를 선택합니다. 이는 앞서 준비한 리소스 그룹이어야 합니다. 
1. 복제를 사용자 지정한 후 **대상 리소스 만들기** 를 선택하여 새 위치에 리소스를 만듭니다. 
1. 리소스 만들기가 완료되면 **복제 사용** 을 선택하여 원본에서 대상 지역으로 SQL Server VM 복제를 시작합니다.
1. 복구 자격 증명 모음으로 이동하고, **복제된 항목** 을 선택하고, SQL Server VM의 **상태** 를 확인하여 복제 상태를 확인할 수 있습니다. **보호됨** 상태는 복제가 완료되었음을 나타냅니다. 

   ![복제 상태 확인](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>이동 프로세스 테스트
다음 단계에서는 Azure Site Recovery를 사용하여 이동 프로세스를 테스트하는 방법을 보여 줍니다. 

1. [Azure Portal](https://portal.azure.com)에서 **Recovery Services 자격 증명 모음** 으로 이동하고 **복제된 항목** 을 선택합니다. 
1. 이동하려는 SQL Server VM을 선택하고, **복제 상태** 가 **정상** 으로 표시되는지 확인한 다음 **테스트 장애 조치(failover)** 를 선택합니다. 

   ![VM 장애 조치(failover) 테스트](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. **테스트 장애 조치(failover)** 페이지에서 장애 조치(failover)에 사용할 **최신 앱 일치** 복구 지점을 선택합니다. 이는 SQL Server 데이터 일관성을 보장할 수 있는 유일한 스냅샷 유형입니다. 
1. **Azure 가상 네트워크** 에서 가상 네트워크를 선택하고 **확인** 을 선택하여 장애 조치(failover)를 테스트합니다. 
   
   >[!IMPORTANT]
   > 장애 조치(failover) 테스트에는 별도의 Azure VM 네트워크를 사용하는 것이 좋습니다. 복제 활성화 시 설정된 프로덕션 네트워크와 결과적으로 VM을 이동하려는 네트워크를 사용하지 마세요. 

1. 진행률을 모니터링하려면 자격 증명 모음으로 이동하여 **모니터링** 에서 **Site Recovery 작업** 을 선택하고 진행 중인 **테스트 장애 조치(failover)** 작업을 선택합니다.

   ![장애 조치(failover) 테스트 진행률 모니터링](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. 테스트가 완료되면 포털에서 **가상 머신** 으로 이동하여 새로 만든 가상 머신을 검토합니다. SQL Server VM이 실행 중이고, 크기가 적절히 조정되었으며, 적절한 네트워크에 연결되어 있는지 확인합니다. 
1. 장애 조치(failover) 테스트 리소스를 정리할 때까지 **장애 조치(failover)** 옵션이 회색으로 표시되므로 테스트의 일부로 만들어진 VM을 삭제합니다. 자격 증명 모음으로 다시 이동하여 **복제된 항목** 을 선택하고 SQL Server VM을 선택한 다음 **테스트 장애 조치(failover) 정리** 를 선택합니다. **참고** 섹션에서 테스트와 연결된 관찰을 기록 및 저장하고 **테스트가 완료되었습니다. 테스트 장애 조치(failover) 가상 머신을 삭제하세요.** 옆의 확인란을 선택합니다. **확인** 을 선택하여 테스트 후 리소스를 정리합니다. 

   ![장애 조치(failover) 테스트 후 항목 정리](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>SQL Server VM 이동 
다음 단계에서는 SQL Server VM을 원본 지역에서 대상 지역으로 이동하는 방법을 보여 줍니다. 

1. **Recovery Services** 자격 증명 모음으로 이동하여 **복제된 항목** 을 선택하고 VM을 선택한 다음 **장애 조치(failover)** 를 선택합니다. 

   ![장애 조치(failover) 시작](./media/move-sql-vm-different-region/initiate-failover.png)

1. **복구 지점** 에서 **최신 앱 일치** 복구 지점을 선택합니다. 
1. **장애 조치(failover)를 시작하기 전에 머신을 종료합니다.** 옆의 확인란을 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 원본 VM을 종료하려고 합니다. 종료가 실패하더라도 장애 조치(failover)는 계속됩니다. 
1. **확인** 을 선택하여 장애 조치(failover)를 시작합니다.
1. 이전 섹션에서 장애 조치(failover) 테스트를 모니터링할 때 본 동일한 **Site Recovery 작업** 페이지에서 장애 조치(failover) 프로세스를 모니터링할 수 있습니다. 
1. 작업이 완료되면 SQL Server VM이 예상대로 대상 지역에 표시되는지 확인합니다. 
1. 자격 증명 모음으로 다시 이동하여 **복제된 항목** 을 선택하고 SQL Server VM을 선택한 다음 **커밋** 을 선택하여 대상 지역으로 이동 프로세스를 마칩니다. 커밋 작업이 완료될 때까지 기다립니다. 
1. SQL Server VM을 SQL IaaS 에이전트 확장에 등록하여 확장에 연결된 Azure Portal 및 기능에서 **SQL 가상 머신** 관리 효율성을 사용하도록 설정합니다. 자세한 내용은 [SQL IaaS 에이전트 확장에 SQL Server VM 등록](sql-agent-extension-manually-register-single-vm.md)을 참조하세요. 

  > [!WARNING]
  > SQL Server 데이터 일관성은 앱 일치 스냅샷으로만 보장됩니다. 충돌 복구 스냅샷은 SQL Server 데이터 일관성을 보장할 수 없기 때문에 **가장 최근에 처리된** 스냅샷은 SQL Server 장애 조치(failover)에 사용할 수 없습니다. 

## <a name="clean-up-source-resources"></a>원본 리소스 정리
요금 청구를 방지하려면 자격 증명 모음에서 SQL Server VM을 제거하고 불필요하게 연결된 리소스를 삭제합니다. 

1. **Site Recovery** 자격 증명 모음으로 다시 이동하여 **복제된 항목** 을 선택하고 SQL Server VM을 선택합니다. 
1. **복제 사용 안 함** 을 선택합니다. 보호를 해제하는 이유를 선택한 다음 **확인** 을 선택하여 복제를 사용하지 않도록 설정합니다. 

   >[!IMPORTANT]
   > Azure Site Recovery 복제 요금이 부과되지 않도록 이 단계를 수행하는 것이 중요합니다. 

1. 원본 지역의 리소스를 다시 사용할 계획이 없는 경우 관련 네트워크 리소스 및 해당 스토리지 계정을 모두 삭제합니다. 


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.yml)
* [Windows VM의 SQL Server 가격 책정 가이드](pricing-guidance.md)
* [Azure vm에서 SQL Server의 새로운 기능](doc-changes-updates-release-notes-whats-new.md)