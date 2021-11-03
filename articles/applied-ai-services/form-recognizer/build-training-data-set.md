---
title: 사용자 지정 모델용 학습 데이터 세트를 빌드하는 방법 - Form Recognizer
titleSuffix: Azure Applied AI Services
description: 학습 데이터 세트가 Form Recognizer 모델 학습에 최적화되도록 하는 방법을 알아봅니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 640cad589343a90912dfe0dba615f1df6baaa6b2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027698"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>사용자 지정 모델용 학습 데이터 세트 빌드

Form Recognizer 사용자 지정 모델을 사용하는 경우 해당 모델에서 산업별 양식을 학습할 수 있도록 사용자 고유의 학습 데이터를 [사용자 지정 모델 학습](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync) 작업에 제공합니다. 이 가이드에 따라 모델을 효과적으로 학습시키기 위해 데이터를 수집하고 준비하는 방법에 대해 알아봅니다.

동일한 유형의 5개 이상의 채워진 양식이 필요합니다.

수동으로 레이블이 지정된 학습 데이터를 사용하려면 동일한 유형의 5개 이상의 채워진 양식으로 시작해야 합니다. 필요한 데이터 세트 외에도 레이블이 없는 양식도 계속 사용할 수 있습니다.

## <a name="custom-model-input-requirements"></a>사용자 지정 모델 입력 요구 사항

먼저 학습 데이터 세트에서 Form Recognizer의 입력 요구 사항을 따르는지 확인합니다.

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>학습 데이터 팁

학습을 위해 데이터 세트를 추가로 최적화하려면 다음 추가 팁을 따릅니다.

* 가능하면 이미지 기반 문서 대신 텍스트 기반 PDF 문서를 사용합니다. 스캔한 PDF는 이미지로 처리됩니다.
* 채워진 양식의 경우 모든 필드가 채워진 예제를 사용합니다.
* 각 필드에 서로 다른 값이 있는 양식을 사용합니다.
* 양식 이미지의 품질이 낮은 경우 더 큰 데이터 세트(예: 10~15개 이미지)를 사용합니다.

## <a name="upload-your-training-data"></a>학습 데이터 업로드

학습에 사용할 양식 문서 세트를 함께 배치한 경우 Azure Blob 스토리지 컨테이너에 업로드해야 합니다. 컨테이너를 사용하여 Azure 스토리지 계정을 만드는 방법을 모르는 경우 [Azure Portal의 Azure Storage 빠른 시작](../../storage/blobs/storage-quickstart-blobs-portal.md)을 따릅니다. 표준 성능 계층을 사용합니다.

수동으로 레이블이 지정된 데이터를 사용하려면 학습 문서에 해당하는 *.labels.json* 및 *.ocr.json* 파일도 업로드해야 합니다. 샘플 레이블 [지정 도구(또는](label-tool.md) 사용자 고유의 UI)를 사용하여 이러한 파일을 생성할 수 있습니다.

### <a name="organize-your-data-in-subfolders-optional"></a>하위 폴더에 데이터 구성(선택 사항)

기본적으로 [사용자 지정 모델 학습](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync) API는 스토리지 컨테이너의 루트에 있는 양식 문서만 사용합니다. 그러나 API 호출에서 데이터를 지정하는 경우 하위 폴더의 데이터를 사용하여 학습시킬 수 있습니다. 일반적으로 [사용자 지정 모델 학습](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync) 호출 본문의 형식은 다음과 같습니다. 여기서 `<SAS URL>`은 컨테이너의 공유 액세스 서명 URL입니다.

```json
{
  "source":"<SAS URL>"
}
```

다음 콘텐츠를 요청 본문에 추가하면 API에서 하위 폴더에 있는 문서를 사용하여 학습시킵니다. `"prefix"` 필드는 선택 사항이며, 학습 데이터 세트를 지정된 문자열로 시작하는 경로의 파일로 제한합니다. 예를 들어 `"Test"` 값은 API에서 "Test"라는 단어로 시작하는 파일 또는 폴더만 확인하도록 합니다.

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>다음 단계

이제 학습 데이터 세트를 빌드하는 방법을 알아보았으므로 빠른 시작에 따라 사용자 지정 Form Recognizer 모델을 학습시키고 양식에서 사용하기 시작합니다.

* [클라이언트 라이브러리 또는 REST API를 사용하여 모델 학습 및 양식 데이터 추출](quickstarts/try-sdk-rest-api.md)
* [샘플 레이블 지정 도구에서 레이블을 사용하여 학습](label-tool.md)

## <a name="see-also"></a>참조

* [Form Recognizer란?](./overview.md)
