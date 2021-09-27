---
title: SMB(Azure Files)를 Windows SAP NW용 Azure VM HA | Microsoft Docs
description: SAP 애플리케이션용 SMB(Azure Files)를 Windows Azure VM의 SAP NetWeaver 고가용성
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
ms.openlocfilehash: d1e01f51d4d83254031407a125c3578fb3feb718
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634117"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-files-premium-smb-for-sap-applications"></a>SAP 애플리케이션용 Azure Files Premium SMB를 Windows Azure VM의 SAP NetWeaver 고가용성

## <a name="introduction"></a>소개
Azure Files Premium SMB는 이제 Microsoft 및 SAP에서 완전히 지원됩니다. **SWPM 1.0 SP32** 및 **SWPM 2.0 SP09** 이상은 Azure Files Premium SMB 스토리지를 지원합니다.  Azure Files Premium SMB 공유 크기를 조정하기 위한 특별한 요구 사항이 있습니다. 이 설명서에는 Azure Files Premium SMB에 워크로드를 배포하는 방법, Azure Files Premium SMB 크기를 적절하게 지정하는 방법 및 Azure Files Premium SMB에 대한 최소 설치 요구 사항에 대한 특정 권장 사항이 포함되어 있습니다.

고가용성 SAP 솔루션은 **sapmnt**, **trans** 및 인터페이스 디렉터리를 호스팅하기 위해 고가용성 파일 **공유가** 필요합니다. Azure Files Premium SMB는 Windows 환경에서 SAP용 공유 파일 시스템용 간단한 Azure PaaS 솔루션입니다. Azure Files Premium SMB는 가용성 집합 및 가용성 영역 함께 사용할 수 있습니다. Azure Files Premium SMB는 다른 지역에 대한 재해 복구 시나리오에도 사용할 수 있습니다.  
 
## <a name="sizing--distribution-of-azure-files-premium-smb-for-sap-systems"></a>SAP 시스템용 Azure Files Premium SMB의 크기 조정 & 배포

Azure Files Premium SMB 배포를 계획할 때 다음 사항을 평가해야 합니다.
* 파일 공유 이름 **sapmnt는** 스토리지 계정당 한 번씩 만들 수 있습니다.  **-/sapmnt/ 및 \<SID1\> /sapmnt/와** **\<SID2\>** 같은 동일한 **/sapmnt** 공유에 디렉터리로 추가 SID를 만들 수 있습니다. 
* 적절한 크기, IOPS 및 처리량을 선택합니다.  공유에 대해 제안된 크기는 SID당 256GB입니다.  공유의 최대 크기는 5120GB입니다.
* Azure Files Premium SMB는 스토리지 계정당 1~200만 개 이상의 파일이 있는 매우 큰 **sapmnt** 공유에 대해 최적으로 수행되지 않을 수 있습니다.수백만 개의 작업 로그 파일을 만드는 수백만 개의 일괄 처리 작업이 있는 고객은 [SAP Note 16083에][16083] 따라 정기적으로 다시 구성해야 합니다. 필요한 경우 이전 작업 로그를 다른 Azure Files Premium SMB로 이동/보관할 수 있습니다. **sapmnt가** 매우 클 것으로 예상되는 경우 대체 옵션(예: Azure ANF)을 고려해야 합니다.
* 프라이빗 네트워크 엔드포인트를 사용하는 것이 좋습니다.
* 단일 스토리지 계정 및 해당 파일 공유에 너무 많은 SID를 통합하지 마십시오.
* 일반적인 지침으로 2~4개의 비-prod SID를 함께 통합할 수 있습니다.
* 전체 개발, QAS + 프로덕션 환경을 하나의 스토리지 계정 및/또는 파일 공유에 통합하지 마십시오.공유가 실패하면 전체 SAP 환경의 가동 중지 시간이 발생합니다.
* 매우 작은 시스템을 제외하고 동일한 스토리지 계정에 **sapmnt** 및 **전송 디렉터리를** 통합하는 것은 바람직하지 않습니다. SAP PAS 인스턴스를 설치하는 동안 SAPInst는 전송 호스트 이름을 요청합니다.  다른 스토리지 계정의 FQDN은>.file.core.windows.net <storage_account 입력해야 합니다.
* 인터페이스에 사용되는 파일 시스템을 **/sapmnt/ \<SID>** 같은 스토리지 계정에 통합하지 마십시오. 
* SAP 사용자/그룹을 'sapmnt' 공유에 추가해야 하며 Azure Portal Storage **파일 데이터 SMB 공유 관리자 권한자** 에서 이 사용 권한을 설정해야 합니다.

