---
title: 프라이빗 엔드포인트에서 Azure Migrate 사용
description: Azure Private Link를 사용한 검색, 평가 및 마이그레이션을 위해 Azure Migrate 프라이빗 링크 지원을 사용합니다.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/10/2020
ms.openlocfilehash: efe9f6ce21e59df3d7f8009b7ff540a77b4cc50a
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094503"
---
# <a name="use-azure-migrate-with-private-endpoints"></a>프라이빗 엔드포인트에서 Azure Migrate 사용

이 문서에서는 [Azure Private Link](../private-link/private-endpoint-overview.md)를 사용하는 개인 네트워크를 통해 서버를 검색, 평가 및 마이그레이션하는 데 Azure Migrate를 사용하는 방법을 설명합니다.

Private Link를 사용하는 ExpressRoute 개인 피어링 또는 사이트 간(S2S) VPN 연결을 통해 비공개로 안전하게 Azure Migrate에 연결하는 데 [Azure Migrate: 검색 및 평가](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) 및 [Azure Migrate: 서버 마이그레이션](./migrate-services-overview.md#azure-migrate-server-migration-tool) 도구를 사용할 수 있습니다.

공용 네트워크를 통과하지 않아도 Azure Migrate 및 기타 Azure 리소스에 액세스하기 위한 조직적 요구 사항이 있는 경우 프라이빗 엔드포인트 연결 방법을 사용하는 것이 좋습니다. Private Link를 사용하면 더 나은 대역폭 또는 대기 시간 요구 사항을 위해 기존 ExpressRoute 개인 피어링 회로를 사용할 수도 있습니다.

## <a name="support-requirements"></a>지원 요구 사항

다음 필수 사용 권한과 지원되는 시나리오 및 도구를 검토합니다.

### <a name="supported-geographies"></a>지원되는 지역

이 기능은 이제 지원되는 [퍼블릭 클라우드](/azure/migrate/migrate-support-matrix#supported-geographies-public-cloud) 및 정부 클라우드 지역에서 미리 [보기로 제공됩니다.](/azure/migrate-support-matrix#supported-geographies-azure-government)

### <a name="required-permissions"></a>필요한 사용 권한

구독에 대한 기여자 + 사용자 액세스 관리자 또는 소유자 권한이 있어야 합니다.

### <a name="supported-scenarios-and-tools"></a>지원되는 시나리오 및 도구

**배포** | **세부 정보** | **도구**
--- | --- | ---
**검색 및 평가** | 모든 플랫폼에서 실행되는 서버에 대한 에이전트 없는 대규모 검색 및 평가를 수행합니다. 예제에는 [VMware vSphere](./tutorial-discover-vmware.md) 또는 [Microsoft Hyper-V](./tutorial-discover-hyper-v.md), [AWS](./tutorial-discover-aws.md)나 [GCP](./tutorial-discover-gcp.md)와 같은 퍼블릭 클라우드 또는 [베어 메탈 서버](./tutorial-discover-physical.md)가 포함됩니다. | Azure Migrate: 검색 및 평가 <br/>
**소프트웨어 인벤토리** | VMware VM에서 실행되는 앱, 역할 및 기능을 검색합니다. | Azure Migrate: 검색 및 평가
**종속성 시각화** | 종속성 분석 기능을 사용하여 서버 간 종속성을 식별하고 이해합니다. <br/> [에이전트 없는 종속성 시각화](./how-to-create-group-machine-dependencies-agentless.md)는 기본적으로 Azure Migrate 프라이빗 링크 지원을 통해 지원됩니다. <br/>[에이전트 기반 종속성 시각화](./how-to-create-group-machine-dependencies.md)를 사용하려면 인터넷에 연결해야 합니다. [에이전트 기반 종속성 시각화에 대해 프라이빗 엔드포인트](../azure-monitor/logs/private-link-security.md)를 사용하는 방법을 알아봅니다. | Azure Migrate: 검색 및 평가 |
**마이그레이션** | [에이전트 없는 Hyper-V 마이그레이션](./tutorial-migrate-hyper-v.md)을 수행하거나 에이전트 기반 접근 방식을 사용하여 [VMware VM](./tutorial-migrate-vmware-agent.md), [Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md), [물리적 서버](./tutorial-migrate-physical-virtual-machines.md), [AWS에서 실행되는 VM](./tutorial-migrate-aws-virtual-machines.md), [GCP에서 실행되는 VM](./tutorial-migrate-gcp-virtual-machines.md) 또는 다른 가상화 공급자에서 실행되는 VM을 마이그레이션합니다. | Azure Migrate: Server Migration

>[!Note]
> [에이전트 없는 VMware 마이그레이션](./tutorial-migrate-vmware.md)을 사용하려면 ExpressRoute Microsoft 피어링을 통해 인터넷에 액세스하거나 연결해야 합니다. [프라이빗 엔드포인트를 사용하여 ExpressRoute 개인 피어링 또는 S2S VPN 연결을 통해 복제를 수행](./replicate-using-expressroute.md)하는 방법을 알아봅니다.

#### <a name="other-integrated-tools"></a>기타 통합 도구

기타 마이그레이션 도구는 공용 네트워크 액세스를 사용할 수 없는 경우 사용량 현황 데이터를 Azure Migrate 프로젝트에 업로드하지 못할 수 있습니다. 모든 네트워크의 트래픽이 다른 Microsoft 또는 외부 [ISV(독립 소프트웨어 공급업체)](./migrate-services-overview.md#isv-integration) 제품에서 데이터를 수신할 수 있도록 Azure Migrate 프로젝트를 구성해야 합니다.

Azure Migrate 프로젝트에 대해 공용 네트워크 액세스를 사용하려면 Azure Portal에 로그인하여 **Azure Migrate 속성** 페이지로 이동한 다음, **아니요** > **저장** 을 선택합니다.

![네트워크 액세스 모드를 변경하는 방법을 보여 주는 스크린샷.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>기타 고려 사항

**고려 사항** | **세부 정보**
--- | ---
**가격** | 가격 책정 정보는 [Azure 페이지 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/page-blobs/) 및 [Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link/)을 참조하세요.
**가상 네트워크 요구 사항** | ExpressRoute/VPN 게이트웨이 엔드포인트는 선택된 가상 네트워크 또는 여기에 연결된 가상 네트워크에 있어야 합니다. 가상 네트워크에서 최대 15개의 IP 주소가 필요할 수 있습니다.

## <a name="create-a-project-with-private-endpoint-connectivity"></a>프라이빗 엔드포인트 연결을 사용하여 프로젝트 만들기

새 Azure Migrate 프로젝트를 설정하려면 [프로젝트 만들기 및 관리](./create-manage-projects.md#create-a-project-for-the-first-time)를 참조하세요.

> [!Note]
> 기존 Azure Migrate 프로젝트의 경우 연결 방법을 프라이빗 엔드포인트 연결로 변경할 수는 없습니다.

**고급** 구성 섹션에서 다음 세부 정보를 입력하여 Azure Migrate 프로젝트의 프라이빗 엔드포인트를 만듭니다.
1. **연결 방법** 에서 **프라이빗 엔드포인트** 를 선택합니다.
1. **퍼블릭 엔드포인트 액세스 사용 안 함** 에서 기본 설정인 **아니요** 를 유지합니다. 일부 마이그레이션 도구는 공용 네트워크 액세스가 사용되지 않는 경우 사용량 현황 데이터를 Azure Migrate 프로젝트에 업로드하지 못할 수 있습니다. [기타 통합 도구](#other-integrated-tools)에 대해 알아보세요.
1. **가상 네트워크 구독** 에서 프라이빗 엔드포인트 가상 네트워크의 구독을 선택합니다.
1. **가상 네트워크** 에서 프라이빗 엔드포인트의 가상 네트워크를 선택합니다. Azure Migrate 프로젝트에 연결하는 데 필요한 Azure Migrate 어플라이언스 및 기타 소프트웨어 구성 요소는 이 네트워크 또는 연결된 가상 네트워크에 있어야 합니다.
1. **서브넷** 에서 프라이빗 엔드포인트의 서브넷을 선택합니다.

   ![프로젝트 만들기 페이지의 고급 섹션을 보여 주는 스크린샷.](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

1. **만들기** 를 선택하여 마이그레이션 프로젝트를 만들고 프라이빗 엔드포인트를 연결합니다. Azure Migrate 프로젝트가 배포될 때까지 몇 분 정도 기다립니다. 프로젝트 만들기가 진행되는 동안에는 이 페이지를 닫지 마세요.

## <a name="discover-and-assess-servers-for-migration-by-using-private-link"></a>Private Link를 사용하여 마이그레이션할 서버 검색 및 평가

이 섹션에서는 Azure Migrate 어플라이언스를 설정하는 방법을 설명합니다. 그런 다음, 이를 사용하여 마이그레이션할 서버를 검색하고 평가합니다.

### <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 설정

1. **머신 검색** > **머신이 가상화되어 있나요?** 에서 서버 유형을 선택합니다.
1. **Azure Migrate 프로젝트 키 생성** 에서 Azure Migrate 어플라이언스의 이름을 제공합니다.
1. **키 생성** 을 선택하여 필요한 Azure 리소스를 만듭니다.

    > [!Important]
    > 리소스를 생성하는 동안에는 **머신 검색** 페이지를 닫지 마세요.
    - 이 단계에서 Azure Migrate는 키 자격 증명 모음, 스토리지 계정, Recovery Services 자격 증명 모음(에이전트 없는 VMware 마이그레이션에만 해당) 및 몇 가지 내부 리소스를 만듭니다. Azure Migrate는 각 리소스에 프라이빗 엔드포인트를 연결합니다. 프라이빗 엔드포인트는 프로젝트를 만드는 동안 선택한 가상 네트워크에서 만들어집니다.
    - 프라이빗 엔드포인트가 만들어지면 Azure Migrate 리소스의 DNS CNAME 리소스 레코드는 접두사 *privatelink* 가 있는 하위 도메인의 별칭으로 업데이트됩니다. 기본적으로 Azure Migrate는 각 리소스 종류의 *privatelink* 하위 도메인에 해당하는 프라이빗 DNS 영역도 만들고 연결된 프라이빗 엔드포인트의 DNS A 레코드를 삽입합니다. 이렇게 하면 원본 네트워크에 있는 Azure Migrate 어플라이언스 및 기타 소프트웨어 구성 요소가 개인 IP 주소의 Azure Migrate 리소스 엔드포인트에 연결할 수 있습니다.
    - 또한 Azure Migrate는 마이그레이션 프로젝트에 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하며 스토리지 계정에 안전하게 액세스할 수 있는 권한을 관리 ID에 부여합니다.

1. 키가 성공적으로 생성된 후 키 세부 정보를 복사하여 어플라이언스를 구성하고 등록합니다.

#### <a name="download-the-appliance-installer-file"></a>어플라이언스 설치 프로그램 파일 다운로드

Azure Migrate: 검색 및 평가는 간단한 Azure Migrate 어플라이언스를 사용합니다. 이 어플라이언스는 서버를 검색하고 서버 구성 및 성능 메타데이터를 Azure Migrate로 보냅니다.

> [!Note]
> 템플릿(VMware 환경의 서버용 OVA, Hyper-V 환경용 VHD)을 사용하여 어플라이언스를 배포한 경우 동일한 어플라이언스를 사용하고 프라이빗 엔드포인트 연결로 Azure Migrate 프로젝트에 등록할 수 있습니다.

어플라이언스를 설정하려면 다음을 수행합니다.
  1. 포털에서 설치 프로그램 스크립트가 포함된 압축 파일을 다운로드합니다.
  1. 어플라이언스를 호스트할 서버에서 압축 파일을 복사합니다.
  1. 압축 파일을 다운로드한 후 파일 보안을 확인합니다.
  1. 설치 관리자 스크립트를 실행하여 어플라이언스를 배포합니다.

#### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  최신 어플라이언스 버전 및 해시 값을 확인합니다.

    **다운로드** | **해시 값**
    --- | ---
    [최신 버전](https://go.microsoft.com/fwlink/?linkid=2160648) | BA84B58E88DDFE23E5D4CE73530227EBBC187B3634B66A3E0F0B3E5DF5F0A94F

> [!NOTE]
> 동일한 스크립트를 사용하여 VMware, Hyper-V, 물리적 또는 기타와 같은 선택한 시나리오에 대해 프라이빗 엔드포인트 연결이 있는 어플라이언스를 설정하여 원하는 구성으로 어플라이언스를 배포할 수 있습니다.

서버가 선택한 시나리오(VMware, Hyper-V, 물리적 또는 기타)의 [하드웨어 요구 사항](./migrate-appliance.md)을 충족하고 [필요한 URL](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity)에 연결할 수 있는지 확인합니다.

#### <a name="run-the-azure-migrate-installer-script"></a>Azure Migrate 설치 프로그램 스크립트 실행

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다.  기존 Azure Migrate 어플라이언스가 있는 서버에서 스크립트를 실행하지 않아야 합니다.

2. 위 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.

3. 다운로드한 압축 파일에서 콘텐츠를 추출한 폴더로 PowerShell 디렉터리를 변경합니다.

4. `AzureMigrateInstaller.ps1`다음 명령을 실행 하 여 이라는 스크립트를 실행 합니다.

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. 시나리오, 클라우드, 연결 옵션 중에서 선택하여 원하는 구성으로 어플라이언스를 배포합니다. 예를 들어 아래와 같이 선택하면 **Azure 퍼블릭 클라우드** 에서 **프라이빗 엔드포인트 연결** 을 사용하는 Azure Migrate 프로젝트에 대해 **VMware 환경에서 실행되는 서버** 를 검색 및 평가하도록 어플라이언스가 설정됩니다.

   :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/script-vmware-private-inline.png" alt-text="프라이빗 엔드포인트에 대해 원하는 구성으로 어플라이언스를 설정하는 방법을 보여 주는 스크린샷." lightbox="./media/how-to-use-azure-migrate-with-private-endpoints/script-vmware-private-expanded.png":::

스크립트를 성공적으로 실행하면 어플라이언스 구성 관리자가 자동으로 시작됩니다.

> [!NOTE]
> 문제가 발생하는 경우 문제 해결을 위해 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그에 액세스할 수 있습니다.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>어플라이언스 구성 및 연속 검색 시작

어플라이언스 서버에 연결 가능한 머신에서 브라우저를 엽니다. 어플라이언스 구성 관리자의 URL(`https://appliance name or IP address: 44368`)을 엽니다. 또는 어플라이언스 서버 바탕 화면에서 구성 관리자 바로 가기를 선택하여 구성 관리자를 열 수도 있습니다.

#### <a name="set-up-prerequisites"></a>필수 조건 설정

1. 타사 정보를 읽어 보고 **사용 조건** 에 동의합니다.

1. 구성 관리자의 **필수 구성 요소 설정** 에서 다음을 수행합니다.
   - **연결**: 어플라이언스가 필요한 URL에 대한 액세스를 확인합니다. 서버에서 프록시를 사용하는 경우:
     - **프록시 설정** 을 선택하여 프록시 주소 `http://ProxyIPAddress` 또는 `http://ProxyFQDN`과 수신 대기 포트를 지정합니다.
     - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다. HTTP 프록시만 지원됩니다.
     - 프록시 서버를 바이패스해야 하는 URL 또는 IP 주소 목록을 추가할 수 있습니다.
        ![프록시 바이패스 목록 추가](./media/how-to-use-azure-migrate-with-private-endpoints/bypass-proxy-list.png)
     - 프록시를 바이패스하기 위해 프록시 서버 세부 정보를 업데이트하거나 URL 또는 IP 주소를 추가한 경우 구성을 등록하려면 **저장** 을 선택합니다.

        > [!Note]
        > 연결 확인 중에 aka.ms/* 링크와 관련된 오류가 발생하고 어플라이언스가 인터넷을 통해 이 URL에 액세스하지 않게 하려면 어플라이언스에서 자동 업데이트 서비스를 사용하지 않도록 설정합니다. [자동 업데이트 해제](./migrate-appliance.md#turn-off-auto-update)의 단계를 수행합니다. 자동 업데이트를 사용하지 않도록 설정한 후에는 aka.ms/* URL 연결 확인을 건너뜁니다.

   - **시간 동기화**: 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
   - **업데이트 설치**: 어플라이언스에서 최신 업데이트가 설치되어 있는지 확인합니다. 확인이 완료된 후 **어플라이언스 서비스 보기** 를 선택하여 어플라이언스 서버에서 실행되는 서비스의 상태와 버전을 확인합니다.
        > [!Note]
        > 어플라이언스에서 자동 업데이트를 사용하지 않도록 설정한 경우 최신 버전의 서비스를 가져오도록 수동으로 어플라이언스 서비스를 업데이트할 수 있습니다. [이전 버전을 수동으로 업데이트](./migrate-appliance.md#manually-update-an-older-version)에 나온 단계를 수행합니다.
   - **VDDK 설치**: _(VMware 어플라이언스에만 필요함)_ 어플라이언스에서 VMware vSphere VDDK(Virtual Disk Development Kit)가 설치되어 있는지 확인합니다. 설치되지 않은 경우, VMware에서 VDDK 6.7을 다운로드합니다. 설치 지침에 따라 다운로드한 zip 파일 콘텐츠를 어플라이언스의 지정된 위치에 추출합니다.

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>어플라이언스 등록 및 연속 검색 시작

필수 구성 요소 확인이 완료된 후 단계에 따라 어플라이언스를 등록하고 각 시나리오에 대해 연속 검색을 시작합니다.
- [VMware VM](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate)
- [Hyper-V VM](./tutorial-discover-hyper-v.md#register-the-appliance-with-azure-migrate)
- [물리적 서버](./tutorial-discover-physical.md#register-the-appliance-with-azure-migrate)
- [AWS VM](./tutorial-discover-aws.md#register-the-appliance-with-azure-migrate)
- [GCP VM](./tutorial-discover-gcp.md#register-the-appliance-with-azure-migrate)

>[!Note]
> 어플라이언스 등록 중에 또는 검색을 시작할 때 DNS 확인에 문제가 발생하면 포털에서 **키 생성** 단계 중에 만든 Azure Migrate 리소스가 Azure Migrate 어플라이언스를 호스트하는 온-프레미스 서버에서 연결할 수 있는지 확인합니다. [네트워크 연결](./troubleshoot-network-connectivity.md)을 확인하는 방법을 자세히 알아봅니다.

### <a name="assess-your-servers-for-migration-to-azure"></a>Azure로 마이그레이션할 서버 평가
검색이 완료된 후 Azure Migrate: 검색 및 평가 도구를 사용하여 Azure VM 또는 Azure VMware Solution으로 마이그레이션할 서버([VMware VM](./tutorial-assess-vmware-azure-vm.md), [Hyper-V VM](./tutorial-assess-hyper-v.md), [물리적 서버](./tutorial-assess-vmware-azure-vm.md), [AWS VM](./tutorial-assess-aws.md) 및 [GCP VM](./tutorial-assess-gcp.md))를 평가합니다.

가져온 CSV 파일을 사용하여 Azure Migrate: 검색 및 평가 도구를 통해 [온-프레미스 머신을 평가](./tutorial-discover-import.md#prepare-the-csv)할 수도 있습니다.

## <a name="migrate-servers-to-azure-by-using-private-link"></a>Private Link를 사용하여 Azure로 서버 마이그레이션

다음 섹션에서는 ExpressRoute 개인 피어링 또는 VPN 연결을 사용하여 마이그레이션을 위해 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)에서 Azure Migrate를 사용하는 데 필요한 단계를 설명합니다.

이 문서에서는 Azure 프라이빗 엔드포인트를 사용하여 [VMware VM](./tutorial-migrate-vmware-agent.md), [Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md), [물리적 서버](./tutorial-migrate-physical-virtual-machines.md), [AWS에서 실행되는 VM](./tutorial-migrate-aws-virtual-machines.md), [GCP에서 실행되는 VM](./tutorial-migrate-gcp-virtual-machines.md) 또는 다른 가상화 공급자에서 실행되는 VM을 마이그레이션하기 위한 에이전트 기반 복제의 개념 증명 배포 경로를 보여 줍니다. Private Link를 사용하여 [에이전트 없는 Hyper-V 마이그레이션](./tutorial-migrate-hyper-v.md)을 수행하는 비슷한 접근 방식을 사용할 수 있습니다.

>[!Note]
>[에이전트 없는 VMware 마이그레이션](./tutorial-assess-physical.md)을 사용하려면 ExpressRoute Microsoft 피어링을 통해 인터넷에 액세스하거나 연결해야 합니다.

### <a name="set-up-a-replication-appliance-for-migration"></a>마이그레이션을 위해 복제 어플라이언스 설정

다음 다이어그램에서는 Azure Migrate: 서버 마이그레이션 도구를 사용하는 프라이빗 엔드포인트를 통한 에이전트 기반 복제 워크플로를 보여 줍니다.

![복제 아키텍처를 보여 주는 다이어그램.](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

이 도구는 복제 어플라이언스를 사용하여 서버를 Azure에 복제합니다. [복제 어플라이언스에 사용할 머신을 준비하고 설정](./tutorial-migrate-physical-virtual-machines.md#prepare-a-machine-for-the-replication-appliance)하는 방법을 알아봅니다.

복제 어플라이언스를 설정한 후 다음 단계를 수행하여 마이그레이션에 필요한 리소스를 만듭니다.

1. **머신 검색** > **머신이 가상화되어 있나요?** 에서 **가상화되지 않음/기타** 를 선택합니다.
1. **대상 지역** 에서 머신을 마이그레이션하려는 Azure 지역을 선택하고 확인합니다.
1. **리소스 만들기** 를 선택하여 필요한 Azure 리소스를 만듭니다. 리소스를 만드는 동안에는 페이지를 닫지 마세요.
    - 이 단계를 통해 백그라운드에 Recovery Services 자격 증명 모음이 생성되고 자격 증명 모음에 대해 관리 ID가 사용하도록 설정됩니다. Recovery Services 자격 증명 모음은 서버의 복제 정보를 포함하고 복제 작업을 실행하는 데 사용되는 엔터티입니다.
    - Azure Migrate 프로젝트가 프라이빗 엔드포인트에 연결된 경우 Recovery Services 자격 증명 모음에 대해 프라이빗 엔드포인트가 생성됩니다. 그러면 Recovery Services 자격 증명 모음에 연결된 각 마이크로 서비스에 대해 하나씩, FQDN(정규화된 도메인 이름) 5개가 프라이빗 엔드포인트에 추가됩니다.
    - 5개의 도메인 이름은 다음 패턴으로 형식이 지정됩니다. <br/> _{Vault-ID}-asr-pod01-{type}-.{target-geo-code}_ .privatelink.siterecovery.windowsazure.com
    - 기본적으로 Azure Migrate는 자동으로 프라이빗 DNS 영역을 만들고 Recovery Services 자격 증명 모음 마이크로 서비스의 DNS A 레코드를 추가합니다. 그런 다음, 프라이빗 DNS는 프라이빗 엔드포인트 가상 네트워크에 연결됩니다.

1. 복제 어플라이언스를 등록하기 전에 복제 어플라이언스를 호스트하는 머신에서 자격 증명 모음의 프라이빗 링크 FQDN에 연결할 수 있는지 확인합니다. 프라이빗 링크 FQDN을 개인 IP 주소로 확인하려면 온-프레미스 복제 어플라이언스에 추가 DNS 구성이 필요할 수 있습니다. [네트워크 연결을 확인하는 방법](./troubleshoot-network-connectivity.md#verify-dns-resolution)을 자세히 알아봅니다.

1. 연결을 확인한 후 어플라이언스 설정 및 키 파일을 다운로드하고, 설치 프로세스를 실행하고, Azure Migrate에 어플라이언스를 등록합니다. [복제 어플라이언스를 설정](./tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)하는 방법을 알아봅니다. 복제 어플라이언스를 설정한 후 이 지침에 따라 마이그레이션할 머신에 [모바일 서비스를 설치](./tutorial-migrate-physical-virtual-machines.md#install-the-mobility-service)합니다.

### <a name="replicate-servers-to-azure-by-using-private-link"></a>Private Link를 사용하여 Azure로 서버 복제

[이 단계](./tutorial-migrate-physical-virtual-machines.md#replicate-machines)를 수행하여 복제할 서버를 선택합니다.

**복제** > **대상 설정** > **캐시/복제 스토리지 계정** 에서 드롭다운 목록을 사용하여 프라이빗 링크를 통해 복제할 스토리지 계정을 선택합니다.

Azure Migrate 프로젝트가 프라이빗 엔드포인트에 연결된 경우 Azure Migrate에 필요한 스토리지 계정에 액세스할 수 있는 [권한을 Recovery Services 자격 증명 모음 관리 ID에 부여](#grant-access-permissions-to-the-recovery-services-vault)해야 합니다.

또한 프라이빗 링크를 통해 복제를 사용하도록 설정하려면 [스토리지 계정에 대한 프라이빗 엔드포인트를 만듭니다.](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 액세스 권한 부여

캐시/복제 스토리지 계정에 대한 인증된 액세스를 위해서는 Recovery Services 자격 증명 모음에 사용 권한을 부여해야 합니다.

Azure Migrate에서 만든 Recovery Services 자격 증명 모음을 식별하고 필요한 사용 권한을 부여하려면 다음 단계를 수행합니다.

**Recovery Services 자격 증명 모음 및 관리 ID 개체 ID 식별**

Azure Migrate: 서버 마이그레이션 **속성** 페이지에서 Recovery Services 자격 증명 모음의 세부 정보를 찾을 수 있습니다.

1. **Azure Migrate** 허브로 이동하고 **Azure Migrate: 서버 마이그레이션** 타일에서 **개요** 를 선택합니다.

    ![Azure Migrate 허브의 개요 페이지를 보여 주는 스크린샷.](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

1. 왼쪽 창에서 **속성** 을 선택합니다. Recovery Services 자격 증명 모음 이름 및 관리 ID를 기록해 둡니다. 자격 증명 모음에는 **프라이빗 엔드포인트** 가 **연결 유형** 으로 포함되고 **기타** 가 **복제 유형** 으로 포함됩니다. 자격 증명 모음에 대한 액세스를 제공할 때 이 정보가 필요합니다.

    ![Azure Migrate: 서버 마이그레이션 속성 페이지를 보여 주는 스크린샷.](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**스토리지 계정에 대한 액세스 권한**

 자격 증명 모음의 관리 ID에는 복제에 필요한 스토리지 계정에 대한 다음 역할 권한이 부여되어야 합니다. 이 경우 스토리지 계정을 미리 만들어야 합니다.

>[!Note]
> 프라이빗 링크를 사용하여 Hyper-V VM을 Azure로 마이그레이션하는 경우 복제 스토리지 계정 및 캐시 스토리지 계정에 대한 액세스 권한을 둘 다 부여해야 합니다.

Azure Resource Manager의 역할 권한은 스토리지 계정 유형에 따라 달라집니다.

|**스토리지 계정 유형** | **역할 권한**|
|--- | ---|
|표준 형식 | [기여자](../role-based-access-control/built-in-roles.md#contributor)<br>[Storage Blob 데이터 기여자](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|
|프리미엄 유형 | [기여자](../role-based-access-control/built-in-roles.md#contributor)<br>[Storage Blob 데이터 소유자](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. 복제하도록 선택된 복제/캐시 스토리지 계정으로 이동합니다. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.

1. **+ 추가** 를 선택하고 **역할 할당 추가** 를 선택합니다.

   ![역할 할당 추가를 보여 주는 스크린샷.](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)

1. **역할 할당 추가** 페이지의 **역할** 상자에서 위에서 언급한 권한 목록에서 적절한 역할을 선택합니다. 이전에 설명한 자격 증명 모음의 이름을 입력하고 **저장** 을 선택합니다.

    ![역할 할당 추가 페이지를 보여 주는 스크린샷.](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

1. 이 권한 외에도 신뢰할 수 있는 Microsoft 서비스에 대한 액세스를 허용해야 합니다. 네트워크 액세스가 선택한 네트워크로 제한된 경우 **네트워킹** 탭의 **예외** 섹션에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용** 을 선택합니다.

   ![신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용 옵션을 보여 주는 스크린샷.](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)

### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>스토리지 계정의 프라이빗 엔드포인트 만들기(선택 사항)

개인 피어링을 통해 ExpressRoute를 사용하여 복제하려면 캐시/복제 스토리지 계정에 대한 [프라이빗 엔드포인트를 만듭니다](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint)(대상 하위 리소스: _blob_).

>[!Note]
> 범용 v2 스토리지 계정에서만 프라이빗 엔드포인트를 만들 수 있습니다. 가격 책정 정보는 [Azure 페이지 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/page-blobs/) 및 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link/)을 참조하세요.

Azure Migrate 프로젝트 프라이빗 엔드포인트 또는 이 네트워크에 연결된 다른 가상 네트워크와 동일한 가상 네트워크에 스토리지 계정의 프라이빗 엔드포인트를 만듭니다.

**예** 를 선택하고 프라이빗 DNS 영역과 통합합니다. 프라이빗 DNS 영역은 프라이빗 링크를 통해 가상 네트워크에서 스토리지 계정으로 연결을 라우팅하는 데 도움이 됩니다. **예** 를 선택하면 가상 네트워크에 DNS 영역이 자동으로 연결됩니다. 또한 새로 생성된 IP 및 FQDN의 확인에 대한 DNS 레코드를 추가합니다. [프라이빗 DNS 영역](../dns/private-dns-overview.md)에 대해 자세히 알아보세요.

프라이빗 엔드포인트를 만든 사용자가 스토리지 계정 소유자인 경우 프라이빗 엔드포인트 생성은 자동 승인됩니다. 그렇지 않으면 스토리지 계정의 소유자가 사용을 위해 프라이빗 엔드포인트를 승인해야 합니다. 요청된 프라이빗 엔드포인트 연결을 승인하거나 거부하려면 스토리지 계정 페이지의 **네트워킹** 에서 **프라이빗 엔드포인트 연결** 로 이동합니다.

계속하기 전에 프라이빗 엔드포인트 연결 상태를 검토합니다.

![프라이빗 엔드포인트 승인 상태를 보여 주는 스크린샷.](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

프라이빗 엔드포인트를 만든 후 **복제** > **대상 설정** > **캐시 스토리지 계정** 에서 드롭다운 목록을 사용하여 프라이빗 링크를 통해 복제할 스토리지 계정을 선택합니다.

온-프레미스 복제 어플라이언스가 프라이빗 엔드포인트에서 스토리지 계정에 네트워크로 연결되어 있는지 확인합니다. [네트워크 연결](./troubleshoot-network-connectivity.md)을 확인하는 방법을 자세히 알아봅니다.

>[!Note]
> Hyper-V VM을 Azure로 마이그레이션하는 경우 복제 스토리지 계정이 ‘프리미엄’ 유형이면 캐시 스토리지 계정에 대해 ‘표준’ 유형의 다른 스토리지 계정을 선택해야 합니다.  이 경우 복제 및 캐시 스토리지 계정의 프라이빗 엔드포인트를 둘 다 만들어야 합니다.

다음으로, 지침에 따라 [복제를 검토 및 시작](./tutorial-migrate-physical-virtual-machines.md#replicate-machines)하고 [마이그레이션을 수행](./tutorial-migrate-physical-virtual-machines.md#run-a-test-migration)합니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 프로세스](./tutorial-migrate-physical-virtual-machines.md#complete-the-migration)를 완료합니다.
- [마이그레이션 후 작업 모범 사례](./tutorial-migrate-physical-virtual-machines.md#post-migration-best-practices)를 검토합니다.
