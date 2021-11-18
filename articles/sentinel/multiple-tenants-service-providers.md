---
title: Microsoft Sentinel에서 관리되는 보안 서비스 공급자로 여러 테넌트 관리 | Microsoft Docs
description: Azure Lighthouse 사용하여 MSSP(관리 보안 서비스 공급자)로 Microsoft Sentinel에서 여러 테넌트를 온보딩하고 관리하는 방법입니다.
author: yelevin
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b4f07f3c7058f200a7d3eaca8be06af018e522c3
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755947"
---
# <a name="manage-multiple-tenants-in-microsoft-sentinel-as-an-mssp"></a>Microsoft Sentinel에서 MSSP로 여러 테넌트 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

MSSP(관리형 보안 서비스 공급자)이고 [Azure Lighthouse](../lighthouse/overview.md) 사용하여 고객에게 SOC(보안 운영 센터) 서비스를 제공하는 경우 고객의 테넌트에서 연결하지 않고도 고객의 Microsoft Sentinel 리소스를 직접 관리할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- [Azure Lighthouse 온보딩](../lighthouse/how-to/onboard-customer.md)

- 제대로 작동하려면 테넌트(MSSP 테넌트)에 Microsoft Sentinel 리소스 공급자가 하나 이상의 구독에 등록되어 있어야 합니다. 또한 각 고객의 테넌트에 리소스 공급자가 등록되어 있어야 합니다. 테넌트에서 Microsoft Sentinel을 등록하고 고객의 경우 시작할 준비가 된 것입니다. 등록을 확인하려면 다음 단계를 수행합니다.

    1. Azure Portal에서 **구독** 을 선택한 다음, 메뉴에서 관련 구독을 선택합니다.

    1. 구독 화면의 탐색 메뉴에서 **설정** 아래에 있는 **리소스 공급자** 를 선택합니다.

    1. **‘구독 이름’ | 리소스 공급자** 화면에서 *Microsoft.OperationalInsights* 및 *Microsoft.SecurityInsights* 를 검색하여 선택하고 **상태** 열을 확인합니다. 공급자 상태가 *NotRegistered* 이면 **등록** 을 선택합니다.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="리소스 공급자 확인":::

## <a name="how-to-access-microsoft-sentinel-in-managed-tenants"></a>관리되는 테넌트에서 Microsoft Sentinel에 액세스하는 방법

1. **디렉터리 + 구독에서** 위임된 디렉터리(디렉터리 = 테넌트) 및 고객의 Microsoft Sentinel 작업 영역이 있는 구독을 선택합니다.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="테넌트 및 구독 선택":::

1. Microsoft Sentinel을 엽니다. 선택한 구독의 모든 작업 영역이 표시되며, 여기서 고유한 테넌트의 작업 영역처럼 원활하게 작업할 수 있습니다.

> [!NOTE]
> Microsoft Sentinel의 커넥터는 관리되는 작업 영역 내에서 배포할 수 없습니다. 커넥터를 배포하려면 커넥터를 배포할 테넌트에 직접 로그인하고 필요한 권한으로 인증해야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 여러 Microsoft Sentinel 테넌트 관리를 원활하게 하는 방법을 배웠습니다. Microsoft Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft Sentinel을 사용하여 위협 검색을](detect-threats-built-in.md)시작합니다.
