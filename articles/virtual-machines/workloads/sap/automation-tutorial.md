---
title: SAP 배포 자동화 프레임워크 실습 랩
description: Azure의 SAP 배포 자동화 프레임워크에 대한 실습 랩입니다.
author: hdamecharla
ms.author: hdamecharla
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: tutorial
ms.service: virtual-machines-sap
ms.openlocfilehash: 42157c6a4fb07d0f9982540f12c4605760597f6b
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132934141"
---
# <a name="enterprise-scale-for-sap-deployment-automation-framework---hands-on-lab"></a>SAP 배포 자동화 프레임워크를 위한 엔터프라이즈 크기 조정 - 실습 랩

이 자습서에서는 [Azure에서 SAP 배포 자동화 프레임워크](automation-deployment-framework.md)를 사용하여, 배포를 위한 엔터프라이즈 크기 조정을 수행하는 방법을 보여 줍니다. 이 예제에서는 Azure Cloud Shell을 사용하여 컨트롤 플레인 인프라를 배포합니다. 배포자 VM(가상 머신)이 나머지 인프라 및 SAP HANA 구성을 만듭니다. 

이 랩에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 컨트롤 플레인 배포(배포자 인프라 및 라이브러리)
> * 워크로드 영역 배포(Landscape, 시스템)
> * BOM 다운로드/업로드
> * 표준 및 SAP 관련 OS 설정 구성
> * HANA DB 설치
> * SCS 서버 설치
> * HANA DB 로드
> * 기본 애플리케이션 서버 설치

자동화 프레임워크를 통해 Azure에 SAP를 배포하기 위한 세 가지 주요 단계가 있습니다.

1. 지역 준비. 이 단계에서는 지정된 Azure 지역에서 SAP 자동화 프레임워크를 지원하는 구성 요소를 배포합니다. 이 단계의 일부는 다음과 같습니다.
   1. 배포 환경 만들기
   2. Terraform 상태 파일에 대한 공유 스토리지 만들기
   3. SAP 설치 미디어에 대한 공유 스토리지 만들기

2. 워크로드 영역 준비. 이 단계에서는 가상 네트워크 및 키 자격 증명 모음과 같은 워크로드 영역 구성 요소를 배포합니다.

3. 시스템 배포. 이 단계에는 SAP 시스템용 인프라가 포함됩니다.

배포 자동화 프로세스에는 여러 워크플로가 있습니다. 그러나 이 자습서에서는 쉽게 배포할 수 있는 하나의 워크플로에 중점을 둡니다. SAP S4 HANA 독립 실행형 환경인 이 워크플로는 Bash를 사용하여 배포할 수 있습니다. 이 자습서에서는 배포의 일반 계층 구조 및 다양한 단계에 대해 설명합니다.
### <a name="environment-overview"></a>환경 개요

Azure의 SAP 배포 자동화 프레임워크에는 두 가지 주요 구성 요소가 있습니다.

- 배포 인프라(컨트롤 플레인)
- SAP 인프라(SAP 워크로드)

다음 다이어그램은 컨트롤 플레인과 애플리케이션 플레인 간의 종속성을 보여줍니다.

:::image type="content" source="./media/automation-deployment-framework/control-plane-sap-infrastructure.png" alt-text="컨트롤 플레인과 애플리케이션 플레인 간의 종속성":::

이 프레임워크는 인프라 배포에는 Terraform을, 운영 체제 및 애플리케이션 구성에는 Ansible을 사용합니다. 다음 다이어그램에서는 컨트롤 플레인과 워크로드 영역의 논리적 분리를 보여 줍니다.

:::image type="content" source="./media/automation-deployment-framework/automation-diagram-full.png" alt-text="SAP 배포 자동화 프레임워크 환경을 보여 주는 다이어그램":::

#### <a name="management-zone"></a>관리 영역

관리 영역에는 다른 환경이 배포되는 컨트롤 플레인 인프라가 포함됩니다. 관리 영역이 배포되면 다시 배포해야 하는 경우는 거의 없습니다.

:::image type="content" source="./media/automation-deployment-framework/control-plane.png" alt-text="다이어그램: 컨트롤 플레인":::

**배포자** 는 SAP 자동화 프레임워크의 실행 엔진입니다. 이 미리 구성된 VM(가상 머신)은 Terraform 및 Ansible 명령을 실행하는 데 사용됩니다.

**SAP 라이브러리** 는 Terraform 상태 파일에 대한 영구 스토리지와 컨트롤 플레인에 대해 다운로드한 SAP 설치 미디어를 제공합니다.

