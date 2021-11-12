---
title: Azure Files 배포에 대한 계획 | Microsoft Docs
description: Azure 파일 배포 계획을 이해합니다. Azure Files 공유를 직접 탑재하거나 Azure 파일 동기화를 사용하여 Azure 파일 공유를 온-프레미스로 캐시할 수 있습니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 2cd4423d1d006555eedcbf7a6f08f04e415ea9bf
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292930"
---
# <a name="planning-for-an-azure-files-deployment"></a>Azure Files 배포에 대한 계획
[Azure Files](storage-files-introduction.md)는 서버리스 Azure 파일 공유를 직접 탑재하거나 Azure 파일 동기화를 사용하여 온-프레미스로 Azure 파일 공유를 캐싱하는 두 가지 방법으로 배포할 수 있습니다. 선택한 배포 옵션에 따라 배포 계획에서 고려해야 할 측면이 달라집니다. 

- **Azure 파일 공유의 직접 탑재**: Azure Files는 SMB(서버 메시지 블록) 또는 NFS(네트워크 파일 시스템) 액세스를 제공하므로, OS에서 사용할 수 있는 표준 SMB 또는 NFS(미리 보기) 클라이언트를 사용하여 온-프레미스 또는 클라우드에서 Azure 파일 공유를 탑재할 수 있습니다. Azure 파일 공유는 서버리스이므로 프로덕션 시나리오를 위해 배포하는 경우 파일 서버 또는 NAS 디바이스를 관리할 필요가 없습니다. 즉 소프트웨어 패치를 적용하거나 실제 디스크를 교환할 필요가 없습니다. 

- **Azure 파일 동기화를 사용하여 온-프레미스에서 Azure 파일 공유 캐시**: Azure 파일 동기화를 사용하면 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 온-프레미스(또는 클라우드) Windows Server를 Azure SMB 파일 공유의 빠른 캐시로 변환합니다. 

이 문서에서는 온-프레미스 또는 클라우드 클라이언트에서 직접 탑재할 Azure 파일 공유를 배포하기 위한 고려 사항을 주로 다룹니다. Azure 파일 동기화 배포를 계획하려면 [Azure 파일 동기화 배포에 대한 계획](../file-sync/file-sync-planning.md)을 참조하세요.

## <a name="available-protocols"></a>사용 가능한 프로토콜
Azure Files는 Azure 파일 공유를 탑재하기 위한 두 가지 업계 표준 프로토콜인 [SMB(서버 메시지 블록)](files-smb-protocol.md) 프로토콜과 [NFS(네트워크 파일 시스템)](files-nfs-protocol.md) 프로토콜을 제공합니다. Azure Files를 사용하여 워크로드에 가장 적합한 파일 시스템 프로토콜을 선택할 수 있습니다. 동일한 스토리지 계정 내에서 SMB와 NFS Azure 파일 공유를 만들 수 있지만 Azure 파일 공유는 동일한 파일 공유에서 SMB와 NFS 프로토콜을 둘 다 지원하지 않습니다. NFS 4.1은 현재 새 **FileStorage** 스토리지 계정 유형 내에서만 지원됩니다(프리미엄 파일 공유만 해당).

SMB 및 NFS 파일 공유를 둘 다 사용하면 Azure Files는 스토리지 요구 사항에 맞게 스케일 업할 수 있고 수천 개의 클라이언트에서 동시에 액세스할 수 있는 엔터프라이즈급 파일 공유를 제공합니다.

