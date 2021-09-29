---
title: Azure Migrate 검색 및 평가를 통해 VMware 환경에서 실행되는 서버 검색
description: Azure Migrate 검색 및 평가 도구를 사용하여 VMware 환경에서 온-프레미스 서버, 애플리케이션 및 종속성을 검색하는 방법을 알아봅니다.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 07/28/2021
ms.custom: mvc
ms.openlocfilehash: 3d3fc18ebceb18e7411a68c19e069a0db4889854
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124797118"
---
# <a name="tutorial-discover-servers-running-in-a-vmware-environment-with-azure-migrate"></a>자습서: Azure Migrate를 사용하여 VMware 환경에서 실행 중인 서버 검색

Azure로 마이그레이션하는 과정의 일환으로 온-프레미스 인벤토리 및 워크로드를 검색합니다.

이 자습서에서는 가벼운 Azure Migrate 어플라이언스인 Azure Migrate: 검색 및 평가 도구를 사용하여 VMware 환경에서 실행 중인 서버를 검색하는 방법을 보여줍니다. 어플라이언스를 vCenter Server 인스턴스에서 실행되는 서버로 배포하여 서버와 서버의 성능 메타데이터, 서버에서 실행 중인 애플리케이션, 서버 종속성, ASP.NET 웹앱, SQL Server 인스턴스 및 데이터베이스를 지속적으로 검색합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure 계정을 설정합니다.
> * 검색을 위해 VMware 환경을 준비합니다.
> * 프로젝트를 만듭니다.
> * Azure Migrate 어플라이언스를 설정합니다.
> * 연속 검색을 시작합니다.

> [!NOTE]
> 이 자습서에서는 시나리오를 가장 빨리 시도할 수 있는 방법을 보여줍니다. 가능한 한 기본 옵션을 사용합니다.  

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에 다음과 같은 사전 요구 사항이 충족되었는지 확인합니다.

