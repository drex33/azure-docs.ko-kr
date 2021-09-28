---
title: Azure NetApp Files에 대한 FAQ | Microsoft Docs
description: 네트워킹, 보안, 성능, 용량 관리, 데이터 마이그레이션/보호와 같은 Azure NetApp Files에 대한 질문과 대답을 검토합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: 01a483e43429f45562cbb464e2b595023bd2ad5f
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091030"
---
# <a name="faqs-about-azure-netapp-files"></a>Azure NetApp Files에 대한 FAQ

이 문서는 Azure NetApp Files에 대한 FAQ(질문과 대답)입니다. 

## <a name="networking-faqs"></a>네트워킹 FAQ

### <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>NFS 또는 SMB의 데이터 경로는 인터넷을 통과하나요?  

아니요. NFS 또는 SMB의 데이터 경로는 인터넷을 통과하지 않습니다. Azure NetApp Files는 서비스를 사용할 수 있는 VNet(Azure Virtual Network)에 배포되는 Azure 네이티브 서비스입니다. Azure NetApp Files는 위임된 서브넷을 사용하고 VNet에서 직접 네트워크 인터페이스를 프로비전합니다. 

자세한 내용은 [Azure NetApp Files 네트워크 계획 지침](./azure-netapp-files-network-topologies.md)을 참조하세요.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>이미 만든 VNet을 Azure NetApp Files 서비스에 연결할 수 있나요?

예, 만든 VNet은 서비스에 연결할 수 있습니다. 

자세한 내용은 [Azure NetApp Files 네트워크 계획 지침](./azure-netapp-files-network-topologies.md)을 참조하세요.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>DNS FQDN 이름을 사용하여 Azure NetApp Files의 NFS 볼륨을 탑재할 수 있나요?

예, 필요한 DNS 항목을 만들기만 하면 됩니다. Azure NetApp Files는 프로비전된 볼륨에 대한 서비스 IP를 제공합니다. 

> [!NOTE] 
> Azure NetApp Files는 필요에 따라 서비스에 대한 추가 IP를 배포할 수 있습니다.  DNS 항목을 정기적으로 업데이트해야 할 수 있습니다.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Azure NetApp Files 볼륨에 대해 고유한 내 IP 주소를 설정하거나 선택할 수 있나요?  

아니요. Azure NetApp Files 볼륨에 대한 IP는 동적으로 할당됩니다. 고정 IP 할당은 지원되지 않습니다. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Azure NetApp Files는 이중 스택(IPv4 및 IPv6) VNet을 지원하나요?

아니요, Azure NetApp Files는 현재 이중 스택(IPv4 및 IPv6) VNet을 지원하지 않습니다.  

### <a name="is-the-number-of-the-ip-addresses-using-azure-vmware-solutions-for-guest-os-mounts-limited-to-1000"></a>게스트 OS 탑재의 Azure VMware 솔루션을 사용 하는 IP 주소의 수가 [1000 개로 제한](azure-netapp-files-resource-limits.md#resource-limits)되나요?

아니요. Azure VMware 솔루션은 온-프레미스 시스템과 유사 하 게 작동 하는 ER 게이트웨이 뒤에 있습니다. AVS "호스트" 및 "게스트" 수는 Azure NetApp Files에 표시되지 않으며 1000개의 IP 주소 제한은 적용되지 않습니다.
 
## <a name="security-faqs"></a>보안 FAQ

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM과 스토리지 간의 네트워크 트래픽을 암호화할 수 있나요?

Azure NetApp Files 데이터 트래픽은 퍼블릭 엔드포인트를 제공하지 않고 데이터 트래픽이 고객 소유의 VNet 내에서 유지되기 때문에 기본적으로 안전합니다. 이동 중인 데이터는 기본적으로 암호화되지 않습니다. 그러나 Azure VM(NFS 또는 SMB 클라이언트 실행)에서 Azure NetApp Files로 이동하는 데이터 트래픽은 다른 Azure VM 간 트래픽만큼 안전합니다. 

NFSv3 프로토콜은 암호화를 지원하지 않으므로 이동 중인 데이터를 암호화할 수 없습니다. 그러나 NFSv4.1 및 SMB3 이동 중인 데이터 암호화를 선택적으로 사용하도록 설정할 수 있습니다. NFSv4.1 클라이언트와 Azure NetApp Files 볼륨 간의 데이터 트래픽은 AES-256 암호화를 사용하는 Kerberos를 통해 암호화할 수 있습니다. 자세한 내용은 [Azure NetApp Files에 대한 NFSv4.1 Kerberos 암호화 구성](configure-kerberos-encryption.md)을 참조하세요. SMB 3.0의 AES-CCM 알고리즘과 SMB 3.1.1 연결의 AES-GCM 알고리즘을 사용하여 SMB3 클라이언트와 Azure NetApp Files 볼륨 간 데이터 트래픽을 암호화할 수 있습니다. 자세한 내용은 [Azure NetApp Files에 대한 SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)를 참조하세요. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>미사용 스토리지를 암호화할 수 있나요?