**전송,** **인터페이스** 및 **sapmnt를** 별도의 스토리지 계정으로 분리하는 중요한 이유가 있습니다.  이러한 구성 요소를 별도의 스토리지 계정에 배포하면 처리량, 복원력이 향상되고 성능 분석이 간소화됩니다.  많은 SID 및 기타 파일 시스템이 단일 Azure Files Storage 계정에 통합되고 처리량 제한에 도달하여 스토리지 계정 성능이 저하되는 경우 문제를 일으키는 SID 또는 애플리케이션을 식별하기가 매우 어렵습니다. 

## <a name="planning"></a>계획 
> [!IMPORTANT]
> Active Directory 통합을 Azure Files Premium SMB에 SAP 고가용성 시스템을 설치하려면 팀 간 협업이 필요합니다.  기본 팀, Active Directory 팀 및 Azure 팀이 함께 작업하여 다음 작업을 완료하는 것이 좋습니다. 
>
* Azure 팀 – Storage 계정, 스크립트 실행 및 AD 디렉터리 동기화의 설정 및 구성
* Active Directory 팀 – 사용자 계정 및 그룹 만들기
* 기본 팀 – SWPM을 실행하고 ACL을 설정합니다(필요한 경우).

Active Directory 통합을 Azure Files Premium SMB에 SAP NetWeaver 고가용성 시스템을 설치하기 위한 필수 조건입니다.

