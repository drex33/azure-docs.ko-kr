---
title: 포함 파일
description: 포함 파일
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 07/08/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: f345ab52b2f663116602d2a3c782ad050ea4c207
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019277"
---
**구성 및 프로세스 서버 요구 사항**


## <a name="hardware-requirements"></a>하드웨어 요구 사항

**구성 요소** | **요구 사항** 
--- | ---
CPU 코어 | 8 
RAM | 16GB
디스크 수 | 3, OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브(장애 복구용) 포함 
사용 가능한 디스크 공간(프로세스 서버 캐시) | 600GB
사용 가능한 디스크 공간(보존 디스크) | 600GB
 | 

## <a name="software-requirements"></a>소프트웨어 요구 사항

**구성 요소** | **요구 사항** 
--- | ---
운영 체제 | Windows Server 2012 R2 <br> Windows Server 2016
운영 체제 로케일 | 영어(en-*)
Windows Server 역할 | 다음 역할을 사용하지 않도록 설정함: <br> - Active Directory Domain Services <br>- 인터넷 정보 서비스 <br> - Hyper-V 
그룹 정책 | 다음 그룹 정책을 사용하지 않도록 설정함: <br> - 명령 프롬프트에 대한 액세스 방지 <br> - 레지스트리 편집 도구에 대한 액세스 방지 <br> - 파일 첨부를 위한 트러스트 논리 <br> - 스크립트 실행 켜기 <br> [자세한 정보](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - 기존의 기본 웹 사이트 없음 <br> - 포트 443에서 수신 대기하는 기존의 웹 사이트/애플리케이션 없음 <br>- [익명 인증](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) 사용 <br> - [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 설정 사용 
FIPS(Federal Information Processing Standards) | FIPS 모드 사용 안 함
|

## <a name="network-requirements"></a>네트워크 요구 사항

**구성 요소** | **요구 사항** 
--- | --- 
IP 주소 유형 | 정적 
포트 | 443(컨트롤 채널 오케스트레이션)<br>9443(데이터 전송) 
NIC 유형 | VMXNET3(구성 서버가 VMware VM인 경우)
 |
**인터넷 액세스**(직접 또는 프록시를 통해 서버에서 다음 URL에 액세스해야 합니다.):|
\*.backup.windowsazure.com | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.blob.core.windows.net | 복제된 데이터를 저장하는 스토리지 계정에 액세스하는 데 사용됩니다. 캐시 스토리지 계정의 특정 URL을 제공할 수 있습니다.
\*.hypervrecoverymanager.windowsazure.com | 복제 관리 작업 및 조정에 사용됩니다.
https:\//login.microsoftonline.com | 복제 관리 작업 및 조정에 사용됩니다. 
time.nist.gov | 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인하는 데 사용됩니다.
time.windows.com | 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인하는 데 사용됩니다.
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> *.services.visualstudio.com(선택 사항) </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF 설정에는 이러한 추가 URL에 액세스가 필요합니다. Azure Active Directory에서 액세스 제어 및 ID 관리에 사용됩니다.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | MySQL 다운로드를 완료하려면 </br> 일부 지역에서는 다운로드가 CDN URL로 리디렉션될 수 있습니다. 필요한 경우 CDN URL도 승인해야 합니다.
|

> [!NOTE]
> Site Recovery 자격 증명 모음에 [프라이빗 링크 연결](../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md)이 있는 경우 구성 서버에 대한 추가 인터넷 액세스가 필요하지 않습니다. 이에 대한 예외는 OVA 템플릿을 사용하여 CS 머신을 설정하는 동안 프라이빗 링크 액세스(https://management.azure.com , https://www.live.com 및 https://www.microsoft.com )를 통해 다음 URL에 액세스해야 한다는 것입니다. 이러한 URL에 대한 액세스를 허용하지 않으려면 통합 설치 관리자를 사용하여 CS를 설정하세요.

> [!NOTE]
> VMware 및 물리적 컴퓨터를 보호 하기 위해 개인 끝점을 설정 하는 동안 구성 서버에 MySQL을 수동으로 설치 해야 합니다. 수동 설치를 수행 하려면 [여기](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server#configure-settings) 의 단계를 따르세요. 

## <a name="required-software"></a>필수 소프트웨어

**구성 요소** | **요구 사항** 
--- | ---
VMware vSphere PowerCLI | 버전 9.14 이상에는 필요하지 않음
MYSQL | MySQL을 설치해야 합니다. 수동으로 설치할 수도 있고 Site Recovery를 통해 설치할 수도 있습니다. (자세한 내용은 [설정 구성](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings)을 참조하세요.)
|

> [!NOTE]
> 구성 서버에서 MySql을 업그레이드 하는 것은 지원 되지 않습니다.

## <a name="sizing-and-capacity-requirements"></a>크기 및 용량 요구 사항

다음 표에서 구성 서버에 대한 용량 요구 사항을 보여 줍니다. 여러 VMware VM을 복제하는 경우 [용량 계획 고려 사항](../articles/site-recovery/site-recovery-plan-capacity-vmware.md)을 검토하고, [Azure Site Recovery Deployment Planner 도구](../articles/site-recovery/site-recovery-deployment-planner.md)를 실행합니다.


**CPU** | **메모리** | **캐시 디스크** | **데이터 변경률** | **복제된 컴퓨터**
--- | --- | --- | --- | ---
vCPU 8대<br/><br/> 2개 소켓 * 4코어 \@ 2.5GHz | 16GB | 300GB | 500GB 이하 | 컴퓨터 100대 미만
vCPU 12대<br/><br/> 2개 소켓 * 6코어 \@ 2.5GHz | 18GB | 600GB | 500GB-1TB | 컴퓨터 100-150대
vCPU 16대<br/><br/> 2개 소켓 * 8코어 \@ 2.5GHz | 32GB | 1TB | 1-2TB | 컴퓨터 150-200대
|