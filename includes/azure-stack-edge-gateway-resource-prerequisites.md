---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: cb0bf7926f1bbc310e422867752e1ee1762c979a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731626"
---
시작하기 전에 다음 사항을 확인합니다.

* Data Stack Edge 리소스에 대해 Microsoft Azure 구독이 활성화되어 있습니다. [Microsoft EA(기업계약)](https://azure.microsoft.com/overview/sales-number/), [CSP(클라우드 솔루션 공급자)](/partner-center/azure-plan-lp) 또는 [Microsoft Azure 스폰서쉽](https://azure.microsoft.com/offers/ms-azr-0036p/)과 같은 지원되는 구독을 사용했는지 확인합니다.
* Azure Stack Edge, IoT Hub 및 Azure Storage 리소스에 대한 리소스 그룹 수준의 소유자 또는 기여자 액세스 권한이 있습니다.

* Azure Edge Hardware Center에서 주문을 만들려면 Microsoft.EdgeOrder 공급자가 등록되어 있는지 확인해야 합니다. 등록 방법에 대한 정보는 [리소스 공급자 등록](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers)으로 이동하세요.   
* Azure Stack Edge 리소스를 만들려면 리소스 그룹 수준에서 범위가 지정된 기여자(또는 그 이상) 권한이 있어야 합니다. 또한 `Microsoft.DataBoxEdge` 공급자가 등록되어 있는지 확인해야 합니다. 등록 방법에 대한 정보는 [리소스 공급자 등록](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers)으로 이동하세요.
  * IoT Hub 리소스를 만들려면 해당 Microsoft.Devices 공급자가 등록되어 있는지 확인합니다. 등록 방법에 대한 정보는 [리소스 공급자 등록](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers)으로 이동하세요.
  * 스토리지 계정 리소스를 만들려면 리소스 그룹 수준에서 범위가 지정되는 기여자 이상의 액세스 권한이 다시 필요합니다. Azure Storage는 기본적으로 등록된 리소스 공급자입니다.
* Azure Active Directory Graph API에 대한 관리자 또는 사용자 액세스 권한이 있습니다. 자세한 내용은 [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)를 참조하세요.
* 액세스 자격 증명이 있는 Microsoft Azure Storage 계정이 있습니다.