---
title: Azure Batch 계정으로 프라이빗 엔드포인트 사용
description: 프라이빗 엔드포인트를 사용하여 Azure Batch 계정에 비공개로 연결하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: references_regions
ms.openlocfilehash: ad3a869f2eee259133c6ab5dd18e1e615825a718
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566324"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Azure Batch 계정으로 프라이빗 엔드포인트 사용

기본적으로 [Azure Batch 계정](accounts.md)에는 공용 엔드포인트가 있으며 공개적으로 액세스할 수 있습니다. Batch 서비스는 프라이빗 Batch 계정을 만들고 공용 네트워크 액세스를 사용하지 않도록 설정하는 기능을 제공합니다.

[Azure Private Link](../private-link/private-link-overview.md)를 사용하면 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 통해 Azure Batch 계정에 연결할 수 있습니다. 프라이빗 엔드포인트는 가상 네트워크 내부에 있는 서브넷의 개인 IP 주소 세트입니다. 그런 다음, 개인 IP 주소를 통해 Azure Batch 계정에 대한 액세스를 제한할 수 있습니다.

Private Link를 사용하면 사용자가 가상 네트워크 내부에서 또는 피어링된 가상 네트워크에서 Azure Batch 계정에 액세스할 수 있습니다. Private Link에 매핑된 리소스 역시 프라이빗 피어링을 사용하여 VPN 또는 [Azure ExpressRoute](../expressroute/expressroute-introduction.md)를 통해 온-프레미스에서 액세스할 수 있습니다. [자동 또는 수동 승인 방법](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)을 사용하여 Private Link를 통해 구성된 Azure Batch 계정에 연결할 수 있습니다.

> [!IMPORTANT]
> Azure Batch의 프라이빗 연결 지원은 현재 독일 중부 및 독일 북동부를 제외한 모든 지역에서 사용할 수 있습니다.

이 문서에서는 프라이빗 Batch 계정을 만들고 프라이빗 엔드포인트를 사용하여 액세스하는 단계를 설명합니다.

## <a name="azure-portal"></a>Azure portal

다음 단계에 따라 Azure Portal을 사용하여 프라이빗 Batch 계정을 만듭니다.

1. **리소스 만들기** 창에서 **일괄 서비스** 를 선택한 다음 **만들기** 를 선택합니다.
2. **기본** 탭에 구독, 리소스 그룹, 지역 및 배치 계정 이름을 입력한 후 **다음: 고급** 을 선택합니다.
3. **고급** 탭에서 **공용 네트워크 액세스** 를 **사용 안 함** 으로 설정합니다.
4. **설정** 에서 **비공개 엔드포인트 연결** 을 선택한 다음 **+ 프라이빗 엔드포인트** 를 선택합니다.
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="프라이빗 엔드포인트 연결":::
5. **기본** 창에서 구독, 리소스 그룹, 프라이빗 엔드포인트 리소스 이름 및 지역 세부 정보를 입력하거나 선택한 후 **다음: 리소스** 를 선택합니다.
6. **리소스** 창에서 **리소스 유형** 을 **Microsoft.Batch/batchAccounts** 로 설정합니다. 액세스하려는 프라이빗 Batch 계정을 선택한 후 **다음: 구성** 을 선택합니다.
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="프라이빗 엔드포인트 만들기 - 리소스 창":::
7. **구성** 창에서 다음 정보를 입력하거나 선택합니다.
   - **가상 네트워크**: 가상 네트워크를 선택합니다.
   - **서브넷**: 서브넷을 선택합니다.
   - **프라이빗 DNS 영역과 통합**: **예** 를 선택합니다. 프라이빗 엔드포인트에 비공개로 연결하려면 DNS 레코드가 필요합니다. 프라이빗 엔드포인트를 프라이빗 DNS 영역과 통합하는 것이 좋습니다. 자체 DNS 서버를 활용하거나 가상 머신의 호스트 파일을 사용하여 DNS 레코드를 만들 수도 있습니다.
   - **프라이빗 DNS 영역**: privatelink.\<region\>.batch.azure.com을 선택합니다. 프라이빗 DNS 영역은 자동으로 결정됩니다. Azure Portal을 사용하여 변경할 수 없습니다.
