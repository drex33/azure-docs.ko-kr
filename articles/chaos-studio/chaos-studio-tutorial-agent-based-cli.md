---
title: Azure CLI를 사용 하 여 Azure 비정상 스튜디오에서 에이전트 기반 오류를 사용 하는 실험 만들기
description: 에이전트 기반 오류를 사용 하는 실험을 만들고 Azure CLI로 비정상 에이전트를 구성 합니다.
author: johnkemnetz
ms.topic: how-to
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: e36799afb7d5884872211d023c9aa4b6874727ef
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352699"
---
# <a name="create-a-chaos-experiment-that-uses-an-agent-based-fault-on-a-virtual-machine-or-virtual-machine-scale-set-with-the-azure-cli"></a>Azure CLI를 사용 하 여 가상 머신 또는 가상 머신 확장 집합에서 에이전트 기반 오류를 사용 하는 비정상 실험을 만듭니다.

비정상 실험을 사용 하 여 제어 되는 환경에서 오류를 발생 시켜 응용 프로그램이 오류에 탄력적으로 대처할 수 있는지 확인할 수 있습니다. 이 가이드에서는 비정상 실험 및 Azure 비정상 스튜디오를 사용 하 여 Linux 가상 머신에서 높은 CPU 이벤트를 발생 시킵니다. 이 실험을 실행 하면 리소스가 않다거나 되는 응용 프로그램을 보호할 수 있습니다.

이러한 동일한 단계를 사용 하 여 에이전트 기반 오류에 대 한 실험을 설정 하 고 실행할 수 있습니다. **에이전트 기반** 오류는 계측 없이 Azure 리소스에 대해 직접 실행 되는 서비스 직접 오류와 달리 비정상 상황 에이전트의 설치 및 설치를 요구 합니다.


## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- 가상 머신 - 가상 컴퓨터가 없는 경우 [다음 단계를 수행 하](../virtual-machines/linux/quick-create-portal.md)여 가상 컴퓨터를 만들 수 있습니다.
- [가상 컴퓨터에 SSH](../virtual-machines/ssh-keys-portal.md) 를 사용할 수 있도록 하는 네트워크 설정
- 사용자가 할당한 관리 ID. 사용자 할당 관리 id가 없는 경우 [다음 단계에 따라 하나를 만들](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md) 수 있습니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 열 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, **Enter** 를 선택하여 실행합니다.

이 자습서에서 CLI를 로컬로 설치하여 사용하려면 Azure CLI 버전 2.0.30 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="assign-managed-identity-to-the-virtual-machine"></a>가상 컴퓨터에 관리 되는 id 할당

가상 머신에서 비정상 스튜디오를 설정 하기 전에 또는 명령을 사용 하 여 에이전트를 설치 하려는 각 가상 머신 및/또는 가상 머신 확장 집합에 사용자 할당 관리 id를 할당 해야 합니다 `az vm identity assign` `az vmss identity assign` . 을 비정상 `$VM_RESOURCE_ID` / `$VMSS_RESOURCE_ID` 대상으로 추가 하는 VM의 리소스 id로 바꾸고을 `$MANAGED_IDENTITY_RESOURCE_ID` 사용자 할당 관리 id의 리소스 id로 바꿉니다.

**Virtual Machine**

```azurecli-interactive
az vm identity assign --ids $VM_RESOURCE_ID --identities $MANAGED_IDENTITY_RESOURCE_ID
```

**Virtual Machine Scale Set**

```azurecli-interactive
az vmss identity assign --ids $VMSS_RESOURCE_ID --identities $MANAGED_IDENTITY_RESOURCE_ID
```

## <a name="enable-chaos-studio-on-your-virtual-machine"></a>가상 머신에서 비정상 스튜디오 사용

가상 머신이 비정상 스튜디오로 등록 하지 않는 한, 비정상 스튜디오는 가상 머신에 대 한 오류를 주입할 수 없습니다. 리소스에 대 한 [대상 및 기능](chaos-studio-targets-capabilities.md) 을 만든 다음 비정상 상황 에이전트를 설치 하 여 비정상 스튜디오로 가상 머신을 등록 합니다. 가상 컴퓨터에는 두 가지 대상 유형이 있습니다. 하나는 서비스 직접 오류 (에이전트가 필요 하지 않음)를 사용 하 고 에이전트 기반 오류 (에이전트를 설치 해야 함)를 사용 하도록 설정 하는 것입니다. 비정상 상황 에이전트는 가상 컴퓨터에 가상 컴퓨터 [확장](../virtual-machines/extensions/overview.md) 으로 설치 된 응용 프로그램으로, 게스트 운영 체제에 오류를 삽입할 수 있도록 합니다.

