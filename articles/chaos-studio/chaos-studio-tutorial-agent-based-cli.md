---
title: Azure CLI Azure Chaos Studio에서 에이전트 기반 오류를 사용하는 실험을 만듭니다.
description: 에이전트 기반 오류를 사용하는 실험을 만들고 Azure CLI 사용하여 비정상 상태 에이전트를 구성합니다.
author: johnkemnetz
ms.topic: how-to
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: e2f605e06cd7d57acbe9fe421f4c1b883cc4d193
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373254"
---
# <a name="create-a-chaos-experiment-that-uses-an-agent-based-fault-on-a-virtual-machine-or-virtual-machine-scale-set-with-the-azure-cli"></a>가상 머신 또는 가상 머신 확장 집합에서 에이전트 기반 오류를 사용하는 비정상 상황에서 Azure CLI

비정상 상황에서는 비정상 상황에서 이러한 오류를 발생시켜 애플리케이션이 오류에 대해 복원력이 있는지 확인할 수 있습니다. 이 가이드에서는 Chaos 실험 및 Azure Chaos Studio를 사용하여 Linux 가상 머신에서 높은 CPU 이벤트를 발생시킬 것입니다. 이 실험을 실행하면 리소스가 부족해지는 애플리케이션을 방어하는 데 도움이 될 수 있습니다.

이러한 동일한 단계를 사용하여 에이전트 기반 오류에 대한 실험을 설정하고 실행할 수 있습니다. **에이전트 기반** 오류는 계측할 필요 없이 Azure 리소스에 대해 직접 실행되는 서비스 직접 오류와 달리 비정상 상태 에이전트를 설치하고 설치해야 합니다.


## <a name="prerequisites"></a>필수 조건

- Azure 구독 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- 가상 머신 - 가상 머신이 없는 경우 다음 [단계에 따라 을 만들](../virtual-machines/linux/quick-create-portal.md)수 있습니다.
- [가상 머신에 SSH를](../virtual-machines/ssh-keys-portal.md) 허용하는 네트워크 설정
- 사용자가 할당한 관리 ID. 사용자 할당 관리 ID가 없는 경우 다음 [단계에 따라 만들](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md) 수 있습니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 열 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, **Enter** 를 선택하여 실행합니다.

이 자습서에서 CLI를 로컬로 설치하여 사용하려면 Azure CLI 버전 2.0.30 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="assign-managed-identity-to-the-virtual-machine"></a>가상 머신에 관리 ID 할당

가상 머신에서 Chaos Studio를 설정하기 전에 또는 명령을 사용하여 에이전트를 설치하려는 각 가상 머신 및/또는 가상 머신 확장 집합에 사용자 할당 관리 ID를 할당해야 `az vm identity assign` `az vmss identity assign` 합니다. 를 비정상 상황이던 `$VM_RESOURCE_ID` / `$VMSS_RESOURCE_ID` 대상으로 추가하는 VM의 리소스 ID로 바꾸고 를 `$MANAGED_IDENTITY_RESOURCE_ID` 사용자 할당 관리 ID의 리소스 ID로 바꿉

**Virtual Machine**

```azurecli-interactive
az vm identity assign --ids $VM_RESOURCE_ID --identities $MANAGED_IDENTITY_RESOURCE_ID
```

**Virtual Machine Scale Set**

```azurecli-interactive
az vmss identity assign --ids $VMSS_RESOURCE_ID --identities $MANAGED_IDENTITY_RESOURCE_ID
```

## <a name="enable-chaos-studio-on-your-virtual-machine"></a>가상 머신에서 Chaos Studio 사용

Chaos Studio는 해당 가상 머신이 Chaos Studio에 먼저 온보딩되지 않은 한 가상 머신에 오류를 삽입할 수 없습니다. 리소스에 [대상 및 기능을](chaos-studio-targets-capabilities.md) 만든 다음, Chaos 에이전트를 설치하여 가상 머신을 Chaos Studio에 온보딩합니다. 가상 머신에는 두 가지 대상 유형이 있습니다. 하나는 서비스 직접 오류를 사용하도록 설정하고(에이전트가 필요하지 않은 경우) 에이전트 기반 오류를 사용하도록 설정하는 유형입니다(에이전트 설치 필요). chaos 에이전트는 게스트 운영 체제에 오류를 삽입할 수 있는 [가상 머신 확장으로 가상 머신에](../virtual-machines/extensions/overview.md) 설치된 애플리케이션입니다.

### <a name="install-stress-ng-linux-only"></a>stress-ng 설치(Linux에만 해당)

