---
title: 작업 영역 진단
titleSuffix: Azure Machine Learning
description: Azure Portal 또는 Python SDK를 사용 하 여 Azure Machine Learning 작업 영역 진단을 사용 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/18/2021
ms.topic: how-to
ms.openlocfilehash: 945524acb1d0292e1ac7a571c203527ac423f2f7
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132817891"
---
# <a name="how-to-use-workspace-diagnostics"></a>작업 영역 진단을 사용 하는 방법

Azure Machine Learning 작업 영역 문제를 식별 하는 데 사용할 수 있는 진단 API를 제공 합니다. 진단 보고서에 반환 되는 오류는 문제를 해결 하는 방법에 대 한 정보를 포함 합니다.

Azure Machine Learning studio 또는 Python SDK의 작업 영역 진단을 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine learning 작업 영역. 없는 경우 [작업 영역 만들기](quickstart-create-resources.md)를 참조 하세요.
* [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml).
## <a name="diagnostics-from-studio"></a>스튜디오 진단

[Azure Machine Learning studio](https://ml.azure.com) 또는 Python SDK에서 작업 영역에 대 한 진단을 실행 하 여 설치를 확인할 수 있습니다. 진단을 실행하려면 페이지 오른쪽 상단에서 ' __?__ ' 아이콘을 선택합니다. 그런 다음 __작업 영역 진단 실행__ 을 선택합니다.

:::image type="content" source="./media/how-to-workspace-diagnostic-api/diagnostics.png" alt-text="작업 영역 진단 단추 스크린샷":::

진단을 실행한 후 감지된 문제 목록이 반환됩니다. 이 목록에는 가능한 솔루션에 대한 링크가 포함되어 있습니다.

## <a name="diagnostics-from-python"></a>Python의 진단

다음 코드 조각은 Python에서 작업 영역 진단을 사용 하는 방법을 보여 줍니다.

```python
from azureml.core import Workspace

ws = Workspace.from_config()

diag_param = {
      "value": {
      }
    }

resp = ws.diagnose_workspace(diag_param)
print(resp)
```

응답은 작업 영역에서 검색 된 문제에 대 한 정보를 포함 하는 JSON 문서입니다. 다음 JSON은 예제 응답입니다.

```json
{
    'value': {
        'user_defined_route_results': [], 
        'network_security_rule_results': [], 
        'resource_lock_results': [], 
        'dns_resolution_results': [{
            'code': 'CustomDnsInUse', 
            'level': 'Warning', 
            'message': "It is detected VNet '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>' of private endpoint '/subscriptions/<subscription-id>/resourceGroups/larrygroup0916/providers/Microsoft.Network/privateEndpoints/<workspace-private-endpoint>' is not using Azure default dns. You need to configure your DNS server and check https://docs.microsoft.com/azure/machine-learning/how-to-custom-dns to make sure the custom dns is set up correctly."
        }], 
        'storage_account_results': [], 
        'key_vault_results': [], 
        'container_registry_results': [], 
        'application_insights_results': [], 
        'other_results': []
    }
}
```

문제가 검색 되지 않으면 빈 JSON 문서가 반환 됩니다.

자세한 내용은 [Workspace.diagnose_workspace ()](/python/api/azureml-core/azureml.core.workspace(class)#diagnose-workspace-diagnose-parameters-) 참조를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Workspace.diagnose_workspace ()](/python/api/azureml-core/azureml.core.workspace(class)#diagnose-workspace-diagnose-parameters-)
* [포털 또는 SDK에서 작업 영역을 관리 하는 방법](how-to-manage-workspace.md)
