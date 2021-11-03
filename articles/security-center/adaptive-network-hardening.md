---
title: Microsoft Defender for Cloud | 적응형 네트워크 강화 Microsoft Docs
description: 실제 트래픽 패턴을 사용하여 NSG(네트워크 보안 그룹) 규칙을 강화하고 보안 태세를 더욱 개선하는 방법을 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 05/26/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2e28058742dfb262c3b8ef68bbc9f940a0d7117e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103668"
---
# <a name="improve-your-network-security-posture-with-adaptive-network-hardening"></a>적응형 네트워크 강화를 통해 네트워크 보안 상태 개선

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

적응형 네트워크 강화는 Microsoft Defender for Cloud의 에이전트 없는 기능입니다. 이 네트워크 강화 도구를 활용하려면 머신에 아무것도 설치할 필요가 없습니다.

이 페이지에서는 Defender for Cloud에서 적응형 네트워크 강화를 구성하고 관리하는 방법을 설명합니다.

## <a name="availability"></a>가용성
|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|[서버용 Microsoft Defender 필요](defender-for-servers-introduction.md)|
|필요한 역할 및 권한:|머신의 NSG에 대한 쓰기 권한|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||

## <a name="what-is-adaptive-network-hardening"></a>적응형 네트워크 강화란?
[NSG(네트워크 보안 그룹)](../virtual-network/network-security-groups-overview.md)를 적용하여 리소스에서 들어오고 나가는 트래픽을 필터링하면 네트워크 보안 태세가 개선됩니다. 그러나 NSG를 통과하는 실제 트래픽이 정의된 NSG 규칙의 하위 집합인 경우도 있을 수 있습니다. 이러한 경우 실제 트래픽 패턴에 따라 NSG 규칙을 강화하여 보안 상태를 더욱 개선할 수 있습니다.

적응형 네트워크 강화는 NSG 규칙을 강화하기 위한 권장 사항을 제공합니다. 실제 트래픽, 알려진 신뢰할 수 있는 구성, 위협 인텔리전스 및 기타 손상 지표를 고려하는 기계 학습 알고리즘을 사용한 후 특정 IP/포트 튜플의 트래픽만 허용하는 권장 사항을 제공합니다.

