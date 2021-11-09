---
title: Microsoft Defender for Cloud의 규정 준수 대시보드
description: 클라우드에 대 한 Defender의 규정 준수 대시보드에서 규제 표준을 추가 하 고 제거 하는 방법을 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 08/05/2021
ms.author: memildin
ms.openlocfilehash: f4b79d1f575d2cccfd2ea6f50af13e439a3ca968
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053503"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>규정 준수 대시보드의 표준 집합 사용자 지정

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

클라우드용 Microsoft Defender는 리소스 구성을 업계 표준, 규정 및 벤치마크의 요구 사항과 지속적으로 비교합니다. **규정 준수 대시보드** 는 특정 규정 준수 요구 사항을 충족하는 방법에 따라 규정 준수 상태에 대한 인사이트를 제공합니다.

> [!TIP]
> Faq for Cloud의 규정 준수 대시보드에 대 한 자세한 내용은 faq를 [확인 하세요.](regulatory-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

## <a name="how-are-regulatory-compliance-standards-represented-in-defender-for-cloud"></a>클라우드 용 Defender에는 규정 준수 표준이 어떻게 표시 되나요?

업계 표준, 규정 표준 및 벤치 마크는 클라우드 규정 준수 대시보드의 Defender에 표시 됩니다. 각 표준은 Azure Policy에 정의된 이니셔티브입니다.

대시보드에서 평가로 매핑된 규정 준수 데이터를 보려면 **보안 정책** 페이지 내에서 관리 그룹 또는 구독에 규정 준수 표준을 추가합니다. Azure Policy 및 이니셔티브에 대해 자세히 알아보려면 [보안 정책 작업](tutorial-security-policy.md)을 참조하세요.

선택한 범위에 표준 또는 벤치마크를 할당하면 규정 준수 대시보드에 평가로 매핑된 모든 관련 준수 데이터가 포함된 표준이 표시됩니다. 할당된 표준에 대한 요약 보고서를 다운로드할 수도 있습니다.

Microsoft는 규정 표준 자체를 추적하고 시간이 지남에 따라 일부 패키지에서 해당 범위를 자동으로 개선합니다. Microsoft에서 이니셔티브에 대한 새 콘텐츠를 릴리스하면 자동으로 대시보드에 표준의 컨트롤에 매핑되는 새 정책으로 표시됩니다.


## <a name="what-regulatory-compliance-standards-are-available-in-defender-for-cloud"></a>클라우드에 대해 Defender에서 사용할 수 있는 규정 준수 표준은 무엇 인가요?

기본적으로 모든 구독에는 **Azure Security Benchmark** 가 할당되어 있습니다. 이는 일반적인 규정 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대해 Microsoft에서 작성한 Azure 관련 지침입니다. [Azure 보안 벤치마크에 대해 자세히 알아보세요](/security/benchmark/azure/introduction).

다음과 같은 표준을 추가할 수도 있습니다.

- NIST SP 800-53
- SWIFT CSP CSCF-v2020
- 영국 공식 및 영국 NHS
- 캐나다 연방 PBMM
- Azure CIS 1.3.0
- CMMC Level 3
- 뉴질랜드 ISM 제한됨

표준은 사용할 수 있게 되면 대시보드에 추가됩니다.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>대시보드에 규정 표준 추가

다음 단계에서는 패키지를 추가하여 지원되는 규정 표준 중 하나를 준수하는지 모니터링하는 방법을 설명합니다.

### <a name="prerequisites"></a>전제 조건
대시보드에 표준을 추가 하려면 다음을 수행 합니다.

- 클라우드의 향상 된 보안 기능을 사용 하도록 설정 하려면 구독에 Defender가 있어야 합니다.
- 사용자에 게 소유자 또는 정책 참가자 권한이 있어야 합니다.

### <a name="add-a-standard"></a>표준 추가

1. 클라우드 용 Defender 메뉴에서 **규제 준수** 를 선택 하 여 규제 준수 대시보드를 엽니다. 여기에서 현재 선택된 구독에 현재 할당된 규정 준수 표준을 확인할 수 있습니다.   

1. 페이지 맨 위에서 **규정 준수 정책 관리** 를 선택합니다. 정책 관리 페이지가 표시됩니다.

1. 규정 준수 상태를 관리할 구독 또는 관리 그룹을 선택합니다. 

    > [!TIP]
    > 모든 중첩된 리소스에 대해 규정 준수 데이터를 집계하고 추적할 수 있도록 표준이 적용되는 가장 높은 범위를 선택하는 것이 좋습니다. 

1. 조직과 관련된 표준을 추가하려면 **산업 및 규정 준수 표준** 섹션을 확장하고 **표준 추가** 를 선택합니다.

1. **규정 준수 표준 추가** 페이지에서 다음과 같은 사용 가능한 표준에 대한 패키지를 검색할 수 있습니다.

    - **NIST SP 800-53**
    - **NIST SP 800 171**
    - **SWIFT CSP CSCF v2020**
    - **UKO 및 영국 NHS**
    - **캐나다 연방 PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.3.0**
    - **CMMC Level 3**
    - **뉴질랜드 ISM 제한됨**
    
    ![클라우드 규정 준수 대시보드의 Microsoft Defender에 규정 표준을 추가 합니다.](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. **추가** 를 선택하고 범위, 매개 변수, 수정 등 특정 이니셔티브에 필요한 모든 세부 정보를 입력합니다.

1. 클라우드 용 Defender 메뉴에서 **규제 준수** 를 다시 선택 하 여 규정 준수 대시보드로 돌아갑니다.

    새 표준이 업계 및 규정 표준 목록에 표시됩니다. 

    > [!NOTE]
    > 새로 추가된 표준이 규정 준수 대시보드에 표시되는 데 몇 시간 정도 걸릴 수 있습니다.

    :::image type="content" source="./media/regulatory-compliance-dashboard/compliance-dashboard.png" alt-text="규정 준수 대시보드" lightbox="./media/regulatory-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>대시보드에서 표준 제거

조직과 관련이 없는 제공 된 규제 표준을 제거 하 여 해당 하는 표준에만 초점을 맞춘 규정 준수 대시보드를 계속 사용자 지정할 수 있습니다.

표준을 제거하려면

1. 클라우드 용 Defender의 메뉴에서 **보안 정책** 을 선택 합니다.

1. 표준을 제거하려는 관련 구독을 선택합니다.

    > [!NOTE]
    > 관리 그룹이 아닌 구독에서 표준을 제거할 수 있습니다. 

    보안 정책 페이지가 열립니다. 선택한 구독에 대해 기본 정책, 업계 및 규정 표준, 사용자가 만든 사용자 지정 이니셔티브가 표시됩니다.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Microsoft Defender for Cloud의 규정 준수 대시보드에서 규제 표준을 제거 합니다.":::

1. 제거하려는 표준에 대해 **사용 안 함** 을 선택합니다. 확인 창이 열립니다.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="선택한 규정 준수 표준을 제거하도록 확인합니다.":::

1. **예** 를 선택합니다. 표준이 제거됩니다. 


## <a name="next-steps"></a>다음 단계

이 문서에서는 규정 및 업계 표준을 모니터링하기 위해 **규정 준수 표준을 추가** 하는 방법을 알아보았습니다.

관련 자료는 다음 페이지를 참조하세요.

- [Azure Security Benchmark](/security/benchmark/azure/introduction)
- [클라우드 규제 준수 대시보드의 defender](regulatory-compliance-dashboard.md) -클라우드 및 외부 도구에 대해 defender를 사용 하 여 규정 준수 데이터를 추적 하 고 내보내는 방법을 알아봅니다.
- [보안 정책 작업](tutorial-security-policy.md)