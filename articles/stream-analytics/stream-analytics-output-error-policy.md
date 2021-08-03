---
title: Azure Stream Analytics의 출력 오류 정책
description: Azure Stream Analytics에서 사용할 수 있는 출력 오류 처리 정책에 대해 알아봅니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/30/2021
ms.custom: seodec18
ms.openlocfilehash: 1fdd973f51ee564bf59a7ed4fac347a007836d52
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110794055"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Azure Stream Analytics 출력 오류 정책
이 문서에서는 Azure Stream Analytics에서 구성할 수 있는 출력 데이터 오류 처리 정책에 대해 설명합니다.

출력 데이터 오류 처리 정책은 Stream Analytics 작업에 의해 생성된 출력 이벤트가 대상 싱크의 스키마와 맞지 않는 경우 발생하는 데이터 변환 오류에만 적용됩니다. **재시도** 또는 **드롭** 을 선택하여 이 정책을 구성할 수 있습니다. Azure Portal에서 Stream Analytics 작업의 **구성** 아래에서 **오류 정책** 을 선택하고 옵션을 선택합니다.

![Azure Stream Analytics 출력 오류 정책 위치](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>다시 시도
오류가 발생하면 Azure Stream Analytics는 쓰기가 성공할 때까지 무기한으로 이벤트 작성을 다시 시도합니다. 재시도에 대한 시간 제한은 없습니다. 결국, 재시도 중인 이벤트에 의해 모든 후속 이벤트의 처리가 차단됩니다. 이 옵션은 기본 출력 오류 처리 정책입니다. 재시도 간격은 각 출력에 따라 다를 수 있으며 후속 재시도는 몇 초에서 몇 분까지 걸릴 수 있습니다.

## <a name="drop"></a>드롭
Azure Stream Analytics는 데이터 변환 오류를 초래하는 모든 출력 이벤트를 삭제합니다. 삭제된 이벤트는 나중에 다시 처리하도록 복구할 수 없습니다.


모든 일시적인 오류(예: 네트워크 오류)는 출력 오류 처리 정책 구성과 상관없이 다시 시도됩니다.


## <a name="next-steps"></a>다음 단계
[Azure Stream Analytics 문제 해결 가이드](./stream-analytics-troubleshoot-query.md)
