---
title: VMware 가상 환경의 온-프레미스 서버를 Azure Arc에 온보딩
description: VMware 가상 환경의 온-프레미스 서버를 Azure Arc에 온보딩
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/27/2021
ms.openlocfilehash: 675c90218f456fc0f238fcf3b1fb93d2e5a7bc44
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296312"
---
# <a name="onboard-on-premises-servers-in-vmware-virtual-environment-to-azure-arc"></a>VMware 가상 환경의 온-프레미스 서버를 Azure Arc에 온보딩   

이 문서에서는 Azure Migrate: 검색 및 평가 도구를 사용하여 Azure 관리를 위해 온-프레미스 VMware VM을 Azure Arc에 온보딩하는 방법을 설명합니다. 

Azure Arc를 사용하면 Azure 관리 환경을 마이그레이션에 적합하지 않은 온-프레미스 서버로 확장하여 단일 창으로 하이브리드 IT 자산을 관리할 수 있습니다. Azure Arc에 관해 [자세히 알아봅니다](../azure-arc/servers/overview.md). 

## <a name="before-you-get-started"></a>시작하기 전에

- Azure Migrate: 검색 및 평가 도구를 사용하여 VMware 환경에서 실행되는 서버를 검색하기 위한 [요구 사항을 검토](./tutorial-discover-vmware.md#prerequisites)합니다.  
- 사용할 [VMware vCenter](./tutorial-discover-vmware.md#prepare-vmware)를 준비하고 소프트웨어 인벤토리를 수행하기 위한 [VMware 요구 사항](migrate-support-matrix-vmware.md#vmware-requirements)을 검토합니다. 검색된 서버를 Azure Arc에 온보딩하기 시작하려면 소프트웨어 인벤토리가 완료되어야 합니다.   
- 서버에서 소프트웨어 인벤토리를 시작하기 전에 [애플리케이션 검색 요구 사항](migrate-support-matrix-vmware.md#software-inventory-requirements)을 검토합니다. Windows 서버에는 PowerShell 버전 3.0 이상이 설치되어 있어야 합니다. 
- Azure Arc에 온보딩하기 위해 검색된 서버의 인벤토리에 대한 원격 연결을 허용하기 위한 필수 조건을 확인합니다. 
    1. 검색된 서버에 대한 인바운드 원격 연결 허용 
        - _Windows:_ WinRM 포트 5985(HTTP)의 인바운드 연결. 모든 대상 Windows 서버에서 “winrm qc” 명령을 실행하여 로컬 컴퓨터에서 WS-Management 프로토콜을 사용하도록 설정합니다. 
        - _Linux의 경우:_ 모든 대상 Linux 서버에서 포트 22(SSH)에서 인바운드 연결을 허용합니다.
        - 원격 시스템(검색된 서버)의 IP 주소를 어플라이언스의 WinRM TrustedHosts 목록에 추가할 수도 있습니다. 
    2. Azure Migrate 어플라이언스에는 대상 서버에 대한 네트워크 가시선이 있어야 합니다. 
- [Azure Arc의 필수 조건](../azure-arc/servers/agent-overview.md#prerequisites)을 확인하고 다음 고려 사항을 검토해야 합니다.
    - vCenter Server 검색 및 소프트웨어 인벤토리가 완료되어야 Azure Arc에 온보딩하기 시작할 수 있습니다. 소프트웨어 인벤토리가 켜진 후 완료될 때까지 최대 6시간이 걸릴 수 있습니다.
    -  [Azure Arc 하이브리드 연결 머신 에이전트](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)는 Arc 온보딩 프로세스 중에 검색된 서버에 설치됩니다. 에이전트를 설치하고 구성하려면 서버에 대한 관리자 권한이 있는 자격 증명을 제공해야 합니다. Linux에서는 루트 계정을 제공하고 Windows에서는 로컬 관리자 그룹의 멤버인 계정을 제공합니다. 
    - 서버에서 [지원되는 운영 체제](../azure-arc/servers/agent-overview.md#supported-operating-systems)를 실행하고 있는지 확인합니다.
    - Azure 계정에 [필요한 Azure 역할](../azure-arc/servers/agent-overview.md#required-permissions)에 대한 할당이 부여되었는지 확인합니다.
    - 검색된 서버가 인터넷을 통해 통신하기 위해 방화벽 또는 프록시 서버를 통해 연결하는 경우 [필요한 URL](../azure-arc/servers/agent-overview.md#networking-configuration)이 차단되지 않는지 확인합니다.
    - Azure Arc에 대해 [지원되는 지역](../azure-arc/servers/overview.md#supported-regions)을 검토합니다. 
    - Azure Arc 지원 서버는 리소스 그룹에서 최대 5000개의 컴퓨터 인스턴스를 지원합니다.


## <a name="set-up-the-azure-migrate-project"></a>Azure Migrate 프로젝트 설정  

1. 시작하기 전에 [Azure 사용자 계정](./tutorial-discover-vmware.md#prepare-an-azure-user-account)을 준비하고 Azure Migrate에 필요한 리소스를 만드는 데 [필요한 역할](./create-manage-projects.md#verify-permissions)이 구독에 있는지 확인합니다. 
2. [이 문서](./create-manage-projects.md)를 통해 Azure Migrate: 검색 및 평가 도구를 추가하여 새 Azure Migrate 프로젝트를 설정합니다.  

    > [!Note]
    > 기존 Migrate 프로젝트를 사용하고 검색된 서버 인벤토리를 Azure Arc에 온보딩할 수도 있습니다. 이렇게 하려면 어플라이언스 서버에서 어플라이언스 구성 관리자를 시작하고 서비스가 최신 버전으로 업데이트되었는지 확인합니다. [자세한 정보](./migrate-appliance.md#appliance-upgrades) <br/> <br/> 다음으로, [이 지침에 따라](#onboard-to-azure-arc) 서버를 온보딩합니다.  

## <a name="deploy-and-register-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 배포 및 등록 

Azure Migrate: 검색 및 평가는 간단한 Azure Migrate 어플라이언스를 사용합니다. 이 어플라이언스는 서버를 검색하고 서버 구성 및 성능 메타데이터를 Azure Migrate로 보냅니다. 

어플라이언스를 설정하기 전에 다음을 수행합니다. 

1. Azure Migrate 어플라이언스 배포 [요구 사항을 검토](migrate-appliance.md#appliance---vmware)합니다.
2. 어플라이언스가 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 Azure URL을 검토합니다. 
3. 어플라이언스에 대한 [포트 액세스 요구 사항](migrate-support-matrix-vmware.md#port-access-requirements)을 확인합니다. 


다음으로,

- 이 문서에 따라 [Azure Migrate 어플라이언스를 설정](./tutorial-discover-vmware.md#set-up-the-appliance)하여 vCenter Server 검색을 시작합니다. 어플라이언스를 배포하기 위해 OVA 템플릿을 다운로드하고 VMware로 가져와 vCenter Server에서 실행되는 서버를 만들 수 있습니다.  
- 어플라이언스를 배포한 후에는 검색을 시작하기 전에 프로젝트에 등록해야 합니다. [이 지침](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate)에 따라 어플라이언스를 등록합니다. 

## <a name="configure-the-appliance-and-start-discovery"></a>어플라이언스 구성 및 검색 시작  

[이 문서](./tutorial-discover-vmware.md#start-continuous-discovery)를 사용하여 Azure Migrate 어플라이언스를 구성하고 vCenter Server 검색을 시작할 수 있습니다. 

검색을 위해 어플라이언스를 구성할 때 어플라이언스 구성 관리자에서 세부 정보를 지정해야 합니다.

- 연결할 vCenter Server의 세부 정보.  
- VMware 환경에서 서버를 검색하도록 범위가 지정된 vCenter Server 자격 증명. 
- 관리자 권한이 있는 서버 자격 증명 Azure Arc에 온보딩하려면 서버에 대한 관리자 권한이 있는 자격 증명을 사용하여 Azure Arc 하이브리드 연결 머신 에이전트를 설치하고 구성해야 합니다. 자격 증명을 제공하는 방법과 처리하는 방법에 대해 [자세히 알아보세요](add-server-credentials.md).

검색이 성공적으로 완료되면 검색된 서버가 포털에 표시되는 데 15분 정도 걸립니다.


## <a name="onboard-to-azure-arc"></a>Azure Arc에 온보딩

>[!Important]
>검색된 서버를 Azure Arc에 온보딩하기 전에 소프트웨어 인벤토리가 완료되어야 합니다. 

vCenter Server 검색이 완료되면 소프트웨어 인벤토리(설치된 애플리케이션 검색)가 자동으로 시작됩니다. 소프트웨어 인벤토리 중에 제공된 서버 자격 증명은 검색된 서버에 대해 반복되고 유효성이 검사됩니다. 소프트웨어 인벤토리가 완료되고 자격 증명이 매핑된 후에 온보딩을 시작할 수 있습니다. 소프트웨어 인벤토리가 켜진 후 완료될 때까지 최대 6시간이 걸릴 수 있습니다.  
1. **Azure Arc에 온보딩** 패널로 이동합니다. 

    ![Arc 온보딩](./media/onboard-to-azure-arc-with-azure-migrate/azure-arc-onboarding-panel-after-being-enabled.png)

2. Azure 내에서 서버를 관리하려는 **구독** 및 **리소스 그룹** 을 제공합니다.

3. **지역** 드롭다운 목록에서 서버 메타데이터를 저장할 Azure 지역을 선택합니다.

4. 대규모로 온보딩하려면 **Azure Active Directory 서비스 주체** 세부 정보를 제공합니다. 이 문서를 검토하여 [Azure Portal 또는 Azure PowerShell을 사용하여 서비스 주체를 만듭니다](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale). <br/>

    다음 입력이 필요합니다.
    - **디렉터리(테넌트) ID** - Azure AD의 전용 인스턴스를 나타내는 [고유 식별자(GUID)](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)입니다. 
    - **애플리케이션(클라이언트) ID** - 서비스 주체의 애플리케이션 ID를 나타내는 [고유 식별자(GUID)](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)입니다.
    - **서비스 주체 비밀(애플리케이션 비밀)** - 암호 기반 인증을 위한 [클라이언트 비밀](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)입니다. 
    
5. _선택 사항_: 검색된 서버에서 인터넷에 연결하는 데 프록시 서버가 필요한 경우 **프록시 서버 IP 주소** 또는 이름과 **포트 번호** 를 제공합니다. 해당 값을 `http://<proxyURL>:<proxyport>` 형식으로 입력합니다. 검색된 서버에서 사용하는 이 프록시 서버는 어플라이언스 서버에서 인터넷에 연결하는 데 필요한 프록시 서버(어플라이언스 구성 관리자의 필수 조건 섹션에 제공됨)와 다를 수 있습니다.   

    > [!Note]
    > 프록시 인증을 설정하는 기능은 지원되지 않습니다. 

6. **온보딩 시작** 을 선택하여 Azure Arc 온보딩 프로세스를 시작합니다. 온보딩 프로세스는 시간이 약간 걸립니다. 온보딩이 완료되면 완료 상태 및 자세한 온보딩 상태 보고서가 표시됩니다.    

    ![Arc 온보딩 보고서](./media/onboard-to-azure-arc-with-azure-migrate/onboarding-completion-report.png)

    > [!Note]
    > 로그인이 만료된 경우 **다시 로그인** 을 선택합니다. 그러면 디바이스 코드가 포함된 모달이 열립니다. **코드 복사 및 로그인** 을 선택하여 디바이스 코드를 복사하고 새 브라우저 탭에서 Azure 로그인 프롬프트를 엽니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.

7. 온보딩이 완료되면 [Azure Arc 홈페이지](https://portal.azure.com/#blade/Microsoft_Azure_HybridCompute/AzureArcCenterBlade/servers)로 이동하여 온보딩된 서버를 확인하고 관리합니다.   

8. 자세한 온보딩 보고서를 확인하여 서버 온보딩 상태를 이해하고 적절한 조치를 취합니다. 

> [!Note]
> Azure Migrate 어플라이언스는 WinRM 세션을 시작하여 Azure Arc 온보딩 스크립트를 실행합니다. 대상 서버의 WinRM 서비스에 대한 액세스를 제한하는 설정이 없는지 확인합니다.    


## <a name="next-steps"></a>다음 단계 

- 자세한 내용 및 오류 정보를 보려면 자세한 온보딩 보고서를 검토합니다. 서버를 성공적으로 온보딩하려면 오류(있는 경우)를 해결합니다. 
- **온보딩 시작** 을 선택하여 Azure Arc 온보딩 프로세스를 다시 실행할 수 있습니다. 새로 검색된 서버 및 이전 실행에서 성공적으로 온보딩하지 못한 서버에 대한 온보딩이 시도됩니다.   

## <a name="troubleshooting-azure-arc-onboarding-errors"></a>Azure Arc 온보딩 오류 문제 해결

Azure Migrate 어플라이언스를 사용하여 Azure Arc에 온보딩하는 동안 오류가 발생하는 경우 다음 섹션을 통해 가능한 원인과 제안된 단계를 확인하여 문제를 해결할 수 있습니다. 

아래 나열된 오류 코드가 표시되지 않거나 오류 코드가 **_AZCM_** 으로 시작하는 경우 [Azure Arc 문제 해결에 대한 이 가이드](../azure-arc/servers/troubleshoot-agent-onboard.md)를 참조하세요.

### <a name="error-60001---unabletoconnecttophysicalserver"></a>오류 60001 - UnableToConnectToPhysicalServer  

**가능한 원인**  
서버에 연결하기 위한 [필수 조건](./migrate-support-matrix-physical.md)이 충족되지 않았거나 서버에 연결할 때 네트워크 문제가 있습니다(예: 일부 프록시 설정).

**권장 작업**   
- 서버가 [필수 조건](#before-you-get-started) 및 [포트 액세스 요구 사항](./migrate-support-matrix-physical.md)을 충족하는지 확인합니다. 
- Azure Migrate 어플라이언스의 WinRM TrustedHosts 목록에 원격 머신(검색된 서버)의 IP 주소를 추가하고 작업을 다시 시도합니다. 이는 서버(_Windows:_ WinRM 포트 5985(HTTP) 및 _Linux:_ SSH 포트 22(TCP))에서 원격 인바운드 연결을 허용하기 위한 것입니다.
- 서버에 연결할 어플라이언스에서 올바른 인증 방법을 선택했는지 확인합니다. 
   > [!Note] 
   > Azure Migrate는 Linux 서버에 대해 암호 기반 및 SSH 키 기반 인증을 둘 다 지원합니다.
- 문제가 지속되면 Microsoft 지원 사례를 제출하여 어플라이언스 머신 ID(어플라이언스 구성 관리자의 바닥글에서 사용 가능)를 제공합니다.     
   

### <a name="error-60002---invalidservercredentials"></a>오류 60002 - InvalidServerCredentials  

**가능한 원인**  
서버에 연결할 수 없습니다. 어플라이언스에서 잘못된 자격 증명을 제공했거나 이전에 제공된 자격 증명이 만료되었습니다.

**권장 작업**  
- 어플라이언스에서 서버의 올바른 자격 증명을 제공했는지 확인합니다. 해당 자격 증명을 사용하여 서버에 연결을 시도하여 이를 확인할 수 있습니다.
- 추가된 자격 증명이 잘못되었거나 만료된 경우 어플라이언스에서 자격 증명을 편집하고 추가된 서버의 유효성을 다시 검사합니다. 유효성 검사에 성공하면 문제가 해결됩니다.
- 문제가 지속되면 Microsoft 지원 사례를 제출하여 어플라이언스 머신 ID(어플라이언스 구성 관리자의 바닥글에서 사용 가능)를 제공합니다.

### <a name="error-60005---sshoperationtimeout"></a>오류 60005 - SSHOperationTimeout  

**가능한 원인**  
- 네트워크 대기 시간 문제 또는 서버에 대한 최신 업데이트 부족으로 인해 작업이 예상보다 오래 걸렸습니다. 

**권장 작업**  
- 영향을 받는 서버에 최신 커널 및 OS 업데이트가 설치되어 있는지 확인합니다.
- 어플라이언스와 서버 간에 네트워크 대기 시간이 없는지 확인합니다. 대기 시간 문제를 방지하려면 어플라이언스와 원본 서버를 동일한 도메인에 포함하는 것이 좋습니다.
- 어플라이언스에서 영향을 받는 서버에 연결하고 [여기에 설명된](./troubleshoot-appliance.md) 명령을 실행하여 null 또는 빈 데이터를 반환하는지 확인합니다.
- 문제가 지속되면 Microsoft 지원 사례를 제출하여 어플라이언스 머신 ID(어플라이언스 구성 관리자의 바닥글에서 사용 가능)를 제공합니다.  

### <a name="error-60108---softwareinventorycredentialnotassociated"></a>오류 60108 - SoftwareInventoryCredentialNotAssociated  

**가능한 원인**  
- 서버와 연결된 자격 증명이 없습니다.

**권장 작업**  
- 검색된 서버를 Azure Arc에 온보딩하기 시작하려면 소프트웨어 인벤토리가 완료되어야 합니다. [자세한 정보](./how-to-discover-applications.md#add-credentials-and-initiate-discovery)
- 어플라이언스 구성 관리자에서 제공된 자격 증명이 유효하고 자격 증명을 사용하여 서버에 액세스할 수 있는지 확인합니다.
- 어플라이언스 구성 관리자로 돌아가서 다른 자격 증명 세트를 제공하거나 기존 자격 증명을 편집합니다.  

### <a name="error-60109---arcosnotsupported"></a>오류 60109 - ArcOsNotSupported  

**가능한 원인**  
- 서버는 Azure Arc 온보딩에 대해 지원되지 않는 운영 체제를 호스트합니다.

**권장 작업**  
- Azure Arc에 대해 [지원되는 운영 체제를 검토](../azure-arc/servers/agent-overview.md#supported-operating-systems)합니다. 
 
### <a name="error-10002---scriptexecutiontimedoutonvm"></a>오류 10002 - ScriptExecutionTimedOutOnVm  

**가능한 원인**  
- 온보딩 작업이 제시간에 완료되지 않았습니다. 따라서 실행이 예상보다 오래 걸렸습니다. 

**권장 작업**  
- 이 문제는 잠시 후 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원에 문의하세요.  
 
### <a name="error-50000---accessdenied"></a>오류 50000 - AccessDenied 

**가능한 원인**  
- 서버에 대한 액세스가 금지되어 작업을 완료할 수 없습니다. 서버에 액세스하기 위해 어플라이언스에 제공된 사용자 계정에 필요한 권한이 없거나 자격 증명이 잘못되었습니다. WinRM 오류 코드: 0x80070005

**권장 작업**  
- 가능한 원인을 확인하고 작업을 다시 시도합니다. 문제가 지속되면 고객 지원 팀에 문의하세요.

### <a name="error-9609516000960078---nullresultunhandledexceptionserverunknownerrorunhandlederror"></a>오류 960/951/60009/60078 - NullResult/UnhandledException/ServerUnknownError/UnhandledError

**가능한 원인**  
- 내부 오류로 인해 작업이 실패했습니다. 

**권장 작업**  
- 잠시 후 작업을 다시 시도하세요. 문제가 지속되면 고객 지원팀에 문의하고 어플라이언스 머신 ID(어플라이언스 구성 관리자의 **바닥글** 에서 사용 가능)를 제공합니다.