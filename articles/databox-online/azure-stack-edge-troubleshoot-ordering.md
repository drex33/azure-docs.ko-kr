---
title: Azure Portal을 통한 Azure Stack Edge 주문 문제 해결
description: Azure Stack Edge 주문 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/21/2021
ms.author: alkohli
ms.openlocfilehash: 46a7d0ed41b8f10874c19c8d70ddc5d8c5d7f197
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608288"
---
# <a name="troubleshoot-your-azure-stack-edge-ordering-issues"></a>Azure Stack Edge 주문 문제 해결

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서는 Azure Stack Edge 주문 문제를 해결하는 방법을 설명합니다.

## <a name="unsupported-subscription-or-region"></a>지원되지 않는 구독 또는 지역

**오류 설명:** Azure Portal에서 다음과 같은 오류가 발생합니다.

*선택한 구독 또는 지역은 지원되지 않습니다. 다른 구독 또는 지역을 선택합니다.*

![지원되지 않는 구독 또는 지역](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**제안된 솔루션:** [Microsoft EA(기업계약)](https://azure.microsoft.com/overview/sales-number/), [CSP(클라우드 솔루션 공급자)](/partner-center/azure-plan-lp), [Microsoft Azure 스폰서쉽](https://azure.microsoft.com/offers/ms-azr-0036p/) 등의 지원되는 구독을 사용했는지 확인합니다. 종량제 구독은 지원되지 않습니다. 자세한 내용은 [Azure Stack Edge 리소스 사전 요구 사항](azure-stack-edge-deploy-prep.md#prerequisites)을 참조하세요.

Microsoft에서 사례별로 구독 유형 업그레이드를 허용했을 가능성이 있습니다. 요구 사항을 이해하고 이러한 제한을 적절하게 조정할 수 있도록 [Microsoft 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

## <a name="selected-subscription-type-not-supported"></a>선택한 구독 유형이 지원되지 않음

**오류:** EA, CSP, 스폰서 구독이 있는데 다음과 같은 오류가 발생합니다.

*선택한 구독 유형은 지원되지 않습니다. 지원되는 구독을 사용하고 있는지 확인합니다. [자세히 알아보기](azure-stack-edge-deploy-prep.md#prerequisites). 지원되는 구독 유형을 사용하는 경우 다음을 확인하세요.
 
- 클래식 포털을 통해 주문할 때 `Microsoft.DataBoxEdge` 공급자가 등록되었는지 확인합니다.
- Azure Edge Hardware Center(미리 보기)를 통해 주문할 때 `Microsoft.EdgeOrder` 공급자가 등록되었는지 확인합니다.
 
등록 방법에 대한 자세한 내용은 [리소스 공급자 등록](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*을 참조하세요.

**제안된 솔루션:** 다음 단계에 따라 Azure Stack Edge 리소스 공급자를 등록합니다.

1. Azure Portal에서 **홈** > **구독** 으로 이동합니다.

2. 디바이스를 주문하는 데 사용할 구독을 선택합니다.

3. **리소스 공급자** 를 선택한 다음, **Microsoft.DataBoxEdge** 를 검색합니다.

    ![리소스 공급자 등록](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

리소스 공급자를 등록할 수 있는 소유자 또는 기여자 액세스 권한이 없는 경우 다음 오류가 표시됩니다. *구독 &lt;구독 이름&gt;에 리소스 공급자 Microsoft.DataBoxEdge를 등록할 수 있는 권한이 없습니다.*

자세한 내용은 [리소스 공급자 등록](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)을 참조하세요.

## <a name="resource-provider-not-registered-for-subscription"></a>구독에 등록되지 않은 리소스 공급자

**오류:** Azure Portal에서 Azure Stack Edge 또는 Data Box Gateway에 사용할 구독을 선택하면 다음 오류 중 하나가 발생합니다.

*리소스 공급자 Microsoft.DataBoxEdge는 구독 &lt;구독 이름&gt;에 대해 등록되지 않으며, 구독 &lt;구독 이름&gt;* 에 대한 리소스 공급자를 등록할 수 있는 권한이 없습니다.

*리소스 공급자 Microsoft.EdgeOrder는 구독 &lt;구독 이름&gt;에 대해 등록되지 않으며, 구독 &lt;구독 이름&gt;* 에 대한 리소스 공급자를 등록할 수 있는 권한이 없습니다.

**제안된 솔루션:** 구독 액세스 권한을 상승시키거나 소유자 또는 기여자 액세스 권한이 있는 사용자를 찾아 리소스 공급자를 등록합니다.

## <a name="resource-disallowed-by-policy"></a>정책에서 허용하지 않는 리소스

**오류:** Azure Portal에서 리소스 공급자를 등록하려고 시도하여 다음 오류가 발생합니다.

정책에서 *리소스 &lt;리소스 이름&gt;을 허용하지 않았습니다. (코드: RequestDisallowedByPolicy). 이니셔티브: 일반적으로 원치 않는 리소스 유형을 거부합니다. 정책: 리소스 종류가 허용되지 않습니다.*

**제안된 솔루션:** 이 오류는 리소스 생성을 차단하는 기존 Azure Policy 할당 때문에 발생합니다. Azure Policy 정의 및 할당은 Azure 리소스를 사용하거나 만드는 동안 규정 준수를 보장하기 위해 조직의 시스템 관리자가 설정합니다. 이러한 정책 할당에서 Azure Stack Edge 리소스 생성을 차단하고 있는 경우 시스템 관리자에게 문의하여 Azure Policy 정의를 편집합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stack Edge 문제 해결](azure-stack-edge-gpu-troubleshoot.md) 방법에 대해 자세히 알아보세요.
