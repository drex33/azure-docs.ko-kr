---
title: Azure Migrate 어플라이언스
description: Azure Migrate 어플라이언스에 대한 지원 요약을 제공합니다.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: d8aa5f6bf955bcc3ee3fa86c17375d47402628ee
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528246"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 어플라이언스

이 문서에서는 Azure Migrate 어플라이언스의 필수 구성 요소 및 지원 요구 사항을 요약하고 있습니다.

## <a name="deployment-scenarios"></a>배포 시나리오

Azure Migrate 어플라이언스가 사용되는 시나리오는 다음과 같습니다.

**시나리오** | **도구** | **용도**
--- | --- | ---
**VMware 환경에서 실행되는 서버 검색 및 평가** | Azure Migrate: 검색 및 평가 | VMware 환경 내에서 실행되는 서버 검색<br/><br/> 설치된 소프트웨어 인벤토리, ASP.NET 웹앱, SQL Server 인스턴스 및 데이터베이스, 에이전트 없는 종속성 분석을 검색합니다.<br/><br/> 평가를 위해 서버 구성 및 성능 메타데이터를 수집합니다.
**VMware 환경에서 실행되는 에이전트 없는 서버 마이그레이션** | Azure Migrate: Server Migration | VMware 환경 내에서 실행되는 서버를 검색합니다. <br/><br/> 서버에 에이전트를 설치하지 않고 서버를 복제합니다.
**Hyper-V 환경에서 실행되는 서버 검색 및 평가** | Azure Migrate: 검색 및 평가 | Hyper-V 환경 내에서 실행되는 서버를 검색합니다.<br/><br/> 평가를 위해 서버 구성 및 성능 메타데이터를 수집합니다.
**온-프레미스에서 물리적 또는 가상화된 서버 검색 및 평가** |  Azure Migrate: 검색 및 평가 |  온-프레미스에서 물리적 또는 가상화된 서버를 검색합니다.<br/><br/> 평가를 위해 서버 구성 및 성능 메타데이터를 수집합니다.

## <a name="deployment-methods"></a>배포 방법

어플라이언스는 다음과 같은 몇 가지 방법을 사용하여 배포할 수 있습니다.

- VMware 또는 Hyper-V 환경([VMware용 OVA 템플릿](how-to-set-up-appliance-vmware.md) 또는 [Hyper-V용 VHD](how-to-set-up-appliance-hyper-v.md))에서 실행되는 서버용 템플릿을 사용하여 이 어플라이언스를 배포할 수 있습니다.
- 템플릿을 사용하지 않으려면 [PowerShell 설치 프로그램 스크립트](deploy-appliance-script.md)를 사용하여 VMware 또는 Hyper-V용 어플라이언스 환경을 배포할 수 있습니다.
- Microsoft Azure Government에서는 PowerShell 설치 프로그램 스크립트를 사용하여 어플라이언스를 배포해야 합니다. [여기](deploy-appliance-script-government.md)에서 배포 단계를 참조하세요.
- 온-프레미스 또는 다른 클라우드의 물리적 또는 가상화된 서버에 대해서는 항상 PowerShell 설치 프로그램 스크립트를 사용하여 어플라이언스를 배포합니다. [여기](how-to-set-up-appliance-physical.md)에서 배포 단계를 참조하세요.
- 다운로드 링크는 아래 표에 나와 있습니다.

## <a name="appliance---vmware"></a>어플라이언스 - VMware

다음 표에는 VMware에 대한 Azure Migrate 어플라이언스 요구 사항이 요약되어 있습니다.

