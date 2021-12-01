---
title: Bicep를 사용 하 여 가상 네트워크 리소스 만들기
description: Bicep를 사용 하 여 가상 네트워크, 네트워크 보안 그룹 및 경로 테이블을 만드는 방법을 설명 합니다.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 12/01/2021
ms.openlocfilehash: dbadfa9bef344a13210d77f019f8e0a6f3eef69f
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386806"
---
# <a name="create-virtual-network-resources-by-using-bicep"></a>Bicep를 사용 하 여 가상 네트워크 리소스 만들기

많은 Azure 배포에서 네트워킹 리소스를 배포 및 구성 해야 합니다. Bicep를 사용 하 여 Azure 네트워킹 리소스를 정의할 수 있습니다.

## <a name="virtual-networks-and-subnets"></a>가상 네트워크 및 서브넷

형식을 사용 하 여 리소스를 만들어 가상 네트워크를 정의 [`Microsoft.Network/virtualNetworks`](/azure/templates/microsoft.network/virtualnetworks?tabs=bicep) 합니다.

### <a name="configure-subnets-by-using-the-subnets-property"></a>서브넷 속성을 사용 하 여 서브넷 구성

가상 네트워크에는 가상 네트워크 내의 IP 주소에 대 한 논리적 그룹인 서브넷이 포함 됩니다. Bicep에서 서브넷을 정의 하는 방법에는 `subnets` 가상 네트워크 리소스의 속성을 사용 하 고 형식으로 [자식 리소스](child-resource-name-type.md) 를 만들어 두 가지가 있습니다 `Microsoft.Network/virtualNetworks/subnets` .

> [!WARNING]
> 서브넷을 자식 리소스로 정의 하지 마십시오. 이 방법을 사용 하면 후속 배포 중에 리소스에 대 한 가동 중지 시간이 발생 하거나 배포가 실패할 수 있습니다.

다음 예제와 같이 가상 네트워크 정의 내에서 서브넷을 정의 하는 것이 가장 좋습니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/scenarios-virtual-networks/vnet.bicep" range="7-30, 39" :::

두 방법을 사용 하 여 서브넷을 정의 하 고 만들 수 있지만 중요 한 차이점이 있습니다. 자식 리소스를 사용 하 여 서브넷을 정의 하는 경우 Bicep 파일을 처음 배포할 때 가상 네트워크가 배포 됩니다. 그런 다음 가상 네트워크 배포가 완료 된 후 각 서브넷이 배포 됩니다. 이러한 시퀀싱은 Azure Resource Manager 각 개별 리소스를 개별적으로 배포 하기 때문에 발생 합니다.

동일한 Bicep 파일을 다시 배포 하는 경우 동일한 배포 시퀀스가 발생 합니다. 그러나 `subnets` 속성은 효과적으로 비어 있기 때문에 구성 된 서브넷 없이 가상 네트워크를 배포 합니다. 그런 다음 가상 네트워크를 다시 구성 하면 서브넷 리소스가 다시 배포 되어 각 서브넷이 다시 설정 됩니다. 경우에 따라이 동작으로 인해 배포 중에 가상 네트워크 내의 리소스가 연결을 잃게 됩니다. 다른 상황에서 Azure는 가상 네트워크를 수정 하지 못하게 하 고 배포는 실패 합니다.

### <a name="access-subnet-resource-ids"></a>서브넷 리소스 Id 액세스

서브넷의 리소스 ID를 참조 해야 하는 경우가 많습니다. 속성을 사용 하 여 서브넷을 정의 하는 경우에 `subnets` 도 [ `existing` 키워드를 사용](resource-declaration.md#existing-resources) 하 여 서브넷에 대 한 강력한 형식의 참조를 가져온 다음 서브넷의 속성에 액세스할 수 있습니다 `id` .

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/scenarios-virtual-networks/vnet.bicep" range="7-42" highlight="26-28, 30-32, 35-36" :::

이 예제에서는 키워드를 사용 하 여 `existing` 서브넷 리소스에 액세스 합니다. 전체 서브넷 리소스를 정의 하는 대신 이전 섹션에서 설명한 위험이 없습니다.

`existing`및 키워드를 결합 하 여 `scope` 다른 리소스 그룹의 가상 네트워크 또는 서브넷 리소스를 참조할 수도 있습니다.

## <a name="network-security-groups"></a>네트워크 보안 그룹

네트워크 보안 그룹은 서브넷 또는 네트워크 인터페이스에서 트래픽의 인바운드 및 아웃 바운드 흐름을 제어 하는 규칙을 적용 하는 데 자주 사용 됩니다. Bicep 파일 내에서 많은 수의 규칙을 정의 하 고 여러 Bicep 파일에서 규칙을 공유 하는 것이 복잡할 수 있습니다. 복잡 하거나 많은 네트워크 보안 그룹을 사용할 때 [공유 변수 파일 패턴](patterns-shared-variable-file.md) 을 사용 하는 것이 좋습니다.

## <a name="private-endpoints"></a>프라이빗 엔드포인트

전용 끝점을 [승인 해야](../../private-link/manage-private-endpoint.md)합니다. 경우에 따라 승인이 자동으로 수행 됩니다. 하지만 다른 시나리오에서는 사용할 수 있기 전에 끝점을 승인 해야 합니다.

개인 끝점 승인은 작업 이므로 Bicep 코드 내에서 직접 수행할 수 없습니다. 그러나 [배포 스크립트](../templates/deployment-script-template.md) 를 사용 하 여 작업을 호출할 수 있습니다. 또는 파이프라인 스크립트와 같이 Bicep 파일 외부에서 작업을 호출할 수 있습니다.

## <a name="related-resources"></a>관련 참고 자료

- 리소스 설명서
  - [`Microsoft.Network/virtualNetworks`](/azure/templates/microsoft.network/virtualNetworks?tabs=bicep)
  - [`Microsoft.Network/networkSecurityGroups`](/azure/templates/microsoft.network/networksecuritygroups?tabs=bicep)
- [자식 리소스](child-resource-name-type.md)
- 빠른 시작 템플릿
  - [두 서브넷을 사용 하 여 Virtual Network 만들기](https://azure.microsoft.com/resources/templates/vnet-two-subnets/)
  - [진단 로그를 사용 하 여 Virtual Network](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/vnet-create-with-diagnostic-logs)
 