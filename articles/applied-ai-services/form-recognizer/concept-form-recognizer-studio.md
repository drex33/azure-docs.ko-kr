---
title: 폼 인식기 스튜디오 | 미리 보기
titleSuffix: Azure Applied AI Services
description: '개념: 양식 인식기 스튜디오 (미리 보기)를 사용 하 여 양식 및 문서 처리, 데이터 추출 및 분석'
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: sajagtap
ms.openlocfilehash: e476f6da88688cb055c741f0888001d864908bae
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812661"
---
# <a name="form-recognizer-studio--preview"></a>폼 인식기 스튜디오 | 미리 보기

>[!NOTE]
> 양식 인식기 스튜디오는 현재 공개 미리 보기로 제공 됩니다. 일부 기능은 지원 되지 않거나 제한 된 기능을 사용할 수 있습니다.

[양식 인식기 스튜디오 미리 보기](https://formrecognizer.appliedai.azure.com/) 는 폼 인식기 서비스에서 응용 프로그램으로 기능을 시각적으로 탐색 하 고 이해 하며 통합 하는 데 사용할 수 있는 온라인 도구입니다. [인식기 Studio 빠른 시작 폼](quickstarts/try-v3-form-recognizer-studio.md) 을 사용 하 여 샘플 문서나 사용자 고유의 미리 학습 된 모델을 탐색할 수 있습니다. 사용자 지정 폼 모델을 빌드하고 [PYTHON SDK 미리 보기](quickstarts/try-v3-python-sdk.md) 및 기타 빠른 시작을 사용 하 여 응용 프로그램에서 모델을 참조 하는 프로젝트를 만듭니다.

다음 이미지에서는 작업 중인 청구서 미리 작성 된 모델 기능을 보여 줍니다.

:::image border="true" type="content" source="media/quickstarts/prebuilt-get-started-v2.gif" alt-text="폼 인식기 미리 작성 한 예제":::

## <a name="form-recognizer-studio-features"></a>폼 인식기 스튜디오 기능

다음 폼 인식기 서비스 기능은 스튜디오에서 사용할 수 있습니다.

* **레이아웃**: 문서에서 PDF, TIFF 및 이미지 (JPG, PNG, BMP)로 텍스트, 테이블, 선택 표시 및 구조 정보를 추출 하는 양식 인식기의 레이아웃 기능을 사용해 보세요. [스튜디오 레이아웃 빠른](quickstarts/try-v3-form-recognizer-studio.md#layout)시작을 시작 합니다. 샘플 문서와 문서를 살펴보세요. 대화형 시각화 및 JSON 출력을 사용 하 여 기능의 작동 방식을 이해 합니다. 레이아웃 [개요](concept-layout.md) 를 참조 하 여 자세한 내용을 알아보고, [레이아웃에 대 한 Python SDK 빠른](quickstarts/try-v3-python-sdk.md#try-it-layout-model)시작을 시작 하세요.

* **미리** 작성 된 모델: 양식 인식기의 미리 작성 된 모델을 사용 하면 사용자 고유의 모델을 학습 하 고 빌드하지 않고도 앱 및 흐름에 지능형 양식 처리를 추가할 수 있습니다. [Studio Prebuilts 빠른](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)시작을 시작 합니다. 샘플 문서와 문서를 살펴보세요. 대화형 시각화, 추출 된 필드 목록 및 JSON 출력을 사용 하 여 기능의 작동 방식을 이해 합니다. 미리 작성 된 청구서를 보려면 [모델 개요](concept-model-overview.md) 를 참조 하 여 자세한 내용을 알아보고 [Python SDK 빠른](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-invoice-model)시작을 시작 하세요.

* **사용자 지정 모델**: 폼 인식기의 사용자 지정 모델을 사용 하면 폼 및 문서에 맞게 데이터를 학습 한 모델에서 필드 및 값을 추출할 수 있습니다. 독립 실행형 사용자 지정 모델을 만들거나 둘 이상의 사용자 지정 모델을 결합 하 여 구성 된 모델을 만들어 여러 양식 유형에 서 데이터를 추출 합니다. [스튜디오 사용자 지정 모델 빠른](quickstarts/try-v3-form-recognizer-studio.md#custom-model-basics)시작을 시작 합니다.  온라인 마법사, 레이블 지정 인터페이스, 학습 단계 및 시각화를 사용 하 여 기능의 작동 방식을 이해 합니다. 샘플 문서로 사용자 지정 모델을 테스트 하 고 반복 하 여 모델을 향상 시킵니다. [사용자 지정 모델 개요](concept-custom.md) 를 참조 하 여 자세한 내용을 알아보고, [인식기 v 3.0 preview 마이그레이션 가이드](v3-migration-guide.md) 를 사용 하 여 새 모델을 응용 프로그램과 통합 하기 시작 합니다.

* **사용자 지정 모델: 레이블 지정 기능**: 폼 인식기 사용자 지정 모델 만들기를 사용 하려면 추출할 필드를 식별 하 고 사용자 지정 모델을 학습 하기 전에 해당 필드를 사용 하 여 문서의 콘텐츠에 레이블을 지정 해야 합니다. 텍스트, 선택 표시, 표 형식 데이터 및 기타 콘텐츠 형식의 레이블 지정은 일반적으로 학습 워크플로를 용이 하 게 하는 사용자 인터페이스와 함께 사용 됩니다. 예를 들어, [서명 검색](quickstarts/try-v3-form-recognizer-studio.md#labeling-for-signature-detection) 빠른 시작을 위한 [테이블](quickstarts/try-v3-form-recognizer-studio.md#labeling-as-tables) 및 레이블 지정을 사용 하 여 폼 인식기 스튜디오의 레이블 지정 환경을 이해할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인식자 스튜디오 양식 시작 (미리 보기)](https://formrecognizer.appliedai.azure.com)