예를 들어 기존 NSG 규칙이 포트 22에서 140.20.30.10/24의 트래픽을 허용한다고 가정해 보겠습니다. 트래픽 분석에 따라 적응형 네트워크 강화는 140.23.30.10/29의 트래픽을 허용하고 해당 포트에 대한 다른 모든 트래픽을 거부하도록 범위 축소를 권장할 수 있습니다. 지원되는 포트의 전체 목록은 FAQ 항목인 [지원되는 포트는 무엇인가요?](#which-ports-are-supported)를 참조하세요.

## <a name="view-hardening-alerts-and-recommended-rules"></a>강화 경고 및 권장 규칙 보기

1. Defender for Cloud 메뉴에서 **워크로드 보호 대시보드를** 엽니다.
1. 적응형 네트워크 강화 타일(1) 또는 적응형 네트워크 강화와 관련된 인사이트 패널 항목을 선택합니다(2). 

    :::image type="content" source="./media/adaptive-network-hardening/traffic-hardening.png" alt-text="적응형 네트워크 강화 도구 액세스" lightbox="./media/adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > 인사이트 패널에는 현재 적응형 네트워크 강화로 보호되는 VM의 백분율이 표시됩니다. 

1. 적응형 네트워크 강화 권장 사항에 대한 세부 정보 페이지는 **인터넷 연결 가상 머신 권장 사항에 적용해야 하며** 네트워크 VM이 다음 세 개의 탭으로 그룹화되어 열립니다.
   * **비정상 리소스**: 현재 적응형 네트워크 강화 알고리즘을 실행하여 트리거된 권장 사항과 경고가 있는 VM입니다. 
   * **정상 리소스**: 경고와 권장 사항이 없는 VM입니다.
   * **검색되지 않은 리소스**: 다음 이유 중 하나로 인해 적응형 네트워크 강화 알고리즘을 실행할 수 없는 VM입니다.
      * **VM이 클래식 VM임**: Azure Resource Manager VM만 지원됩니다.
      * **사용할 수 있는 데이터가 부족합니다.** 정확한 트래픽 강화 권장 사항을 생성하려면 Defender for Cloud에 30일 이상의 트래픽 데이터가 필요합니다.
      * **VM은 서버용 Microsoft Defender로 보호되지 않습니다. 서버용 Microsoft** [Defender로](defender-for-servers-introduction.md) 보호되는 VM만 이 기능을 사용할 수 있습니다.

    :::image type="content" source="./media/adaptive-network-hardening/recommendation-details-page.png" alt-text="인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 함 권장 사항의 세부 정보 페이지":::

1. **비정상 리소스** 탭에서 VM을 선택하여 경고와 적용하도록 권장된 강화 규칙을 확인합니다.

    - **규칙** 탭에는 적응형 네트워크 강화에서 추가하도록 권장하는 규칙이 나열됩니다.
    - **경고** 탭에는 권장 규칙에서 허용되는 IP 범위를 벗어난 리소스로 이동하는 트래픽으로 인해 생성된 경고가 나열됩니다.

1. 필요에 따라 규칙을 편집합니다.

    - [규칙 수정](#modify-rule)
    - [규칙 삭제](#delete-rule) 
    - [규칙 추가](#add-rule)

3. NSG에 적용할 규칙을 선택하고 **적용** 을 선택합니다.

    > [!TIP]
    > 허용된 원본 IP 범위가 ‘없음’으로 표시되면 권장 규칙이 ‘거부’ 규칙임을 의미하고, 표시되지 않으면 ‘허용’ 규칙입니다. 

    :::image type="content" source="./media/adaptive-network-hardening/hardening-alerts.png" alt-text="적응형 네트워크 강화 규칙 관리":::

      > [!NOTE]
      > 적용된 규칙은 VM을 보호하는 NSG에 추가됩니다. VM은 해당 NIC, VM이 있는 서브넷 또는 둘 다에 연결된 NSG로 보호될 수 있습니다.

## <a name="modify-a-rule"></a>규칙 수정 <a name ="modify-rule"> </a>

권장된 규칙의 매개 변수를 수정하는 것이 좋습니다. 예를 들어 권장 IP 범위를 변경하는 것이 좋습니다.

적응형 네트워크 강화 규칙을 수정하는 방법에 대한 몇 가지 중요한 지침은 다음과 같습니다.

- **허용** 규칙을 **거부** 규칙으로 변경할 수 없습니다. 

- **허용** 규칙의 매개 변수만 수정할 수 있습니다. 

    “거부” 규칙을 만들고 수정하는 작업은 NSG에서 직접 수행합니다. 자세한 내용은 [네트워크 보안 그룹 만들기, 변경 또는 삭제](../virtual-network/manage-network-security-group.md)를 참조하세요.

- **모든 트래픽 거부** 규칙은 여기에 나열되는 유일한 “거부” 규칙 유형으로, 수정할 수 없습니다. 그러나 삭제할 수는 있습니다([규칙 삭제](#delete-rule) 참조). 해당 유형의 규칙에 대한 자세한 내용은 FAQ 항목인 [“모든 트래픽 거부” 규칙은 언제 사용해야 하나요?](#when-should-i-use-a-deny-all-traffic-rule)를 참조하세요.

적응형 네트워크 강화 규칙을 수정하려면 다음을 수행합니다.

1. 규칙의 매개 변수 중 일부를 수정하려면 **규칙** 탭에서 규칙 행 끝에 있는 세 개의 점(...)을 선택한 다음, **편집** 을 선택합니다.

   ![규칙 편집](./media/adaptive-network-hardening/edit-hard-rule.png)

1. **규칙 편집** 창에서 변경하려는 세부 정보를 업데이트하고 **저장** 을 선택합니다.

   > [!NOTE]
   > **저장** 을 선택하면 규칙을 성공적으로 변경한 것입니다. ‘그러나 NSG에 적용하지 않았습니다.’ 규칙을 적용하려면 다음 단계에 설명된 것처럼 목록에서 규칙을 선택하고 **적용** 을 선택해야 합니다.

   ![저장 선택](./media/adaptive-network-hardening/edit-hard-rule3.png)

3. 업데이트된 규칙을 적용하려면 목록에서 업데이트된 규칙을 선택한 다음, **적용** 을 선택합니다.

    ![규칙 적용](./media/adaptive-network-hardening/enforce-hard-rule.png)

## <a name="add-a-new-rule"></a>새 규칙 추가 <a name ="add-rule"> </a>

Defender for Cloud에서 권장하지 않는 "허용" 규칙을 추가할 수 있습니다.

> [!NOTE]
> 여기서는 “허용” 규칙만 추가할 수 있습니다. “거부” 규칙을 추가하려는 경우 NSG에서 직접 추가할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 만들기, 변경 또는 삭제](../virtual-network/manage-network-security-group.md)를 참조하세요.

적응형 네트워크 강화 규칙을 추가하려면 다음을 수행합니다.

1. 위쪽 도구 모음에서 **규칙 추가** 를 선택합니다.

   ![규칙 추가](./media/adaptive-network-hardening/add-hard-rule.png)

1. **새 규칙** 창에서 세부 정보를 입력하고 **추가** 를 선택합니다.

   > [!NOTE]
   > **추가** 를 선택하면 규칙을 성공적으로 추가한 것이며, 다른 권장 규칙과 함께 나열됩니다. 그러나 NSG에 ‘적용’하지 않았습니다. 규칙을 활성화하려면 다음 단계에 설명된 것처럼 목록에서 규칙을 선택하고 **적용** 을 선택해야 합니다.

3. 새 규칙을 적용하려면 목록에서 새 규칙을 선택한 다음, **적용** 을 선택합니다.

    ![규칙 적용](./media/adaptive-network-hardening/enforce-hard-rule.png)


## <a name="delete-a-rule"></a>규칙 삭제 <a name ="delete-rule"> </a>

필요한 경우 현재 세션에 대한 권장 규칙을 삭제할 수 있습니다. 예를 들어 제안된 규칙을 적용할 경우 합법적인 트래픽이 차단될 수 있음을 확인할 수 있습니다.

현재 세션에 대한 적응형 네트워크 강화 규칙을 삭제하려면 다음을 수행합니다.

- **규칙** 탭에서 규칙 행 끝에 있는 세 개의 점(...)을 선택한 다음, **삭제** 를 선택합니다.  

    ![규칙 삭제](./media/adaptive-network-hardening/delete-hard-rule.png)


## <a name="faq---adaptive-network-hardening"></a>FAQ - 적응형 네트워크 강화

- [지원되는 포트는 무엇인가요?](#which-ports-are-supported)
- [적응형 네트워크 강화에 필요한 필수 조건이나 VM 확장이 있나요?](#are-there-any-prerequisites-or-vm-extensions-required-for-adaptive-network-hardening)

### <a name="which-ports-are-supported"></a>지원되는 포트는 무엇인가요?

적응형 네트워크 강화 권장 사항은 다음 특정 포트(UDP 및 TCP 모두)에서만 지원됩니다. 

13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215

### <a name="are-there-any-prerequisites-or-vm-extensions-required-for-adaptive-network-hardening"></a>적응형 네트워크 강화에 필요한 필수 조건이나 VM 확장이 있나요?

적응형 네트워크 강화는 Microsoft Defender for Cloud의 에이전트 없는 기능입니다. 이 네트워크 강화 도구를 활용하려면 머신에 아무것도 설치할 필요가 없습니다.

### <a name="when-should-i-use-a-deny-all-traffic-rule"></a>“모든 트래픽 거부” 규칙은 언제 사용해야 하나요?

알고리즘을 실행한 결과 Defender for Cloud가 기존 NSG 구성에 따라 허용해야 하는 트래픽을 식별하지 않는 경우 **모든 트래픽 거부** 규칙을 권장합니다. 따라서 권장 규칙은 지정된 포트에 대한 모든 트래픽을 거부하는 것입니다. 이 유형의 규칙 이름은 “시스템 생성”으로 표시됩니다. 이 규칙을 적용한 후 NSG의 실제 이름은 프로토콜, 트래픽 방향, “DENY”, 난수로 구성된 문자열이 됩니다.
