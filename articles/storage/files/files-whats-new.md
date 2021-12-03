---
title: Azure Files의 새로운 기능
description: Azure Files의 새로운 기능 및 향상된 기능에 대해 자세히 알아보세요.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/30/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8c6157cefb3f23d431c3edffb096493c9a5cedaf
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133517862"
---
# <a name="whats-new-in-azure-files"></a>Azure Files의 새로운 기능
Azure Files는 새로운 기능 및 향상된 기능을 제공하기 위해 정기적으로 업데이트됩니다. 이 문서에서는 Azure Files의 새로운 기능에 대해 자세한 정보를 제공합니다.

## <a name="2021-quarter-4-october-november-december"></a>2021년 4분기(10월, 11월, 12월)
### <a name="nfs-41-protocol-support-is-generally-available"></a>NFS 4.1 프로토콜 지원 일반 제공
Premium Azure 파일 공유는 이제 SMB 또는 NFS 4.1 프로토콜을 지원합니다. NFS 4.1은 로컬 중복 스토리지 및 영역 중복 스토리지 모두에 대해 Azure Files 프리미엄 계층을 지원하는 모든 지역에서 사용할 수 있습니다. NFS 4.1 프로토콜을 사용하여 만든 Azure 파일 공유는 다양한 Linux 및 컨테이너 기반 워크로드를 지원하는 완전히 POSIX 규격의 분산 파일 공유입니다. 워크로드의 예로는 고가용성 SAP 애플리케이션 계층, 엔터프라이즈 메시징, 사용자 홈 디렉터리, 사용자 지정 사업체 애플리케이션, 데이터베이스 백업, 데이터베이스 복제 및 Azure Pipelines 등이 있습니다.

자세한 내용은 다음을 참조하세요.

