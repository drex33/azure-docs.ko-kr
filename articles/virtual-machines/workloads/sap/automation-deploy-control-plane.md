---
title: SAP 배포 자동화 프레임 워크에 대 한 제어 평면 배포 정보
description: Azure의 SAP 배포 자동화 프레임 워크 내에서 제어 계획 배포 프로세스의 개요입니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 8e9c0d5984242178523772e7028b3443e2e197df
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133031121"
---
# <a name="deploy-the-control-plane"></a>컨트롤 플레인 배포

[Azure의 SAP 배포 자동화 프레임 워크](automation-deployment-framework.md) 에 대 한 제어 평면 배포는 다음 구성 요소로 구성 됩니다.
 - 배포자
 - SAP 라이브러리

:::image type="content" source="./media/automation-deployment-framework/control-plane.png" alt-text="다이어그램: 컨트롤 플레인":::

## <a name="prepare-the-deployment-credentials"></a>배포 자격 증명 준비

SAP 배포 프레임 워크는 배포를 수행할 때 서비스 사용자를 사용 합니다. 서비스 주체를 만들 수 있는 권한이 있는 계정을 사용 하 여 다음 단계를 사용 하 여 제어 평면 배포에 대 한 서비스 주체를 만들 수 있습니다.


```azurecli
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

```azurecli
az role assignment create --assignee <appId> --role "User Access Administrator"
```

## <a name="deploy-the-control-plane"></a>컨트롤 플레인 배포
   
샘플 배포자 구성 파일 `MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars` 은 폴더에 있습니다 `~/Azure_SAP_Automated_Deployment/WORKSPACES/DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE` .

샘플 SAP 라이브러리 구성 파일은 `MGMT-WEEU-SAP_LIBRARY.tfvars` 폴더에 있습니다 `~/Azure_SAP_Automated_Deployment/WORKSPACES/LIBRARY/MGMT-WEEU-SAP_LIBRARY` .

아래 명령을 실행 하 여 SAP 라이브러리인 배포자를 만들고 서비스 주체 세부 정보를 배포 키 자격 증명 모음에 추가 합니다.

# <a name="linux"></a>[Linux](#tab/linux)

샘플 구성 파일을 복사 하 여 배포 자동화 프레임 워크 테스트를 시작할 수 있습니다.

```bash
cd ~/Azure_SAP_Automated_Deployment

cp -R sap-automation/deploy/samples/WORKSPACES WORKSPACES

```

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

az logout
az login

export subscriptionID=<subscriptionID>
export appId=<appID>
export spn_secret=<password>
export tenant_id=<tenant>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                         \
        --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  \
        --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                      \
        --subscription $subscriptionID                                                                           \
        --spn_id $appID                                                                                          \
        --spn_secret  $spn_secret                                                                                \ 
        --tenant_id $tenant_id
```

# <a name="windows"></a>[Windows](#tab/windows)

샘플 구성 파일을 복사 하 여 배포 자동화 프레임 워크 테스트를 시작할 수 있습니다.

```powershell

cd C:\Azure_SAP_Automated_Deployment

xcopy sap-automation\deploy\samples\WORKSPACES WORKSPACES

```



```powershell


$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"

cd C:\Azure_SAP_Automated_Deployment\WORKSPACES

New-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-DEP00-INFRASTRUCTURE\MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  -LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars -Subscription $subscription -SPN_id $appId -SPN_password $spn_secret -Tenant_id $tenant_id
```
---


> [!NOTE]
> 샘플 값을 `<subscriptionID>` 구독 ID로 바꾸어야 합니다.
> `<appID>`, `<password>` , 값을 `<tenant>` SPN 만들기의 출력 값으로 바꿉니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [SAP 작업 영역 구성](automation-deploy-workload-zone.md)