| 기능 | SMB | NFS(미리 보기) |
|---------|-----|---------------|
| 지원되는 프로토콜 버전 | SMB 3.1.1, SMB 3.0, SMB 2.1 | NFS 4.1 |
| 권장 OS | <ul><li>Windows 10, 버전 21H1 이상</li><li>Windows Server 2019 이상</li><li>Linux 커널 버전 5.3 이상</li></ul> | Linux 커널 버전 4.3 이상 |
| [사용 가능한 계층](storage-files-planning.md#storage-tiers)  | 프리미엄, 트랜잭션 최적화, 핫, 쿨 | Premium |
| 청구 모델 | <ul><li>[프리미엄 파일 공유의 프로비저닝된 용량](./understanding-billing.md#provisioned-model)</li><li>[표준 파일 공유의 종량제](./understanding-billing.md#pay-as-you-go-model)</li></ul> | [프로비전된 용량](./understanding-billing.md#provisioned-model) |
| [중복](storage-files-planning.md#redundancy) | LRS, ZRS, GRS, GZRS | LRS, ZRS |
| 파일 시스템 의미 체계 | Win32 | POSIX |
| 인증 | ID 기반 인증(Kerberos), 공유 키 인증(NTLMv2) | 호스트 기반 인증 |
| 권한 부여 | Win32 스타일 ACL(액세스 제어 목록) | UNIX 스타일 사용 권한 |
| 대/소문자 구분 | 대/소문자 구분 안 함, 대/소문자 유지 | 대/소문자 구분 |
| 열려 있는 파일 삭제 또는 수정 | 잠금만 사용 | Yes |
| 파일 공유 | [Windows 공유 모드](/windows/win32/fileio/creating-and-opening-files) | 바이트 범위 권고 네트워크 잠금 관리자 |
| 하드 링크 지원 | 지원되지 않음 | 지원 여부 |
| 바로 가기 링크 지원 | 지원되지 않음 | 지원 여부 |
| 필요에 따라 인터넷에 액세스 가능 | 예(SMB 3.0 이상만 해당) | No |
| FileREST 지원 | Yes | 하위 집합: <br /><ul><li>[`FileService`에 대한 작업](/rest/api/storageservices/operations-on-the-account--file-service-)</li><li>[`FileShares`에 대한 작업](/rest/api/storageservices/operations-on-shares--file-service-)</li><li>[`Directories`에 대한 작업](/rest/api/storageservices/operations-on-directories)</li><li>[`Files`에 대한 작업](/rest/api/storageservices/operations-on-files)</li></ul> |

## <a name="management-concepts"></a>관리 개념
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Azure 파일 공유를 스토리지 계정에 배포할 때는 다음을 수행하기를 권합니다.

- Azure 파일 공유만 다른 Azure 파일 공유와 함께 스토리지 계정에 배포합니다. Azure 파일 공유 및 Blob 컨테이너와 같은 스토리지 리소스가 스토리지 계정의 한도를 공유하기 때문에 GPv2 스토리지 계정은 복합 목적 스토리지 계정의 보유를 허용하지만, 리소스를 한데 섞으면 나중에 성능 문제 해결이 어려워질 수 있습니다. 

- Azure 파일 공유를 배포할 때 스토리지 계정의 IOPS 제한 사항에 주의합니다. 파일 공유를 스토리지 계정과 1:1로 매핑하는 것이 가장 좋지만, 조직과 Azure 모두의 다양한 제한과 제약으로 인해 항상 가능한 것은 아닙니다. 하나의 스토리지 계정에 파일 공유를 하나만 배포할 수 없는 경우 많은 작업을 수행하는 공유와 적은 작업을 수행하는 공유를 고려하여 가장 많이 사용하는 파일 공유가 동일한 스토리지 계정에 함께 포함되지 않도록 하십시오.

- 사용자 환경에서 GPv2 및 FileStorage 계정만 배포하고 GPv1 및 클래식 스토리지 계정은 업그레이드합니다. 

## <a name="identity"></a>ID
Azure 파일 공유에 액세스하려면 파일 공유의 사용자를 인증하고 공유에 액세스할 수 있는 권한을 부여해야 합니다. 이 작업은 파일 공유에 액세스하는 사용자의 ID를 기반으로 수행합니다. Azure Files는 주요 ID 공급자 셋과 통합됩니다.
- **온-프레미스 AD DS(Active Directory Domain Services)** : Azure Storage 계정은 Windows Server 파일 서버 또는 NAS 디바이스와 마찬가지로 고객이 소유하는 Active Directory Domain Services 도메인에 조인할 수 있습니다. 도메인 컨트롤러를 온-프레미스, Azure VM 또는 다른 클라우드 공급자의 VM으로 배포할 수 있습니다. Azure Files는 도메인 컨트롤러가 호스트되는 위치와는 무관합니다. 스토리지 계정이 도메인에 조인되면 최종 사용자는 PC에 로그인할 때 쓴 사용자 계정으로 파일 공유를 탑재할 수 있습니다. AD 기반 인증은 Kerberos 인증 프로토콜을 사용합니다.
- **Azure AD DS(Azure Active Directory Domain Services)** : Azure AD DS는 Azure 리소스에 사용할 수 있는 Microsoft 관리형 도메인 컨트롤러를 제공합니다. Azure AD DS에 스토리지 계정을 조인하는 도메인에는 고객이 소유한 Active Directory에 조인하는 도메인과 비슷한 이점이 있습니다. 이 배포 옵션은 AD 기반 사용 권한이 필요한 애플리케이션 리프트 앤 시프트 시나리오에 가장 유용합니다. Azure AD DS는 AD 기반 인증을 제공하므로 이 옵션 또한 Kerberos 인증 프로토콜을 사용합니다.
- **Azure 스토리지 계정 키**: Azure 스토리지 계정 키를 사용하여 Azure 파일 공유를 탑재할 수도 있습니다. 이 방식으로 파일 공유를 탑재하면 스토리지 계정 이름이 사용자 이름으로 사용되고 스토리지 계정 키가 암호로 사용됩니다. 스토리지 계정 키를 사용하여 Azure 파일 공유를 탑재하는 작업은 실질적인 관리자 작업입니다. 탑재된 파일 공유에는 ACL이 있어도 공유되는 모든 파일 및 폴더에 대한 전체 권한이 있기 때문입니다. 스토리지 계정 키를 사용하여 SMB를 통해 탑재하는 경우에는 NTLMv2 인증 프로토콜이 사용됩니다.

온-프레미스 파일 서버에서 마이그레이션하거나 Windows 파일 서버 또는 NAS 어플라이언스와 같이 동작하기 위해 Azure Files에서 새 파일 공유를 만드는 고객의 경우에는,스토리지 계정을 **고객 소유의 Active Directory** 에 조인하는 도메인을 권합니다. 스토리지 계정을 고객 소유의 Active Directory의 도메인에 조인하는 방법에 대한 자세한 내용은 [Azure Files Active Directory 개요](storage-files-active-directory-overview.md)를 참조하세요.

스토리지 계정 키를 사용하여 Azure 파일 공유에 액세스하려는 경우 [네트워킹](#networking) 섹션에 설명된 대로 서비스 엔드포인트 사용을 권합니다.

## <a name="networking"></a>네트워킹
Azure 파일 공유는 스토리지 계정의 퍼블릭 엔드포인트를 통해 어디에서나 액세스할 수 있습니다. 즉, 사용자의 로그온 ID로 권한이 부여된 요청과 같은 인증된 요청이 Azure 내부 또는 외부에서 안전하게 시작될 수 있습니다. 대부분의 고객 환경에서 Azure VM에서의 탑재가 성공하더라도 온-프레미스 워크스테이션에서 Azure 파일 공유의 초기 탑재가 실패합니다. 이는 많은 조직 및 ISP(인터넷 서비스 공급자)에서 SMB를 통해 통신하는 포트인 445 포트를 차단하기 때문입니다. 포트 445에서 시작되는 액세스를 허용하거나 거부하는 ISP에 대한 요약을 확인하려면 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)으로 이동합니다.

Azure 파일 공유로 오는 액세스를 차단 해제하려면 두 가지 주요 옵션이 있습니다.

- 조직의 온-프레미스 네트워크에 445 포트를 차단 해제합니다. Azure 파일 공유는 SMB 3.x 및 FileREST API와 같은 인터넷 안전 프로토콜을 사용하여 퍼블릭 엔드포인트를 통해서만 외부적으로 액세스할 수 있습니다. 이 방법은 조직의 아웃바운드 포트 규칙 변경 외에도 고급 네트워킹 구성이 불필요하므로 온-프레미스로 Azure 파일 공유에 액세스하는 가장 쉬운 방법입니다. 그러나 SMB 프로토콜의 레거시 및 사용되지 않는 버전, 즉 SMB 1.0의 제거를 권합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Windows/Windows 서버 보안 설정](/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3) 및 [Linux 보안 설정](files-remove-smb1-linux.md)을 참조하세요.

- ExpressRoute 또는 VPN 연결을 통해 Azure 파일 공유에 액세스합니다. 네트워크 터널을 통해 Azure 파일 공유에 액세스하는 경우 SMB 트래픽이 조직 경계를 통과하지 않으므로 온-프레미스 파일 공유와 같은 Azure 파일 공유를 탑재할 수 있습니다.   

기술적 관점으로는 퍼블릭 엔드포인트를 통한 Azure 파일 공유 탑재가 훨씬 간단하지만 대부분의 고객은 ExpressRoute 또는 VPN 연결을 통한 Azure 파일 공유를 탑재를 선택할 것으로 추정됩니다. 이러한 옵션 탑재는 SMB 및 NFS 공유 양쪽에서 가능합니다. 옵션을 탑재하려면 다음과 같이 환경을 구성해야 합니다.  

- **ExpressRoute, 사이트 간 또는 지점 및 사이트 간 VPN을 사용하는 네트워크 터널링**: 가상 네트워크로 터널링하면 포트 445가 차단된 경우에도 온-프레미스에서 Azure 파일 공유에 액세스할 수 있습니다.
- **프라이빗 엔드포인트**: 프라이빗 엔드포인트는 가상 네트워크의 주소 공간 내에서 스토리지 계정에 전용 IP 주소를 제공합니다. 이렇게 하면 Azure 스토리지 클러스터에서 소유하는 모든 IP 주소 범위에 온-프레미스 네트워크를 열 필요 없이 네트워크 터널링을 사용할 수 있습니다. 
- **DNS 전달**: 프라이빗 엔드포인트의 IP 주소로 확인할 수 있도록 온-프레미스 DNS를 구성하여 스토리지 계정 이름(퍼블릭 클라우드 지역의 경우에는 `storageaccount.file.core.windows.net`)을 확인합니다.

> [!Important]  
> Azure Files는 여러 네트워크 라우팅 옵션을 지원합니다. 기본 옵션인 Microsoft 라우팅은 모든 Azure Files 구성에서 작동합니다. 인터넷 라우팅 옵션은 AD 도메인 가입 시나리오 또는 Azure 파일 동기화를 지원하지 않습니다.

Azure 파일 공유 배포와 관련된 네트워킹을 계획을 짜려면 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조하세요.

## <a name="encryption"></a>암호화
Azure Files는 Azure 파일 공유를 탑재/액세스할 때 사용되는 암호화와 관련된 전송 중 암호화와, 데이터가 디스크에 저장될 때 사용되는 암호화와 관련된 미사용 암호화의 두 가지 형식의 암호화를 지원합니다. 

### <a name="encryption-in-transit"></a>전송 중 암호화

> [!IMPORTANT]
> 이 섹션에서는 SMB 공유에 대한 전송 중 암호화 세부 정보에 대해 설명합니다. NFS 공유를 통한 전송 중 암호화에 관한 자세한 내용은 [보안 및 네트워킹](files-nfs-protocol.md#security-and-networking)을 참조하세요.

기본적으로 모든 Azure 스토리지 계정은 전송 중 암호화를 사용하도록 설정되어 있습니다. 즉, SMB를 통해 파일 공유를 탑재하거나 FileREST 프로토콜(예: Azure Portal, PowerShell/CLI 또는 Azure SDK)을 통해 액세스할 때 Azure Files는 암호화 또는 HTTPS를 사용하는 SMB 3.x를 통해 만든 연결만 허용합니다. SMB 3.x를 지원하지 않는 클라이언트 또는 SMB 3.x를 지원하지만 SMB 암호화를 지원하지 않는 클라이언트는 전송 중 암호화를 사용하도록 설정된 경우 Azure 파일 공유를 탑재할 수 없습니다. 암호화를 통해 SMB 3.x를 지원하는 운영 체제에 대한 자세한 내용은 자세한 [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) 및 [Linux](storage-how-to-use-files-linux.md)용 설명서를 참조하세요. 모든 현재 버전의 PowerShell, CLI 및 SDK는 HTTPS를 지원합니다.  

Azure 스토리지 계정에 대해 전송 중 암호화를 사용하지 않도록 설정할 수 있습니다. 또한 암호화를 사용하지 않도록 설정하면 Azure Files에서 SMB 2.1, 암호화되지 않은 SMB 3.x 및 HTTP를 통한 암호화되지 않은 FileREST API 호출을 허용합니다. 전송 중 암호화를 사용하지 않도록 설정하는 주된 이유는 Windows Server 2008 R2 또는 이전 버전의 Linux 배포와 같은 이전 운영 체제에서 실행되어야 하는 레거시 애플리케이션을 지원하기 위해서입니다. Azure Files는 Azure 파일 공유와 동일한 Azure 지역 내에서만 SMB 2.1 연결을 허용합니다. Azure 파일 공유의 Azure 지역 외부(예: 온-프레미스 또는 다른 Azure 지역)에 있는 SMB 2.1 클라이언트는 파일 공유에 액세스할 수 없습니다.

전송 중 데이터 암호화를 사용하도록 설정하는 것이 좋습니다.

전송 중 암호화에 대한 자세한 내용은 [Azure Storage에서 보안 전송 필요](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

### <a name="encryption-at-rest"></a>휴지 상태의 암호화
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>데이터 보호
Azure Files에는 데이터를 백업하고 복구하며 보안 위협으로부터 보호할 수 있도록 하는 다중 계층화된 접근 방식이 있습니다.

### <a name="soft-delete"></a>일시 삭제
파일 공유의 일시 삭제는 파일 공유를 실수로 삭제한 경우 복구할 수 있도록 하는 스토리지 계정 수준 설정입니다. 파일 공유가 삭제되면 영구적으로 삭제되는 대신 일시 삭제된 상태로 전환됩니다. 일시 삭제된 데이터를 영구적으로 삭제하기 전에 복구할 수 있는 시간을 설정하고, 이 보존 기간 동안 언제든지 공유 삭제를 취소할 수 있습니다. 

대부분의 파일 공유에 일시 삭제를 설정하기를 권합니다. 공유 삭제를 자주 할 것으로 예상되는 워크플로가 있는 경우에는 보존 기간을 짧게 설정하거나 일시 삭제를 사용하지 않도록 설정할 수 있습니다.

일시 삭제에 대한 자세한 내용은 [실수로 인한 데이터 삭제 방지](./storage-files-prevent-file-share-deletion.md)를 참조하세요.

### <a name="backup"></a>Backup
Azure 파일 공유를 [공유 스냅샷](./storage-snapshots-files.md)(공유의 읽기 전용 지정 시간 복사본)으로 백업할 수 있습니다. 스냅샷은 증분형이므로 이전 스냅샷 이후 변경된 데이터만을 포함합니다. 각 파일 공유마다 최대 200개의 스냅샷을 보유하고 최대 10년 동안 유지할 수 있습니다. PowerShell 또는 CLI(명령줄 인터페이스)를 통해 Azure Portal에서 수동으로 해당 스냅샷을 만들거나 [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 사용할 수 있습니다. 스냅샷은 파일 공유 내에 저장되므로, 파일 공유를 삭제하면 스냅샷도 삭제됩니다. 스냅샷 백업이 실수로 삭제되지 않도록 보호하려면 공유에 일시 삭제를 사용하도록 설정해야 합니다.

[Azure 파일 공유에 대한 Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)은 스냅샷 예약과 보존에 대해 다룹니다. GFS(grandfather-father-son) 기능을 사용하는 경우 매일, 매주, 매월, 매년 스냅샷을 만들 수 있으며 각각 고유한 보존 기간이 설정됩니다. 또한 Azure Backup은 일시 삭제의 사용을 가능하게 조직하고 스토리지 계정 내의 파일 공유가 백업에 구성되는 즉시 스토리지 계정에 삭제 잠금을 수행합니다. 마지막으로, Azure Backup은 고객이 백업 공간을 통합하여 볼 수 있도록 특정 키 모니터링과 경고 기능을 제공합니다.

Azure Backup을 사용해 Azure Portal에서 항목 수준과 공유 수준 복원을 모두 수행할 수 있습니다. 복원 지점(특정 스냅샷), 특정 파일 또는 디렉터리(관련된 경우) 및 복원하려는 위치(원래 위치나 대체 위치)를 선택하기만 하면 됩니다. 백업 서비스는 스냅샷 데이터 복사를 처리하고 포털의 복원 진행률을 보여줍니다.

백업에 대한 자세한 내용은 [Azure 파일 공유 백업 정보](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

### <a name="protect-azure-files-with-microsoft-defender-for-storage"></a>Microsoft Defender for Storage 사용하여 Azure Files 보호
microsoft Defender for Storage 스토리지 계정에서 비정상적인 활동(예: 비정상적인 액세스 시도)을 감지할 때 경고를 생성하는 추가 보안 인텔리전스 계층을 제공합니다. 또한 맬웨어 해시 평판 분석을 실행하고 알려진 맬웨어에 대한 경고를 보냅니다. Microsoft Defender for Cloud를 통해 구독 또는 스토리지 계정 수준에서 Storage Microsoft Defender를 구성할 수 있습니다.

자세한 내용은 [Storage Microsoft Defender 소개를](../../security-center/defender-for-storage-introduction.md)참조하세요.

## <a name="storage-tiers"></a>스토리지 계층
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

#### <a name="limitations"></a>제한 사항
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>중복
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>마이그레이션
대부분의 경우에는 조직에 net 새 파일 공유를 설정하지 않고 대신 기존 파일 공유를 온-프레미스 파일 서버 또는 NAS 디바이스에서 Azure Files로 마이그레이션합니다. 마이그레이션을 성공적으로 수행하려면 시나리오에 적합한 마이그레이션 전략과 도구를 선택하는 것이 중요합니다. 

[마이그레이션 개요 문서](storage-files-migration-overview.md)에서는 기본 사항에 대해 간략하게 설명하고 시나리오를 다루는 마이그레이션 지침을 안내하는 표를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure 파일 동기화 배포에 대한 계획](../file-sync/file-sync-planning.md)
* [Azure Files 배포](./storage-how-to-create-file-share.md)
* [Azure 파일 동기화 배포](../file-sync/file-sync-deployment-guide.md)
* [시나리오에 대한 마이그레이션 지침을 찾으려면 마이그레이션 개요 문서를 확인하세요.](storage-files-migration-overview.md)