요구 사항 | 세부 정보
--- | ---
**vCenter Server/ESXi 호스트** | vCenter Server 버전 6.7, 6.5, 6.0 또는 5.5를 실행하는 서버가 필요합니다.<br /><br /> 서버는 버전 5.5 이상을 실행하는 ESXi 호스트에 호스트되어야 합니다.<br /><br /> 어플라이언스가 구성 및 성능 메타데이터를 수집할 수 있도록 vCenter Server에서 TCP 포트 443을 통한 인바운드 연결을 허용합니다.<br /><br /> 어플라이언스는 기본적으로 포트 443의 vCenter Server에 연결합니다. vCenter Server를 실행하는 서버가 다른 포트에서 수신 대기하는 경우, 어플라이언스 구성 관리자에서 vCenter Server 세부 정보를 제공할 때 포트를 수정할 수 있습니다.<br /><br /> 서버에 설치된 애플리케이션 검색 및 에이전트 없는 종속성 분석을 수행할 수 있도록 ESXi 호스트에서 TCP 포트 443을 통한 인바운드 액세스를 허용해야 합니다.
**Azure Migrate 어플라이언스** | Azure Migrate 어플라이언스를 호스트하는 서버에 할당할 다음과 같은 리소스가 vCenter Server에 있어야 합니다.<br /><br /> - RAM 32GB, vCPU 8개 및 디스크 스토리지 약 80GB<br /><br /> - 외부 가상 스위치 그리고 어플라이언스 서버에서 직접 또는 프록시를 통한 인터넷 액세스
**서버** | 모든 Windows 및 Linux OS 버전이 구성 및 성능 메타데이터 검색을 지원합니다. <br /><br /> 서버에서 애플리케이션 검색을 수행할 수 있도록 모든 Windows 및 Linux OS 버전이 지원됩니다. [에이전트 없는 종속성 분석에 지원되는 OS 버전](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)을 확인하세요.<br /><br /> 설치된 애플리케이션 검색 및 에이전트 없는 종속성 분석을 수행하려면 VMware Tools(버전 10.2.1 이상)를 서버에 설치하고 실행해야 합니다. Windows 서버에는 PowerShell 버전 2.0 이상이 설치되어 있어야 합니다.<br /><br /> SQL Server 인스턴스 및 데이터베이스를 검색하려면 [지원되는 SQL Server 및 Windows OS 버전 및 버전](migrate-support-matrix-vmware.md#sql-server-instance-and-database-discovery-requirements)과 Windows 인증 메커니즘을 확인하세요.<br /><br /> IIS 웹 서버에서 실행되는 ASP.NET 웹앱을 검색하려면 [지원되는 Windows OS 및 IIS 버전](migrate-support-matrix-vmware.md#aspnet-web-apps-discovery-requirements)을 확인하세요.

## <a name="prepare-an-azure-user-account"></a>Azure 사용자 계정 준비

프로젝트를 만들고 Azure Migrate 어플라이언스를 등록하려면 다음 권한이 있는 Azure 계정이 있어야 합니다.

- Azure 구독에 대한 기여자 또는 소유자 권한
- Azure AD(Azure Active Directory) 앱을 등록할 수 있는 권한
- 에이전트 없는 서버 마이그레이션 중에 사용되는 Azure Key Vault 인스턴스를 만드는 데 필요한 Azure 구독의 소유자 또는 기여자 및 사용자 액세스 관리자 권한

Azure 무료 계정을 만든 경우 자신이 구독의 소유자입니다. 구독 소유자가 아닌 경우 권한을 할당해 주도록 소유자에게 문의합니다.

Azure 구독에서 기여자 또는 소유자 권한을 설정하려면 다음과 같이 합니다.

1. Azure Portal에서 "구독"을 검색합니다. 검색 결과의 **서비스** 아래에서 **구독** 을 선택합니다.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="검색 상자에서 Azure 구독을 검색하는 방법을 보여주는 스크린샷":::

1. **구독** 에서 프로젝트를 만들 구독을 선택합니다.
1. 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.
1. **액세스 권한 확인** 탭의 **액세스 확인** 에서 사용하려는 사용자 계정을 검색합니다.
1. **역할 추가 할당** 창에서 **추가** 를 선택합니다.

    :::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="액세스 권한을 확인하고 역할 할당을 추가하기 위해 사용자 계정을 검색하는 방법을 보여주는 스크린샷":::
    
1. **역할 할당 추가** 에서 기여자 또는 소유자 역할을 선택하고 계정을 선택합니다. **저장** 을 선택합니다.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="역할을 계정에 할당할 수 있는 역할 할당 추가 페이지를 보여주는 스크린샷":::

계정에 Azure AD 앱을 등록하는 데 필요한 권한을 부여하려면 다음과 같이 합니다.

1. 포털에서 **Azure Active Directory** > **사용자** > **사용자 설정** 으로 차례로 이동합니다.
1. **사용자 설정** 에서 Azure AD 사용자가 애플리케이션을 등록할 수 있는지 확인합니다(기본적으로 **예** 로 설정됨).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="앱을 등록하는 사용자 설정을 확인하는 것을 보여주는 스크린샷":::

9. **앱 등록** 이 **아니요** 로 설정된 경,우 테넌트 또는 전역 관리자에게 필요한 권한을 할당해주도록 요청합니다. 또는 테넌트 또는 전역 관리자가 계정에 애플리케이션 개발자 역할을 할당하여 사용자가 Azure AD 앱을 등록할 수 있도록 허용할 수도 있습니다. [자세히 알아보기](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>VMware 준비

vCenter Server에서 VMware OVA(Open Virtualization Appliance) VM(가상 머신) 설치 파일을 사용하여 VM을 만들 수 있는 권한이 계정에 있는지 확인합니다. 이러한 권한은 OVA 파일을 사용하여 Azure Migrate 어플라이언스를 VMware VM으로 배포할 때 필요합니다.

VMware 환경에서 실행되는 서버를 검색하고 평가하려면 Azure Migrate에 vCenter Server 읽기 전용 계정이 있어야 합니다. 또한 설치된 애플리케이션의 검색 및 에이전트 없는 종속성 분석을 실행하려면 계정에 VM 게스트 작업에 대한 VMware에서 사용하도록 설정된 권한이 있어야 합니다.

### <a name="create-an-account-to-access-vcenter-server"></a>vCenter에 액세스하는 계정 만들기

VMware vSphere 웹 클라이언트에서 vCenter Server에 사용할 읽기 전용 계정을 설정합니다.

1. 관리자 권한이 있는 계정으로 vSphere 웹 클라이언트의 **홈** 메뉴에서 **관리** 를 선택합니다.
1. **Single Sign-On** 에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자** 에서 **새 사용자** 를 선택합니다.
1. 계정 세부 정보를 입력한 다음, **확인** 을 선택합니다.
1. **관리** 아래에 있는 메뉴의 **Access Control** 에서 **전역 권한** 을 선택합니다.
1. 사용자 계정을 선택한 다음, **읽기 전용** 을 선택하여 계정에 역할을 할당합니다. **확인** 을 선택합니다.
1. 설치된 애플리케이션의 검색 및 에이전트 없는 종속성 분석을 시작하려면 **Access Control** 의 메뉴에서 **역할** 을 선택합니다. **역할** 창의 **역할** 에서 **읽기전용** 을 선택합니다. **권한** 에서 **게스트 작업** 을 선택합니다. vCenter Server 인스턴스의 모든 개체에 권한을 전파하려면 **자식에 전파** 확인란을 선택합니다.

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="v sphere 클라이언트와 함께 새 계정을 만들고 사용자 역할 및 권한을 선택하는 방법을 보여주는 스크린샷":::

> [!NOTE]
> 검색을 특정 vCenter Server 데이터 센터, 클러스터, 호스트, 클러스터 또는 호스트의 폴더 또는 개별 서버로 제한하도록 vCenter Server 계정의 범위를 지정할 수 있습니다. [vCenter Server 사용자 계정의 범위를 지정](set-discovery-scope.md)하는 방법을 알아보세요.

> [!NOTE]
> 연결 모드를 통해 검색을 위해 지정된 vCenter 서버에 연결된 vCenter 자산은 Azure Migrate로 검색되지 않습니다. 검색하려는 각 vCenter 환경에 대해 Azure Migrate 어플라이언스를 배포해야 합니다.

### <a name="create-an-account-to-access-servers"></a>서버에 액세스하는 계정 만들기

서버의 사용자 계정에는 설치된 애플리케이션의 검색과 에이전트 없는 종속성 분석, 그리고 웹앱, SQL Server 인스턴스 및 데이터베이스의 검색을 시작하는 데 필요한 권한이 있어야 합니다. 사용자 계정 정보는 어플라이언스 구성 관리자에서 제공할 수 있습니다. 어플라이언스는 에이전트를 서버에 설치하지 않습니다.

* Windows 서버 및 웹앱 검색의 경우 서버 관리자 권한이 있는 계정(로컬 또는 도메인)을 만듭니다. SQL Server 인스턴스 및 데이터베이스를 검색하려면 Windows 또는 SQL Server 계정이 sysadmin 서버 역할의 멤버여야 합니다. [사용자 계정에 필요한 역할을 할당](/sql/relational-databases/security/authentication-access/server-level-roles)하는 방법을 알아보세요.
* Linux 서버의 경우 루트 사용자 계정 세부 정보를 제공하거나 /bin/netstat 및 /bin/ls 파일에 대한 CAP_DAC_READ_SEARCH 및 CAP_SYS_PTRACE 권한이 있는 계정을 만듭니다.

> [!NOTE]
> 설치된 애플리케이션의 검색, 에이전트 없는 종속성 분석, 그리고 웹앱, SQL Server 인스턴스 및 데이터베이스의 검색을 시작하기 위해 Azure Migrate 구성 관리자에서 여러 서버 자격 증명을 추가할 수도 있습니다. 여러 도메인, Windows(도메인 아님), Linux(도메인 아님) 또는 SQL Server 인증 자격 증명을 추가할 수 있습니다. [서버 자격 증명을 추가](add-server-credentials.md)하는 방법을 알아봅니다.

## <a name="set-up-a-project"></a>프로젝트 설정

새 프로젝트를 설정하려면 다음과 같이 합니다.

1. Azure Portal에서 **모든 서비스** 를 선택한 다음, **Azure Migrate** 를 검색합니다.
1. **서비스** 아래에서 **Azure Migrate** 를 선택합니다.
1. **개요** 에서 마이그레이션 목표에 따라 **서버, 데이터베이스 및 웹앱**, **SQL Server(전용)** 또는 **추가 시나리오 탐색** 옵션 중 하나를 선택합니다.
1. **프로젝트 만들기** 를 선택합니다.
1. **프로젝트 만들기** 에서 Azure 구독 및 리소스 그룹을 선택합니다. 리소스 그룹이 없는 경우 리소스 그룹을 만듭니다.
1. **프로젝트 세부 정보** 에서 프로젝트 이름과 이 프로젝트를 만들려는 지역을 지정합니다. [퍼블릭 클라우드가 지원되는 지역](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드가 지원되는 지역](migrate-support-matrix.md#supported-geographies-azure-government)을 참조하세요.

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="새 Azure Migrate 프로젝트의 프로젝트 세부 정보를 추가하는 방법을 보여주는 스크린샷":::

1. **만들기** 를 선택합니다.
1. 프로젝트가 배포될 때까지 몇 분 정도 기다립니다. **Azure Migrate: 검색 및 평가** 도구는 기본적으로 새 프로젝트에 추가됩니다.

> [!NOTE]
> 이미 프로젝트를 만든 경우 그 프로젝트를 사용하여 추가 어플라이언스를 등록하면 더 많은 서버를 검색하고 평가할 수 있습니다. [프로젝트 관리](create-manage-projects.md#find-a-project) 방법에 대해 알아봅니다.

## <a name="set-up-the-appliance"></a>어플라이언스 설정

Azure Migrate: 검색 및 평가 도구는 가벼운 Azure Migrate 어플라이언스를 사용합니다. 이 어플라이언스는 서버를 검색하고 서버 구성 및 성능 메타데이터를 Azure Migrate로 보냅니다. 프로젝트에서 다운로드할 수 있는 OVA 템플릿을 배포하여 어플라이언스를 설정하세요.

> [!NOTE]
> OVA 템플릿을 사용하여 어플라이언스를 설정할 수 없는 경우, Windows Server 2016을 실행하는 기존 서버에서 PowerShell 스크립트를 실행하여 설정할 수 있습니다. [PowerShell을 사용하여 Azure Migrate 어플라이언스를 설정](deploy-appliance-script.md#set-up-the-appliance-for-vmware)하는 방법을 알아보세요. <br/>
> OVA 템플릿을 사용하여 어플라이언스를 배포하는 옵션은 Azure Government 클라우드에서 지원되지 않습니다. Azure Government 클라우드용 어플라이언스를 배포하는 방법에 대해 [자세히 알아봅니다](./deploy-appliance-script-government.md).

### <a name="deploy-by-using-an-ova-template"></a>OVA 템플릿을 사용하여 배포

OVA 템플릿을 사용하여 어플라이언스를 설정하려면 이 섹션에서 자세히 설명하는 다음 단계를 완료합니다.

1. 포털에서 어플라이언스 이름을 제공하고, 프로젝트 키를 생성합니다.
1. OVA 템플릿 파일을 다운로드한 다음, vCenter Server로 가져옵니다. OVA가 안전한지 확인하세요.
1. OVA 파일에서 어플라이언스를 만듭니다. 어플라이언스에서 Azure Migrate에 연결할 수 있는지 확인합니다.
1. 어플라이언스를 처음으로 설정합니다. 
1. 프로젝트 키를 사용하여 프로젝트에 어플라이언스를 등록합니다.

#### <a name="generate-the-project-key"></a>프로젝트 키 생성

1. **마이그레이션 목표** 에서 **서버, 데이터베이스 및 웹앱** > **Azure Migrate: 검색 및 평가** > **검색** 을 선택합니다.
1. **서버 검색** 에서 **서버가 가상화되어 있나요?** > **예, VMware vSphere 하이퍼바이저 사용** 을 선택합니다.
1. **1: 프로젝트 키 생성** 에 VMware 환경에서 서버를 검색하도록 설정할 Azure Migrate 어플라이언스의 이름을 지정합니다. 이름은 14자 이하의 영숫자여야 합니다.
1. 필요한 Azure 리소스 만들기를 시작하려면 **키 생성** 을 선택합니다. 리소스를 만드는 동안 **검색** 창을 닫지 마세요.
1. Azure 리소스가 성공적으로 생성되고 나면 *프로젝트 키* 가 생성됩니다.
1. 키를 복사합니다. 어플라이언스 구성 시 이 키를 사용하여 어플라이언스 등록을 완료합니다.

#### <a name="download-the-ova-template"></a>OVA 템플릿 다운로드

**2: Azure Migrate 어플라이언스 다운로드** 에서 OVA 파일을 선택하고 **다운로드** 를 클릭합니다.

##### <a name="verify-security"></a>보안 확인

OVA 파일을 배포하기 전에 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 **관리자 권한으로 실행** 옵션을 사용하여 명령 프롬프트 창을 엽니다.
1. 다음 명령을 실행하여 OVA 파일에 대한 해시를 생성합니다.
  
    ```bash
    C:\>CertUtil -HashFile <file_location> <hashing_agorithm>
    ```
   
    예: `C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256`

1. 최신 어플라이언스 버전 및 해시 값을 확인합니다.

    - Azure 퍼블릭 클라우드의 경우:
    
        **알고리즘** | **다운로드** | **SHA256**
        --- | --- | ---
        VMware(11.9GB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Azure Government의 경우:
    
        **알고리즘** | **다운로드** | **SHA256**
        --- | --- | ---
        VMware(85.8MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

#### <a name="create-the-appliance-server"></a>어플라이언스 서버 만들기

다운로드한 파일을 가져온 다음, VMware 환경에서 서버를 만듭니다.

1. vSphere Client 콘솔에서 **파일** > **OVF 템플릿 배포** 를 클릭합니다.
1. OVF 템플릿 배포 마법사에서 **원본** 을 선택한 다음, OVA 파일의 위치를 지정합니다.
1. **이름** 에 서버의 이름을 입력합니다. **위치** 에서 서버를 호스트할 인벤토리 개체를 선택합니다.
1. **호스트/클러스터** 에서 서버를 실행할 호스트 또는 클러스터를 지정합니다.
1. **스토리지** 에서 서버의 스토리지 대상을 지정합니다.
1. **디스크 형식** 에서 디스크 유형 및 크기를 지정합니다.
1. **네트워크 매핑** 에서 서버가 연결할 네트워크를 지정합니다. 메타데이터를 Azure Migrate로 전송하려면 네트워크가 인터넷에 연결되어야 합니다.
1. 설정을 검토하고 확인한 다음 **마침** 을 선택합니다.

#### <a name="verify-appliance-access-to-azure"></a>Azure에 대한 어플라이언스 액세스 확인

어플라이언스 서버에서 [퍼블릭 클라우드](migrate-appliance.md#public-cloud-urls) 및 [정부 클라우드](migrate-appliance.md#government-cloud-urls)의 Azure URL에 연결할 수 있는지 확인합니다.

### <a name="configure-the-appliance"></a>어플라이언스 구성

어플라이언스를 처음으로 설정하려면 다음과 같이 합니다.

> [!NOTE]
> 다운로드한 OVA 대신 [PowerShell 스크립트](deploy-appliance-script.md)를 사용하여 어플라이언스를 설정하는 경우에는 처음 두 단계를 건너뛰어도 됩니다.

1. vSphere Client에서 서버를 마우스 오른쪽 단추로 클릭한 다음, **콘솔 열기** 를 선택합니다.
1. 어플라이언스에 대한 언어, 표준 시간대 및 암호를 선택합니다.
1. 어플라이언스에 연결할 수 있는 컴퓨터에서 브라우저를 엽니다. 그런 다음, 어플라이언스 구성 관리자의 URL(`https://appliance name or IP address: 44368`)을 엽니다.

     또는 어플라이언스 서버 바탕 화면에서 구성 관리자 바로 가기를 선택하여 구성 관리자를 열 수도 있습니다.
1. 사용 조건에 동의하고 타사 정보를 읽어봅니다.
1. 구성 관리자에서 **필수 구성 요소 설정** 을 선택한 후 다음 단계를 완료합니다.
    1. **연결**: 어플라이언스가 서버의 인터넷 연결을 확인합니다. 서버에서 프록시를 사용하는 경우:
        1. **프록시 설정** 을 선택하여 프록시 주소(`http://ProxyIPAddress` 또는 `http://ProxyFQDN` 형식으로, 여기서 *FQDN* 은 *정규화된 도메인 이름* 을 가리킴) 및 수신 대기 포트를 지정합니다.
        1.  프록시에 인증이 필요한 경우 자격 증명을 입력합니다.
        1. 프록시 세부 정보를 추가하거나 프록시 또는 인증을 사용하지 않도록 설정한 경우 **저장** 을 클릭하여 연결을 트리거하고 연결을 다시 확인합니다.

            HTTP 프록시만 지원됩니다.
    1. **시간 동기화**: 검색이 제대로 작동할 수 있도록 어플라이언스의 시간이 인터넷 시간과 동기화되어있는지 확인합니다.
    1. **업데이트 설치**: 어플라이언스에서 최신 업데이트가 설치되어 있는지 확인합니다. 확인이 끝나면 **어플라이언스 서비스 보기** 를 선택하여 어플라이언스 서버에서 실행되는 서비스의 상태와 버전을 확인할 수 있습니다.
    1. **VDDK 설치**: 어플라이언스에서 VMware vSphere VDDK(Virtual Disk Development Kit)가 설치되어 있는지 확인합니다. VDDK가 설치되지 않은 경우, VMware에서 VDDK 6.7을 다운로드합니다. *설치 지침* 에 따라 다운로드한 zip 파일 콘텐츠를 어플라이언스의 지정된 위치에 추출합니다.

        Azure Migrate 서버 마이그레이션에서 VDDK를 사용하여 Azure로 마이그레이션하는 동안 서버를 복제합니다. 
1. 어플라이언스를 구성하는 동안 언제든지 *필수 구성 요소를 다시 실행* 하여 어플라이언스에서 모든 필수 구성 요소를 충족하는지 확인할 수 있습니다.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="어플라이언스 구성 관리자에서 필수 구성 요소를 설정하는 것을 보여주는 스크린샷":::

#### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. 포털에서 복사한 프로젝트 키를 붙여 넣습니다. 키가 없는 경우, **Azure Migrate: 검색 및 평가** > **검색** > **기존 어플라이언스 관리** 로 이동합니다. 프로젝트 키를 생성할 때 지정한 어플라이언스 이름을 선택한 다음, 표시된 키를 복사합니다.
1. Azure에 인증하려면 디바이스 코드가 필요합니다. **로그인** 을 선택합니다. **Azure 로그인 계속** 에서 **코드 복사 및 로그인** 을 클릭하여 디바이스 코드를 복사하고 새 브라우저 탭에서 Azure 로그인 프롬프트를 엽니다. 브라우저에서 팝업 차단 기능을 사용하지 않도록 설정해야만 프롬프트가 표시됩니다.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="디바이스 코드를 복사하고 로그인하는 위치를 보여주는 스크린샷":::

1. 브라우저의 새 탭에서 디바이스 코드를 붙여 넣고 Azure 사용자 이름과 암호를 사용하여 로그인합니다. PIN을 사용한 로그인은 지원되지 않습니다.

    실수로 로그인하지 않고 로그인 탭을 닫은 경우에는 어플라이언스 구성 관리자의 브라우저 탭을 새로 고쳐 디바이스 코드와 **코드 복사 및 로그인** 단추를 표시합니다.
1. 성공적으로 로그인한 후에는 어플라이언스 구성 관리자가 표시된 브라우저 탭으로 돌아갑니다. 로그인하는 데 사용한 Azure 사용자 계정에 키 생성 중에 만든 Azure 리소스에 필요한 권한이 있으면 어플라이언스 등록이 시작됩니다.
1. 어플라이언스가 성공적으로 등록되었으면 **세부 정보 보기** 를 클릭하여 등록 세부 정보를 확인할 수 있습니다.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="어플라이언스가 성공적으로 등록되었음을 보여주는 Azure Migrate에 등록 창의 스크린샷":::

## <a name="start-continuous-discovery"></a>연속 검색 시작

어플라이언스 구성 관리자에서 설정 단계를 완료하여 검색을 준비하고 시작합니다.

### <a name="provide-vcenter-server-details"></a>vCenter Server 세부 정보 입력

어플라이언스가 서버의 구성 및 성능 데이터를 검색하려면 vCenter Server에 연결해야 합니다.

1. **1단계: vCenter Server 자격 증명 제공** 에서 **자격 증명 추가** 를 선택하여 자격 증명의 이름을 입력합니다. 어플라이언스가 vCenter Server에서 실행되는 서버를 검색하는 데 사용할 vCenter Server 계정의 사용자 이름과 암호를 추가합니다.
    - 이 문서의 앞부분에서 설명된 대로 필요한 권한으로 계정을 설정했어야 합니다.
    - 검색 범위를 특정 VMware 개체(vCenter Server 데이터 센터, 클러스터, 호스트, 클러스터 또는 호스트의 폴더 또는 개별 서버)로 지정하려면 지침에 따라 [검색 범위를 설정](set-discovery-scope.md)하여 Azure Migrate에서 사용하는 계정을 제한합니다.
1. **2단계: vCenter Server 세부 정보 제공** 에서 **검색 원본 추가** 를 선택하고 드롭다운 목록에서 자격 증명의 이름을 선택합니다. vCenter Server의 IP 주소 또는 FQDN을 선택합니다. 기본 포트(443)를 그대로 유지하거나 vCenter Server에서 수신 대기하는 포트를 사용자 지정할 수 있습니다. **저장** 을 선택합니다.
1. 어플라이언스는 자격 증명을 사용하여 vCenter Server를 실행하는 서버에 대한 연결의 유효성을 검사하려고 시도합니다. 자격 증명 테이블에 vCenter Server IP 주소 또는 FQDN에 대한 유효성 검사 상태를 표시합니다.
1. 검색을 시작하기 전에 언제든지 vCenter Server에 대한 연결의 *유효성을 다시 검사* 할 수 있습니다.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="어플라이언스 구성 관리자에서 vCenter Server에 대한 자격 증명 및 검색 원본을 관리하는 것을 보여주는 스크린샷":::

### <a name="provide-server-credentials"></a>서버 자격 증명 입력

**3단계: 소프트웨어 인벤토리, 에이전트 없는 종속성 분석, SQL Server 인스턴스 및 데이터베이스 검색, VMware 환경에서 ASP.NET 웹앱 검색을 수행하는 데 필요한 서버 자격 증명 제공** 에서 여러 서버 자격 증명을 제공할 수 있습니다. 이러한 어플라이언스 기능을 사용하지 않으려면 이 단계를 건너뛰고 vCenter Server 검색으로 진행하면 됩니다. 이 옵션은 언제든지 변경할 수 있습니다.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="소프트웨어 인벤토리, 종속성 분석 및 SQL Server 검색을 위한 자격 증명 제공을 보여주는 스크린샷":::

이러한 기능을 사용하려면 아래 단계에 따라 서버 자격 증명을 제공합니다. 어플라이언스가 검색 기능을 수행하기 위해 자격 증명을 서버에 자동으로 매핑하려고 시도합니다.

서버 자격 증명을 추가하려면 다음과 같이 합니다.

1. **자격 증명 추가** 를 선택합니다.
1. 드롭다운 메뉴에서 **자격 증명 유형** 을 선택합니다.
    
    도메인/Windows(도메인 아님)/Linux(도메인 아님)/SQL Server 인증 자격 증명을 제공할 수 있습니다. [자격 증명을 제공](add-server-credentials.md)하는 방법과 자격 증명이 어떻게 처리되는지 알아보세요.
1. 각 유형의 자격 증명에 대해 다음을 입력합니다.
    * 식별 이름
    * ‘사용자 이름’
    * 암호입니다.
    **저장** 을 선택합니다.

    도메인 자격 증명을 선택하는 경우 도메인에 대한 FQDN도 입력해야 합니다. FQDN은 해당 도메인의 Active Directory 인스턴스에서 자격 증명의 신뢰성에 대한 유효성을 검사하는 데 필요합니다.
1. 설치된 애플리케이션의 검색, 에이전트 없는 종속성 분석, 그리고 웹앱, SQL Server 인스턴스 및 데이터베이스의 검색을 위해 계정에 [필요한 권한](add-server-credentials.md#required-permissions)을 검토합니다.
1. 여러 자격 증명을 한 번에 추가하려면 **더 추가** 를 선택하여 더 많은 자격 증명을 저장하고 추가합니다.
    **저장** 또는 **더 추가** 를 선택하면 어플라이언스가 인증을 위해 도메인의 Active Directory 인스턴스에서 도메인 자격 증명의 유효성을 검사합니다. 어플라이언스가 반복되어 자격 증명을 각 서버에 매핑할 때 계정 잠금을 방지하기 위해 각 추가 후에 유효성 검사가 이루어집니다.

도메인 자격 증명의 유효성 검사를 확인하려면 다음과 같이 합니다.

구성 관리자의 자격 증명 테이블에서 도메인 자격 증명에 대한 **유효성 검사 상태** 를 참조하세요. 도메인 자격 증명만 유효성 검사가 수행됩니다.

유효성 검사에 실패하면 **실패** 상태를 선택하여 유효성 검사 오류를 확인할 수 있습니다. 문제를 해결한 다음, **자격 증명 유효성 다시 검사** 를 선택하여 자격 증명의 유효성 검사를 다시 시도합니다.

:::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="여러 자격 증명을 제공하고 유효성을 검사하는 것을 보여주는 스크린샷":::

### <a name="start-discovery"></a>검색 시작

vCenter Server 검색을 시작하려면 **검색 시작** 을 선택합니다. 검색이 성공적으로 시작되면 원본 테이블에서 vCenter Server IP 주소/FQDN에 대한 검색 상태를 확인할 수 있습니다.

## <a name="how-discovery-works"></a>검색의 작동 원리

* 검색된 서버의 인벤토리가 Azure Portal에 표시되는 데 약 15분 정도 걸립니다.
* 서버 자격 증명을 제공한 경우 vCenter Server를 실행하는 서버의 검색이 완료된 후 소프트웨어 인벤토리(설치된 애플리케이션의 검색)가 자동으로 시작됩니다. 소프트웨어 인벤토리는 12시간마다 한 번씩 수행됩니다.
* [소프트웨어 인벤토리](how-to-discover-applications.md)는 서버에서 실행되는 SQL Server 인스턴스를 식별합니다. 어플라이언스는 수집한 정보를 사용하여 어플라이언스에서 제공된 SQL Server 인증 자격 증명 또는 Windows 인증 자격 증명을 통해 SQL Server 인스턴스에 연결하려고 시도합니다. 그런 다음, SQL Server 데이터베이스 및 해당 속성에 대한 데이터를 수집합니다. SQL 검색은 24시간마다 한 번씩 수행됩니다.
* 어플라이언스는 네트워크에 대한 가시성이 확보된 SQL Server에만 연결할 수 있지만, 소프트웨어 인벤토리 자체에는 네트워크에 대한 가시성이 필요하지 않을 수 있습니다.
* 설치된 애플리케이션을 검색하는 데 15분 이상 걸릴 수 있습니다. 소요 시간은 검색할 서버 수에 따라 달라집니다. 서버가 500개인 경우 검색된 인벤토리가 포털의 Azure Migrate 프로젝트에 표시될 때까지 약 1시간 정도 걸립니다.
* [소프트웨어 인벤토리](how-to-discover-applications.md)는 검색된 서버의 기존 웹 서버 역할을 식별합니다. 서버에서 웹 서버 역할을 사용한 것으로 확인되면 Azure Migrate는 서버에서 웹앱 검색을 수행합니다. 웹앱 구성 데이터는 24시간마다 한 번 업데이트됩니다.
* 소프트웨어 인벤토리 중에 추가된 서버 자격 증명은 서버에 대해 반복되고, 에이전트 없는 종속성 분석을 위해 유효성이 검사됩니다. 서버 검색이 완료되면 포털에서 서버에 에이전트 없는 종속성 분석을 사용하도록 설정할 수 있습니다. 유효성 검사에 성공한 서버만 선택하여 [에이전트 없는 종속성 분석](how-to-create-group-machine-dependencies-agentless.md)을 사용하도록 설정할 수 있습니다.
* ASP.NET 웹앱, SQL Server 인스턴스 및 데이터베이스 데이터는 검색을 시작한 후 24시간 이내에 포털에 표시되기 시작합니다.
* 기본적으로 Azure Migrate는 SQL 인스턴스에 연결하는 가장 안전한 방법을 사용합니다. 즉, Azure Migrate는 TrustServerCertificate 속성을 `true`로 설정하여 Azure Migrate 어플라이언스와 원본 SQL Server 인스턴스 간의 통신을 암호화합니다. 또한 전송 계층은 SSL을 사용하여 채널을 암호화하고 인증서 체인을 건너뛰어 신뢰 유효성을 검사합니다. 따라서 어플라이언스 서버는 인증서의 루트 인증 기관을 신뢰하도록 설정되어야 합니다. 그러나 어플라이언스에서 **SQL Server 연결 속성 편집** 을 선택하여 연결 설정을 수정할 수 있습니다. 선택할 항목을 이해하는 방법에 대해 [자세히 알아보세요](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

    :::image type="content" source="./media/tutorial-discover-vmware/sql-connection-properties.png" alt-text="SQL Server 연결 속성을 편집하는 방법을 보여 주는 스크린샷":::

vCenter Server 검색을 시작하려면 **검색 시작** 을 선택합니다. 검색이 성공적으로 시작되면 원본 테이블에서 vCenter Server IP 주소/FQDN에 대한 검색 상태를 확인할 수 있습니다.

### <a name="view-discovered-data"></a>검색된 데이터 보기

1. Azure Migrate 포털로 이동합니다.
1. 발견된 데이터를 보려면 아래 스크린샷에 표시된 대로 새로 고침을 클릭합니다.
    :::image type="content" source="./media/tutorial-discover-vmware/discovery-assessment-tile.png" alt-text="검색 및 평가 타일에서 데이터를 새로 고치는 방법을 보여주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [Azure VM으로 마이그레이션하기 위해 서버를 평가](./tutorial-assess-vmware-azure-vm.md)하는 방법을 알아봅니다.
- [Azure SQL로 마이그레이션하기 위해 SQL Server를 실행하는 서버를 평가](./tutorial-assess-sql.md)하는 방법을 알아봅니다.
- [Azure App Service로 마이그레이션할 웹앱을 평가](./tutorial-assess-webapps.md)하는 방법을 알아봅니다.
- [검색 중에 Azure Migrate 어플라이언스에서 수집하는 데이터](migrate-appliance.md#collected-data---vmware)를 검토합니다.