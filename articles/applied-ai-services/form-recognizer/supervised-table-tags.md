---
title: 테이블 태그를 사용하여 사용자 지정 양식 모델을 학습시키는 방법 - Form Recognizer
titleSuffix: Azure Applied AI Services
description: 감독되는 테이블 태그 레이블을 효과적으로 사용하는 방법을 알아봅니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 07/23/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2d61c48204478cc19be51d5d2c0d2974c674661d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020847"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>테이블 태그를 사용하여 사용자 지정 양식 모델 학습

이 문서에서는 테이블 태그(레이블)를 사용하여 사용자 지정 양식 모델을 학습시키는 방법에 대해 알아봅니다. 일부 시나리오에서는 단순히 키-값 쌍을 맞추는 것보다 더 복잡한 레이블 지정이 필요합니다. 이러한 시나리오에는 복잡한 계층 구조가 있는 양식에서 정보를 추출하거나 서비스에서 자동으로 검색하여 추출하지 않는 항목이 있습니다. 이러한 경우 테이블 태그를 사용하여 사용자 지정 양식 모델을 학습시킬 수 있습니다.

## <a name="when-should-i-use-table-tags"></a>테이블 태그를 사용해야 하는 경우

테이블 태그를 사용하는 경우에 대한 몇 가지 예는 다음과 같습니다.

- 추출하려는 데이터가 양식에 테이블로 표시되고, 테이블의 구조는 의미가 있습니다. 예를 들어 테이블의 각 행은 하나의 항목을 나타내고, 행의 각 열은 해당 항목의 특정 기능을 나타냅니다. 이 경우 열은 기능을 나타내고, 행은 각 기능에 대한 정보를 나타내는 테이블 태그를 사용할 수 있습니다.
- 추출하려는 데이터가 특정 양식 필드에 표시되지 않지만 의미상 2차원 그리드에 맞을 수 있습니다. 예를 들어 양식에는 이름, 성 및 이메일 주소가 포함된 사람 목록이 있습니다. 이 정보를 추출하려고 합니다. 이 경우 이름, 성 및 이메일 주소가 있는 테이블 태그를 열로 사용할 수 있으며, 각 행은 목록의 사람에 대한 정보로 채워집니다.

> [!NOTE]
> Form Recognizer는 태그가 테이블에 지정되었는지 여부에 관계없이 문서의 모든 테이블을 자동으로 찾아서 추출합니다. 따라서 테이블 태그를 양식의 모든 테이블에 지정할 필요가 없으며, 테이블 태그는 양식에서 찾은 해당 테이블의 구조를 복제할 필요가 없습니다. Form Recognizer에서 자동으로 추출한 테이블은 JSON 출력의 pageResults 섹션에 포함됩니다.

## <a name="create-a-table-tag-with-the-form-recognizer-sample-labeling-tool"></a>폼 인식기 샘플 레이블 도구를 사용 하 여 테이블 태그 만들기
<!-- markdownlint-disable MD004 -->
* **동적** 또는 **고정 크기** 의 테이블 태그를 사용할지 여부를 결정합니다. 행 수가 문서마다 다를 경우 동적 테이블 태그를 사용합니다. 행 수가 문서 전체에서 일치하는 경우 고정 크기 테이블 태그를 사용합니다.
* 동적 테이블 태그인 경우 각 열에 대한 열 이름, 데이터 형식 및 형식을 정의합니다.
* 고정 크기 테이블인 경우 각 태그에 대항 열 이름, 행 이름, 데이터 형식 및 형식을 정의합니다.
:::image type="content" source="./media/table-tag-configure.png" alt-text="테이블 태그 구성":::

## <a name="label-your-table-tag-data"></a>테이블 태그 데이터에 레이블 지정

* 테이블 태그가 프로젝트에 있는 경우 레이블 지정 패널을 열고, 키-값 필드에서 레이블을 지정하는 것처럼 태그를 채울 수 있습니다.
:::image type="content" source="media/table-labeling.png" alt-text="테이블 태그가 있는 레이블":::

## <a name="next-steps"></a>다음 단계

빠른 시작에 따라 사용자 지정 Form Recognizer 모델을 학습시키고 사용합니다.

> [!div class="nextstepaction"]
> [샘플 레이블 도구를 사용 하 여 레이블로 학습](label-tool.md)

## <a name="see-also"></a>참고 항목

* [샘플 레이블 도구를 사용 하 여 사용자 지정 모델 학습](label-tool.md)
>
