---
title: 자동화 프레임워크의 워크로드 영역 배포 정보
description: Azure의 SAP 배포 자동화 프레임워크 내에서 SAP 워크로드 영역 배포 프로세스에 대한 개요입니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 8f4e77f1f9688a5c5c9e2875c409df8d7e927eb8
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133031925"
---
# <a name="workload-zone-deployment-in-sap-automation-framework"></a>SAP 자동화 프레임워크의 워크로드 영역 배포

[SAP 애플리케이션에는](automation-deployment-framework.md#sap-concepts) 일반적으로 여러 개발 계층이 있습니다. 예를 들어 개발, 품질 보증 및 프로덕션 계층이 있을 수 있습니다. [Azure의 SAP 배포 자동화 프레임워크는](automation-deployment-framework.md) 이러한 계층을 [워크로드 영역이라고 합니다.](automation-deployment-framework.md#deployment-components)

여러 Azure 지역에서 워크로드 영역을 사용할 수 있습니다. 그러면 각 워크로드 영역에 자체 Azure Virtual Network(Azure VNet)가 있습니다.

SAP 워크로드 영역에서 제공하는 서비스는 다음과 같습니다.

- 서브넷 및 네트워크 보안 그룹을 포함한 Azure Virtual Network.
- 시스템 자격 증명의 경우 Azure Key Vault.
- 부팅 진단에 대한 Storage 계정
- 클라우드 Storage 계정

:::image type="content" source="./media/automation-deployment-framework/workload-zone.png" alt-text="다이어그램 SAP 워크로드 영역.":::

워크로드 영역은 일반적으로 허브 및 스포크 아키텍처의 스포크에 배포됩니다. 자체 구독에 있을 수 있습니다.

컨트롤 플레인에서 프라이빗 DNS 지원합니다.


## <a name="core-configuration"></a>핵심 구성

다음 예제 매개 변수 파일은 필수 매개 변수만 보여줍니다.

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

## <a name="preparing-the-workload-zone-deployment-credentials"></a>워크로드 영역 배포 자격 증명 준비

SAP 배포 프레임워크는 배포를 수행하는 경우 서비스 주체를 사용합니다. 서비스 주체를 만들 수 있는 권한이 있는 계정을 사용하여 다음 단계를 사용하여 워크로드 영역 배포에 대한 서비스 주체를 만들 수 있습니다.


```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscriptionID>" --name="<environment>-Deployment-Account"
  
```

> [!IMPORTANT]
> 서비스 주체의 이름은 고유해야 합니다.
>
> 명령의 출력 값을 기록합니다.
   > - appId
   > - password
   > - tenant

서비스 주체에 올바른 권한을 할당합니다. 

```azurecli
az role assignment create --assignee <appId> --role "User Access Administrator"
```

## <a name="deploying-the-sap-workload-zone"></a>SAP 워크로드 영역 배포
   
샘플 워크로드 영역 구성 `DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars` 파일은 `~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE` 폴더에 있습니다.

아래 명령을 실행하면 SAP 워크로드 영역이 배포됩니다.

# <a name="linux"></a>[Linux](#tab/linux)

> [!TIP]
> 배포자에서 이 작업을 수행합니다.

샘플 구성 파일을 복사하여 배포 자동화 프레임워크 테스트를 시작할 수 있습니다.

```bash
cd ~/Azure_SAP_Automated_Deployment

cp -R sap-automation/deploy/samples/WORKSPACES WORKSPACES

```


```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE

export subscriptionID=<subscriptionID>
export appId=<appID>
export spn_secret=<password>
export tenant_id=<tenant>
export keyvault=<keyvaultName>
export storageaccount=<storageaccountName>
export statefile_subscription=<statefile_subscription>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh \
        --parameterfile DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars   \
        --keyvault $keyvault                                   \
        --state_subscription $statefile_subscription           \
        --subscription $subscriptionID                         \
        --spn_id $appID                                        \
        --spn_secret $spn_secret                               \
        --tenant_id $tenant_id
```
# <a name="windows"></a>[Windows](#tab/windows)

샘플 구성 파일을 복사하여 배포 자동화 프레임워크 테스트를 시작할 수 있습니다.

```powershell

cd C:\Azure_SAP_Automated_Deployment

xcopy sap-automation\deploy\samples\WORKSPACES WORKSPACES

```


```powershell
$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"
$keyvault=<keyvaultName>
$storageaccount=<storageaccountName>
$statefile_subscription=<statefile_subscription>

cd C:\Azure_SAP_Automated_Deployment\WORKSPACES\LANDSCAPE\DEV-WEEU-SAP01-INFRASTRUCTURE

New-SAPWorkloadZone -Parameterfile DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars 
-Subscription $subscription -SPN_id $appId -SPN_password $spn_secret -Tenant_id $tenant_id
-State_subscription $statefile_subscription -Vault $keyvault -$StorageAccountName $storageaccount
```

---

> [!NOTE]
> 샘플 값을 구독 ID로 바꾸어야 `<subscriptionID>` 합니다.
> , , 값을 SPN 생성의 출력 값으로 바꾸고 를 배포자 키 자격 증명 모음 `<appID>` `<password>` 이름으로 `<tenant>` `<keyvault>` 바꾸기를 Terraform 상태 파일이 포함된 스토리지 계정의 이름으로 바꾸기를 `<storageaccount>` Terraform 상태 `<statefile_subscription>` 파일이 포함된 스토리지 계정의 구독 ID로 대체합니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [자동화 프레임워크의 SAP 시스템 배포 정보](automation-configure-system.md)
