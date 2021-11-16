---
title: Azure Virtual Machines 백업 전략에서 Oracle Database
description: Azure Linux VM 환경에서 Oracle Database를 백업하는 옵션입니다.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 5be1a70ecd9a5eefdd1df29a5305747cee09ddaa
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523755"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Azure Linux VM 백업 전략에서 Oracle Database

**적용 대상:** :heavy_check_mark: Linux VM 

데이터베이스 백업은 스토리지 구성 요소 오류 및 데이터 센터 오류로 인한 데이터 손실로부터 데이터베이스를 보호합니다. 또한 사용자 오류에서 복구하는 수단 및 개발 또는 테스트 목적으로 데이터베이스를 복제하는 방법으로 사용할 수도 있습니다. 

Azure에서 모든 스토리지는 크게 중복되어 있으므로 하나 이상의 디스크가 손실되어도 데이터베이스 중단으로 이어지지 않습니다. 이러한 백업은 규정 준수를 위한 복제 작업 또는 데이터 보존을 위해 인적 오류로부터 보호하는 데 주로 사용됩니다. DataGuard 같은 재해 복구 기술을 사용하지 않는 지역에서 가동 중단으로부터 보호하는 수단이기도 합니다. 이 경우 백업은 지역 중복 복제를 사용하여 다른 Azure 지역에 저장되어야 하므로 주 데이터베이스 지역 외부에서 사용할 수 있습니다.

## <a name="azure-storage"></a>Azure Storage 

[Azure Storage 서비스](../../../storage/common/storage-introduction.md)는 최신 데이터 스토리지 시나리오를 위한 Microsoft의 클라우드 스토리지 솔루션입니다. Azure Storage는 Oracle 데이터베이스용 백업 미디어로 적합한 Azure Linux VM에 외부 스토리지를 탑재하는 데 사용할 수 있는 다양한 서비스를 제공합니다. Oracle RMAN과 같은 백업 도구는 백업 또는 복원 작업을 시작하고 Azure Storage 서비스로/로부터 백업을 복사하는 데 필요합니다.
 
Azure Storage 서비스에서 다음과 같은 이점을 제공합니다.

-  내구성 및 고가용성 중복성은 일시적인 하드웨어 오류 발생 시 데이터를 안전하게 보호합니다. 모든 스토리지는 기본적으로 3중으로 미러링됩니다. 또한 로컬 재해 또는 자연 재해로 인한 장애를 방지할 수 있도록 데이터 센터 또는 지리적 영역에서 데이터를 복제하도록 선택할 수도 있습니다. 이러한 방식으로 복제된 데이터는 예기치 않은 중단이 발생할 경우 항상 사용 가능한 상태로 유지됩니다.

-  보안. Azure Storage 계정에 기록된 모든 데이터는 서비스에 의해 암호화됩니다. Azure Storage는 데이터에 액세스할 수 있는 사용자를 자세히 제어할 수 있습니다.

-  확장 가능. Azure Storage는 오늘날의 애플리케이션에 대한 데이터 저장소 및 성능 요구 사항을 충족하기 위해 대규모로 확장할 수 있도록 설계되었습니다.

-  관리됨. 하드웨어 유지 관리, 업데이트 및 중요한 문제를 Azure에서 처리합니다.

-  액세스 가능 Azure Storage의 데이터는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있습니다. Microsoft는 완성도 높은 REST API뿐만 아니라 .NET, Java, Node.js, Python, PHP, Ruby, Go 등 기타 다양한 언어로 Azure Storage용 클라이언트 라이브러리를 제공합니다. Azure Storage는 Azure PowerShell 또는 Azure CLI에서 스크립트를 지원합니다. 또한 Azure Portal 및 Azure Storage Explorer는 데이터 작업을 위한 쉬운 시각적 솔루션을 제공합니다.

Azure Storage 플랫폼에는 Oracle 데이터베이스용 백업 미디어로 사용하기에 적합한 다음과 같은 데이터 서비스가 포함되어 있습니다.

-  Azure Blob: 텍스트 및 이진 데이터에 대한 확장성이 뛰어난 개체 저장소입니다. 또한 Data Lake Storage Gen2를 통한 빅 데이터 분석에 대한 지원도 포함됩니다.

-  Azure Files: 클라우드 또는 온-프레미스 배포에 대한 관리되는 파일 공유입니다.

