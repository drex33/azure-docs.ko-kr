---
title: Azure 공용 IP 구성을 다른 Azure 지역 Azure Portal로 이동
description: Azure Portal을 사용하여 Azure 공용 IP 구성을 한 Azure 지역에서 다른 지역으로 이동하려면 템플릿을 사용합니다.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6deb906d55f74cc3d418473a669a81d3d2ddf5e4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130214666"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 공용 IP 구성을 다른 지역으로 이동

기존 Azure 퍼블릭 IP 구성을 한 지역에서 다른 지역으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성 및 SKU를 사용하여 퍼블릭 IP를 만들 수 있습니다. 재해 복구 계획의 일부로 퍼블릭 IP 구성을 다른 지역으로 이동할 수도 있습니다.

**Azure 퍼블릭 IP는 지역별로 지정되며, 한 지역에서 다른 지역으로 이동할 수 없습니다.** 그러나 Azure Resource Manager 템플릿을 사용하여 퍼블릭 IP의 기존 구성을 내보낼 수 있습니다.  그런 다음, 퍼블릭 IP를 템플릿으로 내보내고, 대상 지역과 일치하도록 매개 변수를 수정한 다음, 템플릿을 새 지역에 배포하여 리소스를 다른 지역에 준비할 수 있습니다.  Resource Manager 및 템플릿에 대한 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 참조하세요.


## <a name="prerequisites"></a>필수 구성 요소

- 이동하려는 Azure 지역에 Azure 퍼블릭 IP가 있는지 확인합니다.

- Azure 공용 IP는 지역 간에 이동할 수 없습니다.  새 퍼블릭 IP를 대상 지역의 리소스에 연결해야 합니다.

- 퍼블릭 IP 구성을 내보내고 템플릿을 배포하여 다른 지역에 퍼블릭 IP를 만들려면 네트워크 기여자 역할 이상이 필요합니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 부하 분산 디바이스, NSG(네트워크 보안 그룹) 및 가상 네트워크를 포함하되 국한되지 않습니다.

- Azure 구독에서 사용되는 대상 Azure 지역에 퍼블릭 IP를 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 퍼블릭 IP 추가를 지원할 수 있는 충분한 리소스가 있는지 확인합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 Resource Manager 템플릿을 사용하여 구성 이동을 위해 공용 IP를 준비하고 Azure Portal을 사용하여 공용 IP 구성을 대상 지역으로 이동하는 방법을 표시합니다.

### <a name="export-the-template-and-deploy-from-a-script"></a>템플릿 내보내기 및 스크립트에서 배포

1. [Azure Portal](https://portal.azure.com) > **리소스 그룹** 에 로그인합니다.
2. 원본 공용 IP를 포함하는 리소스 그룹을 찾아 클릭합니다.
3. > **설정** > **템플릿 내보내기** 를 선택합니다.
4. **템플릿 내보내기** 블레이드에서 **배포** 를 선택합니다.
5. **템플릿** > **매개 변수 편집** 을 클릭하여 온라인 편집기에서 **에parameters.json** 파일을 엽니다.
8. 공용 IP 이름의 매개 변수를 편집하려면 **매개 변수** > **값** 하의 속성을 원본 공용 IP 이름에서 대상 공용 IP의 이름으로 변경하고 이름을 따옴표로 묶었는지 확인합니다.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  편집기에서 **저장** 을 클릭 합니다.

9.  **템플릿** > **템플릿 편집** 을 클릭하여 온라인 편집기에서 **template.json** 파일을 엽니다.

10. 공용 IP가 이동될 대상 영역을 편집하려면 **리소스** 에서 **위치** 속성을 변경합니다.

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. 지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요.  지역 코드는 공백이 없는 지역 이름입니다(예: **Central US** = **centralus**).

12. 또한 선택하는 경우 템플릿의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택적입니다.

    * **Sku** - **template.json** 파일에서 **sku** > **이름** 속성을 변경하여 구성의 공용 IP의 sku를 표준에서 기본 또는 기본에서 표준으로 변경할 수 있습니다.

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        기본 및 표준 sku 공용 IP의 차이점에 대한 자세한 정보는 [공용 IP 주소 만들기, 변경 또는 삭제](./ip-services/virtual-network-public-ip-address.md)를 참조하세요.

    * **퍼블릭 IP 할당 방법** 및 **유휴 시간 제한** - **publicIPAllocationMethod** 속성을 **Dynamic** 에서 **Static** 으로 또는 **Static** 에서 **Dynamic** 으로 변경하여 템플릿에서 이러한 옵션을 모두 변경할 수 있습니다. **idleTimeoutInMinutes** 속성을 원하는 크기만큼 변경하여 유휴 시간 제한을 변경할 수 있습니다.  기본값은 **4** 입니다.

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        할당 방법 및 유휴 시간 제한 값에 대한 자세한 내용은 [퍼블릭 IP 주소 만들기, 변경 또는 삭제](./ip-services/virtual-network-public-ip-address.md)를 참조하세요.


13. 온라인 편집기에서 **저장** 을 클릭합니다.

14. **기본** > **구독** 을 클릭하여 대상 공용 IP를 배포할 구독을 선택 합니다.

15. **기본** > **리소스 그룹** 을 클릭 하여 대상 공용 IP가 배포될 리소스 그룹을 선택 합니다.  **새로 만들기** 를 클릭하여 대상 공용 IP에 대한 새 리소스 그룹을 만들 수 있습니다.  이름이 기존 원본 공용 IP의 원본 리소스 그룹과 같지 않은지 확인 합니다.

16. **기본** > **위치** 가 공용 IP를 배포하려는 대상 위치로 설정되어 있는지 확인합니다.

17. **설정** 에서 이름이 위의 매개 변수 편집기에서 입력한 이름과 일치하는지 확인합니다.

18. **계약조건** 아래 상자를 선택합니다.

19. **구매** 단추를 클릭하여 대상 공용 IP를 배포합니다.

## <a name="discard"></a>취소

대상 공용 IP를 버리고자 하는 경우 대상 공용 IP를 포함하는 리소스 그룹을 삭제합니다.  이렇게 하려면 포털의 대시보드에서 리소스 그룹을 선택하고 개요 페이지 위쪽에서 **삭제** 를 선택합니다.

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 공용 IP의 이동을 완료하려면 공용 IP 또는 리소스 그룹을 삭제합니다. 이렇게 하려면 포털의 대시보드에서 공용 IP 또는 리소스 그룹을 선택하고 각 페이지의 위쪽에서 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 퍼블릭 IP를 한 지역에서 다른 지역으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)