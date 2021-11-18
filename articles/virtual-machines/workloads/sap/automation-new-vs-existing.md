---
title: 신규 및 기존 배포에 대한 자동화 프레임워크 구성
description: 새 시나리오와 기존 시나리오 모두에 대해 Azure에서 SAP 배포 자동화 프레임워크를 구성하는 방법입니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 72ec525988c902c6fbb0c9fec71fa225eb2489ae
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132760832"
---
# <a name="configuring-for-new-and-existing-deployments"></a>새 배포 및 기존 배포 구성

새 배포 시나리오와 기존 배포 시나리오 모두에서 Azure에서 SAP 배포 [자동화 프레임워크를](automation-deployment-framework.md) 사용할 수 있습니다. 

새 배포 시나리오에서 자동화 프레임워크는 기존 Azure 인프라를 사용하지 않습니다. 배포 프로세스는 가상 네트워크, 서브넷, 키 자격 증명 모음 등을 만듭니다.

기존 배포 시나리오에서 자동화 프레임워크는 기존 Azure 인프라를 사용합니다. 예를 들어 배포는 기존 가상 네트워크를 사용합니다.
## <a name="new-deployment-scenarios"></a>새 배포 시나리오

다음은 새 리소스를 만드는 새 배포 시나리오의 예입니다.

> [!IMPORTANT]
> 시나리오에 필요한 모든 예제 구성을 수정합니다.
### <a name="new-deployment"></a>새 배포

