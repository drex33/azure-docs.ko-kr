---
title: Azure Migrate의 Hyper-V 마이그레이션 지원
description: Azure Migrate를 사용한 Hyper-V 마이그레이션 지원에 대해 알아봅니다.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 267b05a13d951e111f056097c6d7dc8f3edef221
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187292"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Hyper-V 마이그레이션을 위한 지원 매트릭스

이 문서에서는 [Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool)으로 Hyper-V VM을 마이그레이션하기 위한 지원 설정 및 제한 사항을 요약합니다. Azure로 마이그레이션하기 위해 Hyper-V VM을 평가하는 방법에 대한 정보를 찾고 있다면 [평가 지원 매트릭스](migrate-support-matrix-hyper-v.md)를 검토하세요.

## <a name="migration-limitations"></a>마이그레이션 제한 사항

복제할 VM을 한 번에 최대 10개까지 선택할 수 있습니다. 더 많은 컴퓨터를 마이그레이션하려면 10개의 그룹으로 복제합니다.


## <a name="hyper-v-host-requirements"></a>Hyper-V 호스트 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **배포**       | Hyper-V 호스트는 독립 실행형이거나 클러스터에 배포할 수 있습니다. <br/>Azure Migrate 복제 소프트웨어(Hyper-V 복제 공급자)는 Hyper-V 호스트에 설치됩니다.|
| **권한**           | Hyper-V 호스트에 대한 관리자 권한이 필요합니다. |
| **호스트 운영 체제** | 최신 업데이트가 포함된 Windows Server 2019, Windows Server 2016 또는 Windows Server 2012 R2 이러한 운영 체제의 Server Core 설치도 지원됩니다. |
| **기타 소프트웨어 요구 사항** | .NET Framework 4.7 이상 |
| **포트 액세스** |  VM 복제 데이터를 보내기 위한 HTTPS 포트 443의 아웃바운드 연결.



## <a name="hyper-v-vms"></a>Hyper-V VM