Terraform `.tfvars` 변수 파일에서 배포자 및 라이브러리를 구성합니다. [컨트롤 플레인 구성](automation-configure-control-plane.md) 참조

#### <a name="workload-zone"></a>워크로드 영역

SAP 애플리케이션에는 일반적으로 여러 배포 계층이 있습니다. 예를 들어 개발, 품질 보증 및 프로덕션 계층이 있을 수 있습니다. SAP 배포 자동화 프레임워크는 이러한 계층을 워크로드 영역이라고 합니다.

:::image type="content" source="./media/automation-deployment-framework/workload-zone.png" alt-text="워크로드 영역.":::

**SAP 워크로드 영역** 에는 SAP VM에 대한 네트워킹 및 공유 구성 요소가 포함됩니다. 이러한 구성 요소에는 경로 테이블, 네트워크 보안 그룹 및 VNet(가상 네트워크)이 포함됩니다. Landscape는 배포를 다른 환경으로 나눌 수 있는 기회를 제공합니다. [워크로드 영역 구성](automation-configure-workload-zone.md) 참조

시스템 배포는 웹, 앱 및 데이터베이스 계층을 포함하여 SAP 애플리케이션을 실행할 가상 머신으로 구성됩니다. [SAP 시스템 구성](automation-configure-system.md) 참조

## <a name="hands-on-lab"></a>실습 랩

### <a name="prerequisites"></a>사전 요구 사항

