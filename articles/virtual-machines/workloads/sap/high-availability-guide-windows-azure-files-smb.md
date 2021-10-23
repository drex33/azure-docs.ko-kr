---
title: Azure Files (SMB)를 사용 하는 Windows에서 SAP NW 용 Azure vm HA | Microsoft Docs
description: sap 응용 프로그램용 SMB (Azure Files)를 사용 하는 Windows에서 Azure vm의 sap NetWeaver에 대 한 고가용성
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: stmuelle
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/13/2021
ms.author: stmuelle
ms.openlocfilehash: 458c63f27e9e17d71b9af97eda0de56f8f935a25
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251360"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-files-premium-smb-for-sap-applications"></a>sap 응용 프로그램에 대 한 Azure Files Premium SMB를 사용 하 Windows의 Azure vm에서 sap NetWeaver에 대 한 고가용성

## <a name="introduction"></a>소개
Azure Files Premium SMB는 이제 Microsoft 및 SAP에서 완벽 하 게 지원 됩니다. **swpm 1.0 SP32** 및 **swpm 2.0 SP09** 이상 Azure Files Premium SMB 저장소를 지원 합니다.  SMB 공유 Azure Files Premium 크기를 조정 하기 위한 특별 한 요구 사항이 있습니다. 이 설명서에는 Azure Files Premium smb에 대 한 작업을 배포 하는 방법, smb를 Premium Azure Files 적절 한 크기를 조정 하는 방법, smb Azure Files Premium에 대 한 최소 설치 요구 사항이 포함 되어 있습니다.

고가용성 SAP 솔루션에는 **sapmnt**, **트랜잭션** 및 **인터페이스 디렉터리** 호스팅을 위한 항상 사용 가능한 파일 공유가 필요 합니다. Azure Files Premium SMB는 Windows 환경에서 SAP 용 공유 파일 시스템에 대 한 간단한 Azure PaaS 솔루션입니다. Azure Files Premium SMB를 가용성 집합 및 가용성 영역와 함께 사용할 수 있습니다. Azure Files Premium SMB는 다른 지역에 대 한 재해 복구 시나리오에도 사용할 수 있습니다.  
 
## <a name="sizing--distribution-of-azure-files-premium-smb-for-sap-systems"></a>SAP 시스템용 SMB Premium Azure Files & 배포 크기 조정

SMB Azure Files Premium 배포를 계획할 때 다음 사항을 평가 해야 합니다.
* 저장소 계정 별로 **Sapmnt** 파일 공유 이름을 한 번 만들 수 있습니다.  - **\<SID1\> /Sapmnt/** 및 **/sapmnt/ \<SID2\>** 와 같은 **/sapmnt** 공유에 디렉터리로 추가 sid를 만들 수 있습니다. 
* 적절 한 크기, IOPS 및 처리량을 선택 합니다.  공유에 권장 되는 크기는 SID 당 256GB입니다.  공유의 최대 크기는 5120 GB입니다.
* Azure Files Premium SMB는 저장소 계정 당 1-2 백만 개 이상의 파일을 포함 하는 매우 큰 **sapmnt** 공유에 대해 최적으로 수행 되지 않을 수 있습니다.수백만 개의 작업 로그 파일을 만드는 수많은 일괄 처리 작업을 포함 하는 고객은 필요한 경우 [SAP Note 16083][16083] 에 따라 정기적으로 다시 구성 해야 합니다. 그러면 이전 작업 로그를 다른 Azure Files Premium SMB로 이동/보관할 수 있습니다. **Sapmnt** 이 매우 클 것으로 예상 되는 경우에는 대체 옵션 (예: AZURE anf)을 고려해 야 합니다.
* 개인 네트워크 끝점을 사용 하는 것이 좋습니다.
* 단일 저장소 계정 및 해당 파일 공유에 너무 많은 Sid를 통합 하지 마십시오.
* 일반 지침에 따라 2 ~ 4 비 prod Sid를 함께 통합할 수 있습니다.
* 전체 개발 인 QAS + 프로덕션 가로를 하나의 저장소 계정 및/또는 파일 공유에 통합 하지 마십시오.공유에 실패 하면 전체 SAP 환경에서 가동 중지 시간이 발생 합니다.
* 매우 작은 시스템을 제외 하 고 동일한 저장소 계정에 **sapmnt** 및 **transport 디렉터리** 를 통합 하는 것은 권장 되지 않습니다. SAP PAS 인스턴스를 설치 하는 동안 SAPInst은 전송 호스트 이름을 요청 합니다.  File.core.windows.net> storage_account <다른 저장소 계정의 FQDN을 입력 해야 합니다.
* 인터페이스에 사용 되는 파일 시스템을 **/sapmnt/ \<SID>** 와 동일한 저장소 계정에 통합 하지 마십시오. 
* SAP 사용자/그룹을 ' sapmnt ' 공유에 추가 하 고 Azure Portal: **Storage 파일 데이터 SMB 공유 승격 된 참가자** 에서이 권한을 설정 해야 합니다.

