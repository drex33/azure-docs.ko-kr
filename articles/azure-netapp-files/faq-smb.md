---
title: Azure NetApp Files에 대 한 SMB Faq | Microsoft Docs
description: Azure NetApp Files의 SMB 프로토콜에 대 한 Faq (질문과 대답)입니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 7b1c6b1e3e9814ed83c92ada2388ff54f5b3829c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273812"
---
# <a name="smb-faqs-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 SMB Faq

이 문서에서는 Azure NetApp Files의 SMB 프로토콜에 대 한 Faq (질문과 대답)에 답변 합니다.

## <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Azure NetApp Files에서 지원하는 SMB 버전은 무엇인가요?

Azure NetApp Files는 SMB 2.1 및 SMB 3.1(SMB 3.0 지원 포함)을 지원합니다. 

## <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB 액세스에 Active Directory 연결이 필요한가요? 

예, SMB 볼륨을 배포하기 전에 Active Directory 연결을 만들어야 합니다. 연결이 성공하려면 Azure NetApp Files의 위임된 서브넷에서 지정된 도메인 컨트롤러에 액세스할 수 있어야 합니다.  자세한 내용은 [SMB 볼륨 만들기](./azure-netapp-files-create-volumes-smb.md)를 참조하세요. 

## <a name="how-many-active-directory-connections-are-supported"></a>지원되는 Active Directory 연결 수는 몇 개인가요?

구독 및 지역당 하나의 AD(Active Directory) 연결만 구성할 수 있습니다. 자세한 내용은 [Active Directory 연결에 대한 요구 사항](create-active-directory-connections.md#requirements-for-active-directory-connections)을 참조하세요. 

그러나 동일한 구독 및 지역에 있는 여러 NetApp 계정을 NetApp 계정 중 하나에서 만든 공통 AD 서버에 매핑할 수 있습니다. [동일한 구독 및 지역의 여러 NetApp 계정을 하나의 AD 연결에 매핑](create-active-directory-connections.md#shared_ad)을 참조하세요. 

## <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp Files에서 Azure Active Directory를 지원하나요? 

[Azure AD(Active Directory) Domain Services](../active-directory-domain-services/overview.md) 및 [AD DS(Active Directory Domain Services)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)가 모두 지원됩니다. 기존 Active Directory 도메인 컨트롤러는 Azure NetApp Files에서 사용할 수 있습니다. 도메인 컨트롤러는 Azure에서 가상 머신으로 상주하거나 ExpressRoute 또는 S2S VPN을 통해 온-프레미스에서 상주할 수 있습니다. Azure NetApp Files는 현재 [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/)에 대한 AD 조인을 지원하지 않습니다.

Azure Active Directory Domain Services와 함께 Azure NetApp Files를 사용하는 경우 NetApp 계정에 대해 Active Directory를 구성할 때 조직 구성 단위 경로는 `OU=AADDC Computers`입니다.

## <a name="what-versions-of-windows-server-active-directory-are-supported"></a>지원되는 Windows Server Active Directory 버전은 무엇인가요?

Azure NetApp Files는 Windows Server 2008r2SP1-2019 버전의 Active Directory Domain Services를 지원합니다.

## <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>내 SMB 공유에 연결하는 데 문제가 있습니다.   어떻게 해야 합니까?

컴퓨터 시계 동기화에 대한 최대 허용 오차를 5분으로 설정하는 것이 가장 좋습니다. 자세한 내용은 [컴퓨터 시계 동기화에 대한 최대 허용 오차](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11))를 참조하세요. 

## <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>MMC(컴퓨터 관리 콘솔)를 통해 `SMB Shares`, `Sessions` 및 `Open Files`를 관리할 수 있나요?

현재 MMC(컴퓨터 관리 콘솔)를 통해 `SMB Shares`, `Sessions` 및 `Open Files`를 관리하도록 지원되지 않습니다.

## <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>포털을 통해 SMB 볼륨의 IP 주소를 가져오려면 어떻게 해야 하나요?

볼륨 개요 창의 **JSON 보기** 링크를 사용하여 **속성** -> **mountTargets** 아래에서 **startIp** 식별자를 찾습니다.

## <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>Azure NetApp Files SMB 공유를 DFS-N(DFS 네임스페이스) 루트로 사용할 수 있나요?

아니요. 그러나 Azure NetApp Files SMB 공유를 DFS-N(DFS 네임스페이스) 폴더 대상으로 사용할 수 있습니다.   
Azure NetApp Files SMB 공유를 DFS-N 폴더 대상으로 사용하려면 [DFS 폴더 대상 추가](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target) 절차를 사용하여 Azure NetApp Files SMB 공유의 UNC(범용 명명 규칙) 탑재 경로를 제공합니다.  

## <a name="can-the-smb-share-permissions-be-changed"></a>SMB 공유 사용 권한을 변경할 수 있나요?   

아니요, 공유 사용 권한은 변경할 수 없습니다. 그러나 [NTFS 파일 및 폴더 권한](azure-netapp-files-create-volumes-smb.md#ntfs-file-and-folder-permissions) 절차를 사용하여 `root` 볼륨의 NTFS 사용 권한을 변경할 수 있습니다. 


## <a name="next-steps"></a>다음 단계  

- [Azure NetApp Files의 SMB 성능에 대한 FAQ](azure-netapp-files-smb-performance.md)
- [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [네트워킹 FAQ](faq-networking.md)
- [보안 FAQ](faq-security.md)
- [성능 FAQ](faq-performance.md)
- [NFS FAQ](faq-nfs.md)
- [용량 관리 FAQ](faq-capacity-management.md)
- [데이터 마이그레이션 및 보호 FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files 백업 FAQ](faq-backup.md)
- [응용 프로그램 복원 력 Faq](faq-application-resilience.md)
- [통합 Faq](faq-integration.md)
