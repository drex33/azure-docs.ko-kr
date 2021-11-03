---
title: Azure Sentinel에서 여러 테넌트를 관리형 보안 서비스 공급자로 관리 | Microsoft Docs
description: Azure Lighthouse를 사용하여 Azure Sentinel에서 여러 테넌트를 MSSP(관리형 보안 서비스 공급자)로 온보딩하고 관리하는 방법입니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3e5c431c8795f464e3ab83e0f6dff47028fdb5a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019783"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Azure Sentinel에서 여러 테넌트를 MSSP로 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Azure Lighthouse](../lighthouse/overview.md)를 사용하여 고객에게 SOC(보안 운영 센터) 서비스를 제공하는 MSSP(관리형 보안 서비스 공급자)는 고객 테넌트에 연결하지 않고도 고유한 Azure 테넌트에서 직접 고객의 Azure Sentinel 리소스를 관리할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- [Azure Lighthouse 온보딩](../lighthouse/how-to/onboard-customer.md)

- 이 작업이 제대로 작동하려면 테넌트(MSSP 테넌트)의 하나 이상 구독에 Azure Sentinel 리소스 공급자가 등록되어 있어야 합니다. 또한 각 고객의 테넌트에 리소스 공급자가 등록되어 있어야 합니다. MSSP와 고객이 각각 해당 테넌트에 Azure Sentinel을 등록했으면 시작할 준비가 된 것입니다. 등록을 확인하려면 다음 단계를 수행합니다.

    1. Azure Portal에서 **구독** 을 선택한 다음, 메뉴에서 관련 구독을 선택합니다.

    1. 구독 화면의 탐색 메뉴에서 **설정** 아래에 있는 **리소스 공급자** 를 선택합니다.

    1. **‘구독 이름’ | 리소스 공급자** 화면에서 *Microsoft.OperationalInsights* 및 *Microsoft.SecurityInsights* 를 검색하여 선택하고 **상태** 열을 확인합니다. 공급자 상태가 *NotRegistered* 이면 **등록** 을 선택합니다.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="리소스 공급자 확인":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>관리형 테넌트에서 Azure Sentinel에 액세스하는 방법

1. **디렉터리 + 구독** 에서 위임된 디렉터리(디렉터리 = 테넌트) 및 고객의 Azure Sentinel 작업 영역이 있는 구독을 선택합니다.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="테넌트 및 구독 선택":::

1. Azure Sentinel을 엽니다. 선택한 구독의 모든 작업 영역이 표시되며, 여기서 고유한 테넌트의 작업 영역처럼 원활하게 작업할 수 있습니다.

> [!NOTE]
> 관리형 작업 영역 내에서 Azure Sentinel에 커넥터를 배포할 수는 없습니다. 커넥터를 배포하려면 커넥터를 배포할 테넌트에 직접 로그인하고 필요한 권한으로 인증해야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 여러 Azure Sentinel 테넌트를 원활하게 관리하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