Linux용 Chaos Studio 에이전트에는 가상 머신에서 다양한 스트레스 이벤트를 일으킬 수 있는 오픈 소스 애플리케이션인 stress-ng가 필요합니다. [Linux 가상 머신에 연결하고](../virtual-machines/ssh-keys-portal.md) 패키지 관리자에 대한 적절한 설치 명령을 실행하여 stress-ng를 설치할 수 있습니다. 예를 들면 다음과 같습니다.

```bash
sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng
```

또는

```bash
sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng
```

### <a name="enable-chaos-target-and-capabilities"></a>비정상 대응 대상 및 기능 사용

다음으로, 에이전트가 Chaos Studio에 연결하는 데 사용할 사용자 할당 관리 ID를 지정하는 각 가상 머신 또는 가상 머신 확장 집합에 Microsoft-Agent 대상을 설정합니다. 이 예제에서는 모든 VM에 대해 하나의 관리 ID를 사용합니다. 대상은 REST API 통해 만들어야 합니다. 이 예제에서는 CLI 명령을 사용하여 `az rest` REST API 호출을 실행합니다.

1. 을 `$USER_IDENTITY_CLIENT_ID` 만든 사용자 할당 관리 ID의 Azure Portal 개요에서 찾을 수 있는 관리 ID의 clientID로 대체하고, 테넌트 정보 아래의 Azure Portal Azure Active Directory 찾을 수 있는 Azure `$USER_IDENTITY_TENANT_ID` **테넌트** ID로  을 수정합니다. JSON을 Azure CLI 실행하는 동일한 위치에 파일로 저장합니다(Cloud Shell JSON 파일을 끌어서 놓아 업로드할 수 있습니다).

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

