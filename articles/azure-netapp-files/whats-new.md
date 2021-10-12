---
title: Azure NetApp Files의 새로운 기능 | Microsoft Docs
description: Azure NetApp Files의 새로운 최신 기능 및 향상된 기능에 대한 요약을 제공합니다.
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
ms.topic: overview
ms.date: 10/05/2021
ms.author: b-juche
ms.openlocfilehash: 2dff42fcf855a41e2e907563b1aecccad0a1661a
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545928"
---
# <a name="whats-new-in-azure-netapp-files"></a>Azure NetApp Files의 새로운 기능

Azure NetApp Files는 정기적으로 업데이트됩니다. 이 문서에서는 새로운 최신 기능과 향상된 기능에 대한 요약을 제공합니다. 

    
## <a name="october-2021"></a>2021년 10월

* [표준 네트워크 기능](configure-network-features.md)(미리 보기)

    Azure NetApp Files는 고객이 개시 이후에 요청하는 볼륨에 대한 **표준** 네트워크 기능을 지원합니다. 혁신적인 하드웨어 및 소프트웨어 통합으로 이 기능을 사용할 수 있습니다. 표준 네트워크 기능은 Azure NetApp Files를 포함하여 모든 워크로드의 보안 태세와 원활하고 일관된 환경을 제공하기 위해 다양한 기능을 통해 향상된 가상 네트워킹 환경을 제공합니다.
    
    이제 새 Azure NetApp Files 볼륨을 만들 때 *표준* 또는 *기본* 네트워크 기능을 선택할 수 있습니다. 표준 네트워크 기능을 선택하면 Azure NetApp Files 볼륨 및 위임된 서브넷에 대해 다음과 같은 지원되는 기능을 활용할 수 있습니다.   
    * VM과 함께 Azure NetApp Files 볼륨이 있는 VNet에 대한 IP 제한 증가
    * Azure NetApp Files 위임된 서브넷의 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)에 대한 지원으로 향상된 네트워크 보안
    * Azure NetApp Files 위임된 서브넷 간 [사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md#custom-routes)를 지원하는 향상된 네트워크 제어
    * 활성/활성 VPN gateway 설치를 통한 연결
    * Azure NetApp Files에 대한 [ExpressRoute 경로](../expressroute/about-fastpath.md) 연결

    이 공개 미리 보기는 현재 **미국 중북부** 에서 시작하여 이용이 가능하며 다른 지역으로 롤아웃됩니다.  더 많은 지역 및 기능을 사용할 수 있게 되면 [Azure 업데이트](https://azure.microsoft.com/updates/)를 통해 추가 정보를 계속 조정하세요.  
 
    자세히 알아보려면, [Azure NetApp Files 볼륨의 네트워크 기능 구성](configure-network-features.md)을 참조하세요.

## <a name="september-2021"></a>2021년 9월

* [Azure NetApp Files 백업](backup-introduction.md)(미리 보기)

    Azure NetApp Files 온라인 스냅샷은 이제 스냅샷 백업으로 향상되었습니다. 이 새로운 백업 기능을 사용하면 Azure NetApp Files 스냅샷을 비용 효율적인 ZRS 사용 Azure 스토리지로 빠르고 비용 효율적인 방식으로 보관할 수 있으며, 데이터가 실수로 삭제되지 않도록 방지할 수 있습니다. Azure NetApp Files 백업은 ONTAP의 기본 제공 스냅샷 기술을 확장합니다. 스냅샷이 Azure 스토리지에 보관되면 이전에 보관된 스냅샷을 기준으로 변경된 블록만 효율적인 형식으로 복사하고 저장합니다. 그러나 보관된 스냅샷은 여전히 ​​전체적으로 표시되며 개별적으로 직접 새 볼륨으로 복원할 수 있으므로 반복적인 전체 증분 복구 프로세스가 필요하지 않습니다. 이 고급 기술은 Azure 스토리지에서 저장하고 검색하는 데 필요한 데이터의 양을 최소화하므로 데이터 전송 및 스토리지 비용을 절약할 수 있습니다. 또한 백업 보관 시간이 단축되어 더 작은 RPO(복원 지점 목표)를 달성할 수 있습니다. 이제 가장 즉각적이고 거의 즉각적인 데이터 복구 요구 사항을 위해 Azure NetApp Files 서비스에서 최소 수의 스냅샷을 온라인 상태로 유지하고, Azure NetApp Files 백업 자격 증명 모음에서 장기 보존을 위해 더 적은 비용으로 더 긴 스냅샷 기록을 구축하도록 선택할 수 있습니다. 자세한 내용은 [Azure NetApp Files 스냅샷 작동 방식](snapshots-introduction.md)을 참조하세요.

* Active Directory 연결의 [**관리자**](create-active-directory-connections.md#create-an-active-directory-connection) 옵션(미리 보기)

    이제 Active Directory 연결 페이지에는 **관리자** 필드가 포함됩니다. 볼륨에 대한 관리자 권한이 부여되는 사용자 또는 그룹을 지정할 수 있습니다.

## <a name="august-2021"></a>2021년 8월

* [기존 SMB 볼륨에서 지속적인 가용성 사용](enable-continuous-availability-existing-SMB.md) 지원

    이미 [새 SMB 볼륨을 만들](azure-netapp-files-create-volumes-smb.md#continuous-availability) 때 SMB CA(지속적인 가용성) 기능을 사용하도록 설정할 수 있습니다. 이제는 기존 SMB 볼륨에서 SMB CA를 사용하도록 설정할 수도 있습니다. [기존 SMB 볼륨에서 지속적인 가용성 사용](enable-continuous-availability-existing-SMB.md)을 참조하세요.

* [스냅샷 정책](snapshots-manage-policy.md)이 이제 GA(일반 공급) 방식으로 제공됨  

    이제 스냅샷 정책 기능이 일반 제공됩니다. 이 기능을 사용하기 전에 더 이상 등록할 필요가 없습니다.

* [NFS `Chown Mode` 내보내기 정책 및 UNIX 내보내기 권한](configure-unix-permissions-change-ownership-mode.md)(미리 보기)   

    이제 Unix 보안 스타일을 통해 Azure NetApp Files NFS 볼륨 또는 이중 프로토콜 볼륨에 대한 Unix 권한과 소유권 모드 변경(`Chown Mode`) 옵션을 설정할 수 있습니다. 볼륨을 만드는 동안 또는 볼륨을 만든 후에 이러한 설정을 지정할 수 있습니다.   

    소유권 모드 변경(`Chown Mode`) 기능을 사용하면 파일 및 디렉터리의 소유권 관리 기능을 설정할 수 있습니다. 볼륨의 내보내기 정책에서 설정을 지정하거나 수정할 수 있습니다. `Chown Mode`에 두 가지 옵션을 사용할 수 있습니다. *제한*(기본값) 옵션에서는 루트 사용자만 파일 및 디렉터리의 소유권을 변경할 수 있고, *무제한* 에서는 루트가 아닌 사용자도 자신이 소유한 파일과 디렉터리의 소유권을 변경할 수 있습니다.   

    Azure NetApp Files Unix 권한 기능을 사용하면 탑재 경로에 대한 변경 권한을 지정할 수 있습니다. 

    이러한 새로운 기능은 특정 파일 및 디렉터리에 대한 액세스 제어 권한을 서비스 운영자가 아닌 데이터 사용자에게 넘길 수 있는 옵션을 제공합니다.   

* [이중 프로토콜(NFSv4.1 및 SMB) 볼륨](create-volumes-dual-protocol.md)(미리 보기)   

    Azure NetApp Files는 이미 [2020년 7월](#july-2020) 기준으로 NFSv3 및 SMB 볼륨에 대한 이중 프로토콜 액세스를 지원하고 있습니다. 이제 LDAP 사용자 매핑을 지원하여 동시 이중 프로토콜(NFSv4.1 및 SMB) 액세스를 허용하는 Azure NetApp Files 볼륨을 만들 수 있습니다. 이 기능을 사용하면 액세스에 NFSv4.1을 사용하는 Linux 기반 워크로드가 있고 해당 워크로드가 Azure NetApp Files 볼륨에 데이터를 생성 및 저장하는 사용 사례를 구현할 수 있습니다. 동시에 직원은 Windows 기반 클라이언트와 소프트웨어를 사용하여 동일한 Azure NetApp Files 볼륨에서 새로 생성된 데이터를 분석해야 합니다. 동시 이중 프로토콜 액세스 기능을 사용하면 다른 프로토콜을 사후 분석에 사용하여 워크로드 생성 데이터를 별도의 볼륨에 복사할 필요가 없으므로 스토리지 비용과 운영 시간을 절약할 수 있습니다. 이 기능은 무료이며(일반 Azure NetApp Files 스토리지 비용은 계속 적용됨) 일반 공급됩니다. [동시 이중 프로토콜 NFSv4.1/SMB 액세스](create-volumes-dual-protocol.md) 설명서에서 자세히 알아보세요.

## <a name="june-2021"></a>2021년 6월  

* [Azure NetApp Files 스토리지 서비스 추가 기능](storage-service-add-ons.md)

    새 Azure NetApp Files **스토리지 서비스 추가 기능** 메뉴 옵션은 Azure NetApp Files 스토리지 서비스에 대해 사용 가능한 타사 에코시스템 추가 기능을 위한 Azure Portal "실행 공간"을 제공합니다. 이 새 포털 메뉴 옵션을 사용하면 추가 기능 타일을 클릭한 후 방문 페이지에 들어가 추가 기능에 빠르게 액세스할 수 있습니다.  

    **NetApp 추가 기능** 은 **스토리지 서비스 추가 기능** 아래에 도입된 추가 기능의 첫 번째 범주입니다. 여기서 **NetApp Cloud Compliance** 에 액세스할 수 있습니다. **NetApp Cloud Compliance** 타일을 클릭하면 새 브라우저가 열리고 추가 기능 설치 페이지로 이동됩니다. 

* [수동 QoS 용량 풀](manual-qos-capacity-pool-introduction.md)은 이제 GA(일반 공급)로 제공됨   

    이제 수동 QoS 용량 풀 기능이 일반 공급됩니다. 이 기능을 사용하기 전에 더 이상 등록할 필요가 없습니다. 

* [구독당 지역별로 하나의 Active Directory에 대해 여러 계정에 대한 공유 AD 지원](create-active-directory-connections.md#shared_ad)(미리 보기)   

    현재까지 Azure NetApp Files는 지역별로 단일 AD(Active Directory)만 지원하므로, 단일 NetApp 계정만 AD에 액세스하도록 구성할 수 있습니다. 새 **공유 AD** 기능을 사용하면 모든 NetApp 계정이 동일한 구독 및 동일한 지역에 속하는 NetApp 계정 중 하나로 만든 AD 연결을 공유할 수 있습니다. 예를 들어, 이 기능을 사용하면 동일한 구독 및 지역의 모든 NetApp 계정이 공통 AD 구성을 사용하여 SMB 볼륨, NFSv4.1 Kerberos 볼륨 또는 이중 프로토콜 볼륨을 만들 수 있습니다. 이 기능을 사용할 경우 동일한 구독 및 동일한 지역에 있는 모든 NetApp 계정에 AD 연결이 표시됩니다.

## <a name="may-2021"></a>2021년 5월 

* Azure NetApp Files 애플리케이션 일치 스냅샷 도구[(AzAcSnap)](azacsnap-introduction.md)가 이제 일반 공급 방식으로 제공됩니다. 

    AzAcSnap은 Linux 환경(예: SUSE 및 RHEL)에서 타사 데이터베이스(SAP HANA)에 대한 데이터 보호를 간소화하는 데 사용할 수 있는 명령줄 도구입니다. 도구에 대한 최신 변경 내용은 [AzAcSnap의 릴리스 정보](azacsnap-release-notes.md)를 참조하세요.   

* [용량 풀 청구 태그 지원](manage-billing-tags.md)   

    이제 Azure NetApp Files는 사업부 또는 기타 내부 소비자와의 상호 참조 비용을 파악하는 데 도움이 되는 청구 태그를 지원합니다. 청구 태그는 볼륨 수준에서 할당되지 않고 용량 풀 수준에서 할당되며, 고객 청구서에 표시됩니다.

* [TLS를 통해 ADDS LDAP](configure-ldap-over-tls.md)(미리 보기) 

    기본적으로 클라이언트와 서버 애플리케이션 간의 LDAP 통신은 암호화되지 않습니다. 즉, 네트워크 모니터링 디바이스나 소프트웨어를 사용하여 LDAP 클라이언트와 서버 컴퓨터 간의 통신을 볼 수 있습니다. LDAP 클라이언트를 LDAP 서버에 바인딩하는 데 사용된 자격 증명(사용자 이름 및 암호)이 네트워크를 통해 암호화되지 않은 상태로 전달되기 때문에 이 시나리오는 LDAP 단순 바인딩을 사용하는 경우 격리되지 않거나 공유된 VNet에서 문제가 될 수 있습니다. TLS를 통한 LDAP(LDAPS라고도 함)는 TLS를 사용하여 LDAP 클라이언트와 LDAP 서버 간의 통신을 보호하는 프로토콜입니다. 이제 Azure NetApp Files는 TLS를 통한 LDAP를 사용하여 ADDS(Active Directory 도메인 서버) 간의 보안 통신을 지원합니다. 이제 Azure NetApp Files는 Active Directory 통합 LDAP 서버 간에 인증된 세션을 설정하는 데 TLS를 통한 LDAP를 사용할 수 있습니다. NFS, SMB 및 이중 프로토콜 볼륨의 경우 TLS 기능을 통해 LDAP를 사용하도록 설정할 수 있습니다. 기본적으로 Azure NetApp Files에서 TLS를 통한 LDAP를 사용할 수 없습니다.  

* 처리량 [메트릭](azure-netapp-files-metrics.md) 지원    

    Azure NetApp Files는 다음 메트릭에 대한 지원을 추가합니다.   
    * 용량 풀 처리량 메트릭
        * *볼륨 처리량에 할당된 풀*
        * *풀 사용 처리량*
        * *볼륨에 할당된 풀 처리량 비율*
        * *풀 사용 처리량 비율*
    * 볼륨 처리량 메트릭
        * *볼륨 할당 처리량*
        * *볼륨 사용 처리량*
        * *볼륨 사용 처리량 비율*

* 복제 볼륨의 [서비스 수준 동적 변경](dynamic-change-volume-service-level.md) 지원   

    이제 Azure NetApp Files를 사용하면 복제 원본 및 대상 볼륨의 서비스 수준을 동적으로 변경할 수 있습니다.

## <a name="april-2021"></a>2021년 4월

* [수동 볼륨 및 용량 풀 관리](volume-quota-introduction.md)(하드 할당량) 

    Azure NetApp Files 볼륨 및 용량 풀 프로비저닝의 동작은 수동 및 제어 가능한 메커니즘으로 변경되었습니다. 볼륨의 스토리지 용량은 볼륨의 집합 크기(할당량)로 제한됩니다. 볼륨 소비가 최대를 초과하면 볼륨과 기본 용량 풀이 자동으로 증가하지 않습니다. 대신 볼륨이 "공간 부족" 상태로 됩니다. 그러나 필요에 따라 [용량 풀 또는 볼륨의 크기를 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md)할 수 있습니다. 적극적으로 기본 용량 풀 및 [볼륨의 용량을 모니터링](monitor-volume-capacity.md)해야 합니다.

    이 동작 변경은 여러 사용자가 제기한 다음과 같은 주요 요청의 결과입니다.

    * 이전에는 VM 클라이언트가 OS 공간 또는 용량 모니터링 도구를 사용할 때 지정된 볼륨의 씬 프로비전(100TiB) 용량을 확인했습니다.  이러한 상황으로 인해 클라이언트 또는 애플리케이션 측에 대한 부정확한 용량 가시성이 제공될 수 있습니다. 이 동작이 이제 수정되었습니다.  
    * 용량 풀의 이전 자동 증가 동작은 애플리케이션 소유자에게 프로비전된 용량 풀 공간(및 관련 비용)에 대한 제어력을 제공하지 않습니다. 이 동작은 특히 "런어웨이 프로세스"가 프로비전된 용량을 빠르게 채우고 증가시킬 수 있는 환경에서는 번거롭습니다. 이 동작이 수정되었습니다.  
    * 사용자는 볼륨 크기(할당량)와 성능 간의 직접적인 상관 관계를 확인하고 유지하기를 원합니다. 이전 동작에서는 볼륨(용량) 및 용량 풀 자동 증가에 대한 (암시적) 초과 구독이 허용되었습니다. 따라서 볼륨 할당량이 적극적으로 설정되거나 다시 설정될 때까지 사용자가 직접 상관 관계를 만들 수 없습니다. 이 동작이 이제 수정되었습니다.

    사용자가 프로비전된 용량에 대한 직접 제어를 요청했습니다. 사용자는 스토리지 용량 및 사용률을 제어하고 균형을 조정하고자 합니다. 또한 애플리케이션 볼륨의 가용, 사용 및 프로비전된 용량 및 성능에 대한 애플리케이션 측 및 클라이언트 측 가시성과 함께 비용을 제어하고자 합니다. 이제 이 새로운 동작으로 이러한 기능이 모두 지원됩니다.

* [FSLogix 사용자 프로필 컨테이너의 SMB CA(지속적인 가용성) 공유 지원](azure-netapp-files-create-volumes-smb.md#continuous-availability)(미리 보기)  

    [FSLogix](/fslogix/overview)는 비 영구 Windows 컴퓨팅 환경을 개선, 사용 및 간소화하는 솔루션 세트입니다. FSLogix 솔루션은 퍼블릭 클라우드와 프라이빗 클라우드의 가상 환경에 적합합니다. 또한 FSLogix 솔루션은 물리적 디바이스를 사용하는 경우 이식 가능 컴퓨팅 세션을 만드는 데 사용될 수 있습니다. FSLogix는 Azure NetApp Files 등 SMB 공유 네트워크 스토리지에 저장된 영구 사용자 프로필 컨테이너에 동적 액세스를 제공하는 데 사용될 수 있습니다. 스토리지 서비스 유지 관리 이벤트에 대한 FSLogix 복원력을 강화하기 위해 Azure NetApp Files에서는 [Azure NetApp Files의 SMB CA(지속적인 가용성) 공유](azure-netapp-files-create-volumes-smb.md#continuous-availability)를 통해 사용자 프로필 컨테이너에 대한 SMB 투명 장애 조치(Failover)를 추가로 지원합니다. 자세한 내용은 Azure NetApp Files [Azure Virtual Desktop 솔루션](azure-netapp-files-solution-architectures.md#windows-virtual-desktop)을 참조하세요.  

* [SMB3 프로토콜 암호화](azure-netapp-files-create-volumes-smb.md#smb3-encryption)(미리 보기) 

    이제 Azure NetApp Files SMB 및 이중 프로토콜 볼륨에서 SMB3 프로토콜 암호화를 사용하도록 설정할 수 있습니다. 이 기능을 통해 [SMB 3.0의 AES-CCM 알고리즘과 SMB 3.1.1의 AES GCM 알고리즘](/windows-server/storage/file-server/file-server-smb-overview#features-added-in-smb-311-with-windows-server-2016-and-windows-10-version-1607) 연결을 사용하여 진행 중인 SMB3 데이터를 암호화할 수 있습니다. SMB3 암호화를 사용하지 않는 SMB 클라이언트는 이 볼륨에 액세스할 수 없습니다. 미사용 데이터는 이 설정에 관계없이 암호화됩니다. SMB 암호화를 통해 보안이 강화됩니다. 그러나 클라이언트(메시지 암호화 및 암호 해독을 위한 CPU 오버헤드)에 영향을 줄 수 있습니다. 스토리지 리소스 사용률(처리량 감소)에도 영향을 줄 수 있습니다. 프로덕션에 워크로드를 배포하기 전에 애플리케이션에 대한 암호화 성능 영향을 테스트해야 합니다.

* [NFS 확장 그룹을 사용하여 ADDS(Active Directory Domain Services) LDAP 사용자 매핑](configure-ldap-extended-groups.md)(미리 보기)   

    기본적으로 Azure NetApp Files는 [RFC 5531](https://tools.ietf.org/html/rfc5531)에 정의된 대로 NFS 사용자 자격 증명을 처리할 때 최대 16개의 그룹 ID를 지원합니다. 이 새로운 기능을 사용하면 기본 그룹 수를 초과하는 사용자가 있는 경우 최대 1,024까지 최대 수를 늘릴 수 있습니다. 이 기능을 지원하기 위해 이제는 NFS 볼륨을 ADDS LDAP에 추가할 수 있습니다. 이를 통해 확장된 그룹 항목(최대 1024 그룹)의 Active Directory LDAP 사용자가 볼륨에 액세스할 수 있습니다. 

## <a name="march-2021"></a>2021년 3월
 
* [SMB CA(지속적인 가용성) 공유](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)(미리 보기)  

    SMB 투명 장애 조치(Failover)를 사용하면 SMB 볼륨의 데이터를 저장 및 액세스하는 서버 애플리케이션에 대한 연결을 중단하지 않고도 Azure NetApp Files 서비스에서 유지 관리 작업을 수행할 수 있습니다. SMB 투명 장애 조치(Failover)를 지원하기 위해 Azure NetApp Files는 이제 Azure VM에서 실행되는 SMB를 통해 SQL Server 애플리케이션에서 사용할 수 있는 SMB 지속적인 가용성 공유 옵션을 지원합니다. 이 기능은 현재 Windows SQL Server에서 지원됩니다. Linux SQL Server는 현재 지원되지 않습니다. 이 기능을 사용하도록 설정하면 [단일 인스턴스, Always-On 장애 조치(Failover) 클러스터 인스턴스 및 Always-On 가용성 그룹 배포](azure-netapp-files-solution-architectures.md#sql-server)에 대한 SQL Server 성능 향상, 확장성 및 비용 혜택이 제공됩니다. [SQL Server 배포에 Azure NetApp Files 사용할 경우의 이점](solutions-benefits-azure-netapp-files-sql-server.md)을 참조하세요.

* [지역 간 복제 대상 볼륨의 자동 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    지역 간 복제 관계에서 대상 볼륨은 원본 볼륨의 크기에 따라 자동으로 크기가 조정됩니다. 따라서 대상 볼륨의 크기를 별도로 조정할 필요가 없습니다. 이러한 자동 크기 조정 동작은 볼륨이 활성 복제 관계에 있거나 복제 피어링이 다시 동기화 작업으로 중단된 경우에 적용됩니다. 이 기능이 작동하려면 원본 볼륨과 대상 볼륨 모두에 대해 충분한 여유 공간이 용량 풀에 있어야 합니다.

## <a name="december-2020"></a>2020년 12월

* [Azure Application Consistent Snapshot Tool](azacsnap-introduction.md)(미리 보기)    

    AzAcSnap(Azure Application Consistent Snapshot Tool)은 Linux 환경(예: SUSE 및 RHEL)에서 타사 데이터베이스(SAP HANA)에 대한 데이터 보호를 간소화하는 데 사용할 수 있는 명령줄 도구입니다.   

    AzAcSnap은 Azure NetApp Files 및 Azure 대규모 인스턴스의 볼륨 스냅샷 및 복제 기능을 활용합니다. 다음과 같은 이점을 제공합니다.

    * 애플리케이션 일치 데이터 보호 
    * 데이터베이스 카탈로그 관리 
    * *임시* 볼륨 보호 
    * 스토리지 볼륨 복제 
    * 재해 복구 지원 

## <a name="november-2020"></a>2020년 11월

* [스냅샷 되돌리기](snapshots-revert-volume.md)

    스냅샷 되돌리기 기능을 사용하면 볼륨을 특정 스냅샷을 만들 때의 상태로 신속하게 되돌릴 수 있습니다. 대부분의 경우 볼륨을 되돌리는 것이 스냅샷에서 활성 파일 시스템으로 개별 파일을 복원하는 것보다 훨씬 빠릅니다. 또한 스냅샷을 새 볼륨으로 복원하는 것에 비해 공간 효율성이 더 높습니다.

## <a name="september-2020"></a>2020년 9월

* [Azure NetApp Files 지역 간 복제](cross-region-replication-introduction.md)(미리 보기)

  Azure NetApp Files는 이제 지역 간 복제를 지원합니다. 이 새로운 재해 복구 기능을 사용하면 Azure NetApp Files 볼륨을 Azure 지역 간에 빠르고 비용 효율적인 방식으로 복제하여 예측할 수 없는 지역 오류로부터 데이터를 보호할 수 있습니다. Azure NetApp Files 지역 간 복제는 NetApp SnapMirror® 기술을 활용하며, 변경된 블록만 압축되고 효율적인 형식으로 네트워크를 통해 전송됩니다. 이 독점적 기술은 지역 간에 복제하는 데 필요한 데이터의 양을 최소화하여 데이터 전송 비용을 절감합니다. 또한 복제 시간이 단축되므로 더 작은 RPO(복원 지점 목표)를 달성할 수 있습니다.

* [수동 QoS 용량 풀](manual-qos-capacity-pool-introduction.md)(미리 보기)  

    수동 QoS 용량 풀에서는 볼륨의 용량과 처리량을 독립적으로 할당할 수 있습니다. 수동 QoS 용량 풀로 만든 모든 볼륨의 총 처리량은 풀의 총 처리량으로 제한됩니다. 이는 풀 크기와 서비스 수준 처리량의 조합에 따라 결정됩니다. 또는 용량 풀의 [QoS 유형](azure-netapp-files-understand-storage-hierarchy.md#qos_types)이 기본값인 auto(자동)가 될 수 있습니다. 자동 QoS 용량 풀에서 처리량은 볼륨에 할당된 크기 할당량에 비례하여 풀의 볼륨에 자동으로 할당됩니다.

* [LDAP 서명](create-active-directory-connections.md#create-an-active-directory-connection)(미리 보기)   

    Azure NetApp Files는 이제 Azure NetApp Files 서비스와 사용자 지정 Active Directory Domain Services 도메인 컨트롤러 간의 보안 LDAP 조회를 위해 LDAP 서명을 지원합니다. 이 기능은 현재 미리 보기로 제공됩니다.

* [AD 인증용 AES 암호화](create-active-directory-connections.md#create-an-active-directory-connection)(미리 보기)

    이제 Azure NetApp Files는 SMB 볼륨에 대한 AES 암호화를 사용하도록 설정하기 위해 DC에 대한 LDAP 연결에서 AES 암호화를 지원합니다. 이 기능은 현재 미리 보기로 제공됩니다. 

* 새 [메트릭](azure-netapp-files-metrics.md):   

    * 새 볼륨 메트릭: 
        * *할당된 볼륨 크기*: 프로비저닝된 볼륨 크기
    * 새 풀 메트릭: 
        * *할당된 풀 크기*: 프로비저닝된 풀 크기 
        * *풀의 총 스냅샷 크기*: 풀의 모든 볼륨에 있는 스냅샷 크기의 합계

## <a name="july-2020"></a>2020년 7월

* [이중 프로토콜(NFSv3 및 SMB) 볼륨](create-volumes-dual-protocol.md)

    이제 LDAP 사용자 매핑을 지원하여 동시 이중 프로토콜(NFS v3 및 SMB) 액세스를 허용하는 Azure NetApp Files 볼륨을 만들 수 있습니다. 이 기능을 사용하면 Azure NetApp Files 볼륨에서 데이터를 생성하고 저장하는 Linux 기반 워크로드가 있을 수 있는 사용 사례를 사용할 수 있습니다. 이와 동시에 직원은 Windows 기반 클라이언트와 소프트웨어를 사용하여 동일한 Azure NetApp Files 볼륨에서 새로 생성된 데이터를 분석해야 합니다. 동시 이중 프로토콜 액세스 기능을 사용하면 다른 프로토콜을 사후 분석에 사용하여 워크로드 생성 데이터를 별도의 볼륨에 복사할 필요가 없으므로 스토리지 비용과 운영 시간을 절약할 수 있습니다. 이 기능은 무료이며(일반 [Azure NetApp Files 스토리지 비용](https://azure.microsoft.com/pricing/details/netapp/)은 계속 적용됨) 일반 공급됩니다. [동시 이중 프로토콜 액세스 설명서](create-volumes-dual-protocol.MD)에서 자세히 알아보세요.

* [전송 중 NFS v4.1 Kerberos 암호화](configure-kerberos-encryption.MD)

    Azure NetApp Files는 이제 AES-256 암호화를 사용하는 Kerberos 모드(krb5, krb5i 및 krb5p)에서 NFS 클라이언트 암호화를 지원하여 추가 데이터 보안을 제공합니다. 이 기능은 무료이며(일반 [Azure NetApp Files 스토리지 비용](https://azure.microsoft.com/pricing/details/netapp/)은 계속 적용됨) 일반 공급됩니다. [NFS v4.1 Kerberos 암호화 설명서](configure-kerberos-encryption.MD)에서 자세히 알아보세요.

* [동적 볼륨 서비스 수준 변경](dynamic-change-volume-service-level.MD)(미리 보기) 

    클라우드는 IT 지출의 유연성을 보장합니다. 이제 볼륨에 대해 원하는 서비스 수준을 사용하는 다른 용량 풀로 볼륨을 이동하여 기존 Azure NetApp Files 볼륨의 서비스 수준을 변경할 수 있습니다. 볼륨에 대한 이러한 내부 서비스 수준 변경에서는 데이터를 마이그레이션할 필요가 없습니다. 또한 볼륨에 대한 데이터 평면 액세스에도 영향을 주지 않습니다. 더 높은 서비스 수준을 사용하여 성능을 향상시키거나 더 낮은 서비스 수준을 사용하여 비용을 최적화하도록 기존 볼륨을 변경할 수 있습니다. 이 기능은 무료입니다(일반 [Azure NetApp Files 스토리지 비용](https://azure.microsoft.com/pricing/details/netapp/)은 계속 적용됨). 현재 미리 보기로 제공되고 있습니다. [동적 볼륨 서비스 수준 변경 설명서](dynamic-change-volume-service-level.md)에 따라 기능 미리 보기에 등록할 수 있습니다.

* [볼륨 스냅샷 정책](snapshots-manage-policy.md)(미리 보기) 

    Azure NetApp Files를 사용하면 볼륨의 특정 시점 스냅샷을 만들 수 있습니다. 이제 Azure NetApp Files에서 볼륨 스냅샷을 선택한 빈도로 자동으로 만들도록 하는 스냅샷 정책을 만들 수 있습니다. 스냅샷을 매시간, 매일, 매주 또는 매월 주기로 수행하도록 예약할 수 있습니다. 또한 스냅샷 정책의 일부로 유지할 최대 스냅샷 수를 지정할 수도 있습니다. 이 기능은 무료이며(일반 [Azure NetApp Files 스토리지 비용](https://azure.microsoft.com/pricing/details/netapp/)은 계속 적용됨) 현재 미리 보기 상태입니다. [볼륨 스냅샷 정책 설명서](snapshots-manage-policy.md)에 따라 기능 미리 보기에 등록할 수 있습니다.

* [NFS 루트 액세스 내보내기 정책](azure-netapp-files-configure-export-policy.md)

    이제 Azure NetApp Files를 사용하면 루트 계정에서 볼륨에 액세스할 수 있는지 여부를 지정할 수 있습니다. 

* [스냅샷 경로 숨기기](snapshots-edit-hide-path.md)

    이제 Azure NetApp Files를 사용하면 사용자가 탑재된 볼륨에서 `.snapshot` 디렉터리(NFS 클라이언트) 또는 `~snapshot` 폴더(SMB 클라이언트)를 보고 액세스할 수 있는지 여부를 지정할 수 있습니다.

## <a name="may-2020"></a>2020년 5월

* [백업 정책 사용자](create-active-directory-connections.md)(미리 보기)

    Azure NetApp Files를 사용하면 Azure NetApp Files에서 사용하기 위해 만든 컴퓨터 계정에 대한 상승된 권한이 필요한 추가 계정을 포함할 수 있습니다. 지정된 계정은 파일 또는 폴더 수준에서 NTFS 권한을 변경할 수 있습니다. 예를 들어 Azure NetApp Files에서 SMB 파일 공유로 데이터를 마이그레이션하는 데 사용되는 권한 없는 서비스 계정을 지정할 수 있습니다. 백업 정책 사용자 기능은 현재 미리 보기 상태입니다.

## <a name="next-steps"></a>다음 단계
* [Azure NetApp Files란?](azure-netapp-files-introduction.md)
* [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md) 
