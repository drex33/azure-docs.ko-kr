---
title: Azure Migrate 복제 어플라이언스
description: 에이전트 기반 VMware 마이그레이션을 위한 Azure Migrate 복제 어플라이언스에 대해 알아봅니다.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 4a06267aae473ecc1b42812f7ed07405faafd7aa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607473"
---
# <a name="replication-appliance"></a>복제 어플라이언스

이 문서에서는 에이전트 기반 마이그레이션을 사용하여 VMware VM, 물리적 머신 및 프라이빗/퍼블릭 클라우드 VM을 Azure로 마이그레이션할 때 [Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool) 도구에서 사용하는 복제 어플라이언스에 대해 설명합니다. 


## <a name="overview"></a>개요

복제 어플라이언스는 VMware VM 또는 물리적 서버의 에이전트 기반 마이그레이션을 설정할 때 배포됩니다. 복제 어플라이언스는 단일 온-프레미스 머신(VMware VM 또는 물리적 서버)으로 배포됩니다. 단일 온-프레미스 머신은 다음과 같은 항목을 실행합니다.

- **복제 어플라이언스**: 복제 어플라이언스는 Azure로 복제하는 온-프레미스 VMware VM 및 물리적 서버에 대해 통신을 조율하고 데이터 복제를 관리합니다.
- **프로세스 서버**: 복제 어플라이언스에 기본적으로 설치되는 프로세스 서버는 다음과 같은 작업을 수행합니다.
    - **복제 게이트웨이**: 복제 게이트웨이로 기능합니다. 복제를 사용하도록 설정된 머신으로부터 복제 데이터를 수신합니다. 캐시, 압축 및 암호화로 복제 데이터를 최적화하여 Azure로 전송합니다.
    - **에이전트 설치 프로그램**: Mobility Service의 푸시 설치를 수행합니다. 이 서비스는 마이그레이션에 대해 복제하려는 각 온-프레미스 머신에 설치되어 실행되어야 합니다.

## <a name="appliance-deployment"></a>어플라이언스 배포

**용도** | **세부 정보**
--- |  ---
**VMware VM 에이전트 기반 마이그레이션** | Azure Migrate 허브에서 OVA 템플릿을 다운로드하고 vCenter Server로 가져와서 어플라이언스 VM을 만듭니다.
**물리적 머신 에이전트 기반 마이그레이션** | VMware 인프라가 없거나 OVA 템플릿을 사용하여 VMware VM을 만들 수 없는 경우 Azure Migrate 허브에서 소프트웨어 설치 프로그램을 다운로드하고 실행하여 어플라이언스 머신을 설정합니다.

> [!NOTE]
> Azure Government를 배포하는 경우에는 설치 파일을 사용하여 복제 어플라이언스를 배포합니다.

## <a name="appliance-requirements"></a>어플라이언스 요구 사항

Azure Migrate 허브에서 제공하는 OVA 템플릿을 사용하여 복제 어플라이언스를 설정하면 어플라이언스가 Windows Server 2016을 실행하고 지원 요구 사항을 준수합니다. 물리적 서버에서 수동으로 복제 어플라이언스를 설정하는 경우에는 어플라이언스가 요구 사항을 준수하는지 확인하세요.

**구성 요소** | **요구 사항**
--- | ---
 | **VMware VM 어플라이언스**
PowerCLI | 복제 어플라이언스를 VMware VM에서 실행하는 경우에는 [PowerCLI 버전 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)이 설치되어 있어야 합니다.
NIC 유형 | VMXNET3(어플라이언스가 VMware VM인 경우)
 | **하드웨어 설정**
