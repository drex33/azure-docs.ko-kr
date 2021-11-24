---
title: '자습서: Logic Apps 내에서 Resource Graph 쿼리 실행 자동화'
description: 이 자습서에서는 논리 앱에서 Resource Graph를 호출하는 방법을 알아봅니다.
ms.date: 11/18/2021
ms.topic: tutorial
ms.openlocfilehash: 7ca4d11c9f7b5627ca0730cae3f771ecc2c210aa
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871790"
---
# <a name="tutorial-run-azure-resource-graph-queries-in-an-azure-logic-app"></a>자습서: Azure Logic App에서 Azure Resource Graph 쿼리 실행

Azure Resource Graph Explorer를 사용하면 구독, 관리 그룹 및 전체 테넌트에서 대규모로 리소스를 쿼리할 수 있습니다.

리소스를 주기적으로 쿼리하여 특정 리소스 또는 관리 속성을 확인하고 결과에 따라 조치를 취해야 하는 경우 Azure Logic Apps를 활용할 수 있습니다.

이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> - 주기적으로 실행할 Azure Resource Graph 쿼리 작성
> - 시스템이 할당한 관리 ID를 사용하여 Azure Logic App 만들기
> - 특정 리소스, 리소스 그룹 및 구독에 액세스하도록 관리 ID 설정
> - 논리 앱을 주기적으로 호출하여 Azure Resource Graph 쿼리 실행 자동화

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 Azure 구독이 필요합니다. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/)을 만드세요.

## <a name="write-an-azure-resource-graph-query"></a>Azure Resource Graph 쿼리 작성

주기적으로 실행하려는 Azure Resource Graph 쿼리가 있는 경우 이를 사용할 수 있습니다. 이 자습서에서는 다음 쿼리를 사용하여 Virtual Machines의 전원 상태 요약을 검색합니다.

   ```kusto
   Resources
   | where type =~ 'microsoft.compute/virtualmachines'
   | extend vmPowerState = tostring(properties.extended.instanceView.powerState.code)
   | summarize count() by vmPowerState
   ```

   자세한 내용은 [샘플 – 전원 상태별로 가상 머신 요약](../samples/advanced.md#vm-powerstate)을 참조하세요.

나중에 논리 앱을 구성할 때 필요하므로 위의 쿼리를 가까운 곳에 보관합니다.

## <a name="create-a-logic-app"></a>논리 앱 만들기

1. 포털 메뉴에서 **Logic Apps** 를 선택하거나, 모든 페이지의 위쪽에 있는 Azure 검색 상자를 사용하여 **Logic Apps** 를 검색하고 선택합니다.

2. 화면 왼쪽 위에 있는 **만들기** 단추를 클릭하고 논리 앱 만들기를 계속합니다.

## <a name="setup-a-managed-identity"></a>관리 ID 설정

### <a name="create-a-new-system-assigned-managed-identity"></a>새로운 시스템이 할당한 관리 ID 만들기

Azure Portal 내에서 만든 논리 앱으로 이동합니다. 페이지 왼쪽에서 **ID** 를 선택합니다. 그런 다음, 시스템이 할당한 ID 단추를 선택하고 상태를 **켜기** 로 설정한 다음, **저장** 을 클릭합니다.

### <a name="add-role-assignments-to-your-managed-identity"></a>관리 ID에 역할 할당 추가

새로 만든 관리 ID에 구독, 리소스 그룹 및 리소스 전반에 걸쳐 쿼리할 수 있는 기능을 제공하려면 해당 쿼리에 역할 할당을 통해 액세스 권한을 할당해야 합니다. 관리 ID에 대한 역할 할당을 할당하는 방법에 대한 자세한 내용은 [관리 ID에 Azure 역할 할당](../../../role-based-access-control/role-assignments-portal-managed-identity.md)을 참조하세요.

## <a name="configure-and-run-your-logic-app"></a>논리 앱 구성 및 실행

Azure Portal 내 논리 앱의 코드 보기에서 다음을 붙여넣습니다.

```json
{
    "definition": {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
        "actions": {
            "HTTP_2": {
                "inputs": {
                    "authentication": {
                        "type": "ManagedServiceIdentity"
                    },
                    "body": {
                        "query": "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend vmPowerState = tostring(properties.extended.instanceView.powerState.code) | summarize count() by vmPowerState"
                    },
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "method": "POST",
                    "queries": {
                        "api-version": "2021-03-01"
                    },
                    "uri": "https://management.azure.com/providers/Microsoft.ResourceGraph/resources"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "contentVersion": "1.0.0.0",
        "outputs": {},
        "parameters": {},
        "triggers": {
            "Recurrence": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 1440
                },
                "type": "Recurrence"
            }
        }
    },
    "parameters": {}
}
```

그런 다음, Azure Portal 내에서 논리 앱의 디자이너 보기로 이동하여 필요에 따라 설정을 수정합니다.

마지막으로 논리 앱을 저장하고 실행합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 설정된 간격으로 ARG 쿼리 요청을 자동화하는 Azure Logic App을 만들었습니다. Resource Graph 언어에 대해 자세히 알아보려면 아래에 연결된 쿼리 언어 세부 정보 페이지로 계속 진행하여 더 많은 Azure Resource Graph 쿼리를 사용해 보세요.

질문이 있는 경우 resourcegraphsupport@microsoft.com으로 문의하세요.

> [!div class="nextstepaction"]
> [쿼리 언어에 대한 자세한 정보 가져오기](../concepts/query-language.md)
