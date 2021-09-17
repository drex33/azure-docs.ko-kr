---
title: Azure Migrate의 물리적 서버 마이그레이션 지원
description: Azure Migrate의 물리적 서버 마이그레이션 지원에 대해 알아봅니다.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 696db5ef934dd0aca5a7485e7d40bf85d0cd8cb4
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473301"
---
# <a name="support-matrix-for-physical-server-migration"></a>물리적 서버 마이그레이션을 위한 지원 매트릭스

이 문서에서는 [Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool)을 사용하여 물리적 서버를 Azure로 마이그레이션하기 위한 지원 설정 및 제한 사항을 요약합니다. Azure로 마이그레이션하기 위해 물리적 서버를 평가하는 방법에 대한 정보를 찾고 있다면 [평가 지원 매트릭스](migrate-support-matrix-physical.md)를 검토하세요.

## <a name="migrating-machines-as-physical"></a>컴퓨터를 물리적으로 마이그레이션

에이전트 기반 복제를 사용하여 온-프레미스 컴퓨터를 물리적 서버로 마이그레이션할 수 있습니다. 이 도구를 사용하면 다음과 같은 다양한 머신을 Azure로 마이그레이션할 수 있습니다.

- 온-프레미스 물리적 서버
- Xen, KVM 등의 플랫폼에서 가상화된 VM.
- 어떤 이유로 표준 [Hyper-V](tutorial-migrate-hyper-v.md) 또는 [VMware](server-migrate-overview.md) 흐름을 사용하지 않으려는 경우 Hyper-V VM 또는 VMware VM.
- 프라이빗 클라우드에서 실행되는 VM.
- AWS(Amazon Web Services) 또는 GCP(Google Cloud Platform)를 포함하여 공용 클라우드에서 실행되는 VM.


## <a name="migration-limitations"></a>마이그레이션 제한 사항

복제를 위해 한 번에 최대 10개의 컴퓨터를 선택할 수 있습니다. 더 많은 컴퓨터를 마이그레이션하려는 경우 10개 그룹으로 복제합니다.


## <a name="physical-server-requirements"></a>물리적 서버 요구 사항

이 표에는 에이전트 기반 마이그레이션을 사용하여 마이그레이션하려는 물리적 서버에 대한 지원이 요약되어 있습니다.

