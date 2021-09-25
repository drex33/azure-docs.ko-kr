---
title: Azure VMware Solution에 대한 플랫폼 업데이트
description: Azure VMware Solution에 대한 플랫폼 업데이트에 대해 알아봅니다.
ms.topic: reference
ms.date: 09/21/2021
ms.openlocfilehash: 3159323fa567bc622c9627ded4e8f70bfac20b4f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636119"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Azure VMware Solution에 대한 플랫폼 업데이트

Azure VMware Solution은 2021년 3월부터 중요 업데이트를 적용합니다. 유지 관리 타임라인을 포함하는 Azure Service Health를 통해 알림을 받게 됩니다. 자세한 내용은 [호스트 유지 관리 및 수명 주기 관리](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management)를 참조하세요.

## <a name="september-21-2021"></a>2021년 9월 21일
VMware 보안 권고 [VMSA-2021-0020에](https://www.vmware.com/security/advisories/VMSA-2021-0020.html)따라 VMware vCenter 서버의 여러 취약성이 VMware에 보고되었습니다.
 
취약성을 해결하려면(CVE-2021-21980, CVE-2021-21991, CVE-2021-21992, CVE-2021-21993, CVE-2021-22005, CVE-2021-22006, CVE-2021-22007, CVE-2021-22008, CVE-2021-22009, CVE-2021-22010, CVE-2021-22011, CVE-2021-22012,CVE-2021-22013, CVE-2021-22014, CVE-2021-22015, CVE-2021-22016, CVE-20211 VMware 보안 권고 VMSA-2021-0020에서 보고된-22017, CVE-2021-22018, [CVE-2021-22019, CVE-2021-22020)](https://www.vmware.com/security/advisories/VMSA-2021-0020.html)vCenter Server 모든 Azure VMware Solution 6.7 업데이트 3o로 업데이트되었습니다. 프라이빗 클라우드. 모든 새 Azure VMware Solution 프라이빗 클라우드는 vCenter 서버 버전 6.7 업데이트 3o와 함께 배포됩니다.
 
자세한 내용은 [VMware vCenter Server 6.7 업데이트 3o 릴리스 정보를 참조하세요.](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3o-release-notes.html)
 
추가적인 조치가 필요하지 않습니다.

## <a name="september-10-2021"></a>2021년 9월 10일

이제 모든 새 Azure VMware Solution 프라이빗 클라우드가 ESXi 버전 ESXi670-202103001(빌드 번호: 17700523)으로 배포됩니다. 기존 프라이빗 클라우드의 ESXi 호스트가 이 버전에 패치되었습니다.

이 ESXi 버전에 대한 자세한 내용은 [VMware ESXi 6.7 패치 릴리스 ESXi670-202103001을 참조하세요.](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202103001.html)




## <a name="july-23-2021"></a>2021년 7월 23일

완전히 새로운 Azure VMware Solution 프라이빗 클라우드가 NSX-T 버전 [!INCLUDE [nsxt-version](includes/nsxt-version.md)]와 함께 배포됩니다. 기존 프라이빗 클라우드의 NSX-T 버전은 2021년 9월까지 NSX-T [!INCLUDE [nsxt-version](includes/nsxt-version.md)] 릴리스로 업그레이드됩니다.
 
계획된 유지 관리 날짜 및 시간이 포함된 메일을 받게 됩니다. 업그레이드를 다시 예약할 수 있습니다. 또한 메일은 업그레이드된 구성 요소에 대한 세부 정보, 워크로드에 대한 영향, 프라이빗 클라우드 액세스 및 기타 Azure 서비스를 제공합니다. 

이 NSX-T 버전에 대한 자세한 내용은 [VMware NSX-T 데이터 센터 [!INCLUDE [nsxt-version](includes/nsxt-version.md)] 릴리스 정보](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/rn/VMware-NSX-T-Data-Center-312-Release-Notes.html)를 참조하세요.




## <a name="may-25-2021"></a>2021년 5월 25일
VMware 보안 권고 [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html)에 따라 VMware ESXi 및 vSphere Client(HTML5)의 여러 취약성이 VMware에 보고되었습니다. 

VMware 보안 권고 [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html)에 보고된 취약성([CVE-2021-21985](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21985) 및 [CVE-2021-21986](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21986))을 해결하기 위해 vCenter Server가 모든 Azure VMware Solution 프라이빗 클라우드에서 업데이트되었습니다.

추가적인 조치가 필요하지 않습니다.

## <a name="may-21-2021"></a>2021년 5월 21일
 
Azure VMware Solution 서비스는 프라이빗 클라우드의 vCenter Server에 중요 업데이트를 적용하기 위해 2021년 5월 23일을 통해 유지 관리 작업을 수행합니다.  프라이빗 클라우드를 위한 유지 관리 타임라인을 포함하는 Azure Service Health를 통해 알림을 받게 됩니다.
 
이 시간 동안에는 VMware vCenter를 사용할 수 없으며 VM을 관리(중지, 시작, 만들기 또는 삭제)할 수 없습니다. 이 시간 동안에는 프라이빗 클라우드 스케일 업, 새 네트워크 만들기 등의 다른 작업을 프라이빗 클라우드에서 계획하지 않는 것이 좋습니다.
 
프라이빗 클라우드에서 실행되는 워크로드에는 영향을 주지 않습니다.


## <a name="april-26-2021"></a>2021년 4월 26일
이제 모든 새 Azure VMware Solution 프라이빗 클라우드가 VMware vCenter 버전 6.7 U3l 및 NSX 버전 2.5.2를 사용하여 배포됩니다. 고객 VM 연결에 영향을 주는 NSX 3.1.1의 식별된 문제로 인해 새 프라이빗 클라우드에 NSX-T 3.1.1을 사용하지 않습니다. 

VMware 권장 완화는 현재 Azure VMware Solution에서 NSX-T 3.1.1을 실행하는 모든 기존 프라이빗 클라우드에 적용되었습니다. 해결 방법은 고객 VM 연결에 영향이 없는 것으로 확인되었습니다.

## <a name="march-24-2021"></a>2021년 3월 24일
모든 새 Azure VMware Solution 프라이빗 클라우드는 VMware vCenter 버전 6.7 U3l 및 NSX-T 버전 3.1.1을 사용하여 배포됩니다. 기존의 모든 프라이빗 클라우드는 **2021년 6 월까지** 위에 언급된 릴리스로 업데이트 및 업그레이드됩니다.

계획된 유지 관리 날짜 및 시간이 포함된 메일을 받게 됩니다. 업그레이드를 다시 예약할 수 있습니다. 또한 메일은 업그레이드된 구성 요소에 대한 세부 정보, 워크로드에 대한 영향, 프라이빗 클라우드 액세스 및 기타 Azure 서비스를 제공합니다.  업그레이드하기 1시간 전에 알림이 표시되고 완료되면 다시 알림이 수신됩니다.

## <a name="march-15-2021"></a>2021년 3월 15일 

- Azure VMware Solution 서비스는 **2021년 3월 19일까지** 유지 관리 작업을 수행하여 프라이빗 클라우드의 vCenter Server를 vCenter Server 6.7 Update 3l 버전으로 업데이트합니다.

- 이 시간 동안에는 VMware vCenter를 사용할 수 없으므로 VM(중지, 시작, 만들기, 삭제) 또는 프라이빗 클라우드 크기 조정(서버 및 클러스터 추가/제거)을 관리할 수 없습니다. 그러나 VMware HA(고가용성)는 기존 VM을 보호하기 위해 계속 작동합니다. 
 
이 vCenter 버전에 대한 자세한 내용은 [VMware vCenter Server 6.7 Update 3l 릴리스 정보](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)를 참조하세요.

## <a name="march-4-2021"></a>2021년 3월 4일

- Azure VMware Solution은 [VMware ESXi 6.7, Patch Release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html)를 **2021년 3월 15일까지** 기본 프라이빗 환경에 적용합니다.

- [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)에 따라 vSphere 스택에 대한 문서화된 해결 방법 또한 **2021년 3월 15일까지** 적용됩니다.

>[!NOTE]
>이것은 무중단 방식으로 진행되며 Azure VMware 서비스 또는 워크로드에 영향을 주지 않습니다. 유지 관리 중에는 _DVPort의 네트워크 연결 끊김_ 및 _DVPort의 손실된 업링크 중복성_ 과 같은 다양한 VMware 경고가 vCenter에 표시되고 유지 관리가 진행되면서 자동으로 제거됩니다.

## <a name="post-update"></a>사후 업데이트
완료되면 새 버전의 VMware 구성 요소가 표시됩니다. 문제가 있거나 질문이 있는 경우 지원 티켓을 열어 지원 팀에 문의하세요.
