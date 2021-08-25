---
title: Azure Form Recognizer란?
titleSuffix: Azure Applied AI Services
description: Azure Form Recognizer 서비스를 사용하면 양식 문서에서 키/값 쌍 및 테이블 데이터를 식별 및 추출할 수 있을 뿐만 아니라 판매 영수증 및 비즈니스 카드에서 주요 정보를 추출할 수 있습니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: 자동화된 데이터 처리, 문서 처리, 자동화된 데이터 입력, 양식 처리
ms.openlocfilehash: 2231832f87b2caf085f5d5278a2b291cda25b606
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327226"
---
# <a name="what-is-azure-form-recognizer"></a>Azure Form Recognizer란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer는 기계 학습 기술을 사용하여 자동화된 데이터 처리 소프트웨어를 빌드할 수 있는 [Azure Applied AI Services](../../applied-ai-services/index.yml)의 일부입니다. 문서에서 텍스트, 키/값 쌍, 선택 표시, 표 및 구조를 식별하고 추출합니다. &mdash;이 서비스는 원본 파일의 관계, 경계 상자, 신뢰도 등을 포함하는 구조화된 데이터를 출력합니다. 많은 수동 작업 또는 광범위한 데이터 과학 전문 지식 없이도 특정 콘텐츠에 맞게 조정된 정확한 결과를 빠르게 얻을 수 있습니다. Form Recognizer를 사용하여 애플리케이션에서 데이터 입력을 자동화하고 문서 검색 기능을 보강합니다.

Form Recognizer는 사용자 지정 문서 처리 모델, 송장, 영수증, ID 및 명함용으로 미리 빌드된 모델, 레이아웃 모델로 구성됩니다. REST API 또는 클라이언트 라이브러리 SDK를 사용하여 Form Recognizer 모델을 호출하여 복잡성을 줄이고 워크플로 또는 애플리케이션에 통합할 수 있습니다.

이 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.