**지원** | **세부 정보**
--- | ---
**머신 워크로드** | Azure Migrate는 지원되는 머신에서 실행되는 워크로드(예: Active Directory, SQL Server)의 마이그레이션을 지원합니다.
**운영 체제** | 최신 정보는 Site Recovery에 대한 [운영 체제 지원](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)을 검토하세요. Azure Migrate는 동일한 운영 체제를 지원합니다.
**Linux 파일 시스템/게스트 스토리지** | 최신 정보는 Site Recovery에 대한 [Linux 파일 시스템 지원](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)을 검토하세요. Azure Migrate는 동일한 Linux 파일 시스템을 지원합니다.
**네트워크/스토리지** | 최신 정보는 Site Recovery에 대한 [네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#network) 및 [스토리지](../site-recovery/vmware-physical-azure-support-matrix.md#storage) 필수 구성 요소를 검토하세요. Azure Migrate는 동일한 네트워크/스토리지 요구 사항을 제공합니다.
**Azure 요구 사항** | 최신 정보는 Site Recovery에 대한 [Azure 네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [스토리지](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage) 및 [컴퓨팅](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) 요구 사항을 검토하세요. Azure Migrate는 물리적 서버 마이그레이션과 동일한 요구 사항이 있습니다.
**모바일 서비스** | 마이그레이션하려는 각 컴퓨터에 모바일 서비스 에이전트가 설치되어 있어야 합니다.
**UEFI 부팅** | 지원됨. UEFI 기반 컴퓨터는 Azure 2세대 VM으로 마이그레이션됩니다.  <br/><br/> OS 디스크에는 최대 4개의 파티션이 있어야 하고 볼륨은 NTFS로 포맷되어야 합니다.
**UEFI - 보안 부팅**         | 마이그레이션에 지원되지 않습니다.
**대상 디스크** | Azure에서 컴퓨터는 관리되는 디스크(표준 HDD, 표준 SSD, 프리미엄 SSD)로만 마이그레이션할 수 있습니다.
**디스크 크기** | 1세대 VM의 경우 최대 2TB OS 디스크, 2세대 VM의 경우 최대 4TB OS 디스크, 데이터 디스크의 경우 32TB입니다.
**디스크 제한** |  컴퓨터당 최대 63개 디스크입니다.
**암호화된 디스크/볼륨** |  암호화된 디스크/볼륨의 컴퓨터는 마이그레이션에 지원되지 않습니다.
**공유된 디스크 클러스터** | 지원되지 않습니다.
**독립 디스크** | 지원됨.
**통과 디스크** | 지원됨.
**NFS** | 컴퓨터에 볼륨으로 탑재된 NFS 볼륨이 복제되지 않습니다.
**iSCSI 대상** | ISCSI 대상을 사용하는 컴퓨터는 에이전트 없는 마이그레이션에 대해 지원되지 않습니다.
**다중 경로 IO** | 지원되지 않습니다.
**팀 NIC** | 지원되지 않습니다.
**IPv6** | 지원되지 않습니다.
**PV 드라이버/XenServer 도구** | 지원 안 됨



## <a name="replication-appliance-requirements"></a>복제 어플라이언스 요구 사항

물리적 서버에서 복제 어플라이언스를 수동으로 설정하는 경우 테이블에 요약되어 있는 요구 사항을 준수하는지 확인합니다. Azure Migrate 허브에 제공된 OVA 템플릿을 사용하여 Azure Migrate 복제 어플라이언스를 VMware VM으로 설정하는 경우 어플라이언스는 Windows Server 2016으로 설정되고 지원 요구 사항을 준수합니다. 

- [복제 어플라이언스 요구 사항](migrate-replication-appliance.md#appliance-requirements)에 대해 알아봅니다.
- MySQL은 어플라이언스에 설치되어야 합니다. [설치 옵션](migrate-replication-appliance.md#mysql-installation)에 대해 알아봅니다.
- 복제 어플라이언스가 액세스해야 하는 [URL](migrate-replication-appliance.md#url-access)에 대해 알아봅니다.

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure로 복제된 모든 온-프레미스 VM은 이 표에 요약된 Azure VM 요구 사항을 충족해야 합니다. Site Recovery에서 복제에 대한 필수 구성 요소 검사를 실행할 때 일부 요구 사항이 충족되지 않으면 실패합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
게스트 운영 체제 | 지원되는 운영 체제를 확인합니다.<br/> 지원되는 운영 체제에서 실행되는 모든 워크로드를 마이그레이션할 수 있습니다. | 지원되지 않는 경우 확인이 실패합니다.
게스트 운영 체제 아키텍처 | 64비트. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 크기 | 최대 2,048GB. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 수 | 64개 이하. | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 크기 | 최대 32TB. | 지원되지 않는 경우 확인이 실패합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
공유 VHD | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
FC 디스크 | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
BitLocker | 지원되지 않습니다. | 컴퓨터의 복제를 사용하도록 설정하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다.
VM 이름 | 1~63자 사이입니다.<br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. |  Site Recovery에서 컴퓨터 속성의 값을 업데이트합니다.
마이그레이션 후 연결-Windows | 마이그레이션 후 Windows를 실행하는 Azure VM에 연결하려면:<br/> - 마이그레이션하기 전에 온-프레미스 VM에서 RDP를 사용하도록 설정합니다. **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱** 에서 모든 프로필에 대해 허용되는지 확인합니다.<br/> 사이트 간 VPN 액세스의 경우, RDP를 사용하도록 설정하고, **도메인 및 프라이빗** 네트워크에 대하여 **Windows 방화벽** -> **허용되는 앱과 기능** 에서 RDP를 허용합니다. 운영 체제의 SAN 정책이 **OnlineAll** 로 설정되어 있는지 확인합니다. [자세히 알아보기](prepare-for-migration.md). |
마이그레이션 후 연결-Linux | 마이그레이션한 후 SSH를 사용하여 Azure VM에 연결해야 합니다.<br/> 마이그레이션 전에 온-프레미스 머신에서 Secure Shell 서비스가 시작으로 설정되어 있고 방화벽 규칙에서 SSH 연결을 허용하는지 확인합니다.<br/> 장애 조치(failover) 후 Azure VM에서 장애 조치된 VM의 네트워크 보안 그룹 규칙 및 연결되어 있는 Azure 서브넷에 대한 SSH 포트로 들어오는 연결을 허용합니다. 또한, VM에 대한 공용 IP 주소를 추가하세요. |  


## <a name="next-steps"></a>다음 단계

물리적 서버 [마이그레이션](tutorial-migrate-physical-virtual-machines.md)