**전송**, **인터페이스** 및 **sapmnt** 를 별도의 저장소 계정으로 분리 하는 중요 한 이유가 있습니다.  이러한 구성 요소를 별도의 저장소 계정에 배포 하면 처리량이 향상 되 고, 복원 력이 향상 되며, 성능 분석이 간단해 집니다.  많은 sid 및 기타 파일 시스템이 단일 Azure Files Storage 계정으로 통합 되 고 저장소 계정 성능이 처리량 한도에 도달 하 여 저하 된 경우 문제를 일으킨 SID 또는 응용 프로그램을 식별 하기가 매우 어렵습니다. 

## <a name="planning"></a>계획 
> [!IMPORTANT]
> Active Directory 통합을 통해 Azure Files Premium SMB에 SAP 고가용성 시스템을 설치 하려면 팀 간 공동 작업을 수행 해야 합니다.  기본 팀, Active Directory 팀 및 Azure 팀이 함께 작업 하 여 이러한 작업을 완료 하는 것이 좋습니다. 
>
* Azure 팀-Storage 계정, 스크립트 실행 및 AD 디렉터리 동기화의 설정 및 구성
* Active Directory 팀 – 사용자 계정 및 그룹 만들기
* 기준 팀 – SWPM을 실행 하 고 Acl을 설정 합니다 (필요한 경우).

Azure Files Premium SMB에서 SAP NetWeaver 고가용성 시스템을 설치 하기 위한 필수 구성 요소 Active Directory 통합.

