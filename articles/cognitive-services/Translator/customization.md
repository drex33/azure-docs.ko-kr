---
title: 번역 사용자 지정 - Translator
titleSuffix: Azure Cognitive Services
description: Microsoft Translator Hub를 사용하여 기본 설정된 용어 및 스타일로 고유한 기계 번역 시스템을 빌드합니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 68ae3735fc1f58ebbf8d7afd6a098e7520880058
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228759"
---
# <a name="customize-your-text-translations"></a>텍스트 번역 사용자 지정

커스텀 번역기는 번역기 서비스의 기능으로, 사용자가 번역기(버전 3만 해당)를 사용하여 텍스트를 번역할 때 Microsoft 번역기의 고급 인공신경망 기계 번역을 사용자 지정할 수 있도록 합니다.

이 기능은 [Cognitive Services Speech](../speech-service/index.yml)에서 사용될 경우 음성 번역을 사용자 지정하는 데도 사용될 수 있습니다.

## <a name="custom-translator"></a>Custom Translator

Custom Translator를 사용하면 고유한 비즈니스 및 산업 분야에 사용되는 용어를 이해하는 신경망 번역 시스템을 빌드할 수 있습니다. 그런 후에 사용자 지정된 변환 시스템은 기존 애플리케이션, 워크플로 및 웹 사이트에 통합됩니다.

### <a name="how-does-it-work"></a>작동 원리

이전에 번역된 문서(광고, 웹 페이지, 설명서 등)를 사용하여 표준 번역 시스템보다 도메인별 용어 및 스타일을 더 잘 반영하는 번역 시스템을 빌드합니다. 사용자는 TMX, XLIFF, TXT, DOCX 및 XLSX 문서를 업로드할 수 있습니다.  

또한 시스템은 문서 수준에서는 동일하게 맞춰져 있고 문장 수준에서는 아직 맞춰지지 않은 데이터만 허용합니다. 사용자가 여러 언어로 되어 있으나 별도 문서에 있는 동일한 콘텐츠 버전에 액세스할 수 있는 경우 Custom Translator는 문서 간에 문장을 자동으로 일치시킬 수 있습니다.  또한 시스템은 하나의 언어로 된 데이터를 하나 또는 두 개의 언어로 사용하여 병렬 학습 데이터를 보완함으로써 번역 품질을 개선할 수도 있습니다.

그런 후 범주 매개 변수를 통해 일반적인 방식으로 Translator를 호출하여 사용자 지정된 시스템을 사용할 수 있습니다.

적절한 형식 및 양의 학습 데이터가 제공될 경우, 5~10 사이의 향상을 기대하는 것도 드문 일은 아니며, Custom Translator를 사용하여 번역 품질에 더 많은 BLUE 포인트가 추가될 수도 있습니다.

사용 가능한 데이터를 기준으로 하는 다양한 수준의 사용자 지정에 대한 자세한 내용은 [Custom Translator 사용자 가이드](./custom-translator/overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Translator를 사용하여 사용자 지정된 언어 시스템 설정](./custom-translator/overview.md)