-  Azure Disks: Azure VM용 블록 수준 스토리지 볼륨입니다.

### <a name="cross-regional-storage-mounting"></a>지역 간 스토리지 탑재

지역 간 백업 스토리지에 액세스하는 기능은 BCDR의 중요한 측면이며 백업에서 다른 지역으로 데이터베이스를 복제하는 데 유용합니다. Azure 클라우드 스토리지는 5개 수준의 [중복도](../../../storage/common/storage-redundancy.md)를 제공합니다.
- 주 지역의 단일 물리적 위치 내에서 데이터가 세 번 복제되는 [LRS](../../../storage/common/storage-redundancy.md#locally-redundant-storage)(로컬 중복 스토리지)  
- 데이터가 주 지역에서 LRS로 보호되고 주 지역의 3개 AZ(가용성 영역)에 동기식으로 복제되는 [ZRS(영역 중복 스토리지)](../../../storage/common/storage-redundancy.md#zone-redundant-storage). 각 AZ는 LRS로도 보호됩니다. 
- 데이터가 주 지역에서 LRS로 보호되고 LRS에 의해 보호되는 보조 지역에 비동기식으로 복제되는 [GRS](../../../storage/common/storage-redundancy.md#geo-redundant-storage)(지역 중복 스토리지).
- [GZRS](../../../storage/common/storage-redundancy.md#geo-redundant-storage)(지역 영역 중복 스토리지)는 ZRS를 사용하여 주 지역에 있는 3개의 Azure 가용성 영역에서 데이터를 동기식으로 복사합니다. 그런 다음 보조 지역의 단일 물리적 위치에 데이터를 비동기적으로 복사합니다. 모든 위치에서 데이터는 LRS에 의해서도 보호됩니다. 
- [RA-GRS](../../../storage/common/storage-redundancy.md#geo-redundant-storage)(읽기 액세스 지역 중복 스토리지) 및 [RA-GZRS](../../../storage/common/storage-redundancy.md#geo-redundant-storage)(읽기 액세스 지역 영역 중복 스토리지)를 사용하면 보조 지역에 복제된 데이터에 대한 읽기 전용 액세스가 항상 허용됩니다. 

#### <a name="blob-storage-and-file-storage"></a>Blob Storage 및 File Storage

SMB 또는 NFS 4.1 프로토콜과 함께 Azure Files를 사용 하 여 백업 저장소로 탑재 하는 경우 Azure Files은 읽기 액세스 지역 중복 저장소 (RA-GRS) 및 읽기 액세스 지역 중복 저장소 (RA-GZRS)를 지원 하지 않습니다. 

또한 백업 스토리지 요구 사항이 5TiB보다 큰 경우 GRS 또는 GZRS 중복도를 지원하지 않고 LRS만 지원하는[대용량 파일 공유 기능](../../../storage/files/storage-files-planning.md)을 사용하도록 설정해야 Azure Files를 사용할 수 있습니다. 

NFS 3.0 프로토콜을 사용 하 여 탑재 된 Azure Blob는 현재 LRS 및 ZRS 중복성만 지원 합니다.  

모든 중복도 옵션으로 구성된 Azure Blob은 Blobfuse를 사용하여 탑재할 수 있습니다.

#### <a name="recovery-services-vault"></a>Recovery Services 자격 증명 모음 

Recovery Services 자격 증명 모음은 시간이 지남에 따라 만들어지는 복구 지점을 저장하고 백업 관련 작업을 수행하기 위한 인터페이스를 제공하는 관리 엔터티입니다. 여기에는 주문형 백업 수행, 복원 수행 및 백업 정책 만들기가 포함됩니다.
Azure Backup은 자격 증명 모음에 대한 스토리지를 자동으로 처리합니다. 스토리지를 만들 때 복제하는 방법을 지정해야 합니다. 항목을 자격 증명 모음에서 보호한 후에는 변경할 수 없습니다. 지역 중복도의 경우 지역 중복 설정을 선택하십시오.

보조 지역으로 복원하려는 경우 [Azure 쌍을 이루는 지역](../../../best-practices-availability-paired-regions.md)은 [지역 간 복원](../../../backup/backup-create-rs-vault.md) 기능을 사용하도록 설정되어야 합니다. 지역 간 복원을 사용하도록 설정한 경우 백업 스토리지는 GRS에서 RA-GRS(읽기 액세스 지역 중복 스토리지)로 이동됩니다. 

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Blob Storage는 대량의 비정형 데이터를 저장하는 데 사용되는 매우 비용 효율적이고 내구성이 뛰어나며 안전한 클라우드 기반 스토리지 서비스이며 Oracle Database 백업에 사용하기에 이상적인 미디어입니다. Azure Blob Storage는 NFS v3.0 프로토콜 또는 Blobfuse(Linux FUSE)를 사용하여 Azure Linux VM에 탑재할 수 있습니다.

#### <a name="azure-blob-blobfuse"></a>Azure Blob Blobfuse

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md)는 Azure Blob Storage에서 지원하는 가상 파일 시스템을 제공하기 위해 개발된 오픈 소스 프로젝트입니다. libfuse 오픈 소스 라이브러리를 사용하여 Linux FUSE 커널 모듈과 통신하고 Azure Storage Blob REST API를 사용하여 파일 시스템 작업을 구현합니다. Blobfuse는 현재 Ubuntu 및 Centos/RedHat 배포판에서 사용할 수 있습니다. 또한 [CSI 드라이버](https://github.com/kubernetes-sigs/blob-csi-driver)를 사용하여 Kubernetes에서도 사용할 수 있습니다. 

모든 Azure 지역에서 사용 가능하고 범용 v1/v2 및 Azure Data Lake Store Gen2를 비롯한 모든 스토리지 계정 형식에서 작동하지만 Blobfuse에서 제공되는 성능은 SMB 또는 NFS와 같은 대체 프로토콜보다 떨어지는 것으로 나타났습니다. 데이터베이스 백업 미디어로 사용하기에 적합하도록 SMB 또는 [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) 프로토콜을 사용하여 Azure Blob Storage를 탑재하는 것이 좋습니다. 

#### <a name="azure-blob-nfs-v30"></a>Azure Blob NFS v 3.0

이제 NFS (네트워크 파일 시스템) v 3.0 프로토콜에 대 한 Azure 지원을 사용할 수 있습니다. [NFS](../../../storage/blobs/network-file-system-protocol-support.md) 지원을 사용하면 Windows 및 Linux 클라이언트에서 Blob 스토리지 컨테이너를 Azure VM에 탑재할 수 있습니다. 

네트워크 보안을 유지하려면 NFS 탑재에 사용되는 스토리지 계정이 VNet 내에 포함되어야 합니다. Azure AD(Active Directory) 보안 및 ACL(액세스 제어 목록)은 NFS 3.0 프로토콜 지원을 사용하도록 설정된 계정에서 아직 지원되지 않습니다.

### <a name="azure-files"></a>Azure 파일

[Azure Files](../../../storage/files/storage-files-introduction.md)는 온-프레미스 또는 클라우드 기반 Windows, Linux 또는 macOS 클라이언트에 탑재할 수 있는 클라우드 기반의 완전 관리형 분산 파일 시스템입니다.

Azure Files은 SMB (서버 메시지 블록) 프로토콜 및 NFS (네트워크 파일 시스템) 프로토콜을 통해 액세스할 수 있는, 클라우드에서 완전히 관리 되는 플랫폼 간 파일 공유를 제공 합니다. Azure Files는 현재 다중 프로토콜 액세스를 지원하지 않으므로, NFS 공유 또는 SMB 공유 중 하나만 사용하여 공유할 수 있습니다. Azure 파일 공유를 만들기 전에, 어느 프로토콜이 요구 사항에 가장 적합한지 결정하는 것이 좋습니다.

또한 Azure 파일 공유는 Recovery Services 자격 증명 모음에 대한 Azure Backup을 통해 보호할 수 있으므로 Oracle RMAN 백업에 대한 추가 보호 계층을 제공합니다.

#### <a name="azure-files-nfs-v41"></a>Azure Files NFS v 4.1

Azure 파일 공유는 NFS(네트워크 파일 시스템) v4.1 프로토콜을 사용하여 Linux 배포판에 탑재할 수 있습니다. 지원 되는 기능에는 몇 가지 제한 사항이 있습니다. 자세한 내용은 [Azure Storage 기능에 대 한 지원](../../../storage/files/files-nfs-protocol.md#support-for-azure-storage-features)을 참조 하세요. 

[!INCLUDE [files-nfs-regional-availability](../../../../includes/files-nfs-regional-availability.md)]

#### <a name="azure-files-smb-30"></a>Azure Files SMB 3.0

Azure 파일 공유는 SMB 커널 클라이언트를 사용하여 Linux 배포판에 탑재할 수 있습니다. Linux 배포판에서 사용할 수 있는 CIFS(Common Internet File System) 프로토콜은 SMB 언어입니다. Linux VM에 Azure Files SMB를 탑재하는 경우 CIFS 유형의 파일 시스템으로 탑재되며, CIFS 패키지를 설치해야 합니다. 

Azure Files SMB는 모든 Azure 지역에서 일반적으로 공급되며 NFS v3.0 및 v4.1 프로토콜과 동일한 성능 특징을 보여 줍니다. 따라서 현재 Azure Linux VM에 백업 스토리지 미디어를 제공하는 권장 방법입니다.  

SMB 2.1과 SMB 3.0의 두 가지 SMB 버전이 지원되며, SMB 3.0이 전송 중에 암호화를 지원하므로 권장됩니다. 그러나 Linux 커널 버전에 따라 SMB 2.1 및 3.0에 대한 지원이 다릅니다. 자세한 내용은 [Linux에서 SMB Azure 파일 공유 탑재](../../../storage/files/storage-how-to-use-files-linux.md)를 참조하여 애플리케이션이 SMB 3.0을 지원하는지 확인하세요. 

Azure Files는 다중 사용자 파일 공유 서비스로 사용되도록 설계되었기 때문에 백업 스토리지 미디어로 더 적합하게 만들려면 특정 특성을 조정해야 합니다. 캐싱을 끄고 생성된 파일에 대한 사용자 및 그룹 ID를 설정하는 것이 좋습니다.

## <a name="azure-netapp-files"></a>Azure NetApp Files

[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) 서비스는 Azure VM에서 Oracle Database를 위한 완전한 스토리지 솔루션입니다. 엔터프라이즈급의 고성능 요금제 파일 스토리지를 기반으로 하여 모든 작업 유형을 지원하며 기본적으로 항상 사용 가능합니다. Oracle dNFS(Direct NFS) 드라이버와 함께 Azure NetApp Files는 Oracle Database에 대해 최적화된 스토리지 계층을 제공합니다.

Azure NetApp Files는 RoW(쓰기 시 리디렉션) 메커니즘을 사용하는 기본 스토리지 시스템에서 효율적인 스토리지 기반 스냅샷 복사본을 제공합니다. 매우 빠르게 스냅샷 복사본을 만들고 복원할 수 있지만 첫 번째 방어선 역할만 합니다. 따라서 해당 조직의 필수 복원 작업을 대부분 처리할 수 있으며 사용자 오류로부터 복원되는 경우가 많습니다. 그러나 스냅샷 복사본은 완전한 백업 방식이 아닙니다. 모든 백업 및 복원 요구 사항을 처리하기 위해서는 외부 스냅샷 복제본 및/또는 기타 백업 복사본을 지리적으로 먼 장소에 만들어 지역 가동 중단으로부터 보호해야 합니다. Azure에서 Oracle Database에 대해 NetApp Files를 사용하는 방법에 대한 자세한 내용을 보려면 이 [보고서](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf)를 참조하세요.

## <a name="azure-backup-service"></a>Azure Backup 서비스

[Azure Backup](../../../backup/backup-overview.md)은 데이터를 백업하고 Microsoft Azure 클라우드에서 복구할 수 있는 간단하고, 안전하며, 비용 효율적인 솔루션을 제공하는 완전 관리형 PaaS입니다. Azure Backup은 Azure VM에서 온-프레미스 클라이언트, Azure VM, Azure Files 공유 뿐만 아니라 SQL Server, Oracle, MySQL, PostreSQL 및 SAP HANA 데이터베이스도 백업 및 복원할 수 있습니다. 

Azure Backup은 실수로 데이터가 삭제되지 않도록 방지하기 위해 독립적이고 격리된 백업을 제공합니다. 백업은 복구 지점에 대한 기본 제공 관리를 사용하여 [Recovery Services 자격 증명 모음](../../../backup/backup-azure-recovery-services-vault-overview.md)에 저장됩니다. 구성 및 확장성이 간단하고, 백업이 최적화되어 있으며, 필요에 따라 쉽게 복원할 수 있습니다. 유지 관리 또는 모니터링 오버헤드 없이 기본 성능과 Azure 클라우드의 무제한 크기 조정을 사용하여 고가용성을 제공합니다. Azure Backup은 전송하는 인바운드 또는 아웃바운드 데이터의 양을 제한하지 않거나, 전송되는 데이터에 대한 요금을 부과하지 않으며, 데이터는 전송 및 저장 시 보호됩니다. 

내구성을 보장하기 위해 Azure Backup은 여러 가지 복제 유형을 제공하여 백업 데이터를 항상 사용 가능하게 유지합니다.

- [LRS](../../../storage/common/storage-redundancy.md#locally-redundant-storage)(로컬 중복 스토리지)는 데이터 센터의 스토리지 배율 단위로 데이터를 세 번 복제합니다(세 개의 데이터 복사본을 만듦).
- [GRS](../../../storage/common/storage-redundancy.md#geo-redundant-storage)(지역 중복 스토리지)는 기본값이며 권장되는 복제 옵션입니다. GRS는 데이터 원본의 기본 위치에서 수백 마일 떨어진 보조 지역으로 데이터를 복제합니다.

GRS 중복도를 사용하여 만든 자격 증명 모음에는 [지역 간 복원](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) 기능을 구성하는 옵션이 포함되어 있으므로, 이를 통해 Azure 쌍을 이루는 보조 지역에서 데이터를 복원할 수 있습니다.

Azure Backup 서비스는 애플리케이션 일관성 스냅샷이라고 하는, Oracle, MySQL, Mongo DB, SAP HANA 및 PostGreSQL과 같은 다양한 애플리케이션에 대해 Windows 및 Linux VM을 백업하는 동안 애플리케이션 일관성을 얻기 위한 [프레임워크](../../../backup/backup-azure-linux-app-consistent.md)를 제공합니다. 여기에는 디스크 스냅샷을 만들기 전에 사전 스크립트(애플리케이션 정지)를 호출하고 스냅샷이 완료된 후 사후 스크립트(애플리케이션 정지 해제 명령)를 호출하여 애플리케이션을 정상 모드로 되돌리는 작업이 포함됩니다. 사전 스크립트 및 사후 스크립트 샘플은 GitHub에서 제공되지만, 이러한 스크립트를 만들고 유지 관리하는 것은 사용자의 책임입니다. Oracle의 경우 데이터베이스는 온라인 백업을 허용하는 archivelog 모드에 있어야 하며, 적절한 데이터베이스 시작 및 종료 백업 명령은 사용자가 만들고 유지 관리해야 하는 사전 및 사후 스크립트에서 실행됩니다. 

이제 Azure Backup은 [향상된 사전 스크립트 및 사후 스크립트 프레임워크](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts)(현재 미리 보기 상태)를 제공합니다. 여기서 Azure Backup 서비스는 선택한 애플리케이션에 대해 패키지된 사전 스크립트 및 사후 스크립트를 제공합니다. Azure Backup 사용자는 애플리케이션 이름만 지정하면 됩니다. 그러면 Azure VM 백업에서 관련 사전 스크립트 및 사후 스크립트를 자동으로 호출합니다. 패키지된 사전 스크립트 및 사후 스크립트는 Azure Backup 팀에서 유지 관리하므로 사용자는 이러한 스크립트의 지원, 소유권 및 유효성을 보장할 수 있습니다. 현재 향상된 프레임워크에 대해 지원되는 애플리케이션은 Oracle 및 MySQL이며 나중에 더 많은 애플리케이션 유형이 지원될 예정입니다. 스냅샷은 증분 또는 쓰기 중 복사 스냅샷이 아니라 스토리지의 전체 복사본이므로 데이터베이스를 복원할 수 있는 효과적인 매체입니다.

## <a name="next-steps"></a>다음 단계

- [Oracle Database 빠른 시작 만들기](oracle-database-quick-create.md)
- [Azure Files로 Oracle Database 백업](oracle-database-backup-azure-storage.md)
- [Azure Backup 서비스를 사용하여 Oracle Database 백업](oracle-database-backup-azure-backup.md)
