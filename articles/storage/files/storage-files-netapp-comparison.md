---
title: Azure Files 및 Azure NetApp Files 비교 | Microsoft Docs
description: Azure Files 및 Azure NetApp Files의 비교입니다.
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 8/31/2021
ms.author: jeffpatt
ms.openlocfilehash: ac4965bf9de7b579704aa522ff08826f4f969cf2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463205"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Azure Files 및 Azure NetApp Files 비교

이 문서에서는 Azure Files와 Azure NetApp Files를 비교합니다. 

클라우드 파일 스토리지가 필요한 대부분의 작업은 Azure Files 또는 Azure NetApp Files에서 잘 작동합니다. 워크로드에 가장 적합한 항목을 결정하려면 이 문서에 제공된 정보를 검토하세요. 자세한 내용은 [Azure Files](./index.yml) 및 [Azure NetApp Files](../../azure-netapp-files/index.yml) 문서, 그리고 Azure Files와 Azure NetApp Files 간의 선택을 다루는 [모든 엔터프라이즈 파일 워크로드를 위한 공유 스토리지](https://www.youtube.com/watch?v=MJEbmITLwwU&t=4s) 세션을 참조하세요.

## <a name="features"></a>기능

| 범주 | Azure 파일 | Azure NetApp Files |
|---------|-------------------------|---------|
| 설명 | [Azure Files](https://azure.microsoft.com/services/storage/files/)는 완전 관리형의 고가용성 엔터프라이즈급 서비스이며 현재 위치 데이터 업데이트를 사용하는 임의 액세스 워크로드에 최적화되어 있습니다.<br><br> Azure Files는 Azure Blob과 같은 다른 서비스와 동일한 Azure Storage 플랫폼을 기반으로 구축됩니다. | [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)는 완전 관리형의 고가용성, 엔터프라이즈급 NAS 서비스입니다. 고급 데이터 관리 기능을 필요로 하는 가장 까다로운 고성능의 대기 시간이 짧은 워크로드를 처리할 수 있습니다. 이 기능을 사용하면 ‘마이그레이션 불가능’으로 간주되는 워크로드의 마이그레이션이 가능합니다.<br><br>  ANF는 일관된 Azure 환경과 온-프레미스와 같은 성능을 위해 Azure 데이터 센터 내에서 실행되는 ONTAP 스토리지 OS와 함께 NetApp의 운영 체제 미설치 컴퓨터를 기반으로 구축됩니다. |
| 프로토콜 | Premium<br><ul><li>SMB 2.1, 3.0, 3.1.1</li><li>NFS 4.1(미리 보기)</li><li>REST</li></ul><br>Standard<br><ul><li>SMB 2.1, 3.0, 3.1.1</li><li>REST</li></ul><br> 자세한 내용은 [사용 가능한 파일 공유 프로토콜](./storage-files-planning.md#available-protocols)을 참조하세요. | 모든 계층<br><ul><li>SMB 2.x, 3.x</li><li>NFS 3.0, 4.1</li><li>이중 프로토콜 액세스(NFSv3/SMB)</li></ul><br> 자세한 내용은 [NFS](../../azure-netapp-files/azure-netapp-files-create-volumes.md), [SMB](../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md) 또는 [이중 프로토콜](../../azure-netapp-files/create-volumes-dual-protocol.md) 볼륨을 만드는 방법을 참조하세요. |
| 지역 가용성 | Premium<br><ul><li>30개 이상 지역</li></ul><br>Standard<br><ul><li>모든 지역</li></ul><br> 자세한 내용은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=storage)을 참조하세요. | 모든 계층<br><ul><li>28 + 지역</li></ul><br> 자세한 내용은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=storage)을 참조하세요. |
| 중복 | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> 자세한 내용은 [중복](./storage-files-planning.md#redundancy)을 참조하세요. | 모든 계층<br><ul><li>기본 제공 로컬 HA</li><li>[지역 간 복제](../../azure-netapp-files/cross-region-replication-introduction.md)</li></ul> |
| SLA(서비스 수준 계약)<br><br> Azure Files 및 Azure NetApp Files에 대한 SLA는 다르게 계산됩니다. | [Azure Files용 SLA](https://azure.microsoft.com/support/legal/sla/storage/) | [Azure NetApp Files용 SLA](https://azure.microsoft.com/support/legal/sla/netapp) |  
| ID 기반 인증 및 권한 부여 | SMB<br><ul><li>AD DS(Active Directory Domain Services)</li><li>Azure AD DS(Azure Active Directory Domain Services)</li></ul><br> ID 기반 인증은 SMB 프로토콜을 사용하는 경우에만 지원됩니다. 자세한 내용은 [FAQ](./storage-files-faq.md#security-authentication-and-access-control)를 참조하세요. | SMB<br><ul><li>AD DS(Active Directory Domain Services)</li><li>Azure AD DS(Azure Active Directory Domain Services)</li></ul><br> NFS/SMB 이중 프로토콜<ul><li>ADDS/LDAP 통합</li></ul><br>NFSv3/NFSv4.1<ul><li>NFS 확장 그룹과 추가/LDAP 통합 [(미리 보기)](../../azure-netapp-files/configure-ldap-extended-groups.md)</li></ul><br> 자세한 내용은 [AZURE NETAPP FILES NFS faq](../../azure-netapp-files/faq-nfs.md) 및 [Azure NetApp Files SMB faq](../../azure-netapp-files/faq-smb.md)를 참조 하세요. |
| 암호화 | 모든 프로토콜<br><ul><li>고객 또는 Microsoft 관리 키를 사용하여 미사용 암호화(AES 256)</li></ul><br>SMB<br><ul><li>AES 256 또는 RC4-HMAC를 사용하여 Kerberos 암호화</li><li>전송 중 암호화</li></ul><br>REST (영문)<br><ul><li>전송 중 암호화</li></ul><br> 자세한 내용은 [보안 및 네트워킹](files-nfs-protocol.md#security-and-networking)을 참조하세요. | 모든 프로토콜<br><ul><li>Microsoft 관리 키를 사용하여 미사용 암호화(AES 256) </li></ul><br>SMB<ul><li>AES-CCM (SMB 3.0) 및 AES GCM (SMB 3.1.1)을 사용 하 여 전송 암호화</li></ul><br>NFS 4.1<ul><li>AES 256과 함께 Kerberos를 사용하여 전송 중 암호화</li></ul><br> 자세한 내용은 [보안 FAQ](../../azure-netapp-files/faq-security.md)를 참조하세요. |
| 액세스 옵션 | <ul><li>인터넷</li><li>보안 VNet 액세스</li><li>VPN Gateway</li><li>ExpressRoute</li><li>Azure 파일 동기화</li></ul><br> 자세한 내용은 [네트워크 고려 사항](./storage-files-networking-overview.md)을 참조하세요. | <ul><li>보안 VNet 액세스</li><li>VPN Gateway</li><li>ExpressRoute</li><li>[전역 파일 캐시](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Cache](../../hpc-cache/hpc-cache-overview.md)</li></ul><br> 자세한 내용은 [네트워크 고려 사항](../../azure-netapp-files/azure-netapp-files-network-topologies.md)을 참조하세요. |
| 데이터 보호  | <ul><li>증분 스냅샷</li><li>파일/디렉터리 사용자 자체 복원</li><li>새 위치로 복원</li><li>현재 위치 되돌리기</li><li>공유 수준 일시 삭제</li><li>Azure Backup 통합</li></ul><br> 자세한 내용은 [Azure Files로 데이터 보호 기능 향상](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/)을 참조하세요. | <ul><li>스냅샷(255/볼륨)</li><li>파일/디렉터리 사용자 자체 복원</li><li>새 볼륨으로 복원</li><li>현재 위치 되돌리기</li><li>[영역 간 복제](../../azure-netapp-files/cross-region-replication-introduction.md)(공개 미리 보기)</li></ul><br> 자세한 내용은 [Azure NetApp Files 스냅샷 작동 방식](../../azure-netapp-files/snapshots-introduction.md)을 참조하세요. |
| 마이그레이션 도구  | <ul><li>Azure Data Box</li><li>Azure 파일 동기화</li><li>스토리지 마이그레이션 서비스</li><li>AzCopy</li><li>Robocopy</li></ul><br> 자세한 내용은 [Azure 파일 공유로 마이그레이션](./storage-files-migration-overview.md)을 참조하세요. | <ul><li>[전역 파일 캐시](https://cloud.netapp.com/global-file-cache/azure)</li><li>[CloudSync](https://cloud.netapp.com/cloud-sync-service), [XCP](https://xcp.netapp.com/)</li><li>스토리지 마이그레이션 서비스</li><li>AzCopy</li><li>Robocopy</li><li>애플리케이션 기반(예: HSR, Data Guard, AOAG)</li></ul> |
| 계층 | <ul><li>Premium</li><li>트랜잭션 최적화</li><li>핫</li><li>쿨</li></ul><br> 자세한 내용은 [스토리지 계층](./storage-files-planning.md#storage-tiers)을 참조하세요. | <ul><li>Ultra</li><li>Premium</li><li>Standard</li></ul><br> 모든 계층은 밀리초 미만의 최소 대기 시간을 제공합니다.<br><br> 자세한 내용은 [서비스 수준](../../azure-netapp-files/azure-netapp-files-service-levels.md) 및 [성능 고려 사항](../../azure-netapp-files/azure-netapp-files-performance-considerations.md)을 참조하세요. |
| 가격 책정 | [Azure Files 가격 책정](https://azure.microsoft.com/pricing/details/storage/files/) | [Azure NetApp Files 가격 책정](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>확장성 및 성능

| 범주 | Azure 파일 | Azure NetApp Files |
|---------|---------|---------|
| 최소 공유/볼륨 크기 | Premium<br><ul><li>100GiB</li></ul><br>Standard<br><ul><li>최소값 없음</li></ul> | 모든 계층<br><ul><li>100GiB(최소 용량 풀 크기: 4TiB)</li></ul> |
| 최대 공유/볼륨 크기 | 100TiB | 모든 계층<br><ul><li>100TiB(500TiB 용량 풀 제한)</li></ul><br>Azure NetApp 계정당 최대 12.5PiB입니다. |
| 최대 공유/볼륨 IOPS | Premium<br><ul><li>최대 10만개</li></ul><br>Standard<br><ul><li>최대 20k</li></ul> | Ultra 및 프리미엄<br><ul><li>최대 45만개 </li></ul><br>Standard<br><ul><li>최대 32만개</li></ul> |
| 최대 공유/볼륨 처리량 | Premium<br><ul><li>최대 10GiB/초</li></ul><br>Standard<br><ul><li>최대 300MiB/초</li></ul> | Ultra 및 프리미엄<br><ul><li>최대 4.5GiB/초</li></ul><br>Standard<br><ul><li>최대 3.2GiB/초</li></ul> |
| 최대 파일 크기 | 4TiB | 16TiB |
| 파일당 최대 IOPS | Premium<br><ul><li>최대 8,000</li></ul><br>Standard<br><ul><li>1,000</li></ul> | 모든 계층<br><ul><li>최대 볼륨 제한</li></ul> |
| 파일당 최대 처리량 | Premium<br><ul><li>300MiB/퍼(SMB 다중 채널로 최대 1GiB/초)</li></ul><br>Standard<br><ul><li>60MiB/초</li></ul> | 모든 계층<br><ul><li>최대 볼륨 제한</li></ul> |
| SMB 다중 채널 | 예 | 예 |
| 대기 시간 | 단일 밀리초 최소 대기 시간(작은 IO의 경우 2ms ~ 3ms) | 밀리초 미만의 최소 대기 시간(임의 IO의 경우 1ms 미만)<br><br>자세한 내용은 [성능 벤치마크](../../azure-netapp-files/performance-benchmarks-linux.md)를 참조하세요. |

확장성 및 성능 목표에 대한 자세한 내용은 [Azure Files](./storage-files-scale-targets.md#azure-files-scale-targets) 및 [Azure NetApp Files](../../azure-netapp-files/azure-netapp-files-resource-limits.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Files 설명서](./index.yml)
* [Azure NetApp Files 설명서](../../azure-netapp-files/index.yml)
* [모든 엔터프라이즈 파일 워크로드 세션을 위한 공유 스토리지](https://www.youtube.com/watch?v=MJEbmITLwwU&t=4s)