[SAP 배포 자동화 프레임워크 리포지토리](https://github.com/Azure/sap-automation)는 GitHub에서 사용할 수 있습니다.

배포자에 연결하려면 SSH 클라이언트가 필요합니다. 익숙한 SSH 클라이언트를 사용합니다.

#### <a name="review-the-azure-subscription-quota"></a>Azure 구독 할당량 검토

Microsoft Azure 구독에 DdSV4 & EdsV4 제품군 SKU에 대한 충분한 코어 견적이 선택되어 있는지 확인합니다. VM 제품군에 사용할 수 있는 코어는 각각 약 50개입니다.

#### <a name="suser-account-for-sap-software-download"></a>SAP 소프트웨어 다운로드를 위한 S.User 계정

SAP 소프트웨어를 다운로드하려면 소프트웨어 다운로드 권한이 있는 유효한 SAP 사용자 계정(SAP-User 또는 S-User 계정)이 필요합니다.

## <a name="set-up-cloud-shell"></a>Cloud Shell 설정

[Azure Cloud Shell](https://shell.azure.com)로 이동합니다.

Azure 계정에 로그인

```cloudshell-interactive
az login
```

로그인을 인증합니다. 메시지가 표시될 때까지 창을 닫지 마세요.

필요에 따라 활성 구독을 변경합니다.
  
```cloudshell-interactive
az account set -s <Subscription ID>
```

활성 구독이 변경된 것을 확인합니다.

```cloudshell-interactive
az account list --query "[?isDefault]"
```
**or**

```cloudshell-interactive
az account list -o table | grep True
```

배포 폴더를 만들고 리포지토리를 복제합니다.

```cloudshell-interactive
mkdir -p ~/Azure_SAP_Automated_Deployment

cd ~/Azure_SAP_Automated_Deployment

git clone https://github.com/Azure/sap-automation.git

cd ~/Azure_SAP_Automated_Deployment/sap-automation
```

필요에 따라 Terraform 버전과 Cloud Shell 인스턴스에서 사용할 수 있는 Azure CLI(Azure 명령줄 인터페이스)의 유효성을 검사합니다.

```bash
deploy/scripts/helpers/check_workstation.sh
```

자동화 프레임워크를 실행하려면 다음 버전으로 업데이트합니다.

1. `az` 버전 2.28.0 이상

1. `terraform` 버전 1.0.8 이상 필요에 따라 [Terraform 지침을 사용하여 업그레이드합니다.](https://www.terraform.io/upgrade-guides/0-12.html)

## <a name="create-service-principal"></a>서비스 주체 만들기

SAP 자동화 배포 프레임워크는 배포에 서비스 주체를 사용합니다. 다음과 같이 컨트롤 플레인 배포를 위한 서비스 주체를 만듭니다. 서비스 주체를 만들 수 있는 권한이 있는 계정을 사용해야 합니다.

> [!NOTE] 
> 서비스 주체의 이름을 선택할 때 이름이 Azure 테넌트 내에서 고유한지 확인합니다.


서비스 주체 기여자 및 사용자 액세스 관리자 권한을 부여합니다.

```cloudshell-interactive
az ad sp create-for-rbac --role="Contributor" \
  --scopes="/subscriptions/<subscriptionID>" \
  --name="<environment>-Deployment-Account"
```

출력을 검토합니다. 예를 들면 다음과 같습니다.

```json
{
    "appId": "<AppID>",
    "displayName": "<environment>-Deployment-Account ",
    "name": "<AppID>",
    "password": "<AppSecret>",
    "tenant": "<Tenant ID>"
}
```

출력 세부 정보를 복사합니다. `appId`, `password` 및 `Tenant` 필드에 대한 값을 저장해야 합니다.

출력은 다음 매개 변수에 매핑됩니다. 이후의 단계에서 자동화 명령에 이 매개 변수를 사용합니다.

| **매개 변수 입력 이름** | **출력 이름** |
|--------------------------|-----------------|
| `spn_id`                 | `appId`         |
| `spn_secret`             | `password`      |
| `tenant_id`              | `tenant`        |

서비스 주체에 **사용자 액세스 관리자** 역할을 할당합니다.

```bash
az role assignment create --assignee <appId> \
  --role "User Access Administrator"
```

## <a name="view-configuration-files"></a>구성 파일 보기

>[!IMPORTANT]
> 항상 GitHub 리포지토리를 읽기 전용으로 처리합니다. `WORKSPACES` 폴더의 복사본에서 구성을 변경합니다. 이 메서드는 리포지토리가 변경되는 경우 구성을 안정적으로 유지합니다.

샘플 구성을 로컬 작업 영역 디렉터리에 복사합니다.

```bash
cd ~/Azure_SAP_Automated_Deployment

cp -Rp ./sap-automation/training-materials/WORKSPACES ./
```

Cloud Shell에서 VS Code 열기 

```cloudshell-interactive
code .
```

**WORKSPACES** 디렉터리를 확장합니다. There are five subfolders: **DEPLOYER**, **LANDSCAPE**, **LIBRARY**, **SYSTEM** 및 **BOMS** 등, 5개의 하위 폴더가 있습니다. 이 폴더를 각각 확장하여 지역 배포 구성 파일을 찾습니다.

사용하려는 Azure 지역에 해당하는 적절한 4자 코드를 찾습니다.

| 지역 이름        | 지역 코드 |
|--------------------|-------------|
| 오스트레일리아 동부     | AUEA        |
| 캐나다 중부     | CACE        |
| 미국 중부         | CEUS        |
| 미국 동부            | EAUS        |
| 북유럽       | NOEU        |
| 남아프리카 북부 | SANO        |
| 동남아시아     | SOEA        |
| 영국 남부           | UKSO        |
| 미국 서부 2          | WES2        |

적절한 하위 폴더에서 Terraform 변수 파일을 찾습니다. 예를 들어 **DEPLOYER** terraform 변수 파일은 다음과 같습니다.

```terraform
# The environment value is a mandatory field, it is used for partitioning the environments, for example, PROD and NP.
environment="MGMT"
# The location/region value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"

# management_network_address_space is the address space for management virtual network
management_network_address_space="10.10.20.0/25"
# management_subnet_address_prefix is the address prefix for the management subnet
management_subnet_address_prefix="10.10.20.64/28"
# management_firewall_subnet_address_prefix is the address prefix for the firewall subnet
management_firewall_subnet_address_prefix="10.10.20.0/26"

deployer_enable_public_ip=true
firewall_deployment=true
```

나중에 배포 중의 편집을 위해 Terraform 변수 파일 위치를 기록해 둡니다.

## <a name="deploy-control-plane"></a>컨트롤 플레인 배포

[prepare_region](bash/automation-prepare-region.md) 스크립트를 사용하여 배포자 및 라이브러리를 배포합니다. 이러한 배포 조각은 선택한 자동화 영역에 대한 컨트롤 플레인을 구성합니다. 

- 배포는 인프라 배포, 상태 새로 고침, 라이브러리 스토리지 계정에 Terraform 상태 파일 업로드 주기를 거칩니다. 이러한 모든 단계는 단일 배포 스크립트로 패키지됩니다. 스크립트에는 배포자 및 라이브러리에 대한 구성 파일의 위치와, 다음과 같은 다른 매개 변수가 필요합니다.

예를 들어 배포 위치로 **북유럽** 을 선택하고, 앞서 설명한 대로 4자로 된 이름을 `NOEU`를 지정합니다. 샘플 배포자 구성 파일 `MGMT-NOEU-DEP00-INFRASTRUCTURE.tfvars`는 `~/Azure_SAP_Automated_Deployment/WORKSPACES/DEPLOYER/MGMT-NOEU-DEP00-INFRASTRUCTURE` 폴더에 있습니다.

샘플 SAP 라이브러리 구성 파일 `MGMT-NOEU-SAP_LIBRARY.tfvars`는 `~/Azure_SAP_Automated_Deployment/WORKSPACES/LIBRARY/MGMT-NOEU-SAP_LIBRARY` 폴더에 있습니다.

1. 배포자 및 SAP 라이브러리를 만듭니다. 또한 서비스 주체 세부 정보를 배포 키 자격 증명 모음에 추가합니다.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

export subscriptionID="<subscriptionID>"
export appId="<appID>"
export spn_secret="<password>"
export tenant_id="<tenant>"
export region_code="NOEU"

export DEPLOYMENT_REPO_PATH="${HOME}/Azure_SAP_Automated_Deployment/sap-automation"
export ARM_SUBSCRIPTION_ID="${subscriptionID}"

${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                                         \
    --deployer_parameter_file DEPLOYER/MGMT-${region_code}-DEP00-INFRASTRUCTURE/MGMT-${region_code}-DEP00-INFRASTRUCTURE.tfvars  \
    --library_parameter_file LIBRARY/MGMT-${region_code}-SAP_LIBRARY/MGMT-${region_code}-SAP_LIBRARY.tfvars                      \
    --subscription "${subscriptionID}"                                                                                           \
    --spn_id "${spn_id}"                                                                                                         \
    --spn_secret "${spn_secret}"                                                                                                 \
    --tenant_id "${tenant_id}"
```

인증 문제가 발생하면 `az logout`을 실행하여 로그아웃합니다. `token-cache`를 지운 다음, `az login`을 실행하여 다시 인증합니다.

자동화 프레임워크가 Terraform 작업 `plan` 및 `apply`를 실행할 때까지 기다립니다.

배포자의 배포는 약 15-20분 동안 실행될 수 있습니다.

[Azure Portal](https://portal.azure.com)로 이동합니다.  

**리소스 그룹** 을 선택합니다. 배포자 인프라 및 라이브러리에 대한 새 리소스 그룹을 찾습니다. 예를 들어 `MGMT-[region]-DEP00-INFRASTRUCTURE` 및 `MGMT-[region]-SAP_LIBRARY`를 지정합니다.

배포자 및 SAP 라이브러리 리소스 그룹의 콘텐츠는 다음과 같습니다.

:::image type="content" source="media/automation-tutorial/deployer-resource-group.png" alt-text="배포자 리소스":::
    
:::image type="content" source="media/automation-tutorial/sap-library-resource-group.png" alt-text="라이브러리 리소스":::

이제 Terraform 상태 파일이, 이름에 'tfstate'가 포함된 스토리지 계정에 있습니다. 스토리지 계정에는 배포자 및 라이브러리 상태 파일이 있는 'tfstate'라는 컨테이너가 있습니다. 컨트롤 플레인 배포에 성공한 후 'tfstate' 컨테이너의 콘텐츠는 아래에서 확인할 수 있습니다.
    
:::image type="content" source="media/automation-tutorial/terraform-state-files.png" alt-text="컨트롤 플레인 tfstate 파일":::

### <a name="common-issues-and-solutions"></a>일반적인 문제 및 솔루션

배포자 모듈 만들기에서 다음 오류가 발생하면 스크립트를 실행할 때 **WORKSPACES** 디렉터리에 있는지 확인합니다.
  
```text
Incorrect parameter file.
The file must contain the environment attribute!!
```

다음 오류는 일시적입니다. 동일한 명령 `prepare_region.sh`를 다시 실행합니다.
  
 ```text
Error: file provisioner error
..
timeout - last error: dial tcp
```


`prepare_region.sh` 스크립트 실행 직후 인증 문제가 있는 경우 다음을 실행합니다.

```bash
az logout

az login
```

## <a name="connect-to-deployer-vm"></a>배포자 VM에 연결

영역을 준비한 후 Terraform 상태 파일은 원격 백 엔드 `azurerm`으로 이동합니다. 배포자 VM에 연결하기 위한 모든 비밀은 배포자의 리소스 그룹 안에 있는 키 자격 증명 모음에서 사용할 수 있습니다.

배포자 VM에 연결할 수 있는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **키 자격 증명 모음** 을 선택하거나 검색합니다.

1. **키 자격 증명 모음** 페이지에서 배포자 키 자격 증명 모음을 찾습니다. 이름은 `MGMT[Region]DEP00user`로 시작합니다. 필요한 경우 **리소스 그룹** 으로 필터링할 수 있습니다.

1. 키 자격 증명 모음의 메뉴에서 **설정** 아래 **비밀** 을 선택합니다.

1. 비밀 페이지에서 현재 버전을 선택합니다. 그런 다음, **비밀 값** 을 복사합니다.

1. 일반 텍스트 편집기를 엽니다. 비밀 값을 복사합니다. 
 
1. SSH 키가 담긴 파일을 저장합니다. 예들 들어 `C:\\Users\\<your-username>\\.ssh`입니다.
 
1. 파일을 저장합니다. **저장 형식** 메시지가 표시될 경우 **SSH** 가 옵션에 없으면 **모든 파일** 을 선택합니다. 예를 들면 `deployer.ssh`를 사용합니다.

1. 임의의 SSH 클라이언트를 통해 배포자 VM에 연결합니다. 이전에 기록한 공용 IP 주소와 다운로드한 SSH 키를 사용합니다. PuTTY를 사용하는 경우 먼저 PuTTYGen을 사용하여 SSH 키 파일을 변환합니다. 

> [!NOTE] 
>기본 사용자 이름은 *azureadm* 입니다.

- 배포자 VM에 연결되면 이제 워크로드 영역을 프로비전할 수 있습니다.


> [!IMPORTANT]
> 나머지 작업은 배포자에서 실행해야 합니다.

## <a name="get-sap-software-using-the-bom"></a>BOM을 사용하여 SAP 소프트웨어 얻기

Automation Framework는 SAP BOM(제품 정보)을 다운로드하는 도구를 제공합니다. SAP 라이브러리는 SAP를 배포하는 데 필요한 모든 미디어에 대한 보관 계층 역할을 합니다.

SAP BOM(제품 정보)은 SAP Maintenance Planner를 모방합니다. 관련 제품 식별자 및 다운로드 URL 패키지가 있습니다. 배포자 VM은 처리된 BOM을 읽고, 설치를 위해 SAP Central Services(SCS)로 파일을 스토리지 계정에서 다운로드합니다.

BOM 파일의 샘플 추출은 다음과 같습니다.

```yaml

---
name:    'S41909SPS03_v0006ms'
target:  'S/4 HANA 1909 SPS 03'
version: 6

product_ids:
  dbl:       NW_ABAP_DB:S4HANA1909.CORE.HDB.ABAP
  scs:       NW_ABAP_ASCS:S4HANA1909.CORE.HDB.ABAP
  scs_ha:    NW_ABAP_ASCS:S4HANA1909.CORE.HDB.ABAPHA
  pas:       NW_ABAP_CI:S4HANA1909.CORE.HDB.ABAP
  pas_ha:    NW_ABAP_CI:S4HANA1909.CORE.HDB.ABAPHA
  app:       NW_DI:S4HANA1909.CORE.HDB.PD
  app_ha:    NW_DI:S4HANA1909.CORE.HDB.ABAPHA
  web:       NW_Webdispatcher:NW750.IND.PD
  ers:       NW_ERS:S4HANA1909.CORE.HDB.ABAP
  ers_ha:    NW_ERS:S4HANA1909.CORE.HDB.ABAPHA

materials:
  dependencies:
    - name:     HANA_2_00_055_v0004ms

  media:
    # SAPCAR 7.22
    - name:         SAPCAR
      archive:      SAPCAR_1010-70006178.EXE
      checksum:     dff45f8df953ef09dc560ea2689e53d46a14788d5d184834bb56544d342d7b
      filename:     SAPCAR
      permissions:  '0755'
      url:          https://softwaredownloads.sap.com/file/0020000002208852020

    # Kernel
    - name:         "Kernel Part I ; OS: Linux on x86_64 64bit ; DB: Database independent"
```


이 예제 구성의 경우 리소스 그룹은 `MGMT-NOEU-DEP00-INFRASTRUCTURE`입니다. 배포자 키 자격 증명 모음 이름에서는 `MGMTNOEUDEP00user`가 이름에 포함됩니다. 이 정보를 사용하여 배포자의 키 자격 증명 모음 비밀을 구성합니다.

SAP 사용자 계정의 사용자 이름을 통해 비밀을 추가합니다. `<vaultID>`을 배포자 자격 증명 모음의 이름으로 바꿉니다. `<sap-username>`도 SAP 사용자 이름으로 바꿉니다.

```bash
export key_vault=<vaultID>
sap_username=<sap-username>

az keyvault secret set --name "S-Username" --vault-name $key_vault --value "${sap_username}";
```

SAP 사용자 계정에 대한 암호를 통해 비밀을 추가합니다. `<vaultID>`을 배포자 키 자격 증명 모음 이름으로, `<sap-password>`를 SAP 암호로 바꿉니다.

```bash
sap_user_password="<sap-password>
az keyvault secret set --name "S-Password" --vault-name "${key_vault}" --value "${sap_user_password}";
```

이제 다운로드 프로세스에 대한 SAP 매개 변수 파일을 구성합니다. 그런 다음, Ansible 플레이북을 사용하여 SAP 소프트웨어를 다운로드합니다. 다음 명령을 실행합니다.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/BOMS

vi sap-parameters.yaml
```
  
`kv_name parameter`에서 배포자 리소스 그룹 키 자격 증명 모음의 이름을 입력합니다.
  
  파일은 다음 예제 구성과 비슷해야 합니다.

```yaml

bom_base_name:                 S41909SPS03_v0006ms
kv_name:                       MGMTNOEUDEP00user99F 
check_storage_account:         true

```
    
Ansible 플레이북을 실행합니다. 플레이북을 실행할 수 있는 한 가지 방법은 다운로더 메뉴를 사용하는 것입니다. download_menu 스크립트를 실행합니다.
  
```bash
~/Azure_SAP_Automated_Deployment/sap-automation/deploy/ansible/download_menu.sh
```
  
실행할 플레이북을 선택합니다.
  
```bash
1) BoM Downloader
3) Quit
Please select playbook:
```

플레이북 `1) BOM Downloader`를 선택하여 BOM 파일에서 설명하는 SAP 소프트웨어를 스토리지 계정으로 다운로드합니다. `sapbits` 컨테이너에 설치를 위한 미디어가 모두 있는지 확인합니다.
    
## <a name="collect-workload-zone-information"></a>워크로드 영역 정보 수집

텍스트 편집기에서 다음 정보를 수집합니다.

  - 라이브러리 리소스 그룹의 Terraform 상태 파일 스토리지 계정 이름입니다.
    - 위의 예제에서 리소스 그룹은 *MGMT-NOEU-SAP_LIBRARY* 입니다.
    - 스토리지 계정 이름에는 *mgmtnoeutfstate* 가 포함됩니다.

  - 배포자 상태 파일의 이름은 라이브러리 리소스 그룹에서 찾을 수 있습니다.
    - 라이브러리 리소스 그룹 -> 상태 스토리지 계정 -> 컨테이너 -> tfstate-> 배포자 상태 파일의 **이름** 을 복사합니다.
    - 위의 예제에서 BLOB의 이름은 *MGMT-NOEU-DEP00-INFRASTRUCTURE.terraform.tfstate* 이 됩니다.

  - 배포자 리소스 그룹에서 키 자격 증명 모음의 이름.
    - 위의 예제에서 리소스 그룹은 *MGMT-NOEU-DEP00-INFRASTRUCTURE* 입니다.
    - 키 자격 증명 모음의 이름에는 *MGMTNOEUDEP00user* 가 포함됩니다.

배포자 VM의 공용 IP 주소. 배포자의 리소스 그룹으로 이동하고, 배포자 VM을 열고, 공용 IP 주소를 복사합니다.
  
## <a name="prepare-the-workload-zone-deployment"></a>워크로드 영역 배포 준비

다음 단계를 위해 배포자 VM에 연결합니다. 이제 리포지토리의 복사본이 있습니다.

홈 디렉터리의 *Azure_SAP_Automated_Deployment* 폴더로 이동합니다. 기존 **WORKSPACES** 폴더를 제거합니다. 나중에 예제에서 복사하게 됩니다.

```bash
rm -rf WORKSPACES
```

**sap-automation** 폴더로 이동합니다. 
  
```bash
cd ~/Azure_SAP_Automated_Deployment/sap-automation/

git pull
```

리포지토리에서 샘플 구성 파일을 복사합니다.

```bash
cd ~/Azure_SAP_Automated_Deployment/

cp -Rp ./sap-automation/training-materials/WORKSPACES ./
```

## <a name="deploy-the-workload-zone"></a>워크로드 영역 배포


[Install_workloadzone](bash/automation-install_workloadzone.md) 스크립트를 사용하여 SAP 워크로드 영역을 배포합니다. 

배포자 VM에서 `Azure_SAP_Automated_Deployment` 폴더로 이동합니다.
  
```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-XXXX-SAP01-INFRASTRUCTURE
```

예제 영역 'northeurope'에서 폴더는 다음과 같습니다.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-NOEU-SAP01-INFRASTRUCTURE
```
   
워크로드 영역 구성 파일을 열고 필요한 경우 네트워크 이름과 일치하도록 네트워크 논리적 이름을 변경합니다.

앞에서 수집한 세부 정보는 여기에 필요합니다. 이 세부 사항은 다음과 같습니다.
  - 배포자 tfstate 파일의 이름(tfstate 컨테이너에 있음)
  - tfstate 스토리지 계정의 이름
  - 배포자 키 자격 증명 모음의 이름

워크로드 영역의 배포를 시작합니다.

```bash

export subscriptionID="<subscriptionID>"
export appId="<appID>"
export spn_secret="<password>"
export tenant_id="<tenant>"
export storage_account="<storageaccountName>"
export statefile_subscription="<subscriptionID>"
export region_code="NOEU"
export key_vault=<vaultID>

cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-${region_code}-SAP01-INFRASTRUCTURE

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh                          \
    --parameterfile ./DEV-${region_code}-SAP01-INFRASTRUCTURE.tfvars                    \
    --deployer_environment "MGMT"                                                       \
    --deployer_tfstate_key "MGMT-${region_code}-DEP00-INFRASTRUCTURE.terraform.tfstate" \
    --keyvault "${key_vault}"                                                           \
    --storageaccountname "${storage_account}"                                           \
    --state_subscription "${statefile_subscription}"                                    \
    --subscription "${subscriptionID}"                                                  \
    --spn_id "${spn_id}"                                                                \
    --spn_secret "${spn_secret}"                                                        \
    --tenant_id "${tenant_id}"
```

워크로드 영역 배포가 자동으로 시작됩니다.

배포가 완료될 때까지 기다립니다. 새 리소스 그룹이 Azure Portal에 나타납니다.

## <a name="deploy-sap-system-infrastructure"></a>SAP 시스템 인프라 배포

워크로드 영역이 완료되면 SAP 시스템 인프라 리소스를 배포할 수 있습니다. SAP 시스템은 SAP 애플리케이션용 VM 및 지원 구성 요소를 만듭니다.
[installer.sh](bash/automation-installer.md) 스크립트를 사용하여 SAP 시스템을 배포합니다. 

SAP 시스템은 다음을 배포합니다.

- 데이터베이스 VM 및 해당 디스크와, Azure Standard Load Balancer를 배포하는 데이터베이스 계층. 이 계층에서 HANA 데이터베이스 또는 AnyDB 데이터베이스를 실행할 수 있습니다.
- 고객이 정의한 개수의 VM과 Azure Standard Load Balancer를 배포하는 SCS 계층.
- VM 및 해당 디스크를 배포하는 애플리케이션 계층.
- 웹 디스패처 계층.

SAP 시스템을 배포합니다.

```bash

export region_code="NOEU"

cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-${region_code}-SAP01-X00

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh      \
  --parameterfile "DEV-${region_code}-SAP01-X00.tfvars"  \
  --type sap_system                                      \
  --auto-approve
```
  
`northeurope` 예제에 대한 배포 명령은 다음과 같습니다.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-NOEU-SAP01-X00

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh  \
  --parameterfile DEV-NOEU-SAP01-X00.tfvars          \
  --type sap_system                                  \
  --auto-approve
```

시스템 리소스 그룹이 현재 Azure Portal에 있는지 확인합니다.

## <a name="sap-application-installation"></a>SAP 애플리케이션 설치

SAP 애플리케이션 설치는 Ansible 플레이북을 통해 수행됩니다. 

시스템 배포 폴더로 이동합니다.

```bash
cd ~/Azure_ SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-NOEU-SAP01-X00/
```

현재 폴더에 `sap-parameters.yaml` 및 `SID_host.yaml` 등의 파일이 있는지 확인합니다.

독립 실행형 SAP S/4HANA 시스템의 경우 순서대로 실행되는 8개의 플레이북이 있습니다. 플레이북을 실행할 수 있는 한 가지 방법은 구성 메뉴를 사용하는 것입니다. 

configuration_menu 스크립트를 실행합니다.
  
```bash
~/Azure_SAP_Automated_Deployment/sap-automation/deploy/ansible/configuration_menu.sh
```


실행할 플레이북을 선택합니다.

### <a name="playbook-os-config"></a>플레이북: OS 구성

이 플레이북은 모든 컴퓨터에서 소프트웨어 리포지토리, 패키지, 서비스 등의 구성을 포함하는 일반 OS 구성 설정을 수행합니다.

### <a name="playbook-sap-specific-os-config"></a>플레이북: SAP 특정 OS 구성

이 플레이북은 모든 컴퓨터에서 볼륨 그룹 및 파일 시스템 만들기와 소프트웨어 리포지토리, 패키지 및 서비스 구성을 포함하는 SAP OS 구성 설정을 수행합니다.

### <a name="playbook-bom-processing"></a>플레이북: BOM 처리

이 플레이북은 SAP 소프트웨어를 SCS 가상 머신에 다운로드합니다. 
    
### <a name="playbook-hana-db-install"></a>플레이북: HANA DB 설치

이 플레이북은 HANA 데이터베이스 인스턴스를 설치합니다.

### <a name="playbook-scs-install"></a>플레이북: SCS 설치
  
이 플레이북은 SAP Central Services를 설치합니다. 고가용성이 구성된 경우 이 플레이북은 ERS 인스턴스도 설치하고 Pacemaker를 구성합니다.

### <a name="playbook-db-load"></a>플레이북: DB 로드

이 플레이북은 기본 애플리케이션 서버로부터 데이터베이스 로드 작업을 호출합니다.

### <a name="playbook-pas-install"></a>플레이북: PAS 설치

이 플레이북은 기본 애플리케이션 서버를 설치합니다.
  
### <a name="playbook-app-install"></a>플레이북: 앱 설치

이 플레이북은 추가 애플리케이션 서버를 설치합니다.

이제 독립 실행형 HANA 시스템을 배포하고 구성했습니다. 가용성 높은 SAP HANA 데이터베이스를 구성해야 하는 경우 HANA HA 플레이북을 실행합니다.

### <a name="playbook-hana-ha-playbook"></a>플레이북: Hana HA 플레이북

이 플레이북은 HANA 데이터베이스용 HSR(HANA System Replication) 및 Pacemaker를 구성합니다.

## <a name="clean-up-installation"></a>설치 정리

> [!NOTE]
> 마친 후에는 이 자습서에서의 SAP 설치를 정리하는 것이 중요합니다. 그렇지 않으면 리소스와 관련한 비용이 계속해 서 발생합니다.

배포한 전체 SAP 인프라를 제거하려면 다음을 수행해야 합니다.

> [!div class="checklist"]
> * SAP 시스템 인프라 리소스 제거
> * 모든 워크로드 영역 제거(Landscape)
> * 컨트롤 플레인 제거

배포자 VM에서 SAP 인프라 리소스 및 워크로드 영역 제거를 실행합니다. Cloud Shell에서 컨트롤 플레인의 제거를 실행합니다.

시작하기 전에 Azure 계정에 로그인합니다. 그런 다음, 올바른 구독에 있는지 확인합니다.

### <a name="remove-sap-infrastructure"></a>SAP 인프라 제거

`SYSTEM` 폴더 내의 `DEV-NOEU-SAP01-X00` 하위 폴더로 이동합니다. 그런 후 다음 명령을 실행합니다.
  
```bash
export region_code="NOEU"

cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-${region_code}-SAP01-X00

${DEPLOYMENT_REPO_PATH}/deploy/scripts/remover.sh        \
  --parameterfile "DEV-${region_code}-SAP01-X00.tfvars"  \
  --type sap_system
```

### <a name="remove-sap-workload-zone"></a>SAP 워크로드 영역 제거

`LANDSCAPE` 폴더 내의 `DEV-XXXX-SAP01-INFRASTRUCTURE` 하위 폴더로 이동합니다. 이제 다음 명령을 실행합니다.

```bash

export region_code="NOEU"

cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-${region_code}-SAP01-INFRASTRUCTURE

${DEPLOYMENT_REPO_PATH}/deploy/scripts/remover.sh          \
      --parameterfile DEV-${region_code}-SAP01-INFRASTRUCTURE.tfvars           \
      --type sap_landscape
```

### <a name="remove-control-plane"></a>컨트롤 플레인 제거

[Cloud Shell](https://shell.azure.com)에 로그인합니다.

`WORKSPACES` 폴더로 이동합니다.
  
```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/
```

다음 두 환경 변수를 내보냅니다.

```bash
export DEPLOYMENT_REPO_PATH="~/Azure_SAP_Automated_Deployment/sap-automation"
export ARM_SUBSCRIPTION_ID="<subscriptionID>"
```

다음 명령을 실행합니다.

```bash
export region_code="NOEU"

${DEPLOYMENT_REPO_PATH}/deploy/scripts/remove_region.sh                                                                          \
    --deployer_parameter_file DEPLOYER/MGMT-${region_code}-DEP00-INFRASTRUCTURE/MGMT-${region_code}-DEP00-INFRASTRUCTURE.tfvars  \
    --library_parameter_file LIBRARY/MGMT-${region_code}-SAP_LIBRARY/MGMT-${region_code}-SAP_LIBRARY.tfvars                      
```

모든 리소스가 정리되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [컨트롤 플레인 구성](automation-configure-control-plane.md)


