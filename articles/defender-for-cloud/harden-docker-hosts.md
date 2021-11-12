---
title: 클라우드 용 Microsoft Defender를 사용 하 여 Docker 호스트를 강화 하 고 컨테이너를 보호 합니다.
description: Docker 호스트를 보호하고 CI Docker 벤치마크를 준수하는지 확인하는 방법
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 9081da3e2db3a902c541b079e32eea1b60ad8d43
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373860"
---
# <a name="harden-your-docker-hosts"></a>Docker 호스트 강화

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

클라우드용 Microsoft Defender는 IaaS Linux VM 또는 Docker 컨테이너를 실행하는 다른 Linux 머신에 호스트되는 관리되지 않는 컨테이너를 식별합니다. Defender for Cloud는 이러한 컨테이너의 구성을 지속적으로 평가합니다. 그런 다음, 이러한 항목을 [CIS(Center for Internet Security) Docker 벤치마크](https://www.cisecurity.org/benchmark/docker/)와 비교합니다.

Defender for Cloud는 CIS Docker 벤치마크의 전체 규칙 세트를 포함하며 컨테이너가 규칙을 위반할 경우 경고를 표시합니다. Defender for Cloud는 잘못된 구성을 발견하면 보안 권장 사항을 생성합니다. Defender for Cloud의 **권장 사항 페이지** 를 사용하여 권장 사항을 살펴보고 문제를 해결하세요.

취약성이 발견되면 단일 권장 사항 내에서 그룹화됩니다.

>[!NOTE]
> CIS 벤치마크 검사는 AKS 관리형 인스턴스 또는 Databricks 관리형 VM에서 실행되지 않습니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|[서버용 Microsoft Defender](defender-for-servers-introduction.md)가 필요합니다.|
|필요한 역할 및 권한:|호스트에서 연결하는 작업 영역에 대한 **판독기**|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Docker 구성에서 보안 취약성 식별 및 수정

1. 클라우드 용 Defender의 메뉴에서 **권장 사항** 페이지를 엽니다.

1. **컨테이너 보안 구성에서 취약성 수정 시 권장 사항에 대한 필터링을 수성하고** 권장 사항을 선택합니다.

    권장 사항 페이지는 영향을 받는 리소스(Docker 호스트)를 표시합니다. 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="컨테이너 보안 구성의 취약성을 수정하기 위한 권장 사항":::

    > [!NOTE]
    > Docker를 실행하지 않는 머신은 **해당되지 않는 리소스** 탭에 표시됩니다. Azure Policy에는 준수 상태로 표시됩니다. 

1. 특정 호스트가 실패한 CIS 컨트롤을 보고 수정하려면 조사하려는 호스트를 선택합니다. 

    > [!TIP]
    > 자산 인벤토리 페이지에서 시작하여 이 권장 사항에 도달한 경우 권장 사항 페이지에서 **작업 수행** 단추를 선택합니다.
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Log Analytics를 시작하는 작업 수행 단추":::

    사용자 지정 작업을 실행할 준비가 되면 Log Analytics가 열립니다. 사용자 지정 쿼리 기본값은 문제를 해결하는 데 도움이 되는 지침과 함께 평가된 모든 실패한 규칙의 목록을 포함합니다.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="실패한 모든 CIS 컨트롤을 표시하는 쿼리가 있는 Log Analytics 페이지":::

1. 필요한 경우 쿼리 매개 변수를 조정합니다.

1. 명령이 적절하고 호스트에 대해 준비되면 **실행** 을 선택합니다.


## <a name="next-steps"></a>다음 단계

Docker 강화는 클라우드의 컨테이너 보안 기능을 위한 Defender의 한 가지 측면 일 뿐입니다. 

[클라우드에 대 한 Defender의 컨테이너 보안](container-security.md)에 대해 자세히 알아보세요.
