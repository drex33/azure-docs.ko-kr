---
title: Azure Recovery Services 자격 증명 모음을 다른 지역으로 이동
description: 이 문서에서는 지역 간에 리소스를 이동한 후 지속적인 백업을 보장하는 방법을 배웁니다.
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: how-to
ms.openlocfilehash: 658b4965c008105957165a858987442f23630c14
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058883"
---
# <a name="back-up-resources-in-recovery-services-vault-after-moving-across-regions"></a>지역 간 이동 후 Recovery Services 자격 증명 모음의 리소스 백업

Azure Resource Mover는 지역 간 여러 리소스의 이동을 지원합니다. 한 지역에서 다른 지역으로 리소스를 이동하는 동안 리소스를 보호할 수 있습니다. Azure Backup은 여러 워크로드의 보호를 지원하므로 새 지역에서 동일한 수준의 보호를 계속 유지하려면 몇 가지 단계를 수행해야 할 수 있습니다.

이를 달성하기 위한 자세한 단계를 이해하려면 아래 섹션을 참조하세요.

>[!Note]
>Azure Backup은 현재 한 Recovery Services 자격 증명 모음에서 다른 자격 증명 모음으로의 백업 데이터 이동을 지원하지 않습니다. 새 지역의 리소스를 보호하려면 새 지역의 새/기존 자격 증명 모음에 리소스를 등록하고 백업해야 합니다. 한 지역에서 다른 지역으로 리소스를 이동할 때 요구 사항에 따라 이전 지역의 기존 Recovery Services 자격 증명 모음에 있는 백업 데이터를 보존/삭제할 수 있습니다. 이전 자격 증명 모음에 데이터를 유지하도록 선택하면 그에 따라 백업 요금이 발생합니다.

## <a name="back-up-azure-virtual-machine-after-moving-across-regions"></a>지역 간 이동 후 Azure Virtual Machine 백업

Recovery Services 자격 증명 모음으로 보호되는 Azure VM(Virtual Machine)이 한 지역에서 다른 지역으로 이동되면 더 이상 이전 자격 증명 모음에 백업할 수 없습니다. 이전 자격 증명 모음의 백업이 **BCMV2VMNotFound** 또는 [**ResourceNotFound**](./backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found) 오류와 함께 실패하기 시작합니다. 새 지역에서 VM을 보호하는 방법에 대한 자세한 내용은 다음 섹션을 참조하세요.

### <a name="prepare-to-move-azure-vms"></a>Azure VM 이동 준비

VM을 이동하기 전에 다음 필수 조건이 충족되는지 확인합니다.