**요구 사항** | **VMware**
--- | ---
**권한** | 어플라이언스 구성 관리자를 로컬이나 원격으로 액세스하려면 어플라이언스 서버에 대한 관리 권한이 있는 로컬 또는 도메인 사용자 계정이 있어야 합니다.
**어플라이언스 서비스** | 어플라이언스에는 다음 서비스가 있습니다.<br/><br/> - **어플라이언스 구성 관리자**: 서버 검색 및 평가를 시작하기 위해 원본 세부 정보로 구성할 수 있는 웹 애플리케이션입니다.<br/> - **VMware 검색 에이전트**: 이 에이전트는 온-프레미스 평가로 만드는 데 사용할 수 있는 서버 구성 메타데이터를 수집합니다.<br/>- **VMware 평가 에이전트**: 이 에이전트는 성능 기반 평가를 만드는 데 사용할 수 있는 서버 성능 메타데이터를 수집합니다.<br/>- **자동 업데이트 서비스**: 이 서비스는 어플라이언스에서 실행되는 모든 에이전트를 최신 상태로 유지합니다. 자동으로 24시간마다 한 번씩 실행됩니다.<br/>- **DRA 에이전트**: VM 복제를 오케스트레이션하고, 복제된 서버와 Azure 간의 통신을 조정합니다. 에이전트 없는 마이그레이션을 사용하여 서버를 Azure에 복제할 때만 사용됩니다.<br/>- **게이트웨이**: 복제된 데이터를 Azure에 보냅니다. 에이전트 없는 마이그레이션을 사용하여 서버를 Azure에 복제할 때만 사용됩니다.<br/>- **SQL 검색 및 평가 에이전트**: SQL Server 인스턴스 및 데이터베이스의 구성 및 성능 메타데이터를 Azure에 보냅니다.<br/>- **웹앱 검색 및 평가 에이전트**: 웹앱 구성 데이터를 Azure에 보냅니다.
**프로젝트 제한** |  한 어플라이언스는 단일 프로젝트에만 등록할 수 있습니다.<br/> 단일 프로젝트에 등록된 기기가 여러 개 있을 수 있습니다.
**검색 제한** | 어플라이언스는 vCenter Server에서 실행되는 서버를 최대 10,000대까지 검색할 수 있습니다.<br/> 어플라이언스는 단일 vCenter Server에 연결할 수 있습니다.
**지원되는 배포** | OVA 템플릿을 사용하여 vCenter Server에서 실행되는 새 서버로 배포합니다.<br/><br/> PowerShell 설치 프로그램 스크립트를 사용하여 Windows Server 2016을 실행하는 기존 서버에 배포합니다.
**OVA 템플릿** | 프로젝트 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2140333)에서 다운로드<br/><br/> 다운로드 크기는 11.9GB입니다.<br/><br/> 다운로드한 어플라이언스 템플릿에는 180일 동안 유효한 Windows Server 2016 평가 라이선스가 제공됩니다.<br/>평가 기간이 곧 만료되는 경우 OVA 템플릿을 사용하여 새 어플라이언스를 다운로드하고 배포하거나 어플라이언스 서버의 운영 체제 라이선스를 활성화하는 것이 좋습니다.
**OVA 확인** | 해시 값을 확인하여 프로젝트에서 다운로드한 OVA 템플릿을 [확인](tutorial-discover-vmware.md#verify-security)합니다.
**PowerShell 스크립트** | PowerShell 설치 프로그램 스크립트를 사용하여 어플라이언스를 배포하는 방법은 이 [문서](./deploy-appliance-script.md#set-up-the-appliance-for-vmware)를 참조하세요.<br/><br/> 
**하드웨어 및 네트워크 요구 사항** |  어플라이언스는 Windows Server 2016, 32GB RAM, 8개의 vCPU, 약 80GB의 디스크 스토리지 및 외부 가상 스위치가 있는 서버에서 실행해야 합니다.<br/> 어플라이언스는 직접 또는 프록시를 통해 인터넷에 액세스해야 합니다.<br/><br/> OVA 템플릿을 사용하여 어플라이언스를 배포하는 경우 vCenter Server에 충분한 리소스가 있어야 하드웨어 요구 사항을 충족하는 서버를 만들 수 있습니다.<br/><br/> 기존 서버에서 어플라이언스를 실행하는 경우 어플라이언스가 Windows Server 2016을 실행하고 하드웨어 요구 사항을 충족하는지 확인합니다.<br/>_(현재 어플라이언스 배포는 Windows Server 2016에서만 지원됩니다.)_
**VMware 요구 사항** | VCenter Server에서 어플라이언스를 서버로 배포하는 경우 5.5, 6.0, 6.5, 6.7 또는 7.0 버전을 실행하는 vCenter Server 및 5.5 버전 이상을 실행하는 ESXi 호스트에 배포해야 합니다.<br/><br/> 
**VDDK(에이전트 없는 마이그레이션)** | 서버의 에이전트 없는 마이그레이션을 위해 어플라이언스를 사용하려면 어플라이언스 서버에 VMware vSphere VDDK를 설치해야 합니다.

## <a name="appliance---hyper-v"></a>어플라이언스 - Hyper-V

**요구 사항** | **Hyper-V**
--- | ---
**권한** | 어플라이언스 구성 관리자를 로컬이나 원격으로 액세스하려면 어플라이언스 서버에 대한 관리 권한이 있는 로컬 또는 도메인 사용자 계정이 있어야 합니다.
**어플라이언스 서비스** | 어플라이언스에는 다음 서비스가 있습니다.<br/><br/> - **어플라이언스 구성 관리자**: 서버 검색 및 평가를 시작하기 위해 원본 세부 정보로 구성할 수 있는 웹 애플리케이션입니다.<br/> - **검색 에이전트**: 이 에이전트는 온-프레미스 평가로 만드는 데 사용할 수 있는 서버 구성 메타데이터를 수집합니다.<br/>- **평가 에이전트**: 이 에이전트는 성능 기반 평가를 만드는 데 사용할 수 있는 서버 성능 메타데이터를 수집합니다.<br/>- **자동 업데이트 서비스**: 이 서비스는 어플라이언스에서 실행되는 모든 에이전트를 최신 상태로 유지합니다. 자동으로 24시간마다 한 번씩 실행됩니다.
**프로젝트 제한** |  한 어플라이언스는 단일 프로젝트에만 등록할 수 있습니다.<br/> 단일 프로젝트에 등록된 기기가 여러 개 있을 수 있습니다.
**검색 제한** | 어플라이언스는 Hyper-v 환경에서 실행되는 서버를 최대 5000개까지 검색할 수 있습니다.<br/> 어플라이언스는 최대 300개의 Hyper-V 호스트에 연결할 수 있습니다.
**지원되는 배포** | VHD 템플릿을 사용하여 Hyper-V 호스트에서 실행되는 서버로 배포합니다.<br/><br/> PowerShell 설치 프로그램 스크립트를 사용하여 Windows Server 2016을 실행하는 기존 서버에 배포합니다.
**VHD 템플릿** | VHD를 포함하는 Zip 파일입니다. 프로젝트 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2140422)에서 다운로드합니다.<br/><br/> 다운로드 크기는 8.91GB입니다.<br/><br/> 다운로드한 어플라이언스 템플릿에는 180일 동안 유효한 Windows Server 2016 평가 라이선스가 제공됩니다.<br/> 평가 기간이 곧 만료되는 경우 새 어플라이언스를 다운로드하여 배포하거나 어플라이언스 서버의 운영 체제 라이선스에 대한 정품 인증을 수행하는 것이 좋습니다.
**VHD 확인** | 해시 값을 확인하여 프로젝트에서 다운로드한 VHD 템플릿을 [확인](tutorial-discover-hyper-v.md#verify-security)합니다.
**PowerShell 스크립트** | PowerShell 설치 프로그램 스크립트를 사용하여 어플라이언스를 배포하는 방법은 이 [문서](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)를 참조하세요.<br/>
**하드웨어 및 네트워크 요구 사항**  |  어플라이언스는 Windows Server 2016, 16GB RAM, 8개의 vCPU, 약 80GB의 디스크 스토리지 및 외부 가상 스위치가 있는 서버에서 실행해야 합니다.<br/> 어플라이언스는 고정 또는 동적 IP 주소가 필요하며, 직접 또는 프록시를 통해 인터넷에 액세스해야 합니다.<br/><br/> Hyper-V 호스트에서 실행되는 서버로 어플라이언스를 실행하는 경우 호스트에 충분한 리소스가 있어야 하드웨어 요구 사항을 충족하는 서버를 만들 수 있습니다.<br/><br/> 기존 서버에서 어플라이언스를 실행하는 경우 어플라이언스가 Windows Server 2016을 실행하고 하드웨어 요구 사항을 충족하는지 확인합니다.<br/>_(현재 어플라이언스 배포는 Windows Server 2016에서만 지원됩니다.)_
**Hyper-V 요구 사항** | VHD 템플릿을 사용하여 어플라이언스를 배포하는 경우 Azure Migrate에서 제공하는 어플라이언스는 Hyper-V VM 버전 5.0입니다.<br/><br/> Hyper-V 호스트에서 Windows Server 2012 R2 이상을 실행해야 합니다.

## <a name="appliance---physical"></a>어플라이언스 - 물리적 서버

**요구 사항** | **물리적 서버**
--- | ---
**권한** | 어플라이언스 구성 관리자를 로컬이나 원격으로 액세스하려면 어플라이언스 서버에 대한 관리 권한이 있는 로컬 또는 도메인 사용자 계정이 있어야 합니다.
**어플라이언스 서비스** | 어플라이언스에는 다음 서비스가 있습니다.<br/><br/> - **어플라이언스 구성 관리자**: 서버 검색 및 평가를 시작하기 위해 원본 세부 정보로 구성할 수 있는 웹 애플리케이션입니다.<br/> - **검색 에이전트**: 이 에이전트는 온-프레미스 평가로 만드는 데 사용할 수 있는 서버 구성 메타데이터를 수집합니다.<br/>- **평가 에이전트**: 이 에이전트는 성능 기반 평가를 만드는 데 사용할 수 있는 서버 성능 메타데이터를 수집합니다.<br/>- **자동 업데이트 서비스**: 이 서비스는 어플라이언스에서 실행되는 모든 에이전트를 최신 상태로 유지합니다. 자동으로 24시간마다 한 번씩 실행됩니다.
**프로젝트 제한** |  한 어플라이언스는 단일 프로젝트에만 등록할 수 있습니다.<br/> 단일 프로젝트에 등록된 기기가 여러 개 있을 수 있습니다.<br/>
**검색 제한** | 어플라이언스는 최대 1,000개의 물리적 서버를 검색할 수 있습니다.
**지원되는 배포** | PowerShell 설치 프로그램 스크립트를 사용하여 Windows Server 2016을 실행하는 기존 서버에 배포합니다.
**PowerShell 스크립트** | 스크립트(AzureMigrateInstaller.ps1)를 프로젝트 또는 [여기서](https://go.microsoft.com/fwlink/?linkid=2140334) zip 파일로 다운로드합니다. [자세히 알아봅니다](tutorial-discover-physical.md).<br/><br/> 다운로드 크기는 85.8MB입니다.
**스크립트 확인** | 해시 값을 확인하여 프로젝트에서 다운로드한 PowerShell 설치 프로그램 스크립트를 [확인](tutorial-discover-physical.md#verify-security)합니다.
**하드웨어 및 네트워크 요구 사항** |  어플라이언스는 Windows Server 2016, 16GB RAM, 8개의 vCPU, 약 80GB의 디스크 스토리지가 있는 서버에서 실행해야 합니다.<br/> 어플라이언스는 고정 또는 동적 IP 주소가 필요하며, 직접 또는 프록시를 통해 인터넷에 액세스해야 합니다.<br/><br/> 기존 서버에서 어플라이언스를 실행하는 경우 어플라이언스가 Windows Server 2016을 실행하고 하드웨어 요구 사항을 충족하는지 확인합니다.<br/>_(현재 어플라이언스 배포는 Windows Server 2016에서만 지원됩니다.)_

## <a name="url-access"></a>URL 액세스

Azure Migrate 어플라이언스는 인터넷에 연결해야 합니다.

- 어플라이언스를 배포하는 경우 Azure Migrate에서 필요한 URL에 대한 연결을 확인합니다.
- 목록의 모든 URL에 대한 액세스를 허용해야 합니다. 평가만 수행하는 경우 VMware 에이전트 없는 마이그레이션에 필요한 것으로 표시된 URL을 건너뛸 수 있습니다.
- URL 기반 프록시를 사용하여 인터넷에 연결하는 경우 프록시에서 URL을 조회하는 동안 받은 모든 CNAME 레코드를 확인하는지 확인합니다.

### <a name="public-cloud-urls"></a>퍼블릭 클라우드 URL

**URL** | **세부 정보**  
--- | --- |
*.portal.azure.com  | Azure Portal로 이동합니다.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com <br/> *.microsoftonline.com <br/> *.microsoftonline-p.com  <br/> *.microsoftazuread-sso.com | Azure Active Directory에서 액세스 제어와 ID 관리에 사용됩니다.
management.azure.com | 리소스 배포와 관리 작업에 사용됩니다.
*.services.visualstudio.com | 내부 모니터링에 사용되는 어플라이언스 로그를 업로드합니다.
*.vault.azure.net | Azure Key Vault에서 비밀을 관리합니다.<br/> 참고: 복제할 서버에서 이 URL에 액세스할 수 있는지 확인합니다.
aka.ms/* | 다음 링크에 대한 액세스를 허용합니다. 어플라이언스 서비스의 최신 업데이트를 다운로드하고 설치하는 데 사용됩니다.
download.microsoft.com/download | Microsoft 다운로드 센터에서 다운로드할 수 있습니다.
\*.servicebus.windows.net | 어플라이언스와 Azure Migrate 서비스 간의 통신입니다.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Azure Migrate 서비스 URL에 연결합니다.
\*.hypervrecoverymanager.windowsazure.com | **VMware 에이전트 없는 마이그레이션에 사용** 됩니다.<br/><br/> Azure Migrate 서비스 URL에 연결합니다.
\*.blob.core.windows.net |  **VMware 에이전트 없는 마이그레이션에 사용** 됩니다.<br/><br/>마이그레이션을 위해 데이터를 스토리지에 업로드합니다.

### <a name="government-cloud-urls"></a>Government 클라우드 URL

**URL** | **세부 정보**  
--- | --- |
*.portal.azure.us  | Azure Portal로 이동합니다.
graph.windows.net | Azure 구독에 로그인합니다.
login.microsoftonline.us  | Azure Active Directory에서 액세스 제어와 ID 관리에 사용됩니다.
management.usgovcloudapi.net |  리소스 배포와 관리 작업에 사용됩니다.
*.services.visualstudio.com | 내부 모니터링에 사용되는 어플라이언스 로그를 업로드합니다.
*.vault.usgovcloudapi.net | Azure Key Vault에서 비밀을 관리합니다.
aka.ms/* | 다음 링크에 대한 액세스를 허용합니다. 어플라이언스 서비스의 최신 업데이트를 다운로드하고 설치하는 데 사용됩니다.
download.microsoft.com/download | Microsoft 다운로드 센터에서 다운로드할 수 있습니다.
*.servicebus.usgovcloudapi.net  | 어플라이언스와 Azure Migrate 서비스 간의 통신입니다.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Azure Migrate 서비스 URL에 연결합니다.
*. hypervrecoverymanager.windowsazure.us | **VMware 에이전트 없는 마이그레이션에 사용** 됩니다.<br/><br/> Azure Migrate 서비스 URL에 연결합니다.
*.blob.core.usgovcloudapi.net  |  **VMware 에이전트 없는 마이그레이션에 사용** 됩니다.<br/><br/>마이그레이션을 위해 데이터를 스토리지에 업로드합니다.
*.applicationinsights.us | 내부 모니터링에 사용되는 어플라이언스 로그를 업로드합니다.  

### <a name="public-cloud-urls-for-private-link-connectivity"></a>프라이빗 링크 연결에 대한 퍼블릭 클라우드 URL

어플라이언스는 프라이빗 링크 액세스를 통해 다음 URL(직접 또는 프록시를 통해)에 액세스해야 합니다.

**URL** | **세부 정보**  
--- | --- |
*.portal.azure.com  | Azure Portal로 이동합니다.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com <br/> *.microsoftonline.com <br/> *.microsoftonline-p.com <br/> *.microsoftazuread-sso.com  | Azure Active Directory에서 액세스 제어와 ID 관리에 사용됩니다.
management.azure.com |  리소스 배포와 관리 작업에 사용됩니다.
*.services.visualstudio.com(선택 사항) | 내부 모니터링에 사용되는 어플라이언스 로그를 업로드합니다.
aka.ms/*(선택 사항) | 다음 링크에 대한 액세스를 허용합니다. 어플라이언스 서비스의 최신 업데이트를 다운로드하고 설치하는 데 사용됩니다.
download.microsoft.com/download | Microsoft 다운로드 센터에서 다운로드할 수 있습니다.
\*.servicebus.windows.net | **VMware 에이전트 없는 마이그레이션에 사용** 됩니다.<br/><br/> 어플라이언스와 Azure Migrate 서비스 간의 통신입니다.
\*.hypervrecoverymanager.windowsazure.com | **VMware 에이전트 없는 마이그레이션에 사용** 됩니다.<br/><br/> Azure Migrate 서비스 URL에 연결합니다.
\*.blob.core.windows.net |  **VMware 에이전트 없는 마이그레이션에 사용** 됩니다.<br/><br/>마이그레이션을 위해 데이터를 스토리지에 업로드합니다. <br/>이는 선택 사항이며 스토리지 계정(캐시 스토리지 계정 및 게이트웨이 스토리지 계정 모두)에 연결된 프라이빗 엔드포인트가 있는 경우에는 필요하지 않습니다.

## <a name="collected-data---vmware"></a>수집되는 데이터 - VMware

어플라이언스는 구성 메타데이터, 성능 메타데이터 및 서버 종속성 데이터를 수집합니다(에이전트 없는 [종속성 분석](concepts-dependency-visualization.md)이 사용되는 경우).

### <a name="metadata"></a>메타데이터

Azure Migrate 어플라이언스에서 검색된 메타데이터를 사용하면 서버를 Azure로 마이그레이션할 준비가 되었는지, 적절한 크기의 서버로 마이그레이션할 준비가 되었는지, 비용 계획을 파악하고 애플리케이션 종속성을 분석할 수 있습니다. Microsoft는 라이선스 규정 준수 감사에서 이 데이터를 사용하지 않습니다.

어플라이언스에서 수집하여 Azure에 보내는 서버 메타데이터의 전체 목록은 다음과 같습니다.

**데이터** | **카운터**
--- | ---
**서버 세부 정보** |
서버 ID입니다. | vm.Config.InstanceUuid
서버 이름 | vm.Config.Name
vCenter Server ID | VMwareClient.Instance.Uuid
서버 설명 | vm.Summary.Config.Annotation
라이선스 제품 이름 | vm.Client.ServiceContent.About.LicenseProductName
운영 체제 유형 | vm.SummaryConfig.GuestFullName
부팅 유형 | vm.Config.Firmware
코어 수 | vm.Config.Hardware.NumCPU
메모리(MB) | vm.Config.Hardware.MemoryMB
디스크 수 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
디스크 크기 목록 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
네트워크 어댑터 목록 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 사용률 | cpu.usage.average
메모리 사용률 |mem.usage.average
**디스크당 세부 정보** |
디스크 키 값 | disk.Key
Dikunit 번호 | disk.UnitNumber
디스크 컨트롤러 키 값 | disk.ControllerKey.Value
프로비전된 기가바이트 | virtualDisk.DeviceInfo.Summary
디스크 이름 | disk.UnitNumber, disk.Key, disk.ControllerKey.VAlue를 사용하여 생성된 값
초당 읽기 작업 | virtualDisk.numberReadAveraged.average
초당 쓰기 작업 | virtualDisk.numberWriteAveraged.average
읽기 처리량(MB/초) | virtualDisk.read.average
쓰기 처리량(MB/초) | virtualDisk.write.average
**NIC당 세부 정보** |
네트워크 어댑터 이름 | nic.Key
MAC 주소 | ((VirtualEthernetCard)nic).MacAddress
IPv4 주소 | vm.Guest.Net
IPv6 주소 | vm.Guest.Net
읽기 처리량(MB/초) | net.received.average
쓰기 처리량(MB/초) | net.transmitted.average
**인벤토리 경로 세부 정보** |
속성 | container.GetType().Name
자식 개체 유형 | container.ChildType
참조 세부 정보 | container.MoRef
부모 세부 정보 | Container.Parent
서버당 폴더 세부 정보 | ((Folder)container).ChildEntity.Type
서버당 데이터 센터 세부 정보 | ((Datacenter)container).VmFolder
호스트 폴더당 데이터 센터 정보 | ((Datacenter)container).HostFolder
호스트당 클러스터 세부 정보 | ((ClusterComputeResource)container).Host
서버당 호스트 세부 정보 | ((HostSystem)container).VM

### <a name="performance-data"></a>성능 데이터

VMware에서 실행되는 서버에 대해 어플라이언스에서 수집하여 Azure에 보내는 성능 데이터는 다음과 같습니다.

**Data** | **카운터** | **평가 영향**
--- | --- | ---
CPU 사용률 | cpu.usage.average | 권장 서버 크기/비용
메모리 사용률 | mem.usage.average | 권장 서버 크기/비용
디스크 읽기 처리량(MB/초) | virtualDisk.read.average | 디스크 크기, 스토리지 비용, 서버 크기 계산
디스크 쓰기 처리량(MB/초) | virtualDisk.write.average | 디스크 크기, 스토리지 비용, 서버 크기 계산
디스크 읽기 작업 수/초 | virtualDisk.numberReadAveraged.average | 디스크 크기, 스토리지 비용, 서버 크기 계산
디스크 쓰기 작업 수/초 | virtualDisk.numberWriteAveraged.average  | 디스크 크기, 스토리지 비용, 서버 크기 계산
NIC 읽기 처리량(MB/초) | net.received.average | 서버 크기 계산
NIC 쓰기 처리량(MB/초) | net.transmitted.average  |서버 크기 계산

### <a name="installed-software-inventory"></a>설치된 소프트웨어 인벤토리

어플라이언스는 서버에 설치된 소프트웨어 인벤토리에 대한 데이터를 수집합니다.

#### <a name="windows-server-software-inventory-data"></a>Windows Server 소프트웨어 인벤토리 데이터

어플라이언스가 VMware 환경에서 검색된 각 Windows Server로부터 수집하는 소프트웨어 인벤토리 데이터는 다음과 같습니다.

**Data** | **레지스트리 위치** | **키**
--- | --- | ---
애플리케이션 이름  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
버전  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion
공급자  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | 게시자

#### <a name="windows-server-features-data"></a>Windows Server 기능 데이터

어플라이언스가 VMware 환경에서 검색된 각 Windows Server로부터 수집하는 기능 데이터는 다음과 같습니다.

**Data**  | **PowerShell cmdlet** | **속성**
--- | --- | ---
이름  | Get-WindowsFeature  | 이름
기능 유형 | Get-WindowsFeature  | FeatureType
Parent  | Get-WindowsFeature  | Parent

#### <a name="sql-server-metadata"></a>SQL Server 메타데이터

어플라이언스가 VMware 환경에서 검색된 각 Windows Server로부터 수집하는 SQL Server 데이터는 다음과 같습니다.

**Data**  | **레지스트리 위치**  | **키**
--- | --- | ---
이름  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
버전  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | 버전
서비스 팩  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | SP
버전  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | 버전

#### <a name="aspnet-web-apps-data"></a>ASP.NET 웹앱 데이터

어플라이언스가 VMware 환경에서 검색된 각 Windows 서버로부터 수집하는 웹앱 구성 데이터는 다음과 같습니다.

**엔터티** | **데이터**
--- | ---
웹앱 | 애플리케이션 이름 <br/>구성 경로 <br/>프런트 엔드 바인딩 <br/>사용으로 설정된 프레임워크 <br/>호스팅 웹 서버<br/>하위 애플리케이션과 가상 애플리케이션 <br/>애플리케이션 풀 이름 <br/>런타임 버전 <br/>관리형 파이프라인 모드
웹 서버 | 서버 이름 <br/>서버 유형(현재 IIS만 해당) <br/>구성 위치 <br/>버전 <br/>FQDN <br/>검색에 사용되는 자격 증명 <br/>애플리케이션 목록

#### <a name="windows-server-operating-system-data"></a>Windows Server 운영 체제 데이터

어플라이언스가 VMware 환경에서 검색된 각 Windows Server로부터 수집하는 운영 체제 데이터는 다음과 같습니다.

**Data**  | **WMI 클래스**  | **WMI 클래스 속성**
--- | --- | ---
이름  | Win32_operatingsystem  | 캡션
버전  | Win32_operatingsystem  | 버전
Architecture  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Linux 서버 소프트웨어 인벤토리 데이터

어플라이언스가 VMware 환경에서 검색된 각 Linux 서버로부터 수집하는 소프트웨어 인벤토리 데이터는 다음과 같습니다. 서버의 운영 체제에 따라 하나 이상의 명령이 실행됩니다.

**Data**  | **명령**
--- | ---
이름 | rpm, dpkg-query, snap
버전 | rpm, dpkg-query, snap
공급자 | rpm, dpkg-query, snap

#### <a name="linux-server-operating-system-data"></a>Linux 서버 운영 체제 데이터

어플라이언스가 VMware 환경에서 검색된 각 Linux Server로부터 수집하는 운영 체제 데이터는 다음과 같습니다.

**Data**  | **명령**
--- | ---
이름 <br/> 버전 | 다음 파일 중 하나 이상에서 수집됩니다.<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version
Architecture | uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server 인스턴스 및 데이터베이스 데이터

어플라이언스는 SQL Server 인스턴스 및 데이터베이스에서 데이터를 수집합니다.

#### <a name="sql-database-metadata"></a>SQL 데이터베이스 메타데이터

**데이터베이스 메타데이터** | **보기/ SQL Server 속성**
--- | ---
데이터베이스의 고유 식별자 | sys.databases
서버 정의 데이터베이스 ID | sys.databases
데이터베이스 이름 | sys.databases
데이터베이스 호환성 수준 | sys.databases
데이터베이스 데이터 정렬 이름 | sys.databases
데이터베이스 상태 | sys.databases
데이터베이스 크기(MB) | sys.master_files
데이터 파일을 포함하는 위치의 드라이브 문자 | SERVERPROPERTY 및 Software\Microsoft\MSSQLServer\MSSQLServer
데이터베이스 파일 목록 | sys.databases, sys.master_files
Service broker 사용 여부 | sys.databases
데이터베이스 변경 데이터 캡처 사용 여부 | sys.databases

#### <a name="sql-server-metadata"></a>SQL Server 메타데이터

**서버 메타데이터** | **보기/ SQL Server 속성**
--- | ---
서버 이름 |SERVERPROPERTY
FQDN | 설치된 애플리케이션의 검색에서 파생된 연결 문자열
ID 설치 | sys.dm_server_registry
서버 버전 | SERVERPROPERTY
서버 버전 | SERVERPROPERTY
서버 호스트 플랫폼(Windows/Linux) | SERVERPROPERTY
서버의 제품 수준(RTM SP CTP) | SERVERPROPERTY
기본 백업 경로 | SERVERPROPERTY
데이터 파일의 기본 경로 | SERVERPROPERTY 및 Software\Microsoft\MSSQLServer\MSSQLServer
로그 파일의 기본 경로 | SERVERPROPERTY 및 Software\Microsoft\MSSQLServer\MSSQLServer
아니요. 서버 코어 | sys.dm_os_schedulers, sys.dm_os_sys_info
Server 데이터 정렬 이름 | SERVERPROPERTY
아니요. 온라인 상태가 표시된 서버 코어 | sys.dm_os_schedulers
고유 서버 ID | sys.dm_server_registry
HA 사용 여부 | SERVERPROPERTY
버퍼 풀 확장 사용 여부 | sys.dm_os_buffer_pool_extension_configuration
장애 조치(Failover) 클러스터 구성 여부 | SERVERPROPERTY
Windows 인증 모드만 사용하는 서버 | SERVERPROPERTY
PolyBase 설치 서버 | SERVERPROPERTY
아니요. 시스템의 논리 CPU 수 | sys.dm_server_registry, sys.dm_os_sys_info
하나의 실제 프로세서 패키지에 표시된 논리적 또는 물리적 코어의 비율 | sys.dm_os_schedulers, sys.dm_os_sys_info
시스템의 실제 CPU 수 | sys.dm_os_schedulers, sys.dm_os_sys_info
서버가 마지막으로 시작한 날짜 및 시간 | sys.dm_server_registry
최대 서버 메모리 사용(MB) | sys.dm_os_process_memory
총 개수 모든 데이터베이스의 사용자 | sys.databases, sys.logins
모든 사용자 데이터베이스의 총 크기 | sys.databases
임시 데이터베이스의 크기 | sys.master_files, sys.configurations sys.dm_os_sys_info
아니요. 로그인 | sys.logins
연결된 서버 목록 | sys.servers
에이전트 작업 목록 | [msdb].[dbo].[sysjobs], [sys].[syslogins], [msdb].[dbo].[syscategories]

### <a name="performance-metadata"></a>성능 메타데이터

**성능** | **보기/ SQL Server 속성** | **평가 영향**
--- | --- | ---
SQL Server CPU 사용률| sys.dm_os_ring_buffers| 권장 SKU 크기(CPU 차원)
SQL 논리 CPU 개수| sys.dm_os_sys_info| 권장 SKU 크기(CPU 차원)
사용 중인 SQL 실제 메모리| sys.dm_os_process_memory| 사용 안 함
SQL 메모리 사용률| sys.dm_os_process_memory | 사용 안 함
데이터베이스 CPU 사용률| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| 권장 SKU 크기(CPU 차원)
사용 중인 데이터베이스 메모리(버퍼 풀)| sys.dm_os_buffer_descriptors| 권장 SKU 크기(메모리 차원)
파일 읽기/쓰기 IO| sys.dm_io_virtual_file_stats, sys.master_files| 권장 SKU 크기(IO 차원)
읽기/쓰기 파일 개수| sys.dm_io_virtual_file_stats, sys.master_files| 권장 SKU 크기(처리량 차원)
파일 IO 정지 읽기/쓰기(ms)| sys.dm_io_virtual_file_stats, sys.master_files| 권장 SKU 크기(IO 대기 시간 차원)
파일 크기| sys.master_files| 권장 SKU 크기(스토리지 차원)


### <a name="application-dependency-data"></a>애플리케이션 종속성 데이터

에이전트 없는 종속성 분석은 연결 및 프로세스 데이터를 수집합니다.

#### <a name="windows-server-dependencies-data"></a>Windows server 종속성 데이터

어플라이언스에서 에이전트 없는 종속성 분석에 사용하도록 설정된 각 Windows Server로부터 수집하는 연결 데이터는 다음과 같습니다.

**Data** | **명령**
--- | ---
로컬 포트 | netstat
로컬 IP 주소 | netstat
원격 포트 | netstat
원격 IP 주소 | netstat
TCP 연결 상태 | netstat
프로세스 ID | netstat
활성 연결 수 | netstat

어플라이언스에서 에이전트 없는 종속성 분석에 사용하도록 설정하여 각 Windows Server로부터 수집하는 연결 데이터는 다음과 같습니다.

**Data** | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
프로세스 이름 | Win32_Process | ExecutablePath
프로세스 인수 | Win32_Process | 명령줄
애플리케이션 이름 | Win32_Process | ExecutablePath 속성의 VersionInfo.ProductName 매개 변수

#### <a name="linux-server-dependencies-data"></a>Linux 서버 종속성 데이터

어플라이언스에서 에이전트 없는 종속성 분석에 사용하도록 설정된 각 Linux 서버로부터 수집하는 연결 데이터는 다음과 같습니다.

**Data** | **명령**
--- | ---
로컬 포트 | netstat
로컬 IP 주소 | netstat
원격 포트 | netstat
원격 IP 주소 | netstat
TCP 연결 상태 | netstat
활성 연결 수 | netstat
프로세스 ID  | netstat
프로세스 이름 | ps
프로세스 인수 | ps
애플리케이션 이름 | dpkg 또는 rpm

## <a name="collected-data---hyper-v"></a>수집되는 데이터 - Hyper-V

어플라이언스는 Hyper-V 환경에서 실행되는 서버에서 구성 및 성능 메타데이터를 수집합니다.

### <a name="metadata"></a>메타데이터
Azure Migrate 어플라이언스에서 검색된 메타데이터를 사용하면 서버를 Azure로 마이그레이션할 준비가 되었는지, 적절한 크기의 서버로 마이그레이션할 준비가 되었는지 파악하여 비용 계획을 세울 수 있습니다. Microsoft는 라이선스 규정 준수 감사에서 이 데이터를 사용하지 않습니다.

어플라이언스에서 수집하여 Azure에 보내는 서버 메타데이터의 전체 목록은 다음과 같습니다.

**Data** | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
**서버 세부 정보** | 
BIOS 일련 번호 | Msvm_BIOSElement | BIOSSerialNumber
서버 유형 (1세대 또는 2세대) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
서버 표시 이름 | Msvm_VirtualSystemSettingData | ElementName
서버 버전 | Msvm_ProcessorSettingData | VirtualQuantity
메모리(바이트) | Msvm_MemorySettingData | VirtualQuantity
서버에서 사용할 수 있는 최대 메모리 | Msvm_MemorySettingData | 제한
동적 메모리 사용 | Msvm_MemorySettingData | DynamicMemoryEnabled
운영 체제 이름/버전/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems 이름 데이터
서버 전원 상태 | Msvm_ComputerSystem | EnabledState
**디스크당 세부 정보** |
디스크 식별자 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
가상 하드 디스크 유형 | Msvm_VirtualHardDiskSettingData | Type
가상 하드 디스크 크기 | Msvm_VirtualHardDiskSettingData | MaxInternalSize
가상 하드 디스크 부모 | Msvm_VirtualHardDiskSettingData | ParentPath
**NIC당 세부 정보** |
IP 주소(가상 NIC) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP 사용(가상 NIC) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID(가상 NIC) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 주소(가상 NIC) | Msvm_SyntheticEthernetPortSettingData | 주소
NIC ID(레거시 NIC) | MsvmEmulatedEthernetPortSetting Data | InstanceID
NIC MAC ID(레거시 NIC) | MsvmEmulatedEthernetPortSetting Data | 주소

### <a name="performance-data"></a>성능 데이터

어플라이언스에서 수집하여 Azure에 보내는 서버 성능 데이터는 다음과 같습니다.

**성능 카운터 클래스** | **카운터** | **평가 영향**
--- | --- | ---
Hyper-V 하이퍼바이저 가상 프로세서 | % 게스트 실행 시간 | 권장 서버 크기/비용
Hyper-V 동적 메모리 서버 | 현재 압력(%)<br/> 게스트 가시 실제 메모리(MB) | 권장 서버 크기/비용
Hyper-V 가상 스토리지 디바이스 | 읽기 바이트 수/초 | 디스크 크기, 스토리지 비용, 서버 크기 계산
Hyper-V 가상 스토리지 디바이스 | 쓰기 바이트 수/초 | 디스크 크기, 스토리지 비용, 서버 크기 계산
Hyper-V 가상 네트워크 어댑터 | 받은 바이트 수/초 | 서버 크기 계산
Hyper-V 가상 네트워크 어댑터 | 보낸 바이트 수/초 | 서버 크기 계산

- CPU 사용률은 서버에 연결된 모든 가상 프로세서에 대한 모든 사용량의 합계입니다.
- 메모리 사용률은 (현재 압력 * 게스트 가시 실제 메모리) / 100입니다.
- 디스크 및 네트워크 사용률 값은 나열된 Hyper-V 성능 카운터에서 수집됩니다.

## <a name="collected-data---physical"></a>수집되는 데이터 - 물리적 서버

어플라이언스는 온-프레미스에서 실행되는 물리적 또는 가상 서버에서 구성 메타데이터 및 성능 메타데이터를 수집합니다.

### <a name="metadata"></a>메타데이터

Azure Migrate 어플라이언스에서 검색된 메타데이터를 사용하면 서버를 Azure로 마이그레이션할 준비가 되었는지, 적절한 크기의 서버로 마이그레이션할 준비가 되었는지 파악하여 비용 계획을 세울 수 있습니다. Microsoft는 라이선스 규정 준수 감사에서 이 데이터를 사용하지 않습니다.

### <a name="windows-server-metadata"></a>Windows server 메타데이터

어플라이언스에서 수집하여 Azure에 보내는 Windows 서버 메타데이터의 전체 목록은 다음과 같습니다.

**Data** | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domain, Name, PartOfDomain
프로세서 코어 수 | Win32_PRocessor | NumberOfCores
할당된 메모리 | Win32_ComputerSystem | TotalPhysicalMemory
BIOS 일련 번호 | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS GUID | Win32_ComputerSystemProduct | UUID
부팅 유형 | Win32_DiskPartition | 파티션에서 EFI/BIOS에 대해 Type = **GPT:System** 인지 확인
OS 이름 | Win32_OperatingSystem | 캡션
OS 버전 |Win32_OperatingSystem | 버전
OS 아키텍처 | Win32_OperatingSystem | OSArchitecture
디스크 수 | Win32_DiskDrive | Model, Size, DeviceID, MediaType, Name
디스크 크기 | Win32_DiskDrive | 크기
NIC 목록 | Win32_NetworkAdapterConfiguration | Description, Index
NIC IP 주소 | Win32_NetworkAdapterConfiguration | IPAddress
NIC MAC 주소 | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-server-metadata"></a>Linux 서버 메타데이터

어플라이언스에서 수집하여 Azure에 보내는 Linux 서버 메타데이터의 전체 목록은 다음과 같습니다.

**Data** | **명령**
--- | ---
FQDN | cat /proc/sys/kernel/hostname, hostname -f
프로세서 코어 수 |  cat/proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
할당된 메모리 | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
BIOS 일련 번호 | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
BIOS GUID | cat /sys/class/dmi/id/product_uuid
부팅 유형 | [ -d /sys/firmware/efi ] && echo EFI \|\| echo BIOS
OS 이름/버전 | OS 버전 및 이름에 대해 다음 파일에 액세스합니다.<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
OS 아키텍처 | uname -m
디스크 수 | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
부팅 디스크 | df /boot \| sed -n 2p \| awk '{print $1}'
디스크 크기 | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
NIC 목록 | ip -o -4 addr show \| awk '{print $2}'
NIC IP 주소 | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
NIC MAC 주소 | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Windows 성능 데이터

어플라이언스에서 수집하여 Azure에 보내는 Windows 서버 성능 데이터는 다음과 같습니다.

**Data** | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
CPU 사용량 | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
메모리 사용량 | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
NIC 수 | Win32_PerfFormattedData_Tcpip_NetworkInterface | 네트워크 디바이스 수를 가져옵니다.
NIC당 받은 데이터 | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
NIC당 전송된 데이터 | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
디스크 수 | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | 디스크 수
디스크 세부 정보 | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec

### <a name="linux-performance-data"></a>Linux 성능 도구

어플라이언스에서 수집하여 Azure에 보내는 Linux 서버 성능 데이터는 다음과 같습니다.

| **Data** | **명령** |
| --- | --- |
| CPU 사용량 | cat /proc/stat/ \| grep 'cpu' /proc/stat |
| 메모리 사용량 | free \| grep Mem \| awk '{print $3/$2 * 100.0}' |
| NIC 수 | lshw -class network \| grep eth[0-60] \| wc -l |
| NIC당 받은 데이터 | cat /sys/class/net/eth$nic/statistics/rx_bytes |
| NIC당 전송된 데이터 | cat /sys/class/net/eth$nic/statistics/tx_bytes |
| 디스크 수 | fdisk -l \| egrep 'Disk.\*bytes' \| awk '{print $2}' \| cut -f1 -d ':' |
| 디스크 세부 정보 | cat /proc/diskstats |

## <a name="appliance-upgrades"></a>어플라이언스 업그레이드

어플라이언스에서 실행되는 Azure Migrate 서비스가 업데이트되면 어플라이언스가 업그레이드됩니다. 이는 어플라이언스에서 기본적으로 자동 업데이트를 사용하도록 설정되어 있으므로 자동으로 수행됩니다. 이 기본 설정을 변경하여 어플라이언스 서비스를 수동으로 업데이트할 수 있습니다.

### <a name="turn-off-auto-update"></a>자동 업데이트 해제

1. 어플라이언스를 실행하는 서버에서 레지스트리 편집기를 엽니다.
2. **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance** 로 이동합니다.
3. 자동 업데이트를 해제하려면 DWORD 값이 0인 **AutoUpdate** 레지스트리 키를 만듭니다.

    ![레지스트리 키 설정](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>자동 업데이트 설정

다음 방법 중 하나를 사용하여 자동 업데이트를 설정할 수 있습니다.

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance에서 AutoUpdate 레지스트리 키를 삭제합니다.
- 자동 업데이트를 설정하려면 **사전 요구 사항 설정** 패널의 최신 업데이트 확인에서 **어플라이언스 서비스 보기** 를 클릭합니다.

레지스트리 키를 삭제하려면 다음을 수행합니다.

1. 어플라이언스를 실행하는 서버에서 레지스트리 편집기를 엽니다.
2. **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance** 로 이동합니다.
3. 자동 업데이트를 해제하기 위해 이전에 만든 **AutoUpdate** 레지스트리 키를 삭제합니다.

검색이 완료된 후 Appliance Configuration Manager에서 설정하려면 다음을 수행합니다.

1. 어플라이언스 구성 관리자에서 **사전 요구 사항 설정** 패널로 이동합니다.
2. 최신 업데이트 확인에서 **어플라이언스 서비스 보기** 를 클릭하고 링크를 클릭하여 자동 업데이트를 설정합니다.

    ![자동 업데이트 설정](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>어플라이언스 서비스 버전 확인

다음 방법 중 하나를 사용하여 어플라이언스 서비스 버전을 확인할 수 있습니다.

- 어플라이언스 구성 관리자에서 **사전 요구 사항 설정** 패널로 이동합니다.
- 어플라이언스의 **제어판** > **프로그램 및 기능** 에 있습니다.

어플라이언스 구성 관리자에서 확인하려면 다음을 수행합니다.

1. 어플라이언스 구성 관리자에서 **사전 요구 사항 설정** 패널로 이동합니다.
2. 최신 업데이트 확인에서 **어플라이언스 서비스 보기** 를 클릭합니다.

    ![버전 확인](./media/migrate-appliance/versions.png)

제어판에서 확인하려면 다음을 수행합니다.

1. 어플라이언스에서 **시작** > **제어판** > **프로그램 및 기능** 을 차례로 클릭합니다.
2. 목록에서 어플라이언스 서비스 버전을 확인합니다.

    ![제어판에서 버전 확인](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>이전 버전을 수동으로 업데이트

서비스 중 하나에 대해 이전 버전을 실행하고 있는 경우 서비스를 제거하고 최신 버전으로 수동으로 업데이트해야 합니다.

1. 최신 어플라이언스 서비스 버전을 확인하려면 LatestComponents.json 파일을 [다운로드](https://aka.ms/latestapplianceservices)합니다.
2. 다운로드되면 메모장에서 LatestComponents.json 파일을 엽니다.
3. 파일에서 최신 서비스 버전 및 해당 다운로드 링크를 찾습니다. 다음은 그 예입니다.

    "Name": "ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4. 파일의 다운로드 링크를 사용하여 오래된 서비스의 최신 버전을 다운로드합니다.
5. 다운로드되면 관리자 명령 창에서 다음 명령을 실행하여 다운로드한 MSI의 무결성을 확인합니다.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` 예:  C:\>CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. 명령 출력이 파일의 서비스에 대한 해시 값 항목(예: 위의 MD5 해시 값)과 일치하는지 확인합니다.
6. 이제 MSI를 실행하여 서비스를 설치합니다. 자동 설치이며, 완료되면 설치 창이 닫힙니다.
7. 설치가 완료되면 **제어판** > **프로그램 및 기능** 에서 서비스 버전을 확인합니다. 이제 서비스 버전을 json 파일에 표시된 최신 버전으로 업그레이드해야 합니다.

## <a name="next-steps"></a>다음 단계

- [VMware용 어플라이언스를 설정하는 방법](how-to-set-up-appliance-vmware.md)에 대해 알아봅니다.
- [Hyper-V용 어플라이언스를 설정하는 방법](how-to-set-up-appliance-hyper-v.md)에 대해 알아봅니다.
- [물리적 서버용 어플라이언스를 설정하는 방법](how-to-set-up-appliance-physical.md)에 대해 알아봅니다.
