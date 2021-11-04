---
title: Azure Digital Twins Explorer
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 탐색기의 기능과 용도에 대해 알아보고 디지털 모델, 쌍 및 그래프를 시각화 하는 데 유용한 도구 일 수 있습니다.
author: baanders
ms.author: baanders
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: bbcc693123f84d5cf789f53c8961f648ce2e90bf
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504249"
---
# <a name="azure-digital-twins-explorer-preview"></a>Azure Digital Twins Explorer(미리 보기)

이 문서에는 해당 사용 사례와 해당 기능에 대 한 개요를 포함 하 여 **Azure Digital Twins 탐색기** 에 대 한 정보가 포함 되어 있습니다. 각 기능을 사용하는 방법에 대한 자세한 단계는 [Azure Digital Twins Explorer 사용](how-to-use-azure-digital-twins-explorer.md)을 참조하세요.

Azure Digital Twins Explorer는 [모델](concepts-models.md) 및 [트윈 그래프](concepts-twins-graph.md)를 포함하여 Azure Digital Twins 인스턴스의 데이터를 시각화하고 상호 작용하기 위한 개발자 도구입니다. 

>[!NOTE]
>이 도구는 현재 **공개 미리 보기** 로 제공됩니다.

다음은 샘플 그래프에 대해 채워진 모델과 트윈을 보여 주는 탐색기 창의 보기입니다.

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="샘플 모델 및 트윈을 보여주는 Azure Digital Twins Explorer의 스크린샷." lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

시각적 인터페이스는 그래프와 모델 세트의 모양을 탐색하고 이해하기 위한 훌륭한 도구입니다. 또한 시각적 인터페이스를 사용해 각 트윈과 관계를 즉시 변경할 수 있습니다.

## <a name="when-to-use"></a>사용 시기

Azure Digital Twins Explorer는 트윈 그래프를 탐색하고 그래프 컨텍스트에서 트윈과 관계를 수정하려는 사용자를 위해 설계된 시각적 도구입니다.

개발자는 이 도구가 다음과 같은 시나리오에서 특히 유용할 수 있습니다.
* **탐색**: 탐색기를 사용하여 Azure Digital Twins 및 실제 환경을 나타내는 방식에 대해 알아봅니다. 서비스를 숙지하기 위해 보고 편집할 수 있는 샘플 모델 및 그래프를 가져옵니다. Azure Digital Twins Explorer 사용을 시작하는 단계 안내는 [Azure Digital Twins Explorer 시작](quickstart-azure-digital-twins-explorer.md)을 참조하세요.
* **개발**: 탐색기를 사용하여 트윈 그래프를 보고 유효성을 검사합니다. 모델, 트윈 및 관계의 특정 속성을 조사할 수도 있습니다. 그래프와 해당 데이터를 즉시 수정할 수 있습니다. 각 기능을 사용하는 방법에 대한 자세한 지침은 [Azure Digital Twins Explorer 사용](how-to-use-azure-digital-twins-explorer.md)을 참조하세요. 

탐색기의 주요 목적은 그래프를 시각화 및 이해하고 필요에 따라 그래프를 업데이트하는 데 도움이 되는 것입니다. 대규모 솔루션 및 반복 또는 자동화해야 하는 작업의 경우 [API 및 SDK](./concepts-apis-sdks.md)를 사용하여 코드를 통해 인스턴스와 상호 작용하는 것이 좋습니다.

[!INCLUDE [digital-twins-access-explorer.md](../../includes/digital-twins-access-explorer.md)]

## <a name="features-and-capabilities"></a>기능 및 특성

Azure Digital Twins Explorer는 패널로 구성되며, 각 패널은 모델, 트윈 및 관계를 탐색하고 관리하기 위한 다양한 기능 집합을 포함합니다.

탐색기의 섹션은 다음과 같습니다.
* **쿼리 탐색기**: 트윈 그래프에 대해 쿼리를 실행하고 **트윈 그래프** 패널에서 시각적 결과를 확인합니다.
* **모델**: 모델 목록을 보고 모델 세부 정보 추가, 제거 및 보기와 같은 모델 동작을 수행 합니다.
* **쌍**: 쌍 및 연결 된 관계의 목록을 표시 합니다.
* **트윈 그래프**: 사용자 지정 가능 트윈 그래프로 트윈 및 관계를 시각화합니다. 트윈 및 관계를 만들고 삭제하고, 해당 속성을 보거나 편집합니다.
* **모델 그래프**: 모델을 시각화하고 사용자 지정 가능한 모델 그래프의 형태로 상호 연결하는 방법을 시각화합니다.

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png" alt-text="위에 설명된 각 패널에 대한 강조 표시를 포함하는 Azure Digital Twins Explorer의 스크린샷" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png":::

각 기능을 사용하는 방법에 대한 자세한 지침은 [Azure Digital Twins Explorer 사용](how-to-use-azure-digital-twins-explorer.md)을 참조하세요. 

## <a name="how-to-contribute"></a>참가 방법

Azure Digital Twins Explorer는 코드 및 설명서에 대한 기여를 시작하는 **오픈 소스** 도구입니다. 호스트된 애플리케이션은 GitHub의 소스 코드 리포지토리에서 정기적으로 배포됩니다.

도구에 대한 소스 코드를 확인하고 코드에 참여하는 방법에 대한 자세한 지침을 보려면 GitHub 리포지토리: [digital-twins-explorer](https://github.com/Azure-Samples/digital-twins-explorer)를 참조하세요.

이 설명서에 참여하기 위한 지침을 보려면 [Microsoft Docs 기여자 가이드](/contribute/)를 참조하세요.

## <a name="other-considerations"></a>기타 고려 사항

### <a name="region-support"></a>지역 지원

Azure Digital Twins Explorer는 모든 [지원되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)에서 Azure Digital Twins의 모든 인스턴스에서 사용할 수 있습니다.

그러나 공개 미리 보기 동안에는 인스턴스가 호스트되는 지역과 다른 지역을 통해 처리하기 위해 데이터가 전송될 수 있습니다. 데이터 주권이 우려되는 상황에서 이런 방식으로 데이터가 라우팅되지 않도록 하기 위해 [오픈 소스 코드](#how-to-contribute)를 다운로드하여 사용자 머신에 로컬로 호스트된 버전의 탐색기를 만들 수 있습니다.

### <a name="billing"></a>결제

Azure Digital Twins Explorer는 Azure Digital Twins 서비스와 상호 작용하기 위한 무료 도구입니다. 도구 자체는 무료이지만 요청이 Azure Digital Twins 서비스로 전송될 때 [Azure Digital Twins 가격 책정](https://azure.microsoft.com/pricing/details/digital-twins/) 지침에 따라 비용이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계 

Azure Digital Twins Explorer 기능을 사용하는 방법을 [Azure Digital Twins Explorer 사용](how-to-use-azure-digital-twins-explorer.md)에서 자세히 알아봅니다.