### <a name="install-stress-ng-linux-only"></a>스트레스 설치 (Linux만 해당)

Linux 용 비정상 스튜디오 에이전트에는 가상 머신에서 다양 한 스트레스 이벤트를 발생 시킬 수 있는 오픈 소스 응용 프로그램의 스트레스 기능이 필요 합니다. [Linux 가상 머신에 연결](../virtual-machines/ssh-keys-portal.md) 하 고 패키지 관리자에 대 한 적절 한 설치 명령을 실행 하 여 스트레스 기능을 설치할 수 있습니다. 예를 들면 다음과 같습니다.

```bash
sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng
```

또는

```bash
sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng
```

### <a name="enable-chaos-target-and-capabilities"></a>비정상 상황 대상 및 기능 사용

그런 다음, 에이전트가 비정상 스튜디오에 연결 하는 데 사용 하는 사용자 할당 관리 id를 지정 하는 각 가상 머신 또는 가상 머신 확장 집합에 대 한 Microsoft-Agent 대상을 설정 합니다. 이 예제에서는 모든 Vm에 대해 하나의 관리 되는 id를 사용 합니다. 대상은 REST API를 통해 만들어야 합니다. 이 예제에서는 `az rest` CLI 명령을 사용 하 여 REST API 호출을 실행 합니다.

1. 사용자가 만든 관리 id의 Azure Portal clientID로 대체 하 여 다음 JSON을 수정 하 `$USER_IDENTITY_CLIENT_ID` 고, `$USER_IDENTITY_TENANT_ID` **테 넌 트 정보** 아래에 있는 **Azure Active Directory** Azure Portal에서 찾을 수 있는 Azure 테 넌 트 id를 사용 하 여 다음 JSON을 수정 합니다. Azure CLI를 실행 하는 위치와 동일한 위치에 JSON을 파일로 저장 합니다. Cloud Shell에서는 JSON 파일을 끌어서 놓아 업로드할 수 있습니다.

    ```json
    {
      "properties": {
        "identities": [
          {
            "clientId": "$USER_IDENTITY_CLIENT_ID",
            "tenantId": "$USER_IDENTITY_TENANT_ID",
            "type": "AzureManagedIdentity"
          }
        ]
      }
    }
    ```