모든 Azure NetApp Files 볼륨은 FIPS 140-2 표준을 사용하여 암호화됩니다. 모든 키는 Azure NetApp Files 서비스에서 관리합니다. 

### <a name="is-azure-netapp-files-cross-region-replication-traffic-encrypted"></a>Azure NetApp Files 지역 간 복제 트래픽은 암호화되나요?

Azure NetApp Files 지역 간 복제는 TLS 1.2 AES-256 GCM 암호화를 사용하여 원본 볼륨과 대상 볼륨 간에 전송되는 모든 데이터를 암호화합니다. 이 암호화는 Azure NetApp Files 지역 간 복제를 포함하여 모든 Azure 트래픽에 대해 기본적으로 설정되는 [Azure MACSec 암호화](../security/fundamentals/encryption-overview.md)에 추가로 적용됩니다. 

### <a name="how-are-encryption-keys-managed"></a>암호 키를 관리하는 방법은 어떻게 되나요? 

Azure NetApp Files에 대한 키 관리는 서비스에서 처리합니다. 각 볼륨에 대해 고유한 XTS-AES-256 데이터 암호화 키가 생성됩니다. 암호화 키 계층 구조가 모든 볼륨 키를 암호화하고 보호하는 데 사용됩니다. 이러한 암호화 키는 암호화되지 않은 형식으로 표시되거나 보고되지 않습니다. 볼륨을 삭제하면 암호화 키가 즉시 삭제됩니다.

Azure Dedicated HSM을 사용하는 고객 관리형 키(Bring Your Own Key)에 대한 지원은 제어되는 기준에 근거하여 미국 동부, 미국 중남부, 미국 서부 2 및 US Gov 버지니아 지역에서 제공됩니다. [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com)에서 액세스를 요청할 수 있습니다. 용량을 사용할 수 있게 되면 요청이 승인됩니다.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Azure NetApp Files 서비스 탑재 대상에 대한 액세스를 제어하도록 NFS 내보내기 정책 규칙을 구성할 수 있나요?

예, 단일 NFS 내보내기 정책에서 최대 5개의 규칙을 구성할 수 있습니다.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp Files에서 네트워크 보안 그룹을 지원하나요?

아니요, 현재 네트워크 보안 그룹은 Azure NetApp Files의 위임된 서브넷 또는 서비스에서 만든 네트워크 인터페이스에 적용할 수 없습니다.

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Azure NetApp Files에서 Azure RBAC를 사용할 수 있나요?

예, Azure NetApp Files는 Azure RBAC 기능을 지원합니다. 기본 제공 Azure 역할과 함께 Azure NetApp Files에 대한 [사용자 지정 역할을 만들](../role-based-access-control/custom-roles.md) 수 있습니다. 

Azure NetApp Files 권한의 전체 목록은 [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp)에 대한 Azure 리소스 공급자 작업을 참조하세요.

### <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Azure NetApp Files에서 Azure 활동 로그를 지원하나요?

Azure NetApp Files는 Azure 네이티브 서비스입니다. Azure NetApp Files에 대한 모든 PUT, POST 및 DELETE API가 기록됩니다. 예를 들어 로그에는 스냅샷을 만든 사용자, 볼륨을 수정한 사용자 등과 같은 작업이 표시됩니다.

API 작업의 전체 목록은 [Azure NetApp Files REST API](/rest/api/netapp/)를 참조하세요.

### <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Azure NetApp Files에서 Azure 정책을 사용할 수 있나요?

예, [사용자 지정 Azure 정책](../governance/policy/tutorials/create-custom-policy-definition.md)을 만들 수 있습니다. 

