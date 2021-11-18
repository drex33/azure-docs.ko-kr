---
title: Microsoft 센티널의 여러 테 넌 트를 관리 되는 보안 서비스 공급자로 관리 | Microsoft Docs
description: Azure Lighthouse를 사용 하 여 Microsoft 센티널에서 여러 테 넌 트를 관리 되는 MSSP (보안 서비스 공급자)로 등록 하 고 관리 하는 방법입니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: a4518498edf3d2da14c09d396aff56542d61478f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132713014"
---
# <a name="manage-multiple-tenants-in-microsoft-sentinel-as-an-mssp"></a>Microsoft 센티널에서 여러 테 넌 트를 MSSP로 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

사용자가 관리 되는 MSSP (security service provider) 인 경우 [Azure Lighthouse](../lighthouse/overview.md) 를 사용 하 여 고객에 게 SOC (security operations center) 서비스를 제공 하는 경우 고객의 테 넌 트에 연결 하지 않고도 자신의 azure 테 넌 트에서 직접 고객의 Microsoft 센티널 리소스를 관리할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- [Azure Lighthouse 온보딩](../lighthouse/how-to/onboard-customer.md)

- 이 작업이 제대로 작동 하려면 테 넌 트 (MSSP 테 넌 트)에 Microsoft 센티널 리소스 공급자가 하나 이상의 구독에 등록 되어 있어야 합니다. 또한 각 고객의 테넌트에 리소스 공급자가 등록되어 있어야 합니다. 테 넌 트에 Microsoft 센티널을 등록 하 고 고객을 동료에 게 등록 한 경우 시작할 준비가 된 것입니다. 등록을 확인하려면 다음 단계를 수행합니다.

    1. Azure Portal에서 **구독** 을 선택한 다음, 메뉴에서 관련 구독을 선택합니다.

    1. 구독 화면의 탐색 메뉴에서 **설정** 아래에 있는 **리소스 공급자** 를 선택합니다.

    1. **‘구독 이름’ | 리소스 공급자** 화면에서 *Microsoft.OperationalInsights* 및 *Microsoft.SecurityInsights* 를 검색하여 선택하고 **상태** 열을 확인합니다. 공급자 상태가 *NotRegistered* 이면 **등록** 을 선택합니다.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="리소스 공급자 확인":::

## <a name="how-to-access-microsoft-sentinel-in-managed-tenants"></a>관리 되는 테 넌 트에서 Microsoft 센티널에 액세스 하는 방법

1. **디렉터리 + 구독** 에서 위임 된 디렉터리 (디렉터리 = 테 넌 트)를 선택 하 고 고객의 Microsoft 센티널 작업 영역이 있는 구독을 선택 합니다.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="테넌트 및 구독 선택":::

1. Microsoft 센티널를 엽니다. 선택한 구독의 모든 작업 영역이 표시되며, 여기서 고유한 테넌트의 작업 영역처럼 원활하게 작업할 수 있습니다.

> [!NOTE]
> Microsoft 센티널에는 관리 작업 영역 내에서 커넥터를 배포할 수 없습니다. 커넥터를 배포하려면 커넥터를 배포할 테넌트에 직접 로그인하고 필요한 권한으로 인증해야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 여러 Microsoft 센티널 테 넌 트를 원활 하 게 관리 하는 방법을 배웠습니다. Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft 센티널을 사용 하 여 위협 검색을](detect-threats-built-in.md)시작 하세요.