2. 를 대상 가상 `$RESOURCE_ID` 머신 또는 가상 머신 확장 집합의 리소스 ID로 바쳐 대상을 만듭니다. `target.json`를 이전 단계에서 만든 JSON 파일의 이름으로 대체합니다.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview --body @target.json --query properties.agentProfileId -o tsv
    ```

3. 이후 단계에서 사용하기 위해 이 명령에서 반환한 **agentProfileId에** 대한 GUID를 복사합니다.

4. 아래와 같이 capability.json 파일을 만들어 대상에 기능을 만듭니다. Azure CLI 실행하는 위치와 동일한 위치에 JSON 파일을 저장합니다. `$RESOURCE_ID`을 대상 가상 머신 또는 가상 머신 확장 집합의 리소스 ID로 바꾸고 `$CAPABILITY` 를 사용하도록 설정하는 오류 [기능의 이름으로](chaos-studio-fault-library.md)대체합니다.
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent/capabilities/$CAPABILITY?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    예를 들어 CPU 압력 기능을 사용하도록 설정하는 경우:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/Microsoft-Agent/capabilities/CPUPressure-1.0?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

### <a name="install-the-chaos-studio-virtual-machine-extension"></a>Chaos Studio 가상 머신 확장 설치

chaos 에이전트는 가상 머신 또는 가상 머신 확장 집합 인스턴스에서 실행되어 에이전트 기반 오류를 실행하는 애플리케이션입니다. 설치하는 동안 에이전트가 Chaos Studio에 인증하는 데 사용해야 하는 관리 ID, 사용자가 만든 Microsoft-Agent 대상의 프로필 ID 및 필요에 따라 에이전트가 진단 이벤트를 Azure 애플리케이션 Insights 보낼 수 있도록 하는 Application Insights 계측 키를 사용하여 에이전트를 구성합니다.

1. 시작하기 전에 다음 세부 정보가 있는지 확인합니다.
    * **agentProfileId** - 대상을 만들 때 반환된 속성입니다. 이 속성이 없는 경우 속성을 실행하고 복사할 수 `az rest --method get --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview` `agentProfileId` 있습니다.
    * **ClientId** - 대상에 사용되는 사용자 할당 관리 ID의 클라이언트 ID입니다. 이 속성이 없는 경우 속성을 실행하고 복사할 수 있습니다. `az rest --method get --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview` `clientId`
    * (선택 사항) **AppInsightsKey** - 애플리케이션 Insights 구성 요소의 계측 키로, 포털의 애플리케이션 Insights 페이지에서 **Essentials** 아래에 있습니다.

2. Chaos Studio VM 확장을 설치합니다. `$VM_RESOURCE_ID`을 VM의 리소스 ID로 바꾸거나 `$SUBSCRIPTION_ID` , 및 를 가상 머신 확장 `$RESOURCE_GROUP` `$VMSS_NAME` 집합에 대한 해당 속성으로 바꿉다. `$AGENT_PROFILE_ID`을 agentProfileId로, 를 `$USER_IDENTITY_CLIENT_ID` 관리 ID의 clientID로, 를 `$APP_INSIGHTS_KEY` Application Insights 계측 키로 대체합니다. 애플리케이션 Insights 사용하지 않는 경우 해당 키/값 쌍을 제거합니다.

    #### <a name="install-the-agent-on-a-virtual-machine"></a>가상 머신에 에이전트 설치

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
3. 가상 머신 확장 집합을 설정하는 경우 인스턴스가 최신 모델로 업그레이드되었는지 확인합니다. 

## <a name="create-an-experiment"></a>실험 만들기

이제 가상 머신을 온보딩하면 실험을 만들 수 있습니다. 비정상 상황에서는 대상 리소스에 대해 수행할 작업을 순차적으로 실행되는 단계와 병렬로 실행되는 분기로 구성하여 정의합니다.

1. 아래 JSON 샘플부터 실험 JSON을 작성합니다. [실험 만들기 API](/rest/api/chaosstudio/experiments/create-or-update) 및 오류 라이브러리를 사용하여 실행하려는 실험에 해당하도록 JSON을 [수정합니다.](chaos-studio-fault-library.md)

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

    가상 머신 확장 집합에 대해 실행하는 경우 대상으로 지정할 인스턴스 번호를 포함하도록 오류 매개 변수를 수정합니다.

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

    가상 머신 확장 집합으로 이동한 다음 인스턴스 를 클릭하여 Azure Portal 확장 집합 인스턴스 번호를 식별할 수 **있습니다.** 인스턴스 이름은 인스턴스 번호로 끝납니다.
    
2. Azure CLI 사용하여 실험을 만들고 `$SUBSCRIPTION_ID` , 및 `$RESOURCE_GROUP` `$EXPERIMENT_NAME` 을 실험의 속성으로 바 엽니다. 실험 JSON을 저장하고 업로드했는지 확인하고 `experiment.json` JSON 파일 이름을 업데이트합니다.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    각 실험은 해당 시스템 할당 관리 ID를 만듭니다. `principalId`다음 단계에 대한 응답에서 이 ID에 대한 를 기록해 둡 수 있습니다.

## <a name="give-experiment-permission-to-your-virtual-machine"></a>가상 머신에 실험 권한 부여
비정상 상황에서 실험을 만들면 Chaos Studio는 대상 리소스에 대해 오류를 실행하는 시스템 할당 관리 ID를 만듭니다. 실험을 성공적으로 실행하려면 이 ID에 대상 리소스에 [대한 적절한 권한이](chaos-studio-fault-providers.md) 부여되어야 합니다. 에이전트 기반 오류에는 판독기 역할이 필요합니다. Virtual Machine 기여자 등 */읽기 권한이 없는 다른 역할은 에이전트 기반 오류에 대한 적절한 권한을 부여하지 않습니다.

아래 명령을 사용하여 가상 머신 또는 가상 머신 확장 집합에 대한 실험 액세스 권한을 부여합니다. 을 `$EXPERIMENT_PRINCIPAL_ID` 이전 `$RESOURCE_ID` 단계의 principalId로, 를 대상 가상 머신 또는 가상 머신 확장 집합의 리소스 ID(실험 정의에 사용된 Chaos Agent의 리소스 ID가 아닌 VM의 리소스 ID)로 바 실험에서 대상으로 지정된 각 가상 머신 또는 가상 머신 확장 집합에 대해 이 명령을 실행합니다.

```azurecli-interactive
az role assignment create --role "Reader" --assignee-principal-type "ServicePrincipal" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID 
```


## <a name="run-your-experiment"></a>실험 실행
이제 실험을 실행할 준비가 되었습니다. 영향을 확인하려면 별도의 브라우저 탭에서 가상 머신의 CPU 압력으로 [Azure Monitor 메트릭 차트를](../azure-monitor/essentials/tutorial-metrics.md) 여는 것이 좋습니다.

1. Azure CLI 사용하여 실험을 시작하고 , 및 `$SUBSCRIPTION_ID` `$RESOURCE_GROUP` `$EXPERIMENT_NAME` 을 실험의 속성으로 바 엽니다.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. 응답에는 실험 실행 시 실험 상태를 쿼리하는 데 사용할 수 있는 상태 URL이 포함됩니다.

## <a name="next-steps"></a>다음 단계
이제 에이전트 기반 실험을 실행했으므로 다음을 수행할 준비가 되었습니다.
- [서비스 직접 오류를 사용하는 실험 만들기](chaos-studio-tutorial-service-direct-portal.md)
- [실험 관리](chaos-studio-run-experiment.md)