이 시나리오에서 자동화 프레임워크는 모든 Azure 구성 요소를 만들고 [배포자](automation-deployment-framework.md#deployment-components)를 사용합니다. 이 배포 예제에는 다음이 포함됩니다.

- 서유럽 Azure 지역의 두 환경
  - Management( `MGMT` )는 컨트롤 플레인을 호스트합니다.
  - `DEV`개발()은 개발 환경을 호스트합니다.
- 배포자
- SAP 라이브러리
- SAP 시스템( `SID X00` ) :
  - 응용 프로그램 서버 2개
  - HA(고가용성) 중앙 서비스 인스턴스
  - SUSE 12 SP5를 사용하는 단일 노드 HANA 백 엔드가 있는 웹 디스패처

| 구성 요소       | 매개 변수 파일 위치                                                       |
| --------------- | ----------------------------------------------------------------------------- |
| 배포자        | DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars |
| 라이브러리         | LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                    |
| 워크로드 영역   | LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE/DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars  |
| 시스템          | SYSTEM/DEV-WEEU-SAP01-X00/DEV-WEEU-SAP01-X00.tfvars                           |


이 시나리오를 테스트하려면 다음을 수행합니다. 

[SAP 배포 자동화 프레임워크](https://github.com/Azure/sap-automation/) 리포지토리를 복제하고 매개 변수 파일에 대한 샘플 파일을 루트 폴더에 복사합니다.

```bash
cd ~/Azure_SAP_Automated_Deployment
mkdir -p WORKSPACES/DEPLOYER
cp sap-automation/samples/WORKSPACES/DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE WORKSPACES/DEPLOYER/. -r

mkdir -p WORKSPACES/LIBRARY
cp sap-automation/samples/WORKSPACES/LIBRARY/MGMT-WEEU-SAP_LIBRARY WORKSPACES/LIBRARY/. -r

mkdir -p WORKSPACES/LANDSCAPE
cp sap-automation/samples/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE WORKSPACES/LANDSCAPE/. -r

mkdir -p WORKSPACES/SYSTEM
cp sap-automation/samples/WORKSPACES/SYSTEM/DEV-WEEU-SAP01-X00 WORKSPACES/SYSTEM/. -r
cd WORKSPACES
```
    
배포자 및 라이브러리를 설치하여 컨트롤 플레인을 준비합니다. 샘플 값을 서비스 주체의 정보로 바꾸어야 합니다.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

export DEPLOYMENT_REPO_PATH="${HOME}/Azure_SAP_Automated_Deployment/sap-automation/"
export ARM_SUBSCRIPTION_ID="${subscriptionID}"

$DEPLOYMENT_REPO_PATH/scripts/prepare_region.sh
    --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars \
    --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                     \
    --subscription $subscriptionID                                                                          \
    --spn_id $appID                                                                                         \
    --spn_secret $spn_secret                                                                                \
    --tenant_id $tenant
    --auto-approve
```

PowerShell을 사용하여 배포를 수행할 수도 있습니다.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

$Subscription=<subscriptionID>
$SPN_id=<appID>
$SPN_password=<password>
$Tenant_id=<tenant>

New-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-DEP01-INFRASTRUCTURE\MGMT-WEEU-DEP01-INFRASTRUCTURE.tfvars 
-LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars
-Subscription $Subscription
-SPN_id $SPN_id
-SPN_password $SPN_password
-Tenant_id $Tenant_id
```

Bash 또는 PowerShell 스크립트를 실행하여 워크로드 영역을 배포합니다. 

샘플 자격 증명을 서비스 주체의 정보로 바꾸어야 합니다. 제어 평면 배포에서 사용한 것과 동일한 서비스 주체 자격 증명을 사용할 수 있지만 프로덕션 배포의 경우 워크로드 영역당 다른 서비스 주체를 사용하는 것이 좋습니다.

```bash

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh \
    --parameterfile DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars       \
    --deployer_environment 'MGMT'                              \           
    --subscription $subscriptionID                             \
    --spn_id $appID                                            \
    --spn_secret $spn_secret                                   \
    --tenant_id $tenant                                        \
    --auto-approve
```

```powershell

cd \Azure_SAP_Automated_Deployment\WORKSPACES\LANDSCAPE\DEV-WEEU-SAP01-INFRASTRUCTURE

$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"

New-SAPWorkloadZone --parameterfile .\DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars 
    -DeployerEnvironment MGMT
    -Subscription $subscription
    -SPN_id $appId
    -SPN_password $spn_secret
    -Tenant_id $tenant_id
```

SAP 시스템을 배포합니다. Bash 또는 PowerShell 명령을 실행합니다.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-WEEU-SAP01-X00

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh --parameterfile DEV-WEEU-SAP01-X00.tfvars --type sap_system --auto-approve
```

```powershell
Import-Module "SAPDeploymentUtilities.psd1"
cd \Azure_SAP_Automated_Deployment\WORKSPACES\SYSTEM\DEV-WEEU-SAP01-X00

New-SAPSystem --parameterfile .\DEV-WEEU-SAP01-X00.tfvars
        -Type sap_system
```
## <a name="existing-example-scenarios"></a>기존 예제 시나리오

다음은 기존 Azure 리소스를 사용하는 기존 시나리오의 예입니다.

> [!IMPORTANT]
> 시나리오에 필요한 모든 예제 구성을 수정합니다.
> 모든 '<arm_resource_id>' 자리 표시자 업데이트

### <a name="existing-environment-scenario"></a>기존 환경 시나리오

이 시나리오에서 자동화 프레임워크는 기존 Azure 구성 요소를 사용하고 [배포자](automation-deployment-framework.md#deployment-components)를 사용합니다. 이러한 기존 구성 요소에는 리소스 그룹, 스토리지 계정, 가상 네트워크, 서브넷 및 네트워크 보안 그룹이 포함됩니다. 이 배포 예제에는 다음이 포함됩니다.

- 미국 동부 2 지역의 두 환경
  - Management( `MGMT` )는 컨트롤 플레인을 호스트합니다.
  - 품질 `QA` 보증()은 SAP QA 환경을 호스트합니다.
- 배포자
- SAP 라이브러리
- SAP 시스템( `SID X01` ) :
  - 응용 프로그램 서버 2개
  - HA 중앙 서비스 인스턴스
  - Windows Server 2016 실행되는 Microsoft SQL 서버 백 엔드를 사용하는 데이터베이스
  - 웹 디스패처 

| 구성 요소       | 매개 변수 파일 위치                                                       |
| --------------- | ----------------------------------------------------------------------------- |
| 배포자        | DEPLOYER/MGMT-EUS2-DEP01-INFRASTRUCTURE/MGMT-EUS2-DEP01-INFRASTRUCTURE.tfvars |
| 라이브러리         | LIBRARY/MGMT-EUS2-SAP_LIBRARY/MGMT-EUS2-SAP_LIBRARY.tfvars                    |
| 워크로드 영역   | LANDSCAPE/QA-EUS2-SAP03-INFRASTRUCTURE/QA-EUS2-SAP03-INFRASTRUCTURE.tfvars    |
| 시스템          | SYSTEM/QA-EUS2-SAP03-X01/QA-EUS2-SAP03-X01.tfvars                             |


매개 변수 파일의 루트 폴더에 샘플 파일을 복사합니다.

```bash
cd ~/Azure_SAP_Automated_Deployment
mkdir -p WORKSPACES/DEPLOYER
cp sap-automation/samples/WORKSPACES/DEPLOYER/MGMT-EUS2-DEP01-INFRASTRUCTURE WORKSPACES/DEPLOYER/. -r
    
mkdir -p WORKSPACES/LIBRARY
cp sap-automation/samples/WORKSPACES/LIBRARY/MGMT-EUS2-SAP_LIBRARY WORKSPACES/LIBRARY/. -r
    
mkdir -p WORKSPACES/LANDSCAPE
cp sap-automation/samples/WORKSPACES/LANDSCAPE/QA-EUS2-SAP03-INFRASTRUCTURE WORKSPACES/LANDSCAPE/. -r
    
mkdir -p WORKSPACES/SYSTEM
cp sap-automation/samples/WORKSPACES/SYSTEM/QA-EUS2-SAP03-X01 WORKSPACES/SYSTEM/. -r
cd WORKSPACES
```

샘플 tfvars 파일에는 리소스 그룹, 가상 네트워크 및 서브넷에 대한 실제 Azure 리소스 ID로 대체해야 하는 자리 표시자 '"<azure_resource_id>"이 있습니다.

배포자 및 SAP 라이브러리를 설치하는 컨트롤 플레인을 배포합니다. Bash 또는 PowerShell 명령을 실행합니다. 샘플 자격 증명을 서비스 주체의 정보로 바꾸어야 합니다.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

export DEPLOYMENT_REPO_PATH="${HOME}/Azure_SAP_Automated_Deployment/sap-automation/"
export ARM_SUBSCRIPTION_ID="${subscriptionID}"

$DEPLOYMENT_REPO_PATH/scripts/prepare_region.sh
    --deployer_parameter_file DEPLOYER/MGMT-EUS2-DEP01-INFRASTRUCTURE/MGMT-EUS2-DEP01-INFRASTRUCTURE.tfvars  \
    --library_parameter_file LIBRARY/MGMT-EUS2-SAP_LIBRARY/MGMT-EUS2-SAP_LIBRARY.tfvars                      \                      
    --subscription $subscriptionID                                                                           \
    --spn_id $appID                                                                                          \
    --spn_secret $spn_secret                                                                                 \
    --tenant_id $tenant
    --auto-approve
```

```powershell

cd \Azure_SAP_Automated_Deployment\WORKSPACES


$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"

New-SAPAutomationRegion 
    -DeployerParameterfile .\DEPLOYER\MGMT-EUS2-DEP01-INFRASTRUCTURE\MGMT-EUS2-DEP01-INFRASTRUCTURE.json 
    -LibraryParameterfile .\LIBRARY\MGMT-EUS2-SAP_LIBRARY\MGMT-EUS2-SAP_LIBRARY.json 
    -Subscription $subscription 
    -SPN_id $appId 
    -SPN_password $spn_secret 
    -Tenant_id $tenant_id
    -Silent      
```

Bash 또는 PowerShell 스크립트를 실행하여 워크로드 영역을 배포합니다. 

샘플 자격 증명을 서비스 주체 정보로 바꾸어야 합니다. 제어 평면 배포에서 사용한 것과 동일한 서비스 주체 자격 증명을 사용할 수 있지만 프로덕션 배포의 경우 워크로드 영역당 다른 서비스 주체를 사용하는 것이 좋습니다.

```bash

cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/QA-EUS2-SAP03-INFRASTRUCTURE

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh \
    --parameterfile QA-EUS2-SAP03-INFRASTRUCTURE.tfvars        \
    --deployer_environment MGMT                                \           
    --subscription $subscriptionID                             \
    --spn_id $appID                                            \
    --spn_secret $spn_secret                                   \
    --tenant_id $tenant                                        \
    --auto-approve
```

```powershell

cd \Azure_SAP_Automated_Deployment\WORKSPACES\LANDSCAPE\QA-EUS2-SAP03-INFRASTRUCTURE

$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"

New-SAPWorkloadZone --parameterfile .\QA-EUS2-SAP03-INFRASTRUCTURE.tfvars 
    -DeployerEnvironment MGMT
    -Subscription $subscription
    -SPN_id $appId
    -SPN_password $spn_secret
    -Tenant_id $tenant_id
```


QA 환경에서 SAP 시스템을 배포합니다. Bash 또는 PowerShell 명령을 실행합니다.


```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/QA-EUS2-SAP03-X01

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh --parameterfile QA-EUS2-SAP03-X01.tfvars --type sap_system --auto-approve
```

```powershell
cd \Azure_SAP_Automated_Deployment\WORKSPACES\SYSTEM\QA-EUS2-SAP03-X01

New-SAPSystem --parameterfile .\QA-EUS2-SAP03-tfvars.json -Type sap_system
```
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [실습 랩 지침](automation-tutorial.md)
