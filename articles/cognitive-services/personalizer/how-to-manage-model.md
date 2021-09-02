---
title: 모델 및 학습 설정 관리 - Personalizer
description: 백업을 위해 기계 학습 모델 및 학습 설정을 자체 원본 제어 시스템에서 내보낼 수 있습니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 1e1e5dd9c21b89aba4c905656dd79c7934f8b061
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831605"
---
# <a name="how-to-manage-model-and-learning-settings"></a>모델 및 학습 설정 관리 방법

백업을 위해 기계 학습 모델 및 학습 설정을 자체 원본 제어 시스템에서 내보낼 수 있습니다.

## <a name="export-the-personalizer-model"></a>Personalizer 모델 내보내기

**모델 및 학습 설정** 의 리소스 관리 섹션에서 모델 생성 및 마지막 업데이트 날짜를 검토하고 현재 모델을 내보냅니다. Azure Portal 또는 Personalizer API를 사용하여 보관을 위해 모델 파일을 내보낼 수 있습니다.

![현재 Personalizer 모델 내보내기](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>학습 루프의 데이터 지우기

1. Azure Portal의 **모델 및 학습 설정** 페이지에서 Personalizer 리소스에 대해 **데이터 지우기** 를 선택합니다.
1. 모든 데이터를 지우고 학습 루프를 원래 상태로 초기화하려면 3개의 확인란을 모두 선택합니다.

    ![Azure Portal에서 Personalizer 리소스의 데이터를 지웁니다.](./media/settings/clear-data-from-personalizer-resource.png)

    |값|목적|
    |--|--|
    |기록된 개인 설정 및 보상 데이터.|이 로깅 데이터는 오프라인 평가에 사용됩니다. 리소스를 다시 설정하는 경우 데이터를 지웁니다.|
    |Personalizer 모델 다시 설정.|이 모델은 재학습할 때마다 변경됩니다. 이 학습 빈도는 **구성** 페이지의 **모델 업로드 빈도** 에서 지정합니다. |
    |학습 정책을 기본값으로 설정합니다.|오프라인 평가의 일환으로 학습 정책을 변경한 경우 원래 학습 정책으로 초기화됩니다.|

1. **선택한 데이터 지우기** 를 선택하여 지우기 프로세스를 시작합니다. 상태는 오른쪽 위 탐색에 있는 Azure 알림에서 보고됩니다.

## <a name="import-a-new-learning-policy"></a>새 학습 정책 가져오기

[학습 정책](concept-active-learning.md#understand-learning-policy-settings) 설정은 모델 학습의 _하이퍼 매개 변수_ 를 결정합니다. [오프라인 평가](how-to-offline-evaluation.md)를 수행하여 새 학습 정책을 찾습니다.

1. [Azure Portal](https://portal.azure.com)을 열고 Personalizer 리소스를 선택합니다.
1. **리소스 관리** 섹션에서 **모델 및 학습 설정** 을 선택합니다.
1. **가져오기 학습 설정** 에서 위에 지정된 JSON 형식을 사용하여 만든 파일을 선택하고 **업로드** 단추를 선택합니다.

    학습 정책이 성공적으로 업로드되었다는 알림을 기다립니다.

## <a name="export-a-learning-policy"></a>학습 정책 내보내기

1. [Azure Portal](https://portal.azure.com)을 열고 Personalizer 리소스를 선택합니다.
1. **리소스 관리** 섹션에서 **모델 및 학습 설정** 을 선택합니다.
1. **학습 설정을 가져오려면** **학습 설정 내보내기** 단추를 선택합니다. 이렇게 하면 `json` 파일이 로컬 컴퓨터에 저장됩니다.

## <a name="next-steps"></a>다음 단계

[오프라인 평가를 통해 학습 루프 분석](how-to-offline-evaluation.md)