그러나 Azure 정책(사용자 지정 명명 정책)은 Azure NetApp Files 인터페이스에서 만들 수 없습니다. [Azure NetApp Files 네트워크 계획 지침](azure-netapp-files-network-topologies.md#considerations)을 참조하십시오.

### <a name="when-i-delete-an-azure-netapp-files-volume-is-the-data-deleted-safely"></a>Azure NetApp Files 볼륨을 삭제하면 데이터가 안전하게 삭제되나요? 

Azure NetApp Files 볼륨의 삭제는 즉각적인 효과를 사용 하 여 프로그래밍 방식으로 수행 됩니다. 삭제 작업에는 미사용 데이터를 암호화하는 데 사용되는 키 삭제가 포함됩니다. 삭제 작업이 성공적으로 실행되면(Azure Portal 및 API와 같은 인터페이스를 통해) 삭제된 볼륨을 복구하는 시나리오에 대한 옵션은 제공되지 않습니다.

## <a name="performance-faqs"></a>성능 FAQ

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Azure NetApp Files 성능을 최적화하거나 튜닝하려면 어떻게 해야 하나요?

성능 요구 사항에 따라 다음 작업을 수행할 수 있습니다. 
- Virtual Machine의 크기가 적절한지 확인합니다.
- 가속화된 네트워킹을 VM에 사용하도록 설정합니다.
- 용량 풀에 대해 원하는 서비스 수준 및 크기를 선택합니다.
- 용량 및 성능에 대해 원하는 할당량 크기의 볼륨을 만듭니다.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Azure NetApp Files의 처리량 기반 서비스 수준을 IOPS로 변환하려면 어떻게 할까요?

다음 수식을 사용하여 MB/초를 IOPS로 변환할 수 있습니다.  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>볼륨의 서비스 수준을 변경하려면 어떻게 할까요?

볼륨에 대해 원하는 [서비스 수준](azure-netapp-files-service-levels.md)을 사용하는 다른 용량 풀로 볼륨을 이동하여 기존 볼륨의 서비스 수준을 변경할 수 있습니다. [볼륨의 서비스 수준을 동적으로 변경](dynamic-change-volume-service-level.md)을 참조하세요. 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Azure NetApp Files 성능을 모니터링하려면 어떻게 할까요?

Azure NetApp Files는 볼륨 성능 메트릭을 제공합니다. Azure Monitor를 사용하여 Azure NetApp Files에 대한 사용 현황 메트릭을 모니터링할 수도 있습니다.  Azure NetApp Files에 대한 성능 메트릭 목록은 [Azure NetApp Files에 대한 메트릭](azure-netapp-files-metrics.md)을 참조하세요.

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>NFSv4.1에서 Kerberos의 성능에 미치는 영향은 어떻게 되나요?

NFSv4.1의 보안 옵션, 테스트된 성능 벡터 및 예상되는 성능 영향에 대한 자세한 내용은 [NFSv4.1에서 Kerberos의 성능 영향](performance-impact-kerberos.md)을 참조하세요. 

### <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files에서 SMB 다이렉트를 지원하나요?

아니요. Azure NetApp Files SMB 다이렉트를 지원하지 않습니다. 

### <a name="is-nic-teaming-supported-in-azure"></a>Azure에서 NIC 팀이 지원되나요?

NIC 팀은 Azure에서 지원되지 않습니다. Azure 가상 머신에서는 여러 네트워크 인터페이스가 지원되지만 물리적 구조가 아닌 논리적 구조를 나타냅니다. 따라서 내결함성을 제공하지 않습니다.  또한 Azure 가상 머신에서 사용할 수 있는 대역폭은 개별 네트워크 인터페이스가 아닌 컴퓨터 자체에 대해 계산됩니다.

### <a name="are-jumbo-frames-supported"></a>점보 프레임은 지원되나요?

점보 프레임은 Azure 가상 머신에서 지원되지 않습니다.

## <a name="nfs-faqs"></a>NFS FAQ

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Azure VM을 시작하거나 다시 부팅할 때 볼륨이 자동으로 탑재되도록 하려고 합니다.  내 호스트를 영구 NFS 볼륨에 대해 구성하려면 어떻게 할까요?

VM 시작 또는 다시 부팅 시 NFS 볼륨이 자동으로 탑재되도록 하려면 항목을 호스트의 `/etc/fstab` 파일에 추가합니다. 

자세한 내용은 [Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 분리](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)를 참조하세요.  

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp Files에서 지원하는 NFS 버전은 무엇인가요?

Azure NetApp Files는 NFSv3 및 NFSv4.1을 지원합니다. NFS 버전 중 하나를 사용하여 [볼륨을 만들](azure-netapp-files-create-volumes.md) 수 있습니다. 

### <a name="how-do-i-enable-root-squashing"></a>루트 quash 병합을 사용하도록 설정하려면 어떻게 할까요?

볼륨의 내보내기 정책을 사용하여 루트 계정에서 볼륨에 액세스할 수 있는지 여부를 지정할 수 있습니다. 자세한 내용은 [NFS 볼륨에 대한 내보내기 정책 구성](azure-netapp-files-configure-export-policy.md)을 참조하세요.

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>동일한 파일 경로(볼륨 만들기 토큰)를 여러 볼륨에 사용할 수 있나요?

예, 할 수 있습니다. 그러나 파일 경로는 다른 구독 또는 다른 지역에서 사용해야 합니다.   

예를 들어 `vol1`이라는 볼륨을 만듭니다. 그런 다음, `vol1`이라고도 하는 또 다른 볼륨을 다른 용량 풀에 있지만 동일한 구독 및 지역에 만듭니다. 이 경우 `vol1`이라는 동일한 볼륨 이름을 사용하면 오류가 발생합니다. 동일한 파일 경로를 사용하려면 이름이 다른 지역 또는 구독에 있어야 합니다.

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Windows 클라이언트를 통해 NFS 볼륨에 액세스하려고 할 때 클라이언트에서 폴더 및 하위 폴더를 검색하는 데 오래 걸리는 이유는 무엇인가요?

Windows 클라이언트에서 `CaseSensitiveLookup`을 사용하도록 설정하여 폴더 및 하위 폴더의 조회 속도를 높일 수 있는지 확인합니다.

1. 다음 PowerShell 명령을 사용하여 CaseSensitiveLookup을 사용하도록 설정합니다.   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. 볼륨을 Windows 서버에 탑재합니다.   
    예제:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Azure NetApp Files에서 NFSv4.1 파일 잠금을 지원하려면 어떻게 해야 하나요? 

NFSv4.1 클라이언트의 경우 Azure NetApp Files는 임대 기반 모델에서 모든 파일 잠금 상태를 유지 관리하는 NFSv4.1 파일 잠금 메커니즘을 지원합니다. 

RFC 3530에 따라 Azure NetApp Files는 NFS 클라이언트에서 보유한 모든 상태에 대한 단일 임대 기간을 정의합니다. 클라이언트에서 정의된 기간 내에 임대를 갱신하지 않으면 서버에서 클라이언트의 임대와 관련된 모든 상태를 해제합니다.  

예를 들어 볼륨을 탑재하는 클라이언트에서 응답하지 않거나 시간 제한을 초과하여 작동이 중단되면 잠금이 해제됩니다. 클라이언트는 파일 읽기 등의 작업을 수행하여 임대를 명시적 또는 암시적으로 갱신할 수 있습니다.   

유예 기간은 클라이언트에서 서버 복구 중에 잠금 상태를 회수하려고 시도할 수 있는 특별한 처리 기간을 정의합니다. 기본 임대 시간 제한은 30초이며, 유예 기간은 45초입니다. 해당 시간이 지나면 클라이언트의 임대가 해제됩니다.   

## <a name="smb-faqs"></a>SMB FAQ

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Azure NetApp Files에서 지원하는 SMB 버전은 무엇인가요?

Azure NetApp Files는 SMB 2.1 및 SMB 3.1(SMB 3.0 지원 포함)을 지원합니다.    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB 액세스에 Active Directory 연결이 필요한가요? 

예, SMB 볼륨을 배포하기 전에 Active Directory 연결을 만들어야 합니다. 연결이 성공하려면 Azure NetApp Files의 위임된 서브넷에서 지정된 도메인 컨트롤러에 액세스할 수 있어야 합니다.  자세한 내용은 [SMB 볼륨 만들기](./azure-netapp-files-create-volumes-smb.md)를 참조하세요. 

### <a name="how-many-active-directory-connections-are-supported"></a>지원되는 Active Directory 연결 수는 몇 개인가요?

구독 및 지역당 하나의 AD(Active Directory) 연결만 구성할 수 있습니다. 자세한 내용은 [Active Directory 연결에 대한 요구 사항](create-active-directory-connections.md#requirements-for-active-directory-connections)을 참조하세요. 

그러나 동일한 구독 및 지역에 있는 여러 NetApp 계정을 NetApp 계정 중 하나에서 만든 공통 AD 서버에 매핑할 수 있습니다. [동일한 구독 및 지역의 여러 NetApp 계정을 하나의 AD 연결에 매핑](create-active-directory-connections.md#shared_ad)을 참조하세요. 

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp Files에서 Azure Active Directory를 지원하나요? 

[Azure AD(Active Directory) Domain Services](../active-directory-domain-services/overview.md) 및 [AD DS(Active Directory Domain Services)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)가 모두 지원됩니다. 기존 Active Directory 도메인 컨트롤러는 Azure NetApp Files에서 사용할 수 있습니다. 도메인 컨트롤러는 Azure에서 가상 머신으로 상주하거나 ExpressRoute 또는 S2S VPN을 통해 온-프레미스에서 상주할 수 있습니다. Azure NetApp Files는 현재 [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/)에 대한 AD 조인을 지원하지 않습니다.

Azure Active Directory Domain Services와 함께 Azure NetApp Files를 사용하는 경우 NetApp 계정에 대해 Active Directory를 구성할 때 조직 구성 단위 경로는 `OU=AADDC Computers`입니다.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>지원되는 Windows Server Active Directory 버전은 무엇인가요?

Azure NetApp Files는 Windows Server 2008r2SP1-2019 버전의 Active Directory Domain Services를 지원합니다.

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>내 SMB 공유에 연결하는 데 문제가 있습니다.   어떻게 해야 합니까?

컴퓨터 시계 동기화에 대한 최대 허용 오차를 5분으로 설정하는 것이 가장 좋습니다. 자세한 내용은 [컴퓨터 시계 동기화에 대한 최대 허용 오차](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11))를 참조하세요. 

### <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>MMC(컴퓨터 관리 콘솔)를 통해 `SMB Shares`, `Sessions` 및 `Open Files`를 관리할 수 있나요?

현재 MMC(컴퓨터 관리 콘솔)를 통해 `SMB Shares`, `Sessions` 및 `Open Files`를 관리하도록 지원되지 않습니다.

### <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>포털을 통해 SMB 볼륨의 IP 주소를 가져오려면 어떻게 해야 하나요?

볼륨 개요 창의 **JSON 보기** 링크를 사용하여 **속성** -> **mountTargets** 아래에서 **startIp** 식별자를 찾습니다.

### <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>Azure NetApp Files SMB 공유를 DFS-N(DFS 네임스페이스) 루트로 사용할 수 있나요?

아니요. 그러나 Azure NetApp Files SMB 공유를 DFS-N(DFS 네임스페이스) 폴더 대상으로 사용할 수 있습니다.   
Azure NetApp Files SMB 공유를 DFS-N 폴더 대상으로 사용하려면 [DFS 폴더 대상 추가](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target) 절차를 사용하여 Azure NetApp Files SMB 공유의 UNC(범용 명명 규칙) 탑재 경로를 제공합니다.  

### <a name="can-the-smb-share-permissions-be-changed"></a>SMB 공유 사용 권한을 변경할 수 있나요?   

아니요, 공유 사용 권한은 변경할 수 없습니다. 그러나 [NTFS 파일 및 폴더 권한](azure-netapp-files-create-volumes-smb.md#ntfs-file-and-folder-permissions) 절차를 사용하여 `root` 볼륨의 NTFS 사용 권한을 변경할 수 있습니다. 

## <a name="capacity-management-faqs"></a>용량 관리 FAQ

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Azure NetApp Files의 용량 풀 및 볼륨에 대한 사용량을 모니터링하려면 어떻게 할까요? 

Azure NetApp Files는 용량 풀 및 볼륨 사용 현황 메트릭을 제공합니다. Azure Monitor를 사용하여 Azure NetApp Files의 사용량을 모니터링할 수도 있습니다. 자세한 내용은 [Azure NetApp Files에 대한 메트릭](azure-netapp-files-metrics.md)을 참조하세요. 

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>디렉터리가 제한 크기에 도달하고 있는지 확인하려면 어떻게 할까요?

클라이언트에서 `stat` 명령을 사용하여 디렉터리가 디렉터리 메타데이터의 [최대 크기 한도](azure-netapp-files-resource-limits.md#resource-limits)(320MB)에 도달하는지 여부를 확인할 수 있습니다.
한도 및 계산은 [Azure NetApp Files에 대한 리소스 한도](azure-netapp-files-resource-limits.md#directory-limit)를 참조하세요. 

<!-- 
You can use the `stat` command from a client to see whether a directory is approaching the maximum size limit for directory metadata (320 MB).   

For a 320-MB directory, the number of blocks is 655360, with each block size being 512 bytes.  (That is, 320x1024x1024/512.)  This number translates to approximately 4 million files maximum for a 320-MB directory. However, the actual number of maximum files might be lower, depending on factors such as the number of files containing non-ASCII characters in the directory. As such, you should use the `stat` command as follows to determine whether your directory is approaching its limit.  

Examples:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```
--> 

### <a name="does-snapshot-space-count-towards-the-usable--provisioned-capacity-of-a-volume"></a>스냅샷 공간이 볼륨의 가용/프로비저닝된 용량에 포함되나요?

예. [사용된 스냅샷 용량](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots)은 볼륨의 프로비저닝된 공간에 포함됩니다. 볼륨이 가득 찬 경우 다음 작업을 수행하는 것이 좋습니다.

* [볼륨의 크기를 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md)합니다.
* [이전 스냅샷을 제거](snapshots-delete.md)하여 호스팅 볼륨의 공간을 확보합니다. 

### <a name="does-azure-netapp-files-support-auto-grow-for-volumes-or-capacity-pools"></a>Azure NetApp Files는 볼륨 또는 용량 풀에 대해 자동 증가를 지원하나요?

아니요, Azure NetApp Files 볼륨 및 용량 풀은 가득 찰 때 자동으로 증가하지 않습니다. [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)을 참조하세요.   

커뮤니티에서 지원하는 [Logic Apps ANFCapacityManager 도구](https://github.com/ANFTechTeam/ANFCapacityManager)를 사용하여 용량 기반 경고 규칙을 관리할 수 있습니다. 이 도구는 볼륨 공간이 부족하지 않도록 볼륨 크기를 자동으로 늘릴 수 있습니다.

### <a name="does-the-destination-volume-of-a-replication-count-towards-hard-volume-quota"></a>복제 대상 볼륨은 하드 볼륨 할당량에 포함되나요?  

아니요, 복제 대상 볼륨은 하드 볼륨 할당량에 포함되지 않습니다.

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Azure Storage Explorer를 통해 Azure NetApp Files를 관리할 수 있나요?

아니요. Azure NetApp Files는 Azure Storage Explorer에서 지원되지 않습니다.

## <a name="data-migration-and-protection-faqs"></a>데이터 마이그레이션 및 보호 FAQ

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>데이터를 Azure NetApp Files로 마이그레이션하려면 어떻게 할까요?
Azure NetApp Files는 NFS 및 SMB 볼륨을 제공합니다.  파일 기반 복사 도구를 사용하여 데이터를 서비스로 마이그레이션할 수 있습니다. 

NetApp은 SaaS 기반 솔루션인 [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service)를 제공합니다. 이 솔루션을 사용하면 NFS 또는 SMB 데이터를 Azure NetApp Files NFS 내보내기 또는 SMB 공유에 복제할 수 있습니다. 

또한 다양한 무료 도구를 사용하여 데이터를 복사할 수 있습니다. NFS의 경우 [rsync](https://rsync.samba.org/examples.html)와 같은 워크로드 도구를 사용하여 원본 데이터를 Azure NetApp Files 볼륨으로 복사하고 동기화할 수 있습니다. SMB의 경우 워크로드 [robocopy](/windows-server/administration/windows-commands/robocopy)를 동일한 방식으로 사용할 수 있습니다.  이러한 도구는 파일 또는 폴더 권한도 복제할 수 있습니다. 

데이터를 온-프레미스에서 Azure NetApp Files로 마이그레이션하기 위한 요구 사항은 다음과 같습니다. 

- Azure NetApp Files를 대상 Azure 지역에서 사용할 수 있는지 확인합니다.
- 원본과 Azure NetApp Files 대상 볼륨 IP 주소 간의 네트워크 연결을 확인합니다. 온-프레미스와 Azure NetApp Files 서비스 간의 데이터 전송은 ExpressRoute를 통해 지원됩니다.
- 대상 Azure NetApp Files 볼륨을 만듭니다.
- 기본 설정 파일 복사 도구를 사용하여 원본 데이터를 대상 볼륨으로 전송합니다.

### <a name="where-does-azure-netapp-files-store-customer-data"></a>Azure NetApp Files는 고객 데이터를 어디에 저장하나요?   

기본적으로 데이터는 Azure NetApp Files 볼륨을 배포하는 지역 내에 유지됩니다. 그러나 [지역 간 복제](cross-region-replication-introduction.md)를 사용하여 볼륨 단위로 데이터를 사용 가능한 대상 지역에 복제하도록 선택할 수 있습니다.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>다른 Azure 지역에서 Azure NetApp Files 볼륨의 복사본을 만들려면 어떻게 할까요?
    
Azure NetApp Files는 NFS 및 SMB 볼륨을 제공합니다.  모든 파일 기반 복사 도구를 사용하여 Azure 지역 간에 데이터를 복제할 수 있습니다. 

[지역 간 복제](cross-region-replication-introduction.md) 기능을 사용하면 한 지역의 Azure NetApp Files 볼륨(원본)에서 다른 지역의 다른 Azure NetApp Files 볼륨(대상)으로 데이터를 비동기식으로 복제할 수 있습니다.  [기존 볼륨의 스냅샷을 사용하여 새 볼륨을 만들](snapshots-restore-new-volume.md) 수도 있습니다.

NetApp은 SaaS 기반 솔루션인 [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service)를 제공합니다. 이 솔루션을 사용하면 NFS 또는 SMB 데이터를 Azure NetApp Files NFS 내보내기 또는 SMB 공유에 복제할 수 있습니다. 

또한 다양한 무료 도구를 사용하여 데이터를 복사할 수 있습니다. NFS의 경우 [rsync](https://rsync.samba.org/examples.html)와 같은 워크로드 도구를 사용하여 원본 데이터를 Azure NetApp Files 볼륨으로 복사하고 동기화할 수 있습니다. SMB의 경우 워크로드 [robocopy](/windows-server/administration/windows-commands/robocopy)를 동일한 방식으로 사용할 수 있습니다.  이러한 도구는 파일 또는 폴더 권한도 복제할 수 있습니다. 

Azure NetApp Files 볼륨을 다른 Azure 지역으로 복제하기 위한 요구 사항은 다음과 같습니다. 
- Azure NetApp Files를 대상 Azure 지역에서 사용할 수 있는지 확인합니다.
- 각 지역에서 VNet 간 네트워크 연결의 유효성을 검사합니다. 현재 VNet 간의 글로벌 피어링은 지원되지 않습니다.  VNet 간 연결은 ExpressRoute 회로에 연결하거나 S2S VPN 연결을 사용하여 설정할 수 있습니다. 
- 대상 Azure NetApp Files 볼륨을 만듭니다.
- 기본 설정 파일 복사 도구를 사용하여 원본 데이터를 대상 볼륨으로 전송합니다.

### <a name="is-migration-with-azure-data-box-supported"></a>Azure Data Box를 통한 마이그레이션이 지원되나요?

아니요. Azure Data Box는 현재 Azure NetApp Files를 지원하지 않습니다. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Azure Import/Export 서비스를 통한 마이그레이션이 지원되나요?

아니요. Azure Import/Export 서비스는 현재 Azure NetApp Files를 지원하지 않습니다.

## <a name="azure-netapp-files-backup-faqs"></a>Azure NetApp Files 백업 FAQ

이 섹션에서는 Azure NetApp Files [백업](backup-introduction.md) 기능에 대한 질문에 답변합니다. 

### <a name="when-do-my-backups-occur"></a>내 백업은 언제 발생합니까?   

Azure NetApp Files 백업은 백업 정책의 빈도가 입력된 후 임의 시간 프레임 내에 시작됩니다. 예를 들어 매주 백업은 오전 12시 이후에 임의로 할당된 간격 내에 일요일 시작됩니다. 자정. 이 타이밍은 현재 사용자가 수정할 수 없습니다. 기준 백업은 볼륨에 백업 정책을 할당하는 즉시 시작됩니다.

### <a name="what-happens-if-a-backup-operation-encounters-a-problem"></a>백업 작업에 문제가 발생하면 어떻게 되나요?

백업 작업 중에 문제가 발생하는 경우 Azure NetApp Files 백업은 사용자 상호 작용 없이 작업을 자동으로 다시 수행합니다. 재시도에 계속 실패하면 Azure NetApp Files 백업에서 작업 실패를 보고합니다.

### <a name="can-i-change-the-location-or-storage-tier-of-my-backup-vault"></a>백업 자격 증명 모음의 위치 또는 스토리지 계층을 변경할 수 있나요?

아니요, Azure NetApp Files Azure Storage 내에서 백업 데이터 위치를 자동으로 관리하며 이 위치 또는 Azure Storage 계층은 사용자가 수정할 수 없습니다.

### <a name="what-types-of-security-are-provided-for-the-backup-data"></a>백업 데이터에 대해 제공되는 보안 유형은 무엇인가요?

Azure NetApp Files 받은 백업 데이터를 인코딩하는 동안 AES-256 비트 암호화를 사용합니다. 또한 암호화된 데이터는 HTTPS TLSv1.2 연결을 사용하여 Azure Storage로 안전하게 전송됩니다. Azure NetApp Files 백업은 백업 데이터를 저장하기 위한 Azure Storage 계정의 미사용 데이터 암호화 기능에 따라 달라집니다.

### <a name="what-happens-to-the-backups-when-i-delete-a-volume-or-my-netapp-account"></a>볼륨 또는 내 NetApp 계정을 삭제하면 백업은 어떻게 되나요? 

 Azure NetApp Files 볼륨을 삭제하면 백업이 유지됩니다. 백업을 유지하지 않으려면 볼륨을 삭제하기 전에 백업을 사용하지 않도록 설정합니다. NetApp 계정을 삭제해도 동일한 구독의 다른 NetApp 계정에서 백업이 유지되고 표시되므로 복원에 계속 사용할 수 있습니다. 구독에서 모든 NetApp 계정을 삭제하는 경우 모든 NetApp 계정의 모든 볼륨을 삭제하기 전에 백업을 사용하지 않도록 설정해야 합니다.

### <a name="whats-the-systems-maximum-backup-retries-for-a-volume"></a>볼륨에 대한 시스템의 최대 백업 재시도는 어떻게 되나요?  

시스템은 예약된 백업 작업을 처리할 때 10회 재시도를 수행합니다. 작업이 실패하면 시스템에서 백업 작업이 실패합니다. 구성된 정책에 따라 예약된 백업의 경우 시스템은 1시간마다 한 번씩 데이터를 백업하려고 시도합니다. 전송되지 않았거나 마지막 시도 중에 실패한 새 스냅샷을 사용할 수 있는 경우 해당 스냅샷은 전송으로 간주됩니다. 

## <a name="product-faqs"></a>제품 FAQ

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>AVS(Azure VMware Solution)에서 Azure NetApp Files NFS 또는 SMB 볼륨을 사용할 수 있나요?

Azure NetApp Files NFS 볼륨은 AVS Windows VM 또는 Linux VM에서 탑재할 수 있습니다. Azure NetApp Files SMB 공유는 AVS Windows VM에서 매핑할 수 있습니다. 자세한 내용은 [Azure VMware Solution을 사용하는 Azure NetApp Files]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md)를 참조하세요.  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>AVS(Azure VMware Solution)에서 Azure NetApp Files NFS 또는 SMB 볼륨을 사용하도록 지원되는 지역은 무엇인가요?

*게스트 OS 탑재* 를 위해 AVS에서 Azure NetApp Files NFS 또는 SMB 볼륨을 사용하는 방식은 [모든 AVS 및 ANF 지원 지역](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware,netapp)에서 지원됩니다.

### <a name="does-azure-netapp-files-work-with-azure-policy"></a>Azure NetApp Files는 Azure Policy와 함께 작동하나요?

예. Azure NetApp Files는 자사 서비스입니다. Azure 리소스 공급자 표준을 완벽하게 준수합니다. 따라서 ‘사용자 지정 정책 정의’를 통해 Azure NetApp Files를 Azure Policy에 통합할 수 있습니다. Azure NetApp Files에 대한 사용자 지정 정책을 구현하는 방법에 대한 자세한 내용은 Microsoft Tech Community에서 [이제 Azure NetApp Files에 Azure Policy를 사용할 수 있음](https://techcommunity.microsoft.com/t5/azure/azure-policy-now-available-for-azure-netapp-files/m-p/2282258)을 참조하세요. 

### <a name="which-unicode-character-encoding-is-supported-by-azure-netapp-files-for-the-creation-and-display-of-file-and-directory-names"></a>파일 및 디렉터리 이름을 만들고 표시하기 위해 Azure NetApp Files에서 어떤 유니코드 문자 인코딩을 지원하나요?   

Azure NetApp Files는 NFS 및 SMB 볼륨 모두에 대해 UTF-8 유니코드 문자 인코딩 형식으로 인코딩된 파일 및 디렉터리 이름만 지원합니다.

UTF-8에서 지원되지 않는 비정규 문자 및 이모지와 같은 보조 문자 또는 서로게이트 쌍을 사용하는 이름으로 파일 또는 디렉터리를 만들려고 하면 작업이 실패합니다. 이 경우 Windows 클라이언트에서 "지정한 파일 이름이 잘못되었거나 너무 깁니다. 다른 파일 이름을 지정하세요."와 같은 오류가 표시될 수 있습니다. 

## <a name="next-steps"></a>다음 단계  

- [Microsoft Azure ExpressRoute FAQ](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Virtual Network FAQ](../virtual-network/virtual-networks-faq.md)
- [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Azure NetApp Files의 SMB 성능에 대한 FAQ](azure-netapp-files-smb-performance.md)
