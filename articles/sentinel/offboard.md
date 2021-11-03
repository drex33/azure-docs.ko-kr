---
title: Azure Sentinel 제거 | Microsoft Docs
description: Azure Sentinel 인스턴스를 삭제하는 방법
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: afb17cf0cc999680d2cd60a0be8e4a227aa146bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009190"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>작업 영역에서 Azure Sentinel 제거

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Sentinel 사용이 더 이상 필요하지 않은 경우, 이를 작업 영역에서 제거하는 방법을 이 문서에서 설명합니다.

## <a name="how-to-remove-azure-sentinel"></a>Azure Sentinel 제거 방법

작업 영역에서 Azure Sentinel을 제거하려면 이 프로세스를 수행합니다.

1. **Azure Sentinel** 로 이동하고, **설정** 을 입력한 다음, **Azure Sentinel 제거** 탭을 선택합니다.

1. Azure Sentinel을 제거하기 전에 확인란을 사용하여 제거하는 이유를 당사에 알려 주세요.

1. **작업 영역에서 Azure Sentinel 제거** 를 선택합니다.
    
    ![SecurityInsights 솔루션 삭제](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>백그라운드에서 발생하는 일

솔루션을 제거하는 경우 Azure Sentinel을 통해 삭제 프로세스의 첫 번째 단계를 완료하는 데 최대 48시간이 걸립니다.

연결이 끊어진 것이 식별된 후에는 오프보딩 프로세스가 시작됩니다.

**다음 커넥터의 구성이 제거됩니다.**
-   Office 365

-   AWS

-   Microsoft 서비스 보안 경고: ID용 Microsoft Defender (*이전의 Azure ATP*), Microsoft Cloud App Security (Cloud Discovery Shadow IT 보고 포함), Azure AD Identity Protection, Microsoft Defender for Endpoint (*이전의 Microsoft Defender ATP*), Azure Security Center의 Azure Defender 경고

-   위협 인텔리전스

-   일반 보안 로그(CEF 기반 로그, Barracuda 및 Syslog 포함) (Azure Security Center에서 Azure Defender 경고가 발생하는 경우, 이러한 로그는 계속 수집됩니다.)

-   Windows 보안 이벤트 (Azure Security Center에서 Azure Defender 경고가 발생하는 경우 이러한 로그는 계속 수집됩니다.)

처음 48시간 이내에는 데이터 및 분석 규칙(실시간 자동화 구성 포함)에 더 이상 Azure Sentinel에서 액세스하거나 쿼리할 수 ​​없습니다.

**30일 후에는 다음 리소스가 제거됩니다.**

-   인시던트(조사 메타 데이터 포함)

-   분석 규칙

-   책갈피

플레이북, 저장된 통합 문서, 저장된 헌팅 쿼리 및 Notebooks는 제거되지 않습니다. **제거된 데이터로 인해 일부는 중단 될 수 있습니다. 수동으로 이를 제거할 수 있습니다.**

서비스를 제거하고 나면 30일 간의 유예 기간이 있습니다. 이 기간 동안에는 솔루션을 다시 사용하도록 설정할 수 있어서 데이터 및 분석 규칙을 복원할 수 있지만, 연결이 끊어진 구성된 커넥터는 다시 연결해야 합니다.

> [!NOTE]
> 솔루션을 제거하면 구독이 Azure Sentinel 리소스 공급자에 계속 등록됩니다. **수동으로 제거할 수 있습니다.**




## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel 서비스를 제거하는 방법을 배웠습니다. 생각이 바뀌어 이를 다시 설치하려면 다음을 수행합니다.
- [Azure Sentinel 등록](quickstart-onboard.md)을 시작합니다.
