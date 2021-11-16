---
title: 엔터티 동작 분석을 사용하여 지능형 위협 감지 | Microsoft Docs
description: Microsoft 센티널에서 사용자 및 엔터티 동작 분석을 사용 하도록 설정 하 고 데이터 원본 구성
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: e8541c6205f8cfd7503b16fa918ca531d30d4339
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521361"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-microsoft-sentinel"></a>Microsoft 센티널에서 사용자 및 엔터티 동작 분석 (UEBA) 사용 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> UEBA 및 엔터티 페이지 기능은 이제 **_모든_** Microsoft 센티널 지역 및 지역에서 **일반** 공급으로 제공 됩니다. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>필수 조건

이 기능을 사용하거나 사용하지 않도록 설정하려면 다음이 필요합니다(이러한 필수 조건은 기능을 사용하는 데 필요하지 않음).

- 사용자는 게스트 사용자가 아닌 조직의 Azure Active Directory 멤버여야 합니다.

- 사용자에게 Azure AD에서 **전역 관리자** 또는 **보안 관리자** 역할이 할당되어야 합니다.

- 사용자에게 다음 **Azure 역할** 중 하나 이상이 할당되어야 합니다([Azure RBAC 자세히 알아보기](roles.md)).
    - 작업 영역 또는 리소스 그룹 수준에서 **Microsoft 센티널 참가자**
    - 리소스 그룹 또는 구독 수준에서 **Log Analytics 기여자**.

- 작업 영역에 Azure 리소스 잠금이 적용되지 않아야 합니다. [Azure 리소스 잠금에 대해 자세히 알아보세요](../azure-resource-manager/management/lock-resources.md).

> [!NOTE]
> Microsoft 센티널에 UEBA 기능을 추가 하는 데 특별 한 라이선스는 필요 하지 않지만 **추가 요금은** 적용 될 수 있습니다.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>사용자 및 엔터티 동작 분석을 사용 설정하는 방법

1. Microsoft 센티널 탐색 메뉴에서 **엔터티 동작** 을 선택 합니다.

1. **켜기** 에서 토글을 **켜짐** 으로 전환합니다.

1. **데이터 원본 선택** 단추를 선택합니다.

1. **데이터 원본 선택** 창에서 UEBA를 사용 설정할 데이터 원본 옆에 있는 확인란을 선택한 후 **적용** 을 선택합니다.

    > [!NOTE]
    >
    > **데이터 원본 선택** 창의 하단에는 아직 사용 설정되지 않은 UEBA 지원 데이터 원본 목록이 표시됩니다. 
    >
    > UEBA를 사용하도록 설정하면 새 데이터 원본을 연결할 때 UEBA 지원 대상인 경우 데이터 커넥터 창에서 직접 UEBA를 사용 설정할 수 있는 옵션이 있습니다.

1. **엔터티 검색으로 이동** 을 선택합니다. 그러면 기본 Microsoft 센티널 메뉴에서 **엔터티 동작** 을 선택 하는 경우 현재부터 표시 되는 엔터티 검색 창으로 이동 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft 센티널에서 사용자 및 엔터티 동작 분석 (UEBA)을 사용 하도록 설정 하 고 구성 하는 방법을 배웠습니다. Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft 센티널을 사용 하 여 위협 검색을](detect-threats-built-in.md)시작 하세요.