* SAP 서버는 Active Directory 도메인 조인해야 합니다.
* SAP 서버를 포함하는 Active Directory 도메인 Azure AD connect를 사용하여 Azure Active Directory 복제해야 합니다.
* Express 경로가 온-프레미스 도메인 컨트롤러에 연락하지 않도록 Azure 지형에 하나 이상의 Active Directory 도메인 컨트롤러가 있는 것이 좋습니다.
* Azure 지원 팀은 [Active Directory 통합을](/azure/storage/files/storage-files-identity-auth-active-directory-enable#videos) Azure Files SMB 설명서를 검토해야 합니다. *비디오는 단순화를 위해 수정(DNS) 및 건너뛴(DFS-N) 추가 구성 옵션을 보여 줍니다.* 그럼에도 불구하고 유효한 구성 옵션입니다. 
* Azure Files PowerShell 스크립트를 실행하는 사용자에게는 Active Directory에서 개체를 만들 수 있는 권한이 있어야 합니다.
* **SWPM 버전 1.0 SP32 및 SWPM 2.0 SP09 이상은 필수입니다. SAPInst 패치는 749.0.91 이상이어야 합니다.**
* PowerShell의 최신 릴리스는 스크립트가 실행되는 Windows Server에 설치해야 합니다. 

## <a name="installation-sequence"></a>설치 순서
 1. Active Directory 관리자는 **로컬 관리자** 권한이 있는 도메인 사용자 3명과 **로컬 Windows AD의** 전역 그룹 1개를 미리 만들어야 합니다. **SAPCONT_ADMIN@SAPCONTOSO.local** 에는 도메인 관리자 권한이 있으며 SAP 시스템 사용자 및 **SAP_ \<SAPSID> _GlobalAdmin** 그룹으로 **SAPInst,** **\<sid> adm** 및 **SAPService를 \<SID>** 실행하는 데 사용됩니다. SAP 설치 가이드에는 이러한 계정에 필요한 특정 세부 정보가 포함되어 있습니다.  **SAP 사용자 계정은 도메인 관리자가 아니어야 합니다.** 일반적으로 **\<sid> ADM을 사용하여 SAPInst 를 실행하지 않는** 것이 좋습니다.
 2. Active Directory 관리자 또는 Azure 관리자는 **Azure AD 커넥트 Synchronization Service Manager** 확인해야 합니다. 기본적으로 **Azure Active Directory** 복제하는 데 약 30분이 걸립니다. 
 3. Azure 관리자는 다음 작업을 완료해야 합니다.
     1. Premium ZRS 또는 **LRS를** **사용하여 Storage** 계정을 만듭니다. 영역 배포가 있는 고객은 ZRS를 선택해야 합니다. 여기서는 **표준** 또는 Premium **계정** 설정 중에서 선택해야 합니다. ![ Azure Portal 스토리지 계정 만들기 스크린샷 - 1단계 Azure Portal 스토리지 계정 만들기 ](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-1.png) 스크린샷 - 1단계
         > [!IMPORTANT]
         > 생산성을 위해 권장 사항은 Premium 계정을 사용하는 **것입니다.** 비 생산성의 경우 **표준 계정을** 사용하는 것으로 충분합니다. 
         >
         ![스토리지 계정 만들기에 대한 Azure Portal 스크린샷 - 2단계](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-2.png)스토리지 계정 만들기에 대한 Azure Portal 스크린샷 - 2단계
         
         이 화면에서 기본 설정은 정상이어야 합니다.
        
         ![Azure Portal 스토리지 계정 만들기 스크린샷 - 3단계](media/virtual-machines-shared-sap-high-availability-guide/create-sa-4.png)Azure Portal 스토리지 계정 만들기 스크린샷 - 3단계 
         
         이 단계에서는 프라이빗 엔드포인트를 사용하기로 결정합니다.
     1. 스토리지 계정에 대해 **프라이빗 네트워크 엔드포인트를 선택합니다.**
     필요한 경우<storage_account_name **>.file.core.windows.net** 대한 Windows DNS에 DNS A-레코드를 추가합니다(새 DNS 영역에 있어야 할 수 있음).  이 항목은 DNS 관리자에게 설명합니다.  새 영역은 조직 외부에서 업데이트해서는 안 됩니다.  
         ![프라이빗 엔드포인트 정의에 대한 pivate-endpoint-creation ](media/virtual-machines-shared-sap-high-availability-guide/create-sa-3.png) Azure Portal 스크린샷
         ![프라이빗 엔드포인트 DNS ](media/virtual-machines-shared-sap-high-availability-guide/pe-dns-1.png) 정의에 대한 private-endpoint-dns-1 DNS 서버 스크린샷    
     1. 적절한 크기의 **sapmnt** 파일 공유를 만듭니다.  제안된 크기는 650 IOPS, 75MB/초 Egress 및 50MB/초 수신을 제공하는 256GB입니다.
         ![create-storage-account-5 ](media/virtual-machines-shared-sap-high-availability-guide/create-sa-5.png) Azure Portal SMB 공유 정의 스크린샷 
      
     1. [Azure Files GitHub](/azure/storage/files/storage-files-identity-ad-ds-enable#download-azfileshybrid-module) 콘텐츠를 다운로드하고 [스크립트를](/azure/storage/files/storage-files-identity-ad-ds-enable#run-join-azstorageaccountforauth)실행합니다.   
     이 스크립트는 Active Directory에 컴퓨터 계정 또는 서비스 계정을 만듭니다.  스크립트를 실행하는 사용자에게는 다음 속성이 있어야 합니다. 
         * 스크립트를 실행하는 사용자는 SAP 서버를 포함하는 Active Directory 도메인 개체를 만들 수 있는 권한이 있어야 합니다. 일반적으로 도메인 관리자 계정은 과 같이 사용됩니다. **SAPCONT_ADMIN@SAPCONTOSO.local** 
         * 스크립트를 실행하기 전에 이 Active Directory 도메인 사용자 계정이 AAD(Azure Active Directory)와 동기화되어 있는지 확인합니다.  예를 들어 Azure Portal 열고 AAD 사용자로 이동하여 사용자가 있는지 확인하고 **SAPCONT_ADMIN@SAPCONTOSO.local** AAD 사용자 계정을 확인하는 **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** 것입니다.
         * 파일 공유를 보유하는 스토리지 계정을 포함하는 리소스 그룹에 대해 이 Azure Active Directory 사용자 계정에 **기여자 RBAC** 역할을 부여합니다.  이 예제에서는 사용자에게 **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** 해당 리소스 그룹에 대한 **기여자 역할이** 부여됩니다. 
         * 위에서 지정한 사용 권한이 있는 Active Directory 도메인 사용자 계정을 사용하여 Windows 서버에 로그온하는 동안 스크립트를 실행해야 합니다. 이 예제에서는 계정이 **SAPCONT_ADMIN@SAPCONTOSO.local** 사용됩니다.
         >[!IMPORTANT]
         > PowerShell 스크립트 명령 **커넥트-AzAccount** 를 실행하는 경우 Windows Server에 로그온하는 데 사용되는 Active Directory 도메인 사용자 계정에 해당하고 매핑되는 Azure Active Directory 사용자 계정을 입력하는 것이 좋습니다. 이 예제에서는 사용자 계정입니다.**SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com**
         >
         이 예제 시나리오에서 Active Directory 관리자는 **SAPCONT_ADMIN@SAPCONTOSO.local** **PS 명령 커넥트-AzAccount** connect를 사용자 으로 사용할 때 및 로 Windows 서버에 로그온합니다. **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com**  이상적으로는 Active Directory 관리자와 Azure 관리자가 이 작업을 함께 수행해야 합니다.
         ![powershell-script-1 ](media/virtual-machines-shared-sap-high-availability-guide/ps-script-1.png) 로컬 AD 계정을 만드는 PowerShell 스크립트의 스크린샷

         ![smb-configured-screenshot](media/virtual-machines-shared-sap-high-availability-guide/smb-config-1.png)PowerShell 스크립트 실행이 성공한 후 Azure Portal 스크린샷 

         다음이 "구성됨"으로 표시되어야 합니다.  
         Storage -> 파일 공유 "Active Directory: 구성됨"
     1. Azure Portal 역할 Storage **파일 데이터 SMB 공유 관리자 권한 기여자를** 사용하여 SAP 사용자 **\<sid> adm,** **SAPService \<SID>** 및 SAP_ _GlobalAdmin 그룹을 Azure Files Premium SMB 파일 공유에 할당합니다. **\<SAPSID>** 
     1. 설치 후 **sapmnt 파일 공유에서** ACL을 확인하고 **DOMAIN\CLUSTER_NAME$** 계정, **DOMAIN \\ \<sid> adm**, **DOMAIN\SAPService \<SID>** 및 **Group SAP_ \<SID> _GlobalAdmin** 추가합니다. 이러한 계정 및 **그룹에는 sapmnt 디렉터리를 완전히 제어할 수 있어야 합니다.**

         > [!IMPORTANT]
         > 이 단계는 SAPInst 설치 전에 완료해야 합니다. 또는 SAPInst가 파일 공유에 디렉터리와 파일을 만든 후 ACL을 변경하기 어렵거나 불가능합니다.
         >
         ![ACL 속성](media/virtual-machines-shared-sap-high-availability-guide/smb-share-properties-1.png)Windows 할당된 사용자 권한의 탐색기 스크린샷

         다음 스크린샷에서는 클러스터 이름을 로컬 AD에 추가하는 클러스터 이름을 로컬 AD Windows Server 스크린샷에 추가하는 개체 유형 -> 컴퓨터 ![ Windows Server 스크린샷을 선택하여 컴퓨터 컴퓨터 계정을 추가하는 방법을 보여 주며, ](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-2.png)
         
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