* SAP 서버는 Active Directory 도메인에 가입 되어 있어야 합니다.
* SAP 서버를 포함 하는 Active Directory 도메인는 Azure AD connect를 사용 하 여 Azure Active Directory에 복제 되어야 합니다.
* 온-프레미스에서 도메인 컨트롤러에 연결 하는 Express 경로를 트래버스 하지 않도록 Azure에 하나 이상의 Active Directory 도메인 컨트롤러가 있는 것이 좋습니다.
* Azure 지원 팀은 [Active Directory 통합](../../../storage/files/storage-files-identity-auth-active-directory-enable.md#videos) 설명서를 사용 하 여 Azure Files SMB를 검토 해야 합니다. *이 비디오에는 단순화를 위해 수정 (DNS) 하 고 건너뛴 (DFS-N) 추가 구성 옵션이 표시 됩니다.* 그럼에도 불구 하 고 유효한 구성 옵션입니다. 
* Azure Files PowerShell 스크립트를 실행 하는 사용자는 Active Directory에서 개체를 만들 수 있는 권한이 있어야 합니다.
* **SWPM 버전 1.0 SP32 및 SWPM 2.0 SP09 이상이 필요 합니다. SAPInst patch는 749.0.91 이상 이어야 합니다.**
* PowerShell의 최신 릴리스는 스크립트가 실행 되는 Windows 서버에 설치 해야 합니다. 

## <a name="installation-sequence"></a>설치 순서
 1. Active Directory 관리자는 **로컬 관리자** 권한이 있는 도메인 사용자 3 대와 로컬 **Windows AD** 에 하나의 글로벌 그룹 ( **SAPCONT_ADMIN@SAPCONTOSO.local** 도메인 관리자 권한 있음)을 만들고 SAP 시스템 사용자 및 **SAP_ \<SAPSID> _GlobalAdmin** 그룹으로 **SAPInst**, **\<sid> adm** 및 **SAPService \<SID>** 를 실행 하는 데 사용 됩니다. SAP 설치 가이드에는 이러한 계정에 필요한 특정 정보가 포함 되어 있습니다.  **SAP 사용자 계정은 도메인 관리자** 가 아니어야 합니다. 일반적으로 **adm을 사용 하 여 \<sid> SAPInst를 실행 하지 않는** 것이 좋습니다.
 2. Active Directory 관리자나 Azure 관리자는 **Azure AD Connect** Synchronization Service Manager를 확인 해야 합니다. 기본적으로 **Azure Active Directory** 에 복제 하는 데 약 30 분이 걸립니다. 
 3. Azure 관리자는 다음 작업을 완료 해야 합니다.
     1. **Premium ZRS** 또는 **LRS** 를 사용 하 여 Storage 계정을 만듭니다. 영역 배포를 사용 하는 고객은 ZRS를 선택 해야 합니다. **표준** 또는 **Premium 계정을** 설정 하는 방법을 선택 해야 합니다. ![ 저장소 계정 만들기에 대 한 Azure Portal 스크린샷-1 단계 ](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-1.png) Azure Portal 저장소 계정 만들기에 대 한 스크린샷-1 단계
         > [!IMPORTANT]
         > 생산적인 사용에 대 한 권장 사항은 **Premium 계정을** 사용 하는 것입니다. **표준 계정을** 사용 하는 경우 생산적으로 충분 합니다. 
         >
         ![저장소 계정 만들기에 대 한 Azure Portal 스크린샷-2 단계](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-2.png)저장소 계정 만들기에 대 한 Azure Portal 스크린샷-2 단계
         
         이 화면에서는 기본 설정이 양호 해야 합니다.
        
         ![저장소 계정 만들기에 대 한 Azure Portal 스크린샷-3 단계](media/virtual-machines-shared-sap-high-availability-guide/create-sa-4.png)저장소 계정 만들기에 대 한 Azure Portal 스크린샷-3 단계 
         
         이 단계에서는 개인 끝점을 사용 하기로 결정 했습니다.
     1. 저장소 계정에 대 한 **개인 네트워크 끝점을 선택** 합니다.
     필요한 경우 Windows **> storage_account_name<** dns에 dns a 레코드를 추가 합니다. file.core.windows.net (이는 새 dns 영역에 있어야 할 수 있음).  DNS 관리자와 함께이 항목에 대해 설명 합니다.  새 영역은 조직 외부에서 업데이트 해서는 안 됩니다.  
         ![pivate- ](media/virtual-machines-shared-sap-high-availability-guide/create-sa-3.png) 개인 끝점 정의에 대 한 Azure Portal 스크린샷을 만듭니다.
         ![개인 끝점-dns-1 ](media/virtual-machines-shared-sap-high-availability-guide/pe-dns-1.png) dns 서버 개인 끝점 dns 정의의 스크린샷    
     1. 적절 한 크기로 **sapmnt** 파일 공유를 만듭니다.  권장 크기는 256gb 이며 650 IOPS, 75 m b/초 Egress 및 50 m b/초 수신이 제공 됩니다.
         ![](media/virtual-machines-shared-sap-high-availability-guide/create-sa-5.png)SMB 공유 정의에 대 한 만들기-저장소-계정 5 Azure Portal 스크린샷 
      
     1. [Azure Files GitHub](../../../storage/files/storage-files-identity-ad-ds-enable.md#download-azfileshybrid-module) 콘텐츠를 다운로드 하 고 [스크립트](../../../storage/files/storage-files-identity-ad-ds-enable.md#run-join-azstorageaccountforauth)를 실행 합니다.   
     이 스크립트는 Active Directory에 컴퓨터 계정 또는 서비스 계정을 만듭니다.  스크립트를 실행 하는 사용자에 게는 다음과 같은 속성이 있어야 합니다. 
         * 스크립트를 실행 하는 사용자에 게는 SAP 서버를 포함 하는 Active Directory 도메인에서 개체를 만들 수 있는 권한이 있어야 합니다. 일반적으로 도메인 관리자 계정이 사용 됩니다. **SAPCONT_ADMIN@SAPCONTOSO.local** 
         * 스크립트를 실행 하기 전에이 Active Directory 도메인 사용자 계정이 Azure Active Directory (AAD)와 동기화 되었는지 확인 합니다.  이에 대 한 예는 Azure Portal를 열고 AAD 사용자로 이동한 다음 사용자가 존재 하는지 확인 하 **SAPCONT_ADMIN@SAPCONTOSO.local** 고 AAD 사용자 계정을 확인 하는 것입니다 **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** .
         * 파일 공유를 포함 하는 저장소 계정을 포함 하는 리소스 그룹에 대해이 Azure Active Directory 사용자 계정에 **참가자 RBAC** 역할을 부여 합니다.  이 예제에서는 사용자에 **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** 게 해당 리소스 그룹에 대 한 **참가자 역할이** 부여 됩니다. 
         * 위의 지정 된 사용 권한이 있는 Active Directory 도메인 사용자 계정을 사용 하 여 Windows 서버에 로그온 하는 동안 스크립트를 실행 해야 합니다 .이 예제에서는 계정이 **SAPCONT_ADMIN@SAPCONTOSO.local** 사용 됩니다.
         >[!IMPORTANT]
         > PowerShell 스크립트 명령 **커넥트** 를 실행 하는 경우 Windows 서버에 로그온 하는 데 사용 되는 Active Directory 도메인 사용자 계정에 해당 하는 Azure Active Directory 사용자 계정을 입력 하는 것이 좋습니다 .이 예제에서는 사용자 계정입니다.**SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com**
         >
         이 예제 시나리오에서는 Active Directory 관리자가 **SAPCONT_ADMIN@SAPCONTOSO.local** **PS 명령 커넥트-AzAccount** Connect as user를 사용 하 여 Windows 서버에 로그온 합니다 **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** .  Active Directory 관리자와 Azure 관리자는이 작업을 함께 수행 하는 것이 가장 좋습니다.
         ![powershell-스크립트-1 ](media/virtual-machines-shared-sap-high-availability-guide/ps-script-1.png) 로컬 AD 계정을 만드는 powershell 스크립트의 스크린샷

         ![smb 구성-스크린샷](media/virtual-machines-shared-sap-high-availability-guide/smb-config-1.png)PowerShell 스크립트를 성공적으로 실행 한 후 스크린샷을 Azure Portal 합니다. 

         다음은 "구성 됨"으로 표시 되어야 합니다.  
         Storage-> 파일 공유 "Active Directory: 구성 됨"
     1. SAP 사용자 **\<sid> adm**, **SAPService \<SID>** 및 **SAP_ \<SAPSID> _GlobalAdmin** 그룹을 Azure Files Premium SMB 파일 공유 역할 **Storage 파일 데이터 smb 공유의 승격 된 참가자** Azure Portal 
     1. 설치 후 **sapmnt 파일 공유** 의 ACL을 확인 하 고 **도메인 \ CLUSTER_NAME $** 계정, **도메인 \\ \<sid> adm**, **DOMAIN\SAPService \<SID>** 및 **그룹 SAP_ \<SID> _GlobalAdmin** 를 추가 합니다. 이러한 계정 및 그룹에는 **sapmnt 디렉터리에 대 한 모든** 권한이 있어야 합니다.

         > [!IMPORTANT]
         > 이 단계는 SAPInst 설치 전에 완료해야 합니다. 또는 SAPInst가 파일 공유에 디렉터리와 파일을 만든 후 ACL을 변경하기 어렵거나 불가능합니다.
         >
         ![ACL 속성](media/virtual-machines-shared-sap-high-availability-guide/smb-share-properties-1.png)Windows 할당된 사용자 권한의 탐색기 스크린샷

         다음 스크린샷에서는 로컬 AD에 클러스터 이름을 추가하는 로컬 AD Windows Server 스크린샷에 클러스터 이름을 추가하는 개체 유형 -> 컴퓨터 ![ Windows Server 스크린샷을 선택하여 컴퓨터 컴퓨터 계정을 추가하는 방법을 보여 주며, ](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-2.png)
         
         DOMAIN\CLUSTER_NAME$은 "개체 유형"에서 "컴퓨터"를 선택하여 찾을 수 있습니다.  
         ![AD 컴퓨터 계정 추가 스크린샷 - ](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-3.png) 2단계 AD 컴퓨터 계정 추가 스크린샷 - 2단계 ![ AD 컴퓨터 계정 추가 스크린샷 - 3단계 ](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-4.png) AD 컴퓨터 계정 추가 스크린샷 - 3단계 ![ 컴퓨터 계정 액세스 속성 ](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-5.png) 스크린샷 컴퓨터 계정 액세스 속성 스크린샷.

     8. 필요한 경우 Azure Files 위해 만든 컴퓨터 계정을 계정 만료가 없는 Active Directory 컨테이너로 이동합니다.  컴퓨터 계정의 이름은 스토리지 계정의 약식 이름이 됩니다. 

     
     > [!IMPORTANT]
     > SMB 공유에 대한 Windows ACL을 초기화하려면 공유를 드라이브 문자에 한 번 탑재해야 합니다.
     >
     스토리지 키는 암호이며, 사용자는 다음과 같이 **\\ \<SMB share name> Azure입니다.** 일회성 순 사용 탑재 Windows net use ![ ](media/virtual-machines-shared-sap-high-availability-guide/one-time-net-use-mount-1.png) SMB 공유의 일회성 탑재 스크린샷.

 4. 기본 관리자는 아래 작업을 완료해야 합니다.
     1. [ASCS/ERS 노드에 Windows 클러스터 설치 및 클라우드 감시 추가](sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
     2. 첫 번째 클러스터 노드 설치에서 Azure Files SMB 스토리지 계정 이름을 요청합니다.  FQDN <storage_account_name>.file.core.windows.net 입력합니다.  SAPInst가 >13자를 허용하지 않으면 SWPM 버전이 너무 오래되었습니다.
     3. [ASCS/SCS 인스턴스의 SAP 프로필 수정](sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)
     4. [WSFC에서 SAP 역할에 대한 프로브 포트 업데이트 \<SID>](sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)
     5. 두 번째 ASCS/ERS 노드에 대한 SWPM 설치를 계속합니다. SWPM에는 프로필 디렉터리 경로만 필요합니다.  프로필 디렉터리에 대한 전체 UNC 경로를 입력합니다.
     6. DB 및 PAS/AAS 설치에 대한 UNC 프로필 경로를 입력합니다.
     7. PAS 설치에서 전송 호스트 이름을 요청합니다. 전송 디렉터리에 대한 별도의 스토리지 계정 이름의 FQDN을 제공합니다.
     8. SID 및 trans 디렉터리에서 ACL을 확인합니다.

## <a name="disaster-recovery-setup"></a>재해 복구 설정
재해 복구 시나리오 또는 지역 간 복제 시나리오는 Azure Files Premium SMB에서 지원됩니다. Azure Files Premium SMB 디렉터리의 모든 데이터는 이 링크를 사용하여 DR 지역 스토리지 계정에 지속적으로 동기화될 수 있습니다. 재해 복구 이벤트 및 ASCS 인스턴스를 DR 지역으로 장애 조치한 후 SAPGLOBALHOST 프로필 매개 변수를 지점으로 변경하여 DR 지역에서 SMB를 Azure Files. 스토리지 계정을 Active Directory에 조인하고 SAP 사용자 및 그룹에 대한 RBAC 역할을 할당하려면 DR 스토리지 계정에서 동일한 준비 단계를 수행해야 합니다.

## <a name="troubleshooting"></a>문제 해결
3.c 단계에서 다운로드한 PowerShell 스크립트에는 구성의 유효성을 검사하기 위한 몇 가지 기본 검사를 수행하는 디버그 스크립트가 포함되어 있습니다.
```powershell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
![Powershell-script-output](media/virtual-machines-shared-sap-high-availability-guide/smb-share-validation-2.png)디버그 스크립트 출력의 PowerShell 스크린샷.

![Powershell-script-technical-info](media/virtual-machines-shared-sap-high-availability-guide/smb-share-validation-1.png)다음 화면에서는 성공적인 도메인 가입의 유효성을 검사하기 위한 기술 정보를 보여 있습니다.
## <a name="useful-links--resources"></a>리소스에 & 유용한 링크

* SAP Note [2273806][2273806] 스토리지 또는 파일 시스템 관련 솔루션에 대한 SAP 지원 
* [Azure에서 SAP ASCS/SCS 인스턴스의 Windows 장애 조치(Failover) 클러스터 및 파일 공유에 SAP NetWeaver 고가용성 설치](./sap-high-availability-installation-wsfc-file-share.md) 
* [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성 아키텍처 및 시나리오](./sap-high-availability-architecture-scenarios.md)
* [ASCS 클러스터 구성에서 프로브 포트 추가](sap-high-availability-installation-wsfc-file-share.md)
* [장애 조치(failover) 클러스터에 (A)SCS 인스턴스 설치](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)

[16083]:https://launchpad.support.sap.com/#/notes/16083
[2273806]:https://launchpad.support.sap.com/#/notes/2273806