2. `$RESOURCE_ID`대상 가상 머신 또는 가상 머신 확장 집합의 리소스 ID로 대체 하 여 대상을 만듭니다. `target.json`을 이전 단계에서 만든 JSON 파일의 이름으로 바꿉니다.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview --body @target.json --query properties.agentProfileId -o tsv
    ```

3. 이 명령은 **agentProfileId** 에 대 한 GUID를 반환 합니다. 이후 단계에서 사용 하기 위해이를 복사 합니다.

4. 아래 표시 된 것 처럼 기능 json 파일을 만들어 대상에 기능을 만듭니다. Azure CLI를 실행 하는 위치와 동일한 위치에 JSON 파일을 저장 합니다. 를 `$RESOURCE_ID` 대상 가상 컴퓨터 또는 가상 컴퓨터 확장 집합의 리소스 ID로 바꾸고,을 `$CAPABILITY` [사용할 수 있는 오류 기능의 이름](chaos-studio-fault-library.md)으로 바꿉니다.
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent/capabilities/$CAPABILITY?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    예를 들어 CPU 압력 기능을 사용 하도록 설정 하는 경우:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/Microsoft-Agent/capabilities/CPUPressure-1.0?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

### <a name="install-the-chaos-studio-virtual-machine-extension"></a>비정상 스튜디오 가상 머신 확장 설치

비정상 상황 에이전트는 가상 머신 또는 가상 머신 확장 집합 인스턴스에서 실행 되어 에이전트 기반 오류를 실행 하는 응용 프로그램입니다. 설치 하는 동안 에이전트가 비정상 스튜디오에 인증 하는 데 사용 해야 하는 관리 되는 id를 사용 하 여 에이전트를 구성 하 고, 만든 Microsoft-Agent 대상의 프로필 ID를 구성 하 고, 에이전트에서 진단 이벤트를 Azure 애플리케이션 Insights 보낼 수 있도록 하는 Application Insights 계측 키를 선택적으로 구성 합니다.

1. 시작 하기 전에 다음과 같은 세부 정보가 있는지 확인 합니다.
    * **agentProfileId** -대상을 만들 때 반환 되는 속성입니다. 이 권한이 없으면를 실행 `az rest --method get --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview` 하 고 속성을 복사할 수 있습니다 `agentProfileId` .
    * **ClientId** -대상에서 사용 되는 사용자 할당 관리 id의 클라이언트 ID입니다. 이 권한이 없으면를 실행 `az rest --method get --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview` 하 고 속성을 복사할 수 있습니다 `clientId` .
    * 생략할 **AppInsightsKey** -Application Insights 구성 요소에 대 한 계측 키입니다. 이는 포털의 **Essentials** 에서 Application Insights 블레이드에서 찾을 수 있습니다.

2. 비정상 스튜디오 VM 확장을 설치 합니다. `$VM_RESOURCE_ID`을 VM의 리소스 ID로 바꾸고 `$SUBSCRIPTION_ID` ,을 `$RESOURCE_GROUP` `$VMSS_NAME` 가상 컴퓨터 확장 집합에 대 한 해당 속성으로 바꿉니다. 를 `$AGENT_PROFILE_ID` agentProfileId로 바꾸고,을 `$USER_IDENTITY_CLIENT_ID` 관리 되는 id의 clientID로 바꾸고,을 `$APP_INSIGHTS_KEY` Application Insights 계측 키로 바꿉니다. Application Insights 사용 하지 않는 경우 해당 키/값 쌍을 제거 합니다.

    #### <a name="install-the-agent-on-a-virtual-machine"></a>가상 컴퓨터에 에이전트 설치

    **Windows**

    ```azurecli-interactive
    az vm extension set --ids $VM_RESOURCE_ID --name ChaosWindowsAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    **Linux**

    ```azurecli-interactive
    az vm extension set --ids $VM_RESOURCE_ID --name ChaosLinuxAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    #### <a name="install-the-agent-on-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에 에이전트 설치

    **Windows**

    ```azurecli-interactive
    az vmss extension set --subscription $SUBSCRIPTION_ID --resource-group $RESOURCE_GROUP --vmss-name $VMSS_NAME --name ChaosWindowsAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    **Linux**

    ```azurecli-interactive
    az vmss extension set --subscription $SUBSCRIPTION_ID --resource-group $RESOURCE_GROUP --vmss-name $VMSS_NAME --name ChaosLinuxAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```
3. 가상 머신 확장 집합을 설정 하는 경우 인스턴스가 최신 모델로 업그레이드 되었는지 확인 합니다. 

## <a name="create-an-experiment"></a>실험 만들기

이제 가상 머신을 등록 하 여 실험을 만들 수 있습니다. 비정상 실험은 순서 대로 실행 되는 단계, 병렬로 실행 되는 분기, 병렬로 실행 되는 대상 리소스에 대해 수행 하려는 작업을 정의 합니다.