- [Azure Files NFS 파일 공유](files-nfs-protocol.md)
- [Azure Files NFS를 통해 Azure VM의 SAP NetWeaver 고가용성](../../virtual-machines/workloads/sap/high-availability-guide-suse-nfs-azure-files.md)
- [Azure Files 가격 책정](https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="azure-files-supports-symmetric-throughput-for-premium-file-shares"></a>Azure Files 프리미엄 파일 공유에 대한 대칭 처리량을 지원합니다.
Premium Azure 파일 공유는 이제 대칭 처리량 프로비저닝을 지원합니다. 이를 통해 Azure 파일 공유에 대해 프로비전된 처리량을 100% 수신, 100% 송신 또는 일부 수신 및 송신 혼합에 사용할 수 있습니다. 대칭 처리량은 사용 가능한 처리량을 최대한 활용할 수 있는 유연성을 제공하고 프리미엄 파일 공유를 표준 파일 공유에 맞춥니다.

자세한 내용은 다음을 참조하세요.
- [프리미엄 Azure 파일 공유에 대해 프로비전된 모델](understanding-billing.md#provisioned-model)
- [Azure Files 가격 책정](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="2021-quarter-3-july-august-september"></a>2021년 3사분기(7월, 8월, 9월)
### <a name="smb-multichannel-is-generally-available"></a>SMB 다중 채널은 일반적으로 사용 가능합니다.
SMB 다중 채널을 사용하면 SMB 클라이언트가 Azure 파일 공유에 대해 여러 병렬 연결을 설정할 수 있습니다. 이를 통해 SMB 클라이언트는 사용 가능한 모든 네트워크 대역폭을 완벽하게 활용하고, 네트워크 실패에 대한 복원력을 높이고, 총 소유 비용을 절감하며, 단일 클라이언트를 통해 읽기의 경우 2~3배, 쓰기의 경우 3~4배 가능하도록 설정할 수 있습니다. SMB 다중 채널은 프리미엄 파일 공유(FileStorage 스토리지 계정 종류에 배포된 파일 공유)에 사용할 수 있으며 기본값으로 사용하지 않도록 설정되어 있습니다. 

자세한 내용은 다음을 참조하세요.

- [Azure Files에서 SMB 다중 채널 성능](storage-files-smb-multichannel-performance.md)
- [SMB 다중 채널 사용](files-smb-protocol.md#smb-multichannel)
- [Windows Server의 SMB 다중 채널에 대한 개요 설명서](/azure-stack/hci/manage/manage-smb-multichannel)

### <a name="smb-311-and-smb-security-settings"></a>SMB 3.1.1 및 SMB 보안 설정
SMB 3.1.1는 Windows 10와 함께 출시된 가장 최신 버전의 SMB 프로토콜로 중요한 보안 및 성능 업데이트를 포함합니다. Azure Files SMB 3.1.1는 이미 지원되는 AES-128-CCM 및 AES-128-GCM과 AES-256-GCM의 두 가지 추가 암호화 모드가 함께 제공됩니다. 성능을 최대화하기 위해 AES-128-GCM은 기본 SMB 채널 암호화 옵션으로서 협상 됩니다. AES-128-CCM은 AES-128-GCM을 지원하지 않는 이전 클라이언트에서만 협상됩니다. 

조직의 규정 및 규정 준수 요구 사항에 따라, SMB 클라이언트, Azure Files 또는 둘 모두에서 허용되는 SMB 채널 암호화 옵션을 제한하여 AES-128-GCM 대신 AES-256-GCM을 협상할 수 있습니다. Windows Server 2022 및 Windows 10 버전 21H1에 AES-256-GCM에 대한 지원이 추가되었습니다.

SMB 3.1.1 외에도 Azure Files는 SMB 프로토콜의 동작을 변경하는 보안 설정을 노출합니다. 이 릴리스에서는 허용되는 SMB 프로토콜 버전, SMB 채널 암호화 옵션, 인증 메서드 및 Kerberos 티켓 암호화 옵션을 구성할 수 있습니다. 기본값으로 Azure Files는 호환성이 가장 높은 옵션을 사용 하도록 설정하지만 언제든지 이 옵션을 설정/해제할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [SMB 보안 설정](files-smb-protocol.md#smb-security-settings)
- [Windows](storage-how-to-use-files-windows.md) 및 [Linux](storage-how-to-use-files-linux.md) SMB 버전 정보
- [Windows Server의 SMB 기능 개요 설명서](/windows-server/storage/file-server/file-server-smb-overview)

## <a name="2021-quarter-2-april-may-june"></a>2021년 2사분기(4월, 5월, 6월)
### <a name="premium-hot-and-cool-storage-capacity-reservations"></a>프리미엄, 핫 및 쿨 스토리지 용량 예약 
Azure Files는 스토리지 용량 예약(*예약 인스턴스* 라고도 함)을 지원합니다. 스토리지 용량 예약을 통해 사용자는 스토리지 사용률을 미리 커밋하여 스토리지를 할인 받을 수 있습니다. Azure Files는 프리미엄, 핫 및 쿨 계층의 용량 예약을 지원합니다. 용량 예약은 1년 또는 3년을 기준으로 10TiB 또는 100TiB 단위로 판매됩니다. 

자세한 내용은 다음을 참조하세요.

- [Azure Files 청구 이해](understanding-billing.md)
- [예약된 용량을 통한 Azure Files 비용 최적화](files-reserve-capacity.md)
- [Azure Files 가격 책정](https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="improved-portal-experience-for-domain-joining-to-active-directory"></a>Active Directory에 가입하는 도메인에 대한 포털 환경 개선
Azure Storage 계정에 가입하는 도메인에 대한 경험이 프로세스를 통해 처음 Azure 파일 공유를 이용하는 관리자를 안내할 수 있도록 개선되었습니다. Azure Portal의 **파일 공유** 섹션에 있는 **파일 공유 설정** 에서 Active Directory를 선택 하는 경우 도메인 가입에 필요한 단계를 안내합니다.

:::image type="content" source="media/files-whats-new/ad-domain-join-1.png" alt-text="스토리지 계정을 Active Directory에 가입시키는 도메인에 대한 새 포털 환경의 스크린샷" lightbox="media/files-whats-new/ad-domain-join-1.png":::

자세한 내용은 다음을 참조하세요.

- [SMB 액세스에 대한 Azure Files ID 기반 인증 옵션 개요](storage-files-active-directory-overview.md)
- [개요 - Azure 파일 공유에 대해 SMB를 통한 온-프레미스 Active Directory Domain Services 인증](storage-files-identity-auth-active-directory-enable.md)

## <a name="2021-quarter-1-january-february-march"></a>2021년 1사분기(1월, 2월, 3월)
### <a name="azure-files-management-now-available-through-the-control-plane"></a>컨트롤 플레인을 통해 Azure Files 관리 사용 가능
Azure Files 리소스, 파일 서비스 및 파일 공유용 API 관리는 이제 컨트롤 플레인(`Microsoft.Storage` 리소스 공급자)을 통해 사용할 수 있습니다. 이를 통해 Azure Resource Manager 또는 Bicep 템플릿을 사용하여 Azure 파일 공유를 만들고, 데이터 평면(예: FileREST API)에 액세스할 수 없는 경우(예: 스토리지 계정의 공용 엔드포인트가 사용하지 않도록 설정된 경우) 완전히 관리 가능 하며 전체 RBAC(역할 기반 액세스 제어) 의미 체계를 지원할 수 있습니다.

대부분의 경우 컨트롤 플레인을 통해 Azure Files를 관리하는 것이 좋습니다. 컨트롤 플레인을 통해 파일 서비스 및 파일 공유의 관리를 지원하기 위해 Azure Portal, Azure storage PowerShell 모듈 및 Azure CLI는 컨트롤 플레인을 통해 대부분의 관리 작업을 지원하도록 업데이트 되었습니다. 

기존 스크립트 동작을 유지하기 위해 Azure storage PowerShell 모듈 및 Azure CLI는 데이터 평면을 사용하여 파일 서비스 및 파일 공유를 관리하는 기존 명령을 유지하고, 컨트롤 플레인을 사용하는 새 명령을 추가합니다. 포털 요청은 컨트롤 플레인 리소스 공급자를 통해서만 진행됩니다. PowerShell 및 CLI 명령은 다음과 같이 이름이 지정됩니다.

- Az.Storage PowerShell:
    - 컨트롤 플레인 파일 공유 cmdlet에는 `Rm` 접두사가 붙습니다(예: `New-AzRmStorageShare`, `Get-AzRmStorageShare`, `Update-AzRmStorageShare`, `Remove-AzRmStorageShare`). 
    - 기존 데이터 평면 파일 공유 cmdlet에는 접두사가 없습니다(예:`New-AzStorageShare`, `Get-AzStorageShare`, `Set-AzStorageShareQuota`, `Remove-AzStorageShare`).
    - 파일 서비스를 관리하는 cmdlet은 컨트롤 플레인을 통해서만 사용할 수 있으며 특수 한 접두사가 없습니다(예: `Get-AzStorageFileServiceProperty`, `Update-AzStorageFileServiceProperty`).
- Azure Storage CLI:
    - 컨트롤 플레인 파일 공유 명령은 `az storage share-rm` 명령 그룹에서 사용할 수 있습니다(예: `az storage share-rm create`, `az storage share-rm update` 등).
    - 기존 파일 공유 명령은 `az storage share` 명령 그룹에서 사용할 수 있습니다(예: `az storage share create`, `az storage share update` 등).
    - 파일 서비스를 관리하는 명령은 컨트롤 플레인을 통해서만 사용할 수 있으며 `az storage account file-service-properties` 명령 그룹을 통해 사용할 수 있습니다(예: `az storage account file-service-properties show`, `az storage account file-service-properties update`).

Azure Files 관리 API에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure Files REST API 개요](/rest/api/storageservices/file-service-rest-api)
- Azure Files 리소스에 대한 컨트롤 플레인(`Microsoft.Storage` 리소스 공급자) API: 
    - [`FileService`](/rest/api/storagerp/file-services) 
    - [`FileShare`](/rest/api/storagerp/file-shares) 
- [Azure PowerShell](/powershell/module/az.storage) 및 [Azure CLI](/en-us/cli/azure/storage)

## <a name="see-also"></a>추가 정보
- [Azure Files란?](storage-files-introduction.md)
- [Azure Files 배포 계획](storage-files-planning.md)
- [Azure 파일 공유 만들기](storage-how-to-create-file-share.md)
