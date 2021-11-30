---
title: 부하 테스트에 대한 서버 쪽 애플리케이션 메트릭 모니터링
titleSuffix: Azure Load Testing
description: Azure Load Testing을 사용하여 서버 쪽 애플리케이션 메트릭을 모니터링하도록 부하 테스트를 구성하는 방법을 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: nicktrog
author: ntrogh
ms.date: 11/30/2021
ms.topic: how-to
ms.openlocfilehash: 1cefcccebfc2eab178a0c0751025a4d0251f7d2e
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133305593"
---
# <a name="monitor-server-side-application-metrics-with-azure-load-testing-preview"></a>Azure 부하 테스트 미리 보기를 사용하여 서버 쪽 애플리케이션 메트릭 모니터링

이 문서에서는 Azure Load Testing 미리 보기를 사용하여 서버 쪽 애플리케이션 메트릭을 모니터링하도록 부하 테스트를 구성하는 방법을 알아봅니다.

Azure Load Testing은 Azure Monitor 통합하여 Azure 호스팅 애플리케이션에 대한 서버 쪽 리소스 메트릭을 캡처합니다. 부하 테스트 실행을 모니터링할 Azure 구성 요소 및 리소스 메트릭을 지정할 수 있습니다.

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소  

- 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 계정을 만듭니다.  
- 하나 이상의 완료된 테스트 실행이 있는 Azure Load Testing 리소스 Azure 부하 테스트 리소스를 만들어야 하는 경우 [자습서: 부하 테스트를 실행하여 성능 병목 현상 식별을 참조하세요.](./tutorial-identify-bottlenecks-azure-portal.md)

## <a name="configure-server-side-monitoring-for-a-load-test"></a>부하 테스트에 대한 서버 쪽 모니터링 구성

이 섹션에서는 기존 부하 테스트를 업데이트하여 서버 쪽 리소스 메트릭을 캡처하도록 Azure 애플리케이션 구성 요소를 구성합니다.

1. Azure Portal Azure Load Testing 리소스로 이동합니다. 

1. 왼쪽 탐색 창에서 **테스트를** 선택한 다음, 목록에서 부하 테스트를 선택합니다.

    :::image type="content" source="media/how-to-update-rerun-test/select-test.png" alt-text="부하 테스트를 선택하는 방법을 보여 주는 스크린샷":::  

1. 테스트 실행 페이지에서 **구성을** 선택한 다음, **앱 구성 요소를** 선택하여 부하 테스트 중에 모니터링할 Azure 리소스를 추가하거나 제거합니다.

    :::image type="content" source="media/how-to-update-rerun-test/configure-app-components.png" alt-text="부하 테스트에 대한 앱 구성 요소를 구성하는 방법을 보여 주는 스크린샷":::  

    확인란을 선택한 **다음, 적용을** 선택하여 Azure 리소스를 추가하거나 제거할 수 있습니다.

    :::image type="content" source="media/how-to-update-rerun-test/modify-app-components.png" alt-text="부하 테스트 구성에 앱 구성 요소를 추가하거나 제거하는 방법을 보여 주는 스크린샷":::  

    Azure Load Testing 서비스는 선택한 Azure 구성 요소에 대한 리소스 메트릭을 캡처하여 테스트 결과 대시보드에 표시합니다.
    
1. **구성을** 선택한 **다음, 메트릭을** 선택하여 부하 테스트 중에 캡처할 특정 리소스 메트릭을 선택합니다.

    :::image type="content" source="media/how-to-update-rerun-test/configure-metrics.png" alt-text="부하 테스트에 대한 메트릭을 구성하는 방법을 보여 주는 스크린샷":::  

1. 캡처하려는 메트릭 목록을 업데이트한 다음 적용을 **선택하여** 변경 내용을 저장합니다.

    :::image type="content" source="media/how-to-update-rerun-test/modify-metrics.png" alt-text="리소스 메트릭 목록을 수정하는 방법을 보여 주는 스크린샷":::  

    또는 테스트 결과 세부 정보 페이지에서 앱 구성 요소 및 메트릭을 업데이트할 수도 있습니다.

1. **실행을** 선택하여 새 구성 설정으로 부하 테스트를 실행합니다.

    :::image type="content" source="media/how-to-update-rerun-test/run-load-test.png" alt-text="테스트 실행 페이지에서 테스트를 실행하는 방법을 보여 주는 스크린샷":::  

    테스트 결과 페이지에서 부하 테스트를 업데이트한 경우 다시 **실행을** 선택하여 새 구성 설정으로 부하 테스트를 실행할 수 있습니다.

    :::image type="content" source="media/how-to-update-rerun-test/dashboard-run.png" alt-text="테스트 결과 대시보드에서 부하 테스트를 다시 실행 하는 방법을 보여 주는 스크린샷":::

    이제 테스트 결과 대시보드에 업데이트된 서버 쪽 메트릭이 표시됩니다.

    :::image type="content" source="media/how-to-update-rerun-test/dashboard-updated-metrics.png" alt-text="테스트 결과 대시보드에서 업데이트된 서버 쪽 메트릭을 보여 주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

- 대규모 부하 테스트에 대한 자세한 내용은 [대규모 부하 테스트 설정을](./how-to-high-scale-load.md)참조하세요.

- 성능 테스트 자동화에 대한 자세한 내용은 [자동화된 성능 테스트 구성을](./tutorial-cicd-azure-pipelines.md) 참조하세요.