* [**개념**](concept-layout.md)은 서비스 기능에 대한 심층적인 설명을 제공합니다.
* [**빠른 시작**](quickstarts/client-library.md)은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* [**방법 가이드**](build-training-data-set.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
* [**자습서**](tutorial-ai-builder.md)는 보다 광범위한 비즈니스 솔루션에서 서비스를 구성 요소로 사용하는 방법을 보여주는 긴 가이드입니다.

## <a name="form-recognizer-features"></a>Form Recognizer 기능

Form Recognizer를 사용하면 다음과 같은 기능을 통해 문서 데이터를 쉽게 추출하고 분석할 수 있습니다.

### <a name="layout"></a>[레이아웃](concept-layout.md)

문서에서 경계 상자 좌표와 함께 텍스트, 선택 표시 및 표 구조를 추출합니다.

Form Recognizer는 고해상도 OCR(광학 문자 인식)과 문서의 향상된 딥 러닝 모델을 사용하여 텍스트, 선택 표시 및 표 구조(텍스트와 관련된 행 및 열 번호)를 추출할 수 있습니다.

:::image type="content" source="./media/tables-example.jpg" alt-text="표 예" lightbox="./media/tables-example.jpg":::

### <a name="custom-models"></a>[사용자 지정 모델](concept-custom.md)

양식에서 텍스트, 키/값 쌍, 선택 표시 및 표 데이터를 추출합니다. 이러한 모델은 사용자 고유의 데이터로 학습되므로 사용자의 양식에 맞게 조정됩니다.

Form Recognizer 사용자 지정 모델은 사용자 고유의 데이터로 학습하며, 샘플 입력 양식 5개만 있으면 시작할 수 있습니다. 학습된 문서 처리 모델은 원본 양식 문서의 관계가 포함된 정형 데이터를 출력할 수 있습니다. 모델이 학습되면 데이터를 더 많은 양식에서 안정적으로 추출하기 위해 필요에 따라 모델을 테스트, 재학습 및 최종적으로 사용할 수 있습니다.

사용자 지정 모델을 학습할 때 레이블 지정 데이터를 사용할 수도 있고 사용하지 않을 수도 있습니다.

#### <a name="train-without-labels"></a>레이블 없이 학습

Form Recognizer는 자율 학습을 사용하여 양식의 필드와 항목 간의 레이아웃 및 관계를 이해합니다. 입력 양식을 제출하면 알고리즘에서 형식별로 양식을 클러스터링하고, 존재하는 키와 테이블을 검색하고, 값을 키에 연결하고, 항목을 테이블에 연결합니다. 레이블이 없는 학습에는 수동 데이터 레이블을 지정하거나 과도한 코딩 및 유지 관리가 필요 없으므로 이 방법을 먼저 시도하는 것이 좋습니다.

학습 문서를 수집하는 방법에 대한 팁은 [학습 데이터 세트 빌드](./build-training-data-set.md)를 참조하세요.

#### <a name="train-with-labels"></a>레이블을 사용하여 학습

레이블이 지정된 데이터로 학습하는 경우 모델은 감독 학습을 통해 사용자가 제공하는 레이블 지정 양식을 사용하여 관심 있는 값을 추출합니다. 레이블이 지정된 데이터는 모델 성능이 향상되며, 복잡한 양식 또는 키 없는 값을 포함하는 양식과 함께 작동하는 모델을 생성할 수 있습니다.

Form Recognizer는 [레이아웃 API](#layout)를 사용하여 인쇄 및 필기된 텍스트 요소의 예상 크기와 위치를 학습하고 테이블을 추출합니다. 그런 다음, 사용자 지정 레이블을 사용하여 문서의 키/값 연결 및 테이블을 학습합니다. 새 모델을 학습시킬 때 동일한 유형(동일한 구조)의 수동 레이블 지정 양식 5개를 사용하여 시작한 후 필요에 따라 레이블 지정 데이터를 추가하여 모델 정확도를 개선하는 것이 좋습니다. Form Recognizer를 통해 감독 학습 기능을 사용하여 키 값 쌍과 테이블을 추출하도록 모델을 학습할 수 있습니다.

[레이블로 학습 시작](label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


### <a name="prebuilt-models"></a>미리 빌드된 모델

 Form Recognizer에는 영수증, 명함, 청구서 및 ID 문서의 자동화된 데이터 처리를 위한 미리 빌드된 모델도 포함되어 있습니다.

### <a name="receipts"></a>[Receipts](concept-receipts.md)

미리 빌드된 영수증 모델은 오스트레일리아, 캐나다, 영국, 인도, 미국&mdash;(식당, 주유소, 소매점 등)에서 사용되는 유형의 영어 판매 영수증을 읽는 데 사용됩니다. 이 모델은 트랜잭션 시간 및 날짜, 판매자 정보, 세금의 합계, 줄 항목, 총액 등과 같은 주요 정보를 추출합니다. 또한 미리 빌드된 영수증 모델은 영수증의 모든 텍스트를 분석하고 반환하도록 학습됩니다.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="샘플 영수증" lightbox="./media/overview-receipt.jpg":::

### <a name="business-cards"></a>[명함](concept-business-cards.md)

명함 모델을 사용하면 영어로 된 명함에서 사람의 이름, 직함, 주소, 이메일, 회사 및 전화번호와 같은 정보를 추출할 수 있습니다.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="샘플 명함" lightbox="./media/overview-business-card.jpg":::

### <a name="invoices"></a>[송장](concept-invoices.md)

미리 빌드된 송장 모델은 다양한 형식의 송장에서 데이터를 추출하고 구조화된 데이터를 반환합니다. 이 모델은 송장 ID, 고객 세부 정보, 공급 업체 세부 정보, 배송지, 청구지, 합계, 세금, 소계, 개별 항목 등과 같은 주요 정보를 추출합니다. 또한 미리 빌드된 청구서 모델은 청구서의 모든 텍스트와 표를 분석하고 반환하도록 학습됩니다.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="샘플 송장" lightbox="./media/overview-invoices.jpg":::

### <a name="identity-documents"></a>[ID 문서](concept-identification-cards.md)

ID(ID 문서) 모델을 사용하면 전 세계 여권 및 미국 드라이버 라이선스에서 주요 정보를 추출할 수 있습니다. 문서 ID, 생년월일, 만료 날짜, 이름, 국가, 지역, 머신에서 읽을 수 있는 영역 등과 같은 데이터를 추출합니다.

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="샘플 식별 카드" lightbox="./media/overview-id.jpg":::

## <a name="get-started"></a>시작

샘플 Form Recognizer 도구를 사용하여 레이아웃, 미리 빌드된 모델을 사용해 보고 문서에 대한 사용자 지정 모델을 학습합니다. Form Recognizer 서비스를 사용해보려면 Azure 구독([**무료로 하나 생성**](https://azure.microsoft.com/free/cognitive-services))과 [**Form Recognizer 리소스**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 엔드포인트 및 키가 필요합니다.

>
> [!div class="nextstepaction"]
> [Form Recognizer 사용해보기](https://aka.ms/fott-2.1-ga/)
>

[클라이언트 라이브러리/REST API 빠른 시작](./quickstarts/client-library.md)에 따라 문서에서 데이터 추출을 시작합니다. 기술을 학습할 때 체험판 서비스를 이용하는 것이 좋습니다. 체험판 페이지는 한 달에 500페이지로 제한됩니다.

자세히 알아보려면 [REST API 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)를 확인하세요. 이전 버전의 API에 대해 잘 알고 있는 경우에는 [새로운 기능](./whats-new.md) 문서를 읽고 최신 변경 내용에 대해 알아보세요.

## <a name="input-requirements"></a>입력 요구 사항

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="service-availability-and-redundancy"></a>서비스 가용성 및 중복성

### <a name="is-form-recognizer-service-zone-resilient"></a>Form Recognizer 서비스가 영역 복원력이 있습니까?

예. Form Recognizer 서비스는 기본적으로 영역 복원력이 있습니다.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>영역 복원력을 갖도록 Form Recognizer 서비스를 구성하려면 어떻게 해야 하나요?

영역 복원력을 사용하도록 설정하기 위한 고객 구성은 필요 없습니다. Form Recognizer 리소스의 영역 복원력은 기본적으로 제공되며 서비스 자체에서 관리합니다.

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

* 모든 Cognitive Services와 마찬가지로 Form Recognizer 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft 정책을 알고 있어야 합니다. Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)에서 자세히 알아보세요.

* Form Recognizer에서는 고객이 서비스 인스턴스를 배포하는 지역 외부에서 고객 데이터를 저장하거나 처리하지 않습니다.

## <a name="next-steps"></a>다음 단계

Form Recognizer 서비스에 대해 자세히 알아보려면 온라인 도구 및 빠른 시작을 사용해 보세요.

* [**Form Recognizer 도구**](https://aka.ms/fott-2.1-ga)
* [**클라이언트 라이브러리 및 REST API 빠른 시작**](quickstarts/client-library.md)