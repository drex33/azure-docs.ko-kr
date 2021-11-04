---
title: Azure Functions 진단 개요
description: Azure Functions 진단을 사용하여 함수 앱 관련 문제를 해결하는 방법을 알아봅니다.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 81d8da21d5da91c79fa40077832dd6543fd69499
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432228"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure Functions 진단 개요

함수 앱을 실행하는 경우 4xx 오류부터 트리거 실패까지 발생할 수 있는 모든 문제에 대해 준비하고 싶을 것입니다. Azure Functions 진단은 구성이나 추가 비용 없이 함수 앱 문제를 해결하는 데 도움이 되는 지능적인 대화형 환경입니다. 함수 앱에서 문제가 발생하는 경우 Azure Functions 진단에서 오류를 보여줍니다. 이를 통해 보다 쉽고 빠르게 문제 및 이슈를 해결할 수 있는 적절한 정보를 안내해 줍니다. 이 문서에서는 Azure Functions 진단을 사용하여 일반적인 함수 앱 문제를 보다 신속하게 진단하고 해결하는 기본적인 방법을 보여줍니다.

## <a name="start-azure-functions-diagnostics"></a>Azure Functions 진단 시작

Azure Functions 진단을 시작하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에서 해당 함수 앱으로 이동합니다.
1. Azure Functions 진단을 열려면 **문제 진단 및 해결** 을 선택합니다.
1. 홈페이지 타일의 키워드를 사용하여 함수 앱의 문제를 가장 잘 설명하는 범주를 선택합니다. 검색 창에서 문제를 가장 잘 설명하는 키워드를 입력할 수도 있습니다. 예를 들어 `execution`을(를) 입력하여 함수 앱 실행과 관련된 진단 보고서 목록을 확인하고 홈페이지에서 직접 열 수 있습니다.

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Azure Functions 진단을 찾습니다." border="true":::

## <a name="use-the-interactive-interface"></a>대화형 인터페이스 사용

함수 앱의 문제에 가장 잘 맞는 홈페이지 범주를 선택하면 Genie라는 Azure Functions 진단의 대화형 인터페이스를 사용하여 앱의 문제를 진단하고 해결하는 과정을 안내받을 수 있습니다. Genie에서 제공하는 타일 바로 가기를 사용하여 관심 있는 문제 범주의 전체 진단 보고서를 볼 수 있습니다. 타일 바로 가기를 통해 진단 메트릭에 직접 액세스할 수 있습니다.

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Genie는 Azure Functions 진단 인터페이스입니다." border="false":::

타일을 선택한 후 타일에 설명된 이슈와 관련된 토픽의 목록을 볼 수 있습니다. 이러한 토픽에서는 전체 보고서에서 주목할 만한 정보 조각을 제공합니다. 이러한 토픽 중 하나를 선택하여 이슈를 자세히 조사합니다. 또한 **전체 보고서 보기** 를 선택하여 단일 페이지의 모든 토픽을 탐색할 수 있습니다.

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="진단 보고서 미리 보기" border="false":::

## <a name="view-a-diagnostic-report"></a>진단 보고서 보기

토픽을 선택한 후에는 함수 앱과 관련된 진단 보고서를 볼 수 있습니다. 진단 보고서는 앱에 특정 문제가 있는지 여부를 표시하는 데 상태 아이콘을 사용합니다. 이슈에 대한 자세한 설명, 권장 조치, 관련 메트릭 및 유용한 문서를 볼 수 있습니다. 사용자 지정된 진단 보고서는 함수 앱에서 실행되는 일련의 검사를 통해 생성됩니다. 진단 보고서는 함수 앱의 문제를 찾아내는 데 유용한 도구이며 이슈를 해결할 수 있도록 안내합니다.

## <a name="find-the-problem-code"></a>문제 코드 찾기

스크립트 기반 함수의 경우 **함수 앱 다운 또는 오류 보고** 아래의 **함수 실행 및 오류** 를 사용하여 예외나 오류가 발생하는 코드 줄의 범위를 좁힐 수 있습니다. 이 도구를 사용하여 근본 원인을 파악하고 특정 코드 줄에서 문제를 수정할 수 있습니다. 미리 컴파일된 C# 및 Java 함수에는 이 옵션을 사용할 수 없습니다.

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="함수 실행 오류에 대한 진단 보고서" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="예외 세부 정보 보기" border="false":::.

## <a name="next-steps"></a>다음 단계

[UserVoice](https://feedback.azure.com/d365community/forum/9df02822-f224-ec11-b6e6-000d3a4f0da0)에서 Azure Functions 진단에 대해 질문하거나 피드백을 제공할 수 있습니다. 피드백 제목에 `[Diag]`을(를) 포함합니다.

> [!div class="nextstepaction"]
> [함수 앱 모니터링](functions-monitoring.md)
