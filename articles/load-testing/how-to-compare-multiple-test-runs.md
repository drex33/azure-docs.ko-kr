---
title: 부하 테스트 실행을 비교 하 여 재발 찾기
titleSuffix: Azure Load Testing
description: 성능 재발을 보다 잘 이해할 수 있도록 Azure 부하 테스트와 여러 테스트 실행을 시각적으로 비교 하는 방법을 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: nicktrog
author: ntrogh
ms.date: 11/30/2021
ms.topic: how-to
ms.openlocfilehash: 0a8d39f130ebd4c93004851f1a54b3fd2757d512
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133305629"
---
# <a name="identify-performance-regressions-by-comparing-load-test-runs"></a>부하 테스트 실행을 비교 하 여 성능 재발 식별

이 문서에서는 Azure 부하 테스트 미리 보기 대시보드에서 여러 부하 테스트 실행을 시각적으로 비교 하 여 성능 재발을 식별 하는 방법에 대해 알아봅니다.

테스트 실행에는 클라이언트 쪽 및 서버 쪽 메트릭이 포함 됩니다. 테스트 엔진은 가상 사용자 수와 같은 클라이언트 쪽 메트릭을 보고 합니다. 서버 쪽 메트릭은 응용 프로그램 및 리소스 관련 정보를 제공 합니다.

여러 메트릭 차트를 겹치게 하면 성능 변화를 보다 쉽게 파악 하 고 문제를 일으키는 응용 프로그램 구성 요소를 식별할 수 있습니다.

Azure Portal에서 부하 테스트 실행을 비교 하는 데는 두 가지 진입점이 있습니다.

- 테스트 실행 페이지에서 비교할 여러 결과를 선택 합니다.
- 특정 테스트 실행에서 시작 하 여 다른 결과를 선택 하 여과 실행을 비교 합니다.

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 계정을 만듭니다.  

- 여러 테스트를 실행 하는 테스트 계획을 사용 하는 Azure 부하 테스트 리소스입니다. 부하 테스트 리소스를 만들려면 [부하 테스트 만들기 및 실행](./quickstart-create-and-run-load-test.md)을 참조 하세요.

    > [!NOTE]
    > 테스트 실행은 **완료**, **중지** 또는 **실패** 상태에 있어야 비교할 수 있습니다.
    
## <a name="compare-test-runs-from-the-test-runs-page"></a>테스트 실행 페이지에서 테스트 실행 비교

이 섹션에서는 테스트 실행 페이지에서 실행을 선택 하 여 여러 결과를 비교 합니다.

1. Azure 구독에 대한 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 부하 테스트 리소스로 이동 하 고, 왼쪽 탐색 모음에서 **테스트** 를 선택 하 여 테스트 목록을 봅니다.

    :::image type="content" source="media/how-to-compare-multiple-test-runs/choose-test-from-list.png" alt-text="부하 테스트 리소스에 대 한 테스트 목록을 보여 주는 스크린샷":::

    필터를 사용 하 여 부하 테스트를 찾을 수도 있습니다.

1. 테스트 목록에서 비교할 실행을 포함 하는 테스트를 선택 합니다.

1. 확인란을 사용 하 여 목록에서 여러 테스트 실행을 선택 하 고 **비교** 를 선택 합니다.

    :::image type="content" source="media/how-to-compare-multiple-test-runs/compare-test-results-from-list.png" alt-text="비교할 테스트 실행을 보여 주는 스크린샷":::

    > [!NOTE]
    > 비교할 최대 5 개의 테스트 실행을 선택할 수 있습니다.

    선택한 테스트 실행은 대시보드에 표시 됩니다. 각 실행은 다른 차트에 오버레이로 표시 됩니다.

    :::image type="content" source="media/how-to-compare-multiple-test-runs/compare-screen.png" alt-text="비교 하는 두 개의 테스트 실행을 보여 주는 스크린샷":::

    필터를 사용 하 여 그래프를 사용자 지정할 수 있습니다. 클라이언트 및 서버 메트릭에 대 한 별도의 필터가 있습니다.

    > [!TIP]
    > 시간 필터는 테스트의 상대적인 기간을 기반으로 합니다. 0 값은 테스트 시작을 나타내고 최대값은 가장 긴 테스트 실행 기간을 표시 합니다. 클라이언트 쪽 메트릭의 경우 테스트 실행은 테스트 기간 동안만 데이터를 표시 합니다.

## <a name="compare-test-runs-from-the-run-details-page"></a>실행 세부 정보 페이지에서 테스트 실행 비교

이 섹션에서는 테스트 실행 세부 정보 페이지를 사용 하 고 다른 테스트 실행을 추가 하 여 비교 합니다.

1. 테스트 실행 세부 정보 페이지로 이동한 다음 **비교** 를 선택 합니다.

    :::image type="content" source="media/how-to-compare-multiple-test-runs/test-run-details.png" alt-text="테스트 실행 세부 정보 페이지를 보여 주는 스크린샷":::

1. 확인란을 사용 하 여 비교 하려는 테스트 실행을 선택 합니다.

    :::image type="content" source="media/how-to-compare-multiple-test-runs/choose-runs-to-compare.png" alt-text="비교할 테스트 실행을 보여 주는 스크린샷":::

    > [!NOTE]
    > 비교할 최대 5 개의 테스트 실행을 선택할 수 있습니다.

1. **비교** 를 선택합니다.

    선택한 테스트 실행은 대시보드에 표시 됩니다. 각 실행은 다른 차트에 오버레이로 표시 됩니다.

    :::image type="content" source="media/how-to-compare-multiple-test-runs/compare-screen.png" alt-text="비교 하는 두 개의 테스트 실행을 보여 주는 스크린샷":::

    필터를 사용 하 여 그래프를 사용자 지정할 수 있습니다. 클라이언트 및 서버 메트릭에 대 한 별도의 필터가 있습니다.

## <a name="next-steps"></a>다음 단계

- 확장성이 뛰어난 부하 테스트에 대 한 자세한 내용은 [대규모 부하 테스트 설정](./how-to-high-scale-load.md)을 참조 하세요.

- 성능 테스트 자동화에 대 한 자세한 내용은 [자동화 된 성능 테스트 구성](./tutorial-cicd-azure-pipelines.md) 을 참조 하세요.
