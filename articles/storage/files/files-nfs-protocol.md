---
title: Azure Files NFS 파일 공유
description: NFS(네트워크 파일 시스템) 프로토콜을 사용하여 Azure Files에서 호스트되는 파일 공유에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 31aec4e32178b2f0ca7905c6d584df298dd2059a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524344"
---
# <a name="nfs-file-shares-in-azure-files"></a>Azure Files NFS 파일 공유
Azure Files는 Azure 파일 공유를 탑재하기 위한 두 가지 업계 표준 프로토콜인 [SMB(서버 메시지 블록)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 프로토콜과 [NFS(네트워크 파일 시스템)](https://en.wikipedia.org/wiki/Network_File_System) 프로토콜을 제공합니다. Azure Files를 사용하여 워크로드에 가장 적합한 파일 시스템 프로토콜을 선택할 수 있습니다. 동일한 스토리지 계정 내에서 SMB 및 NFS 파일 공유를 만들 수 있지만 Azure 파일 공유는 SMB 및 NFS 프로토콜 둘 다에서 개별 Azure 파일 공유에 액세스하는 것을 지원하지는 않습니다. 모든 파일 공유에 대해 Azure Files는 스토리지 요구 사항에 맞게 스케일 업할 수 있고 수천 개의 클라이언트에서 동시에 액세스할 수 있는 엔터프라이즈급 파일 공유를 제공합니다.

이 문서에서는 NFS Azure 파일 공유에 대해 설명합니다. SMB Azure 파일 공유에 대한 내용은 [Azure Files의 SMB 파일 공유](files-smb-protocol.md)를 참조하세요.

> [!IMPORTANT]
>  프로덕션에 NFS 파일 공유를 사용하기 전에 알려진 문제 목록은 [Azure NFS 파일 공유 문제 해결](storage-troubleshooting-files-nfs.md) 문서를 참조하세요.

## <a name="common-scenarios"></a>일반적인 시나리오
NFS 파일 공유는 다음과 같은 시나리오에서 자주 사용됩니다.

- Linux 또는 POSIX 파일 시스템 API를 사용하여 작성된 LOB(기간 업무) 애플리케이션과 같은 Linux/UNIX 기반 애플리케이션용 백업 스토리지(POSIX 준수를 요구하지 않는 경우에도)
- POSIX 규격 파일 공유, 대/소문자 구분 또는 Unix 스타일 권한(UID/GID)이 필요한 워크로드.
- 새 애플리케이션 및 서비스 개발(특히 애플리케이션 또는 서비스에 임의 IO 및 계층 구조 스토리지에 대한 요구 사항이 있는 경우) 

## <a name="features"></a>기능
- 완전 POSIX 준수 파일 시스템
- 하드 링크 지원
- 바로 가기 링크 지원
- NFS 파일 공유는 현재 [4.1 프로토콜 사양](https://tools.ietf.org/html/rfc5661)의 대다수 기능만 지원합니다. 모든 종류의 위임 및 콜백, Kerberos 인증 및 전송 중 암호화와 같은 일부 기능은 지원되지 않습니다.


## <a name="security-and-networking"></a>보안 및 네트워킹
Azure Files에 저장된 모든 데이터는 Azure SSE(스토리지 서비스 암호화)를 사용하여 저장 데이터로 암호화됩니다. 스토리지 서비스 암호화는 Windows의 BitLocker와 유사하게 작동하며, 데이터는 파일 시스템 수준 아래에서 암호화됩니다. Azure 파일 공유의 파일 시스템 아래에서 데이터가 암호화되어 디스크로 인코딩되므로 Azure 파일 공유를 읽거나 쓰기 위해 클라이언트의 기본 키에 액세스할 필요가 없습니다. 저장 데이터 암호화는 SMB 및 NFS 프로토콜 모두에 적용됩니다.

전송 중인 암호화의 경우 Azure는 [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE)을 사용하여 Azure Datacenter 간에 전송 중인 모든 데이터에 대해 암호화 계층을 제공합니다. 이를 통해 Azure 데이터 센터 간에 데이터를 전송할 때 암호화가 존재합니다. SMB 프로토콜을 사용하는 Azure Files와 달리 NFS 프로토콜을 사용하는 파일 공유는 사용자 기반 인증을 제공하지 않습니다. NFS 공유에 대한 인증은 구성된 네트워크 보안 규칙을 기반으로 합니다. 따라서 NFS 공유에 안전한 연결만 설정되도록 하려면, 서비스 엔드포인트 또는 프라이빗 엔드포인트를 사용해야 합니다. 온-프레미스 공유에 액세스하려면, 프라이빗 엔드포인트 외에도 VPN 또는 ExpressRoute를 설정해야 합니다. 다음 원본에서 시작되지 않은 요청은 거부됩니다.

- [프라이빗 엔드포인트](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [P2S(지점 및 사이트) VPN](../../vpn-gateway/point-to-site-about.md)
    - [사이트 간](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [제한된 퍼블릭 엔드포인트](storage-files-networking-overview.md#storage-account-firewall-settings)

사용 가능한 네트워킹 옵션에 대한 자세한 내용은 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조하세요.

## <a name="support-for-azure-storage-features"></a>Azure Storage 기능 지원

다음 표에서는 NFS 4.1 기능이 사용하도록 설정된 계정의 Azure Storage 기능에 대한 현재 지원 수준을 보여줍니다. 

이러한 표에 표시되는 항목의 상태는 이후 지원이 계속 확장됨에 따라 변경될 수 있습니다.

| 스토리지 기능 | NFS 공유에 지원됨 |
|-----------------|---------|
| [파일 관리 평면 REST API](/rest/api/storagerp/file-shares) | ✔️ |
| [파일 데이터 평면 REST API](/rest/api/storageservices/file-service-rest-api)| ⛔ |
| 저장 데이터 암호화|   ✔️ |
| 전송 중 암호화| ⛔ |
| [LRS 또는 ZRS 중복 유형](storage-files-planning.md#redundancy)|  ✔️ |
| [프라이빗 엔드포인트](storage-files-networking-overview.md#private-endpoints) | ✔️  |
| 하위 디렉터리 탑재|  ✔️ |
| [특정 Azure Virtual Network에 대한 네트워크 액세스 권한 부여](storage-files-networking-endpoints.md#restrict-access-to-the-public-endpoint-to-specific-virtual-networks)|  ✔️  |
| [특정 IP 주소에 대한 네트워크 액세스 권한 부여](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#grant-access-from-an-internet-ip-range)| ⛔ |
| [프리미엄 계층](storage-files-planning.md#storage-tiers) |  ✔️  |
| [표준 계층(핫, 쿨 및 트랜잭션 최적화)](storage-files-planning.md#storage-tiers)| ⛔ |
| [POSIX-사용 권한](https://en.wikipedia.org/wiki/File-system_permissions#Notation_of_traditional_Unix_permissions)|  ✔️  |
| 루트 Squash|  ✔️  |
| [ID 기반 인증](storage-files-active-directory-overview.md) | ⛔ |
| [Azure 파일 공유 일시 삭제](storage-files-prevent-file-share-deletion.md) | ⛔  |
| [Azure 파일 동기화](../file-sync/file-sync-introduction.md)| ⛔ |
| [Azure 파일 공유 백업](../../backup/azure-file-share-backup-overview.md)| ⛔ |
| [Azure 파일 공유 스냅샷](storage-snapshots-files.md)| ⛔ |
| [GRS 또는 GZRS 중복 유형](storage-files-planning.md#redundancy)| ⛔ |
| [AZCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)| ⛔ |
| Azure Storage Explorer| ⛔ |
| 기존 스토리지 계정에 NFS 공유 만들기*| ⛔ |
| 16개를 초과하는 그룹 지원| ⛔ |

## <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="performance"></a>성능
NFS Azure 파일 공유는 SSD(반도체 드라이브)에 데이터를 저장하는 프리미엄 파일 공유에만 제공됩니다. NFS 공유의 IOPS 및 처리량은 프로비전된 용량으로 확장됩니다. IOPS, IO 버스트 및 처리량에 대한 수식을 이해하려면 청구 이해 문서의 [프로비전된 모델](understanding-billing.md#provisioned-model) 섹션을 참조하세요. 평균 IO 대기 시간은 작은 IO 크기의 경우 낮은 한 자리 밀리초이며 평균 메타데이터 대기 시간은 높은 한자리 밀리초입니다. untar와 같은 메타데이터 사용량이 많은 작업 및 WordPress와 같은 워크로드는 많은 수의 열기 및 닫기 작업으로 인해 추가 대기 시간이 발생할 수 있습니다.

## <a name="workloads"></a>워크로드
> [!IMPORTANT]
> 프로덕션에 NFS 파일 공유를 사용하기 전에 알려진 문제 목록은 [Azure NFS 파일 공유 문제 해결](storage-troubleshooting-files-nfs.md) 문서를 참조하세요.

NFS는 SAP 애플리케이션 계층, 데이터베이스 백업, 데이터베이스 복제, 메시징 큐, 범용 파일 서버용 홈 디렉터리 및 애플리케이션 워크로드용 콘텐츠 리포지토리와 같은 워크로드에서 잘 작동하는지 확인되었습니다.

다음 워크로드에는 알려진 문제가 있습니다. 알려진 문제 목록은 [Azure NFS 파일 공유 문제 해결](storage-troubleshooting-files-nfs.md) 문서를 참조하세요.
- Oracle Database는 dNFS 기능과 호환되지 않습니다.


## <a name="next-steps"></a>다음 단계
- [NFS 파일 공유 만들기](storage-files-how-to-create-nfs-shares.md)
- [NFS를 사용한 Azure Files, Blob Storage, Azure NetApp Files에 대한 액세스 비교](../common/nfs-comparison.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