1. 아래 JSON 샘플부터 실험 JSON을 작성 합니다. [Create 실험 API](https://docs.microsoft.com/rest/api/chaosstudio/experiments/create-or-update) 및 [오류 라이브러리](chaos-studio-fault-library.md) 를 사용 하 여 실행 하려는 실험에 해당 하는 JSON을 수정 합니다.

    ```json
    {
      "identity": {
        "type": "SystemAssigned"
      },
      "location": "centralus",
      "properties": {
        "selectors": [
          {
            "id": "Selector1",
            "targets": [
              {
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myWindowsVM/providers/Microsoft.Chaos/targets/microsoft-agent",
                "type": "ChaosTarget"
              },
              {
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myLinuxVM/providers/Microsoft.Chaos/targets/micosoft-agent",
                "type": "ChaosTarget"
              }
            ],
            "type": "List"
          }
        ],
        "steps": [
          {
            "branches": [
              {
                "actions": [
                  {
                    "duration": "PT10M",
                    "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
                    "parameters": [
                      {
                        "key": "pressureLevel",
                        "value": "95"
                      }
                    ],
                    "selectorId": "Selector1",
                    "type": "continuous"
                  }
                ],
                "name": "Branch 1"
              }
            ],
            "name": "Step 1"
          }
        ]
      }
    }
    ```

    가상 머신 확장 집합에 대해 실행 하는 경우 대상으로 사용할 인스턴스 번호를 포함 하도록 오류 매개 변수를 수정 합니다.

    ```json
    "parameters": [
      {
        "key": "pressureLevel",
        "value": "95"
      },
      {
        "key": "virtualMachineScaleSetInstances",
        "value": "[0,1,2]"
      }
    ]
    ```

    가상 머신 확장 집합으로 이동 하 고 **인스턴스** 를 클릭 하 여 Azure Portal에서 확장 집합 인스턴스 번호를 식별할 수 있습니다. 인스턴스 이름은 인스턴스 번호에서 종료 됩니다.
    
2. `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` 및를 `$EXPERIMENT_NAME` 실험의 속성으로 바꿔 Azure CLI를 사용 하 여 실험을 만듭니다. JSON 파일 이름과 함께 실험 JSON 및 업데이트를 저장 하 고 업로드 했는지 확인 `experiment.json` 합니다.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    각 실험은 해당 하는 시스템 할당 관리 id를 만듭니다. `principalId`다음 단계에 대 한 응답에서이 id의를 확인 합니다.

## <a name="give-experiment-permission-to-your-virtual-machine"></a>가상 컴퓨터에 대 한 실험 권한 제공
비정상 실험을 만들 때 비정상 스튜디오는 대상 리소스에 대해 오류를 실행 하는 시스템 할당 관리 id를 만듭니다. 실험을 성공적으로 실행 하려면이 id에 대상 리소스에 대 [한 적절 한 권한을](chaos-studio-fault-providers.md) 부여 해야 합니다. 판독기 역할은 에이전트 기반 오류에 필요 합니다. 가상 컴퓨터 참가자와 같이 */읽기 권한이 없는 다른 역할은 에이전트 기반 오류에 대 한 적절 한 권한을 부여 하지 않습니다.

아래의 명령을 사용 하 여 가상 머신 또는 가상 머신 확장 집합에 대 한 실험 액세스를 제공 합니다 `$EXPERIMENT_PRINCIPAL_ID` . 여기서는 이전 단계의 principalId로,을 `$RESOURCE_ID` 대상 가상 머신 또는 가상 머신 확장 집합의 리소스 id (실험 정의에 사용 된 비정상 에이전트의 리소스 id가 아닌 VM의 리소스 id)로 바꿉니다. 실험을 대상으로 하는 각 가상 머신 또는 가상 머신 확장 집합에 대해이 명령을 실행 합니다.

```azurecli-interactive
az role assignment create --role "Reader" --assignee-principal-type "ServicePrincipal" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID 
```


## <a name="run-your-experiment"></a>실험 실행
이제 실험을 실행할 준비가 되었습니다. 영향을 확인 하려면 별도의 브라우저 탭에서 가상 컴퓨터의 CPU 압력을 포함 하는 [Azure Monitor 메트릭 차트](../azure-monitor/essentials/tutorial-metrics.md) 를 여는 것이 좋습니다.

1. Azure CLI를 사용 하 여 실험을 시작 하 고, `$SUBSCRIPTION_ID` `$RESOURCE_GROUP` 및를 `$EXPERIMENT_NAME` 실험의 속성으로 바꿉니다.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. 응답에는 실험을 실행할 때 실험 상태를 쿼리 하는 데 사용할 수 있는 상태 URL이 포함 되어 있습니다.

## <a name="next-steps"></a>다음 단계
이제 에이전트 기반 실험을 실행 했으므로 다음을 수행할 준비가 되었습니다.
- [서비스 직접 오류를 사용 하는 실험 만들기](chaos-studio-tutorial-service-direct.md)
- [실험 관리](chaos-studio-run-experiment.md)