8. **검토 + 만들기** 를 선택한 다음 Azure가 구성을 확인할 때까지 기다립니다.
9. **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다.

프라이빗 엔드포인트가 프로비저닝되면 프라이빗 엔드포인트를 사용하여 동일한 가상 네트워크의 VM에서 Batch 계정에 액세스할 수 있습니다.

> [!IMPORTANT]
> 프라이빗 엔드포인트가 프로비저닝된 가상 네트워크 외부에서 작업을 수행하면 Azure Portal에 "AuthorizationFailure" 메시지가 표시됩니다.

Azure Portal에서 IP 주소를 보려면:

1. **모든 리소스** 를 선택합니다.
2. 이전에 만든 프라이빗 엔드포인트를 검색합니다.
3. **개요** 탭을 선택하여 DNS 설정 및 IP 주소를 확인합니다.

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="프라이빗 엔드포인트 DNS 설정 및 IP 주소":::

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

[Azure Resource Manager 템플릿을 사용하여 Batch 계정을 만들 때](quick-create-template.md) 템플릿을 수정하여 아래와 같이 **publicNetworkAccess** 를 **사용 안 함** 으로 설정합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>DNS 영역 구성

프라이빗 엔드포인트를 만든 서브넷 내부의 [프라이빗 DNS 영역](../dns/private-dns-privatednszone.md)을 사용합니다. 각 개인 IP 주소가 DNS 항목에 매핑되도록 엔드포인트를 구성합니다.

프라이빗 엔드포인트를 만들 때 Azure의 [프라이빗 DNS 영역](../dns/private-dns-privatednszone.md)과 통합할 수 있습니다. [사용자 지정 도메인](../dns/dns-custom-domain.md)을 대신 사용하기로 선택하는 경우 프라이빗 엔드포인트에 대해 예약된 모든 개인 IP 주소의 DNS 레코드를 추가하도록 구성해야 합니다.

## <a name="pricing"></a>가격 책정

프라이빗 엔드포인트와 관련된 비용에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link/)을 참조하세요.

## <a name="current-limitations-and-best-practices"></a>현재 제한 사항 및 모범 사례

프라이빗 Batch 계정을 만들 때 다음 사항에 유의하세요.

- 프라이빗 엔드포인트 리소스는 Batch 계정과 동일한 구독에 만들어야 합니다.
- 프라이빗 연결을 삭제하려면 프라이빗 엔드포인트 리소스를 삭제해야 합니다.
- Batch 계정이 공용 네트워크 액세스를 사용하여 만들어진 후에는 프라이빗 액세스로만 변경할 수 없습니다.
- 프라이빗 엔드포인트를 삭제하거나 Batch 계정에서 지역을 제거해도 프라이빗 DNS 영역의 DNS 레코드는 자동으로 제거되지 않습니다. 이 프라이빗 DNS 영역에 연결된 새 프라이빗 엔드포인트를 추가하기 전에 DNS 레코드를 수동으로 제거해야 합니다. DNS 레코드를 정리하지 않으면 프라이빗 엔드포인트 제거 또는 지역 제거 후에 추가된 지역에 대한 데이터 중단이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [가상 네트워크에서 배치 풀을 만드는 방법](batch-virtual-network.md)에 대해 알아봅니다.
- [공용 IP 주소 없이 배치 풀을 만드는 방법](batch-pool-no-public-ip-address.md) 알아보기
- [지정된 공용 IP 주소로 배치 풀을 만드는 방법](create-pool-public-ip.md)에 대해 알아보세요.
- [Azure Private Link](../private-link/private-link-overview.md)에 대해 알아봅니다.
