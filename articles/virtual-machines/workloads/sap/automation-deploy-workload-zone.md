---
title: Automation framework의 작업 영역 배포 정보
description: Azure의 SAP 배포 자동화 프레임 워크 내에서 SAP 워크 로드 영역 배포 프로세스의 개요입니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 1942eb957d601696c6dcb8044386d7c2304748c8
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730420"
---
# <a name="workload-zone-deployment-in-sap-automation-framework"></a>SAP automation framework의 작업 영역 배포

[SAP 응용 프로그램](automation-deployment-framework.md#sap-concepts) 에는 일반적으로 여러 개발 계층이 있습니다. 예를 들어 개발, 품질 보증 및 프로덕션 계층이 있을 수 있습니다. [Azure의 SAP 배포 자동화 프레임 워크](automation-deployment-framework.md) 는 이러한 계층을 [작업 영역](automation-deployment-framework.md#deployment-components)으로 참조 합니다.

여러 Azure 지역에서 작업 영역을 사용할 수 있습니다. 각 작업 영역에는 자체 Azure VNet (azure Virtual Network)이 있습니다.

SAP 작업 영역에서 제공 하는 서비스는 다음과 같습니다.

- Azure Virtual Network (서브넷 및 네트워크 보안 그룹 포함)
- 시스템 자격 증명을 Azure Key Vault 합니다.
- 부트 진단에 대 한 Storage 계정
- cloud 미러링 모니터 서버에 대 한 Storage 계정

:::image type="content" source="./media/automation-deployment-framework/workload-zone.png" alt-text="다이어그램 SAP 워크 로드 영역.":::

작업 영역은 일반적으로 허브 및 스포크 아키텍처의 스포크에 배포 됩니다. 사용자의 구독에 있을 수 있습니다.

컨트롤 평면에서 사설 DNS을 지원 합니다.


## <a name="core-configuration"></a>핵심 구성

다음 예제 매개 변수 파일에는 필수 매개 변수만 표시 됩니다.

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example (PROD and NP)
environment="DEV"

# The location value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"

# The network logical name is mandatory - it is used in the naming convention and should map to the workload virtual network logical name 
network_name="SAP01"

# network_address_space is a mandatory parameter when an existing Virtual network is not used
network_address_space="10.110.0.0/16"

# admin_subnet_address_prefix is a mandatory parameter if the subnets are not defined in the workload or if existing subnets are not used
admin_subnet_address_prefix="10.110.0.0/19"

# db_subnet_address_prefix is a mandatory parameter if the subnets are not defined in the workload or if existing subnets are not used
db_subnet_address_prefix="10.110.96.0/19"

# app_subnet_address_prefix is a mandatory parameter if the subnets are not defined in the workload or if existing subnets are not used
app_subnet_address_prefix="10.110.32.0/19"

# The automation_username defines the user account used by the automation
automation_username="azureadm"

```

## <a name="preparing-the-workload-zone-deployment-credentials"></a>작업 영역 배포 자격 증명 준비

SAP 배포 프레임 워크는 배포를 수행할 때 서비스 사용자를 사용 합니다. 서비스 주체를 만들 수 있는 권한이 있는 계정을 사용 하 여 다음 단계를 사용 하 여 워크 로드 영역 배포에 대 한 서비스 주체를 만들 수 있습니다.


```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscriptionID>" --name="<environment>-Deployment-Account"
  
```

> [!IMPORTANT]
> 서비스 사용자의 이름은 고유 해야 합니다.
>
> 명령에서 출력 값을 기록 합니다.
   > - appId
   > - password
   > - tenant

서비스 사용자에 게 올바른 사용 권한을 할당 합니다. 

```azurecli-interactive
az role assignment create --assignee <appId> --role "User Access Administrator"
```

## <a name="deploying-the-sap-workload-zone"></a>SAP 워크 로드 영역 배포
   
샘플 작업 영역 구성 파일은 `DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars` 폴더에 있습니다 `~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE` .

아래 명령을 실행 하면 SAP 워크 로드 영역이 배포 됩니다.

# <a name="linux"></a>[Linux](#tab/linux)

> [!TIP]
> 배포자에서이 작업을 수행 합니다.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>
keyvault=<keyvaultName>
storageaccount=<storageaccountName>
statefile_subscription=<statefile_subscription>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh \
        --parameterfile DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars   \
        --keyvault $keyvault                                   \
        --state_subscription $statefile_subscription           \
        --subscription $subscriptionID                         \
        --spn_id $appID                                        \
        --spn_secret "$spn_secret"                             \
        --tenant_id $tenant_id
```
# <a name="windows"></a>[Windows](#tab/windows)

```powershell
$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"
$keyvault=<keyvaultName>
$storageaccount=<storageaccountName>
$statefile_subscription=<statefile_subscription>

cd C:\Azure_SAP_Automated_Deployment\WORKSPACES\LANDSCAPE\DEV-WEEU-SAP01-INFRASTRUCTURE

New-SAPWorkloadZone -Parameterfile .DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars 
-Subscription $subscription -SPN_id $appId -SPN_password $spn_secret -Tenant_id $tenant_id
-State_subscription $statefile_subscription -Vault $keyvault -$StorageAccountName $storageaccount
```

---

> [!NOTE]
> 샘플 값을 `<subscriptionID>` 구독 ID로 바꾸어야 합니다.
> `<appID>`, `<password>` , 값을 `<tenant>` SPN 만들기의 출력 값으로 바꾸고,을 배포자 key vault name으로 바꾸고,을 terraform 상태 파일이 포함 된 `<keyvaultName>` `<storageaccountName>` `<statefile_subscription>` 저장소 계정의 구독 ID로 바꿉니다 .를 입력 합니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [Automation framework의 SAP 시스템 배포 정보](automation-configure-system.md)