CPU 코어 | 8
RAM | 16GB
디스크 수 | 3개: OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브.
사용 가능한 디스크 공간(캐시) | 600GB
사용 가능한 디스크 공간(보존 디스크) | 600GB
**소프트웨어 설정** |
운영 체제 | Windows Server 2016 또는 Windows Server 2012 R2
라이선스 | 어플라이언스는 180일 동안 유효한 Windows Server 2016 평가 라이선스와 함께 제공됩니다.<br/><br/> 평가 기간이 곧 만료되는 경우 새 어플라이언스를 다운로드하여 배포하거나 어플라이언스 VM의 운영 체제 라이선스에 대한 정품 인증을 수행하는 것이 좋습니다.
운영 체제 로케일 | 미국 영어(en-us)
TLS | TLS 1.2를 사용하도록 설정해야 합니다.
.NET Framework | 머신에 .NET Framework 4.6 이상이 (강력한 암호화를 사용하도록 설정한 상태로) 설치되어 있어야 합니다.
MySQL | 어플라이언스에 MySQL이 설치되어 있어야 합니다.<br/> MySQL을 설치해야 합니다. 수동으로 설치 하거나 어플라이언스를 배포 하는 동안 설치할 수 Azure Migrate.
다른 앱 | 복제 어플라이언스에서 다른 앱을 실행하지 마세요.
Windows Server 역할 | 다음 역할을 사용하지 않도록 설정함: <br> - Active Directory Domain Services <br>- 인터넷 정보 서비스 <br> - Hyper-V
그룹 정책 | 다음 그룹 정책을 사용하지 않도록 설정함: <br> - 명령 프롬프트에 대한 액세스 방지 <br> - 레지스트리 편집 도구에 대한 액세스 방지 <br> - 파일 첨부를 위한 트러스트 논리 <br> - 스크립트 실행 켜기 <br> [자세한 정보](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - 기존의 기본 웹 사이트 없음 <br> - 포트 443에서 수신 대기하는 기존의 웹 사이트/애플리케이션 없음 <br>- [익명 인증](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) 사용 <br> - [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 설정 사용
**네트워크 설정** |
IP 주소 유형 | 정적
포트 | 443(컨트롤 채널 오케스트레이션)<br>9443(데이터 전송)
NIC 유형 | VMXNET3

## <a name="mysql-installation"></a>MySQL 설치 

복제 어플라이언스 머신에 MySQL이 설치되어 있어야 합니다. 다음 방법 중 하나를 사용하여 설치할 수 있습니다.

**방법** | **세부 정보**
--- | ---
수동으로 다운로드하여 설치 | MySQL 애플리케이션을 다운로드하고, C:\Temp\ASRSetup 폴더에 배치한 다음, 수동으로 설치합니다.<br/> 어플라이언스를 설정하면 MySQL이 이미 설치되어 있다고 표시됩니다.
온라인 다운로드를 사용하지 않는 방법 | MySQL 설치 프로그램 애플리케이션을 C:\Temp\ASRSetup 폴더에 저장합니다. 어플라이언스를 설치하고 다운로드를 선택하여 MySQL을 설치하면 설정에서 사용자가 추가한 설치 프로그램을 사용합니다.
Azure Migrate를 다운로드하여 설치 | 어플라이언스를 설치하는 과정에서 MySQL을 요구하는 메시지가 표시되면 **다운로드 및 설치** 를 선택합니다.

## <a name="url-access"></a>URL 액세스

복제 어플라이언스가 Azure 퍼블릭 클라우드에서 다음과 같은 URL에 액세스해야 합니다.

**URL** | **세부 정보**
--- | ---
\*.backup.windowsazure.com | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.store.core.windows.net | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.blob.core.windows.net | 복제된 데이터를 저장하는 스토리지 계정에 액세스하는 데 사용됩니다.
\*.hypervrecoverymanager.windowsazure.com | 복제 관리 작업 및 조정에 사용됩니다.
https:\//management.azure.com | 복제 관리 작업 및 조정에 사용됩니다.
*.services.visualstudio.com | 로깅 목적으로 사용됩니다(선택 사항입니다).
time.windows.com | 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인하는 데 사용됩니다.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | 어플라이언스 설정에서 이들 URL에 액세스해야 합니다. Azure Active Directory에서 액세스 제어 및 ID 관리에 사용됩니다.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL 다운로드를 완료하려면 일부 지역에서는 다운로드가 CDN URL로 리디렉션될 수 있습니다. 필요한 경우 CDN URL도 허용되어 있는지 확인합니다.


## <a name="azure-government-url-access"></a>Azure Government URL 액세스

복제 어플라이언스가 Azure Government에서 다음과 같은 URL에 액세스해야 합니다.

**URL** | **세부 정보**
--- | ---
\*.backup.windowsazure.us | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.store.core.windows.net | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.blob.core.windows.net | 복제된 데이터를 저장하는 스토리지 계정에 액세스하는 데 사용됩니다.
\*.hypervrecoverymanager.windowsazure.us | 복제 관리 작업 및 조정에 사용됩니다.
https:\//management.usgovcloudapi.net | 복제 관리 작업 및 조정에 사용됩니다.
*.services.visualstudio.com | 로깅 목적으로 사용됩니다(선택 사항입니다).
time.nist.gov | 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인하는 데 사용됩니다.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | OVA를 사용한 어플라이언스 설정에서 이들 URL에 액세스해야 합니다. Azure Active Directory에서 액세스 제어 및 ID 관리에 사용됩니다.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL 다운로드를 완료하려면 일부 지역에서는 다운로드가 CDN URL로 리디렉션될 수 있습니다. 필요한 경우 CDN URL도 허용되어 있는지 확인합니다.  

>[!Note]
>
> Migrate 프로젝트에 프라이빗 엔드포인트 연결이 있는 경우 프라이빗 링크 액세스에 우선하여 다음과 같은 URL에 액세스할 수 있어야 합니다.   
> - *.blob.core.windows.com - 복제된 데이터를 저장하는 스토리지 계정에 액세스하는 용도입니다. 이 옵션은 선택 사항이며, 스토리지 계정에 연결된 프라이빗 엔드포인트가 있는 경우 필요하지 않습니다. 
> - https:\//management.azure.com - 복제 관리 작업 및 조율을 위한 용도입니다. 
>- https:\//login.microsoftonline.com <br/>https:\//login.windows.net <br/> https:\//www.live.com 및 <br/> https:\//www.microsoft.com - Azure Active Directory에서 액세스 제어 및 ID 관리에 사용합니다.

## <a name="port-access"></a>포트 액세스

**디바이스** | **연결**
--- | ---
VM | VM에서 실행되는 Mobility Service는 복제 관리를 위해 HTTPS 443 인바운드 포트의 온-프레미스 복제 어플라이언스(구성 서버)와 통신합니다.<br/><br/> VM은 HTTPS 9443 인바운드 포트의 프로세스 서버(구성 서버 컴퓨터에서 실행)로 복제 데이터를 전송합니다. 이 포트는 수정할 수 있습니다.
복제 어플라이언스 | 복제 어플라이언스는 HTTPS 443 아웃바운드 포트에서 Azure와 복제를 오케스트레이션합니다.
프로세스 서버 | 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.<br/> 기본적으로 프로세스 서버는 복제 어플라이언스에서 실행됩니다.


## <a name="replication-process"></a>복제 프로세스

1. VM에 대해 복제를 사용하도록 설정하면 지정된 복제 정책을 사용하여 Azure Storage에 초기 복제가 시작됩니다. 
2. 트래픽은 인터넷을 통해 Azure Storage 공용 엔드포인트에 복제됩니다. 온-프레미스 사이트에서 Azure로의 사이트 간 VPN(가상 사설망)을 통한 트래픽 복제는 지원되지 않습니다.
3. 초기 복제가 완료되면 델타 복제가 시작됩니다. 머신에 대한 추적된 변경 내용이 로깅됩니다.
4. 다음과 같이 통신이 발생합니다.
    - VM은 복제 관리를 위해 HTTPS 443 인바운드 포트에서 복제 어플라이언스와 통신합니다.
    - 복제 어플라이언스는 HTTPS 443 아웃바운드 포트에서 Azure와 복제를 오케스트레이션합니다.
    - VM은 HTTPS 9443 인바운드 포트에서 (복제 어플라이언스에서 실행 중인) 프로세스 서버로 복제 데이터를 전송합니다. 이 포트는 수정할 수 있습니다.
    - 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.
5. 복제 데이터 로그는 먼저 Azure의 캐시 스토리지 계정으로 이동합니다. 이러한 로그는 처리되고 데이터는 Azure 관리 디스크에 저장됩니다.

![복제 프로세스 아키텍처를 보여 주는 다이어그램.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>어플라이언스 업그레이드

어플라이언스는 Azure Migrate 허브에서 수동으로 업그레이드됩니다. 항상 최신 버전을 실행하는 것이 좋습니다.

1. Azure Migrate > 서버 > Azure Migrate: 서버 평가, 인프라 서버에서 **구성 서버** 를 선택합니다.
2. 새 버전의 복제 어플라이언스를 사용할 수 있는 경우 **구성 서버** 에서 **에이전트 버전** 에 링크가 표시됩니다. 
3. 설치 프로그램을 복제 어플라이언스 머신으로 다운로드하고 업그레이드를 설치합니다. 설치 프로그램이 현재 어플라이언스에서 실행 중인 버전을 확인합니다.
 
## <a name="next-steps"></a>다음 단계

- 에이전트 기반 VMware VM 마이그레이션을 위해 복제 어플라이언스를 설정하는 [방법을 알아봅니다](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance).
- 물리적 서버를 위해 복제 어플라이언스를 설정하는 [방법을 알아봅니다](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance).
