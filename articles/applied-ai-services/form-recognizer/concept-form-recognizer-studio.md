---
title: Form Recognizer Studio | 미리 보기
titleSuffix: Azure Applied AI Services
description: '개념: Form Recognizer Studio를 사용한 양식 및 문서 처리, 데이터 추출 및 분석(미리 보기)'
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: sajagtap
ms.custom: ignite-fall-2021
ms.openlocfilehash: 392442c3c4598461a1168b7c5a3aeb48b7d0ea22
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133570234"
---
# <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio(미리 보기)

>[!NOTE]
> Form Recognizer Studio는 현재 공개 미리 보기에 있습니다. 일부 기능은 지원되지 않거나 기능이 제한될 수 있습니다.

[Form Recognizer Studio 미리 보기는](https://formrecognizer.appliedai.azure.com/) Form Recognizer 서비스의 기능을 시각적으로 탐색, 이해 및 애플리케이션에 통합하기 위한 온라인 도구입니다. Form Recognizer [Studio 빠른 시작을](quickstarts/try-v3-form-recognizer-studio.md) 사용하여 미리 학습된 모델을 사용하여 문서 분석을 시작합니다. [Python SDK 미리 보기](quickstarts/try-v3-python-sdk.md) 및 기타 빠른 시작에서 사용자 지정 양식 모델을 빌드하고 애플리케이션에서 모델을 참조합니다.

다음 이미지는 작업 시 청구서 미리 붙는 모델 기능을 보여줍니다.

:::image border="true" type="content" source="media/quickstarts/prebuilt-get-started-v2.gif" alt-text="Form Recognizer 미리 빌드된 모델 예":::

## <a name="form-recognizer-studio-features"></a>Form Recognizer Studio 기능

다음 Form Recognizer 서비스 기능은 Studio에서 사용할 수 있습니다.

* **레이아웃:** 문서의 텍스트, 테이블, 선택 표시 및 구조 정보(PDF, TIFF 및 이미지)(JPG, PNG, BMP)를 추출하는 Form Recognizer 레이아웃 기능을 사용해 보세요. [Studio 레이아웃 빠른 시작부터 시작합니다.](quickstarts/try-v3-form-recognizer-studio.md#layout) 샘플 문서 및 문서를 탐색합니다. 대화형 시각화 및 JSON 출력을 사용하여 기능의 작동 방식을 이해합니다. 레이아웃 [개요를](concept-layout.md) 참조하여 자세한 내용을 알아보고 [레이아웃에 대한 Python SDK 빠른 시작을](quickstarts/try-v3-python-sdk.md#try-it-layout-model)시작하세요.

* **미리 빌드된 모델:** Form Recognizer 미리 빌드된 모델을 사용하면 사용자 고유의 모델을 학습시키고 빌드할 필요 없이 앱 및 흐름에 지능형 양식 처리를 추가할 수 있습니다. [Studio 미리 디버전 빠른 시작부터 시작합니다.](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models) 샘플 문서 및 문서를 탐색합니다. 대화형 시각화, 추출된 필드 목록 및 JSON 출력을 사용하여 기능의 작동 방식을 이해합니다. [미리 완성된 청구서에 대한 Python SDK 빠른](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-model)시작에 대해 자세히 알아보고 시작하려면 [모델 개요를](concept-model-overview.md) 참조하세요.

* **사용자 지정 모델:** Form Recognizer 사용자 지정 모델을 사용하면 데이터로 학습된 모델에서 양식 및 문서에 맞게 조정된 필드와 값을 추출할 수 있습니다. 독립 실행형 사용자 지정 모델을 만들거나 둘 이상의 사용자 지정 모델을 결합하여 여러 양식 형식에서 데이터를 추출하는 구성된 모델을 만듭니다. [Studio 사용자 지정 모델 빠른 시작부터 시작합니다.](quickstarts/try-v3-form-recognizer-studio.md#custom-projects)  온라인 마법사, 레이블 지정 인터페이스, 학습 단계 및 시각화를 사용하여 기능의 작동 방식을 이해합니다. 샘플 문서를 사용하여 사용자 지정 모델을 테스트하고 반복하여 모델을 개선합니다. 자세한 내용은 [사용자 지정 모델 개요를](concept-custom.md) 참조하고 [Form Recognizer v3.0 미리 보기 마이그레이션 가이드를](v3-migration-guide.md) 사용하여 새 모델을 애플리케이션과 통합하기 시작합니다.

* **사용자 지정 모델: 레이블 지정 기능:** Form Recognizer 사용자 지정 모델을 만들려면 사용자 지정 모델을 학습하기 전에 추출할 필드를 식별하고 해당 필드에 레이블을 지정해야 합니다. 텍스트, 선택 표시, 테이블 형식 데이터 및 기타 콘텐츠 형식에 레이블을 지정하는 것은 일반적으로 학습 워크플로를 용이하게 하기 위해 사용자 인터페이스를 통해 지원됩니다. 예를 들어 [레이블을 테이블로](quickstarts/try-v3-form-recognizer-studio.md#labeling-as-tables) 사용하고 서명 검색 빠른 [시작에 레이블 지정을](quickstarts/try-v3-form-recognizer-studio.md#labeling-for-signature-detection) 사용하여 Form Recognizer Studio의 레이블 지정 환경을 이해합니다.

## <a name="next-steps"></a>다음 단계

* 이전 버전의 REST API와의 차이점을 알아보려면 [**Form Recognizer v3.0 마이그레이션 가이드**](v3-migration-guide.md)를 따르세요.
* 새 SDK를 통해 애플리케이션에서 미리 보기 기능을 사용해 보려면 [**미리 보기 SDK 빠른 시작**](quickstarts/try-v3-python-sdk.md)을 살펴보세요.
* 새 REST API 사용하여 미리 보기 기능을 사용해 보려면 미리 보기 [**REST API 빠른**](quickstarts/try-v3-rest-api.md) 시작 을 참조하세요.

> [!div class="nextstepaction"]
> [Form Recognizer Studio(미리 보기) 빠른 시작](quickstarts/try-v3-form-recognizer-studio.md)