1. [VM 이동과 관련된 필수 구성을 확인하고 VM을](/azure/resource-mover/tutorial-move-region-virtual-machines#prerequisites) 이동할 수 있는지 확인합니다.
1. [](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) 기존 자격 증명 모음 대시보드의 백업 항목 탭에서 VM을 선택하고, 요구 사항에 따라 **보호 중지,** 데이터 보존/삭제를 선택합니다. 데이터 보존으로 VM의 백업 데이터를 중지하면 복구 지점이 영구적으로 유지되고 어떤 정책도 준수하지 않습니다. 이렇게 하면 항상 백업 데이터를 복원할 준비가 된 것입니다.
   >[!Note]
   >이전 자격 증명 모음에 데이터를 보관하면 백업 비용이 발생합니다. 청구를 방지하기 위해 더 이상 데이터를 보관하지 않으려면 [데이터 삭제 옵션](./backup-azure-manage-vms.md#delete-backup-data)을 사용하여 보관된 백업 데이터를 삭제해야 합니다.
1. VM이 켜져 있는지 확인합니다. 대상 지역에서 사용할 수 있어야 하는 모든 VM의 디스크는 VM에서 연결되고 초기화됩니다.
1. VM에 신뢰할 수 있는 최신 루트 인증서와 업데이트된 CRL(인증서 해지 목록)이 있는지 확인합니다. 이를 수행하려면:
   - Windows VM에서 최신 Windows 업데이트를 설치합니다.
   - Linux VM에서 배포자 지침을 참조하여 머신에 최신 인증서 및 CRL이 있는지 확인합니다.
1. 다음과 같이 VM에서 아웃바운드 연결을 허용합니다.
   - URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 이러한 URL에 대한 액세스를 [허용합니다.](/azure/resource-mover/support-matrix-move-region-azure-vm#url-access)
   - NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 [이러한 서비스 태그 규칙을](/azure/resource-mover/support-matrix-move-region-azure-vm#nsg-rules)만듭니다.

### <a name="move-azure-vms"></a>Azure VM 이동

[Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md)를 사용하여 VM을 새 지역으로 이동합니다.

### <a name="protect-azure-vms-using-azure-backup"></a>Azure Backup 사용하여 Azure VM 보호

새 지역의 신규 또는 기존 Recovery Services 자격 증명 모음에서 VM 보호를 시작합니다. 이전 백업에서 복원해야 하는 경우 백업 데이터를 유지하도록 선택했다면 이전 Recovery Services 자격 증명 모음에서 복원할 수 있습니다. 

위의 단계는 리소스가 새 지역에서도 백업되고 있는지 확인하는 데 도움이 됩니다.

## <a name="back-up-azure-file-share-after-moving-across-regions"></a>지역 간 이동 후 Azure 파일 공유 백업

Azure Backup은 현재 Azure Files에 대한 [스냅샷 관리 솔루션](./backup-afs.md)을 제공합니다. 즉, 파일 공유 데이터를 Recovery Services 자격 증명 모음으로 이동시키지 않습니다. 또한 스냅샷은 스토리지 계정과 함께 이동하지 않기 때문에 기존 지역에만 모든 백업(스냅샷)을 효과적으로 저장하고 기존 자격 증명 모음으로 보호합니다. 그러나 지역 간에 파일 공유와 함께 Storage 계정을 이동하거나 새 지역에서 새 파일 공유를 만드는 경우 다음 섹션을 참조하여 Azure Backup 보호되는지 확인합니다.

### <a name="prepare-to-move-azure-file-share"></a>Azure 파일 공유 이동 준비

Storage 계정을 이동하기 전에 다음 필수 조건이 충족되는지 확인합니다.

1.  Storage [계정을 이동하려면 필수 구성 Storage 참조하세요.](/azure/storage/common/storage-account-move?tabs=azure-portal#prerequisites) 
1. 리소스 이동 템플릿을 내보내고 수정합니다. 자세한 내용은 [지역 이동을 위한 Storage 계정 준비를 참조하세요.](/azure/storage/common/storage-account-move?tabs=azure-portal#prepare)

### <a name="move-azure-file-share"></a>Azure 파일 공유 이동

azure 파일 공유와 함께 Storage 계정을 한 지역에서 다른 지역으로 이동하려면 [Azure Storage 계정을 다른 지역으로 이동을](../storage/common/storage-account-move.md)참조하세요.

>[!Note]
>Azure 파일 공유가 지역 간에 복사될 때 연결된 스냅샷은 함께 이동하지 않습니다. 스냅샷 데이터를 새 지역으로 이동하려면 [AzCopy](../storage/common/storage-use-azcopy-files.md#copy-all-file-shares-directories-and-files-to-another-storage-account)를 사용하여 스냅샷의 개별 파일과 디렉터리를 새 지역의 스토리지 계정으로 이동해야 합니다.

### <a name="protect-azure-file-share-using-azure-backup"></a>Azure Backup 사용하여 Azure 파일 공유 보호

새 지역의 신규 또는 기존 Recovery Services 자격 증명 모음에서 새 스토리지 계정으로 복사된 Azure 파일 공유 보호를 시작합니다.  

Azure 파일 공유가 새 지역에 복사되면 요구 사항에 따라 보호를 중지하고 원본 Azure 파일 공유의 스냅샷(및 해당 복구 지점)을 유지/삭제하도록 선택할 수 있습니다. 원본 자격 증명 모음 대시보드의 [백업 항목 탭](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud)에서 파일 공유를 선택하면 됩니다. Azure 파일 공유에 대한 백업 데이터가 데이터 유지로 중지되면 복구 지점이 영구적으로 유지되고 어떤 정책도 준수하지 않습니다.
   
이렇게 하면 항상 이전 자격 증명 모음에서 복원할 수 있도록 스냅샷을 준비할 수 있습니다. 
 
## <a name="back-up-sql-serversap-hana-in-azure-vm-after-moving-across-regions"></a>지역 간에 이동한 후 Azure VM에서 SQL Server/SAP HANA 백업

SQL 또는 SAP HANA 서버를 실행하는 VM을 다른 지역으로 이동하면 해당 VM의 SQL 및 SAP HANA 데이터베이스를 더 이상 이전 지역의 자격 증명 모음에서 백업할 수 없습니다. 새 지역의 Azure VM에서 실행되는 SQL 및 SAP HANA 서버를 보호하려면 다음 섹션을 참조하세요.

### <a name="prepare-to-move-sql-serversap-hana-in-azure-vm"></a>Azure VM에서 SQL Server/SAP HANA 이동 준비

VM에서 실행되는 SQL Server/SAP HANA 새 지역으로 이동하기 전에 다음 필수 조건이 충족되는지 확인합니다.

1. [VM 이동과 관련된 필수 구성을 확인하고 VM을](/azure/resource-mover/tutorial-move-region-virtual-machines#prerequisites) 이동할 수 있는지 확인합니다. 
1. 기존 자격 증명 모음 대시보드의 [백업 항목 탭에서](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) VM을 선택하고 백업을 중지해야 하는 _데이터베이스를_ 선택합니다. **보호 중지** 를 선택한 다음 요구 사항에 따라 데이터 유지/삭제를 선택합니다. 데이터 유지로 백업 데이터를 중지하면 복구 지점이 영구적으로 유지되며 정책을 준수하지 않습니다. 이렇게 하면 백업 데이터를 항상 복원할 수 있습니다.
   >[!Note]
   >이전 자격 증명 모음에 데이터를 보관하면 백업 비용이 발생합니다. 청구를 방지하기 위해 더 이상 데이터를 보관하지 않으려면 [데이터 삭제 옵션](./backup-azure-manage-vms.md#delete-backup-data)을 사용하여 보관된 백업 데이터를 삭제해야 합니다.
1. 이동할 VM이 켜져 있는지 확인합니다. 대상 지역에서 사용할 수 있어야 하는 모든 VM 디스크는 VM에 연결되고 초기화됩니다.
1. VM에 신뢰할 수 있는 최신 루트 인증서와 업데이트된 CRL(인증서 해지 목록)이 있는지 확인합니다. 이를 수행하려면:
   - Windows VM에서 최신 Windows 업데이트를 설치합니다.
   - Linux VM에서 배포자 지침을 참조하고 머신에 최신 인증서 및 CRL이 있는지 확인합니다.
1. 다음과 같이 VM에서 아웃바운드 연결을 허용합니다.
   - URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 이러한 URL에 대한 액세스를 [허용합니다.](/azure/resource-mover/support-matrix-move-region-azure-vm#url-access)
   - NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 [이러한 서비스 태그 규칙을](/azure/resource-mover/support-matrix-move-region-azure-vm#nsg-rules)만듭니다.

### <a name="move-sql-serversap-hana-in-azure-vm"></a>Azure VM에서 SQL Server/SAP HANA 이동

[Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md)를 사용하여 VM을 새 지역으로 이동합니다.

### <a name="protect-sql-serversap-hana-in-azure-vm-using-azure-backup"></a>Azure Backup 사용하여 Azure VM에서 SQL Server/SAP HANA 보호

새 지역의 신규/기존 Recovery Services 자격 증명 모음에서 VM 보호를 시작합니다. 이전 백업에서 복원해야 하는 경우에도 이전 Recovery Services 자격 증명 모음에서 복원할 수 있습니다.
 
위의 단계는 리소스가 새 지역에서도 백업되고 있는지 확인하는 데 도움이 됩니다.