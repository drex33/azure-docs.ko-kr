---
title: Azure App Service 워크 로드를 테스트할 때 더 많은 정보 얻기
titleSuffix: Azure Load Testing
description: Azure App Service 작업을 테스트할 때 App Service 진단을 사용 하 여 더 많은 정보를 얻는 방법에 대해 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: nicktrog
author: ntrogh
ms.date: 11/30/2021
ms.topic: how-to
ms.openlocfilehash: 9cc05eb465ec7d5f73fbd287627b7c8846f0c8f4
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133305578"
---
# <a name="get-more-insights-when-load-testing-azure-app-service-workloads"></a>부하 테스트 Azure App Service 워크 로드에 대 한 자세한 정보 얻기

이 문서에서는 Azure 부하 테스트 미리 보기 및 App Service 진단을 사용 하 여 Azure App Service 워크 로드에서 더 많은 정보를 얻는 방법에 대해 알아봅니다.

App Service 진단은 구성할 필요 없이 앱 문제를 해결하는 데 도움이 되는 지능적인 대화형 환경입니다. 앱에서 문제가 발생하면 App Service 진단은 무엇이 문제인지를 표시하여 문제를 더 쉽고 빠르게 확인하고 해결하기 위한 올바른 정보로 안내합니다.

Azure App Service에서 실행 되는 응용 프로그램에서 부하 테스트를 실행할 때 App Service 진단을 활용할 수 있습니다.

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소  

- 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.  
- Azure 부하 테스트 리소스입니다. Azure 부하 테스트 리소스를 만들어야 하는 경우 빠른 시작 [부하 테스트 만들기 및 실행](./quickstart-create-and-run-load-test.md)을 참조 하세요.
- 부하 테스트를 실행 하 고 부하 테스트 중 모니터링 하기 위해 앱 구성 요소에 추가한 Azure App Service 작업입니다.

## <a name="get-more-insights-when-testing-an-app-service-workload"></a>App Service 워크 로드를 테스트할 때 더 많은 정보 얻기  

이 섹션에서는 App Service 진단을 사용 하 여 Azure App Service 워크 로드를 부하 테스트 하는 방법에 대 한 자세한 정보를 얻습니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure 부하 테스트 리소스로 이동 합니다.

1. 테스트 **를 선택 하** 여 테스트 목록을 확인 한 다음 테스트를 선택 합니다.

1. 테스트 실행 페이지에서 **구성** 을 선택 하 고 **앱 구성 요소** 를 선택 하 여 부하 테스트 중에 모니터링할 Azure 리소스를 추가 하거나 제거 합니다.

    :::image type="content" source="media/how-to-appservice-insights/configure-app-components.png" alt-text="부하 테스트에 대 한 앱 구성 요소를 구성 하는 방법을 보여 주는 스크린샷":::  

1. 모니터링할 응용 프로그램 구성 요소 목록에 App Service를 추가 합니다.

    :::image type="content" source="media/how-to-appservice-insights/test-monitoring-app-service.png" alt-text="App Service 구성 요소를 모니터링 하도록 부하 테스트를 구성 하는 방법을 보여 주는 스크린샷":::  

1. **실행** 을 선택 하 여 부하 테스트를 실행 합니다.

    테스트가 완료 되 면 테스트 결과 대시보드에서 App Service에 대 한 섹션을 확인할 수 있습니다.

1. App Service 메시지에서 링크를 선택 합니다.

    :::image type="content" source="media/how-to-appservice-insights/test-result-app-service-diagnostics.png" alt-text="App Service 섹션을 사용 하 여 테스트 결과 대시보드를 보여 주는 스크린샷":::

    링크를 누르면 Azure App Service에 대 한 App Service 진단 페이지로 이동 합니다. 보다 구체적으로, **가용성 및 성능** 페이지가 표시 됩니다.

1. App Service 진단에서 사용할 수 있는 다양 한 대화형 보고서를 선택 합니다.

    :::image type="content" source="media/how-to-appservice-insights/app-diagnostics-overview.png" alt-text="App Service 진단 개요를 보여 주는 스크린샷":::

    :::image type="content" source="media/how-to-appservice-insights/app-diagnostics-high-cpu.png" alt-text="App Service 진단 CPU 사용량 정보를 보여 주는 스크린샷":::

    > [!IMPORTANT]
    > Insights 데이터를 사용할 수 있는 경우 최대 45 분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 부하 테스트를 매개 변수화 하는 방법에 대 한 자세한 내용은 [부하 테스트 매개 변수화](./how-to-parameterize-load-tests.md)를 참조 하세요.

- 성능 테스트 자동화에 대 한 자세한 내용은 [자동화 된 성능 테스트 구성](./tutorial-cicd-azure-pipelines.md) 을 참조 하세요.

- 진단 App Service에 대 한 자세한 내용은 [Azure App Service 진단 개요](/app-service/overview-diagnostics/)를 확인 하세요.