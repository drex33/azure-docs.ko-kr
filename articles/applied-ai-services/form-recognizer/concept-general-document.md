---
title: 일반 문서 모델 | Form Recognizer 미리 보기
titleSuffix: Azure Applied AI Services
description: 미리 작성된 일반 문서 미리 보기 모델을 사용한 데이터 추출 및 분석을 포함하는 개념
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 49ee6ed3ff8f23cb819a901aba3f370b95901fa9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129717696"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-general-document-model--preview"></a>일반 문서 모델 | Form Recognizer 미리 보기

일반 문서 미리 보기 모델은 강력한 OCR(광학 문자 인식) 기능을 딥 러닝 모델과 결합하여 문서에서 키-값 쌍 및 엔터티를 추출합니다. 일반 문서는 미리 보기(v3.0) API에서만 사용할 수 있습니다.  미리 보기(v3.0) API 사용에 대한 자세한 내용은 [마이그레이션 가이드](v3-migration-guide.md)를 참조하세요.

* 일반 문서 API는 대부분의 양식 형식을 지원하며 문서를 분석하고 검색된 테이블에 키 및 항목에 값을 연결합니다. 문서에서 일반적인 키-값 쌍을 추출하는 데 적합합니다. 레이블 없이 사용자 지정 모델을 학습하는 대신 일반 문서 모델을 사용할 수 [있습니다.](compose-custom-models.md#train-without-labels)

## <a name="try-form-recognizer-studio-preview"></a>Form Recognizer Studio(미리 보기) 사용해 보기

* Form Recognizer Studio 및 일반 문서 모델은 미리 보기(v3.0) API와 함께 사용할 수 있습니다.

* Form Recognizer Studio 일반 문서 기능을 통해 양식 및 문서에서 테이블, 값 및 엔터티를 추출합니다.

##### <a name="sample-document-processed-in-the-form-recognizer-studio"></a>[Form Recognizer Studio에서](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)처리된 샘플 문서:

:::image type="content" source="media/general-document-analyze.png" alt-text="스크린샷: Form Recognizer Studio의 일반 문서 분석":::

> [!div class="nextstepaction"]
> [Form Recognizer Studio 사용해 보세요.](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)

## <a name="general-document-features"></a>일반 문서 기능

* 키-값 쌍을 추출하기 위해 사용자 지정 모델을 학습할 필요가 없습니다.

* 단일 API는 문서에서 키 값 쌍, 엔터티, 텍스트, 테이블 및 구조를 추출하는 데 사용됩니다.

* 검사 및 정확도를 향상시키기 위해 새 데이터에 대해 주기적으로 학습되는 미리 학습된 모델입니다.

* 일반 문서 모델은 구조적, 반구조적 및 비구조적 데이터를 지원합니다.

## <a name="key-value-pairs"></a>키-값 쌍

키 값 쌍은 레이블 또는 키와 관련 응답 또는 값을 식별하는 문서 내의 특정 범위입니다. 정형 형식에서 이 레이블은 사용자가 해당 필드에 대해 입력한 값 또는 구조화되지 않은 문서에서 단락의 텍스트에 따라 계약이 실행된 날짜일 수 있습니다.  AI 모델은 다양한 문서 유형, 형식 및 구조를 기반으로 식별 가능한 키와 값을 추출하도록 학습됩니다.

키는 모델이 연결된 값 없이 또는 선택적 필드를 처리할 때 키가 존재하는 것을 감지할 때 격리되어 있을 수도 있습니다. 예를 들어 중간 이름 필드는 일부 인스턴스의 양식에서 비워 두어도 됩니다. 키 값 쌍은 항상 문서에 포함된 텍스트의 범위이며, 고객 또는 사용자와 같이 동일한 값이 여러 가지 방법으로 설명되는 문서가 있는 경우 연결된 키는 문서에 포함된 내용에 따라 고객 또는 사용자가 됩니다. 

## <a name="entities"></a>엔터티

자연어 처리 모델은 음성 부분을 식별하고 각 토큰 또는 단어를 분류할 수 있습니다. 명명된 엔터티 인식 모델은 사람, 위치 및 날짜와 같은 엔터티를 식별하여 보다 풍부한 환경을 제공할 수 있습니다. 엔터티를 식별하면 고객 유형(예: 개인 또는 조직)을 구분할 수 있습니다.
키 값 쌍 추출 모델 및 엔터티 식별 모델은 추출된 키 값 쌍의 값뿐만 아니라 전체 문서에서 병렬로 실행됩니다. 이렇게 하면 참조된 엔터티를 식별하여 키를 식별할 수 없는 복잡한 구조가 계속 보강됩니다. 식별된 범위의 오프셋에 따라 키 또는 값을 엔터티에 계속 일치시킬 수 있습니다.

* 일반 문서는 미리 학습된 모델이며 REST API 통해 직접 호출할 수 있습니다. 

* 일반 문서 모델은 여러 엔터티 범주에 대해 명명된 엔터티 인식(NER)을 지원합니다. NER는 텍스트의 다른 엔터티를 식별하고 사용자, 위치, 이벤트, 제품 및 조직과 같은 미리 정의된 클래스 또는 형식으로 분류하는 기능입니다. 엔터티 추출은 추출된 값의 유효성을 검사하려는 시나리오에서 유용할 수 있습니다. 엔터티는 추출된 값뿐만 아니라 전체 콘텐츠에서 추출됩니다.

## <a name="general-document-model-data-extraction"></a>일반 문서 모델 데이터 추출

| **모델**   | **텍스트 추출** |**키-값 쌍** |**선택 표시**   | **테이블**   |**엔터티** |
| --- | :---: |:---:| :---: | :---: |:---: |
|일반 문서  | ✓  |  ✓ | ✓  | ✓  | ✓  |

## <a name="input-requirements"></a>입력 요구 사항

* 최상의 결과를 위해 문서당 하나의 명확한 사진 또는 고품질 스캔을 제공합니다.
* 지원되는 파일 형식: JPEG, PNG, BMP, TIFF 및 PDF(텍스트 포함 또는 검사). 텍스트 포함 PDF는 문자 추출 및 위치에 오류가 발생할 가능성을 제거하는 데 가장 좋습니다.
* PDF 및 TIFF의 경우 최대 2000페이지를 처리할 수 있습니다(무료 계층 구독을 사용하면 처음 두 페이지만 처리).
* 파일 크기는 50MB 미만이어야 합니다.
* 이미지 크기는 50x50 픽셀에서 10,000x10,000 픽셀 사이여야 합니다.
* PDF 크기는 Legal 또는 A3 용지 크기에 해당하는 최대 17 x 17인치 또는 더 작은 크기입니다.
* 학습 데이터의 총 크기는 500페이지 이하입니다.
* PDF가 암호로 잠겨 있는 경우 제출하기 전에 잠금을 제거해야 합니다.
* 감독되지 않은 학습의 경우(레이블이 지정되지 않은 데이터 제외):
  * 데이터에는 키와 값이 포함되어야 합니다.
  * 키는 값의 위 또는 왼쪽에 나타나야 합니다. 아래 또는 오른쪽에 표시할 수 없습니다.

## <a name="supported-languages-and-locales"></a>지원되는 언어 및 로케일

| 모델 | 언어 - 로케일 코드 | 기본값 |
|--------|:----------------------|:---------|
|일반 문서| <ul><li>영어(미국)-en-US</li></ul>| 영어(미국)-en-US|

### <a name="named-entity-recognition-ner-categories"></a>NER(명명된 엔터티 인식) 범주

| Category | Type | 설명 |
|-----------|-------|--------------------|
| 사람 | string | 사람의 일부 또는 전체 이름입니다. |
|PersonType | string | 사람의 작업 유형 또는 역할입니다.  |
| Location | 문자열 | 자연 및 인간이 만든 랜드마크, 구조, 지리적 기능 및 지정학적 엔터티입니다. |
| 조직 | string | 회사, 정치 그룹, 음악 밴드, 스포츠 클럽, 정부 기관, 공공 단체. |
| 이벤트 | string | 역사적, 사회적 및 자연적으로 발생하는 이벤트입니다. |
| 제품 | string |다양한 범주의 물리적 개체. |
| 기술 | string | 기능, 기술 또는 전문 지식. |
| 주소 | 문자열 | 전체 우편 주소. |
| 전화 번호 | string| 전화 번호. | 
메일 | 문자열 | 메일 주소입니다. |
| URL | string| 웹 사이트 Url 및 링크|
| IP 주소 | string| 네트워크 IP 주소. |
| DateTime | string| 날짜 및 하루 중 시간. |
| 수량 | string | 숫자 측정 및 단위. |

## <a name="considerations"></a>고려 사항

* 엔터티 추출은 추출 된 값의 유효성을 검사 하려는 시나리오에서 유용할 수 있습니다. 엔터티는 추출 된 값 뿐만 아니라 문서의 전체 내용에서 추출 됩니다.

* 키가 문서에서 추출 된 텍스트의 범위입니다. 반구조적 문서에서는 키를 기존 키 사전에 매핑해야 할 수 있습니다.

* 키에 키 값 쌍을 표시 하지만 값은 표시 하지 않을 것입니다. 예를 들어 사용자가 양식에서 전자 메일 주소를 제공 하지 않기로 선택한 경우입니다.

## <a name="next-steps"></a>다음 단계

* 응용 프로그램 및 워크플로에서 미리 보기 버전을 사용 하는 방법에 대 한 자세한 내용은 [**인식기 v 3.0 마이그레이션 가이드를 참조**](v3-migration-guide.md) 하세요.

* 미리 보기 버전 및 새로운 기능에 대 한 자세한 내용은 [**REST API (미리 보기)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) 를 살펴보세요.

> [!div class="nextstepaction"]
> [양식 인식기 스튜디오 사용해 보기](https://formrecognizer.appliedai.azure.com/studio)