| **지원**                  | **세부 정보**               
| :----------------------------- | :------------------- |
| **운영 체제** | Azure에서 지원하는 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](../virtual-machines/linux/endorsed-distros.md) 운영 체제 |
**Windows Server 2003** | Windows Server 2003을 실행하는 VM의 경우 마이그레이션하기 전에 [Hyper-V 통합 서비스를 설치](prepare-windows-server-2003-migration.md)해야 합니다. | 
**Azure의 Linux VM** | 일부 VM은 Azure에서 실행될 수 있도록 변경해야 할 수 있습니다.<br/><br/> Linux의 경우, Azure Migrate는 다음 운영 체제에 대해 자동으로 변경합니다.<br/> - Red Hat Enterprise Linux 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x<br/> - Cent OS  7.7, 7.6, 7.5, 7.4, 6.x</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - SUSE Linux Enterprise Server 15 SP1 <br/>- Ubuntu 20.04, 19.04, 19.10, 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8 <br/> Oracle Linux 7.7, 7.7-CI<br/> 다른 운영 체제의 경우 [필요한 변경](prepare-for-migration.md#verify-required-changes-before-migrating) 작업을 수동으로 수행합니다.
| **Azure의 필수 변경 사항** | 일부 VM은 Azure에서 실행될 수 있도록 변경해야 할 수 있습니다. 마이그레이션하기 전에 수동으로 조정합니다. 관련 문서에는 이를 수행하는 방법에 대한 지침이 포함되어 있습니다. |
| **Linux 부팅**                 | /boot가 전용 파티션에 있는 경우, OS 디스크에 상주해야 하며 여러 디스크에 분산되면 안 됩니다.<br/> /boot가 루트(/) 파티션의 일부인 경우 '/' 파티션은 OS 디스크에 있어야 하며 다른 디스크에 포함되지 않아야 합니다. |
| **UEFI 부팅**                  | 지원됨. UEFI 기반 VM은 Azure 2세대 VM으로 마이그레이션됩니다.  |
| **UEFI - 보안 부팅**         | 마이그레이션이 지원되지 않습니다.|
| **디스크 크기**                  | 최대 2TB OS 디스크이며, 데이터 디스크의 경우에는 4TB입니다.|
| **디스크 수** | VM당 최대 16개의 디스크입니다.|
| **암호화된 디스크/볼륨**    | 마이그레이션이 지원되지 않습니다.|
| **RDM/통과 디스크**      | 마이그레이션이 지원되지 않습니다.|
| **공유 디스크** | 공유 디스크를 사용하는 VM은 마이그레이션이 지원되지 않습니다.|
| **NFS**                        | VM에 볼륨으로 탑재된 NFS 볼륨은 복제되지 않습니다.|
| **ISCSI**                      | iSCSI 대상을 사용하는 VM은 마이그레이션이 지원되지 않습니다.
| **대상 디스크**                | 관리 디스크에서만 Azure VM으로 마이그레이션할 수 있습니다. |
| **IPv6** | 지원 안 됨|
| **NIC 팀** | 지원 안 됨|
| **Azure Site Recovery** | VM이 Azure Site Recovery를 사용하여 복제하도록 설정된 경우 Azure Migrate 서버 마이그레이션을 사용하여 복제할 수 없습니다.|
| **Ports** | VM 복제 데이터를 보내기 위한 HTTPS 포트 443의 아웃바운드 연결.|

### <a name="url-access-public-cloud"></a>URL 액세스(퍼블릭 클라우드)

Hyper-V 호스트의 복제 공급자 소프트웨어는 이러한 URL에 액세스해야 합니다.

**URL** | **세부 정보**
--- | ---
login.microsoftonline.com | Active Directory를 사용한 액세스 제어 및 ID 관리
*.backup.windowsazure.com | 복제 데이터 전송 및 조정
\*.hypervrecoverymanager.windowsazure.com | 복제 관리에 사용됩니다.
*.blob.core.windows.net | 스토리지 계정에 데이터를 업로드합니다. 
dc.services.visualstudio.com | 내부 모니터링에 사용되는 앱 로그를 업로드합니다.
time.windows.com | 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인합니다.

### <a name="url-access-azure-government"></a>URL 액세스(Azure Government)

Hyper-V 호스트의 복제 공급자 소프트웨어는 이러한 URL에 액세스해야 합니다.

**URL** | **세부 정보**
--- | ---
login.microsoftonline.us | Active Directory를 사용한 액세스 제어 및 ID 관리
backup.windowsazure.us | 복제 데이터 전송 및 조정
*. hypervrecoverymanager.windowsazure.us | 복제 관리에 사용됩니다.
*.blob.core.usgovcloudapi.net | 스토리지 계정에 데이터를 업로드합니다.
dc.services.visualstudio.com | 내부 모니터링에 사용되는 앱 로그를 업로드합니다.
time.nist.gov | 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인합니다.   

>[!Note]
>
> Migrate 프로젝트에 **프라이빗 엔드포인트 연결** 이 있는 경우 Hyper-V 호스트의 복제 공급자 소프트웨어는 프라이빗 링크 지원을 위해 이러한 URL에 액세스해야 합니다. 
> - *.blob.core.windows.com - 복제된 데이터를 저장하는 스토리지 계정 액세스를 위해 사용합니다. 이 옵션은 선택 사항이며, 스토리지 계정에 연결된 프라이빗 엔드포인트가 있는 경우 필요하지 않습니다. 
> - login.windows.net - Active Directory를 사용한 액세스 제어 및 ID 관리를 위해 사용합니다.

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure로 복제된 모든 온-프레미스 VM은 이 표에 요약된 Azure VM 요구 사항을 충족해야 합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
운영 체제 디스크 크기 | 최대 2,048GB. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 수 | 16개 이하. | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 크기 | 최대 4,095GB | 지원되지 않는 경우 확인이 실패합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
공유 VHD | 지원 안 됨 | 지원되지 않는 경우 확인이 실패합니다.
FC 디스크 | 지원 안 됨 | 지원되지 않는 경우 확인이 실패합니다.
BitLocker | 지원 안 됨 | 컴퓨터의 복제를 사용하도록 설정하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다.
VM 이름 | 1~63자 사이입니다.<br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. |  Site Recovery에서 컴퓨터 속성의 값을 업데이트합니다.
마이그레이션 후 연결-Windows | 마이그레이션 후 Windows를 실행하는 Azure VM에 연결하려면:<br/><br/> - 마이그레이션하기 전에 온-프레미스 VM에서 RDP를 사용하도록 설정합니다. **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱** 에서 모든 프로필에 대해 허용되는지 확인합니다.<br/><br/> - 사이트 간 VPN 액세스의 경우, RDP를 사용하도록 설정하고, **도메인 및 프라이빗** 네트워크에 대해 **Windows 방화벽** -> **허용되는 앱과 기능** 에서 RDP를 허용합니다. 운영 체제의 SAN 정책이 **OnlineAll** 로 설정되어 있는지 확인합니다. [자세히 알아보기](prepare-for-migration.md). |
마이그레이션 후 연결-Linux | 마이그레이션한 후 SSH를 사용하여 Azure VM에 연결하려면:<br/><br/> - 마이그레이션 전에 온-프레미스 컴퓨터에서 Secure Shell 서비스가 시작으로 설정되어 있고 방화벽 규칙에서 SSH 연결을 허용하는지 확인합니다.<br/><br/> - 마이그레이션 후 Azure VM에서 장애 조치(failover)된 VM의 네트워크 보안 그룹 규칙 및 연결된 Azure 서브넷에 대해 SSH 포트로 들어오는 연결을 허용합니다. 또한 VM에 대한 공용 IP 주소를 추가합니다. |  

## <a name="next-steps"></a>다음 단계

마이그레이션을 위해 [Hyper-V VM을 마이그레이션](tutorial-migrate-hyper-v.md)합니다.
