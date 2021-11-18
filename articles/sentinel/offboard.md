---
title: Microsoft 센티널 제거 | Microsoft Docs
description: Microsoft 센티널 인스턴스를 삭제 하는 방법
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: ff22dabc5e2e2f73140145f7ac5f377e91d3def8
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712569"
---
# <a name="remove-microsoft-sentinel-from-your-workspace"></a>작업 영역에서 Microsoft Sentinel 제거

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft 센티널을 더 이상 사용 하지 않으려는 경우이 문서에서는 작업 영역에서 제거 하는 방법을 설명 합니다.

## <a name="how-to-remove-microsoft-sentinel"></a>Microsoft 센티널을 제거 하는 방법

작업 영역에서 Microsoft 센티널을 제거 하려면이 프로세스를 수행 합니다.

1. **microsoft 센티널** 로 이동한 다음 **설정** 하 고 **microsoft 센티널 제거** 탭을 선택 합니다.

1. Microsoft 센티널을 제거 하기 전에 확인란을 사용 하 여 제거 하는 이유를 알려 주세요.

1. **작업 영역에서 Microsoft 센티널 제거를** 선택 합니다.
    
    ![SecurityInsights 솔루션 삭제](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>백그라운드에서 발생하는 일

솔루션을 제거 하면 Microsoft 센티널에서 삭제 프로세스의 첫 번째 단계를 완료 하는 데 최대 48 시간이 소요 됩니다.

연결이 끊어진 것이 식별된 후에는 오프보딩 프로세스가 시작됩니다.

**다음 커넥터의 구성이 제거됩니다.**
-   Office 365

-   AWS

-   Microsoft 서비스 보안 경고: id 용 microsoft defender (*이전의 Azure ATP*), Cloud Discovery 섀도 IT 보고, Azure AD ID 보호, microsoft defender for cloud (*이전의 microsoft defender ATP*), 클라우드 용 microsoft defender의 보안 경고

-   위협 인텔리전스

-   공용 보안 로그 (CEF 기반 로그, Barracuda 및 Syslog 포함) (Microsoft Defender for Cloud에서 보안 경고가 발생 하는 경우 이러한 로그는 계속 수집 됩니다.)

-   이벤트 Windows 보안 (Microsoft Defender for Cloud에서 보안 경고가 발생 하는 경우 이러한 로그는 계속 수집 됩니다.)

처음 48 시간 이내에 데이터 및 분석 규칙 (실시간 자동화 구성 포함)은 더 이상 Microsoft 센티널에서 액세스 하거나 쿼리할 수 없습니다.

**30일 후에는 다음 리소스가 제거됩니다.**

-   인시던트(조사 메타 데이터 포함)

-   분석 규칙

-   책갈피

플레이북, 저장된 통합 문서, 저장된 헌팅 쿼리 및 Notebooks는 제거되지 않습니다. **제거된 데이터로 인해 일부는 중단 될 수 있습니다. 수동으로 이를 제거할 수 있습니다.**

서비스를 제거하고 나면 30일 간의 유예 기간이 있습니다. 이 기간 동안에는 솔루션을 다시 사용하도록 설정할 수 있어서 데이터 및 분석 규칙을 복원할 수 있지만, 연결이 끊어진 구성된 커넥터는 다시 연결해야 합니다.

> [!NOTE]
> 솔루션을 제거하면 구독이 Microsoft Sentinel 리소스 공급자에 계속 등록됩니다. **수동으로 제거할 수 있습니다.**




## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft 센티널 서비스를 제거 하는 방법을 배웠습니다. 생각이 바뀌어 이를 다시 설치하려면 다음을 수행합니다.
- [Microsoft 센티널을 탑재](quickstart-onboard.md)하 여 시작 하세요.
