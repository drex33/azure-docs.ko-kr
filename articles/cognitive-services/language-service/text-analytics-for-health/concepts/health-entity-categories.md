---
title: 상태 Text Analytics 인식되는 엔터티 범주
titleSuffix: Azure Cognitive Services
description: 상태 Text Analytics 인식되는 범주에 대해 알아보기
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: e61589eaf90da3cb7e8310724a28bf993e8efb21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052903"
---
# <a name="supported-text-analytics-for-health-entity-categories"></a>상태 엔터티 범주에 대해 지원되는 Text Analytics

의료용 Text Analytics는 비정형 의료 데이터에서 정보를 처리하고 추출합니다. 서비스는 의료 개념을 검색 및 표시하고, 개념에 어설션을 할당하고, 개념 간의 의미 관계를 유추하고, 공통 의료 온톨로지에 연결합니다.

의료용 Text Analytics는 다음 범주의 의료 개념을 검색합니다.

## <a name="anatomy"></a>구조

### <a name="entities"></a>엔터티

**BODY_STRUCTURE** - 신체 체계, 해부학적 위치 또는 영역 및 신체 부위. 예를 들어 팔, 무릎, 복부, 코, 간, 머리, 호흡계, 림프구가 있습니다.

:::image type="content" source="../media/entities/anatomy-entities-body-structure.png" alt-text="신체 구조 엔터티의 예제입니다." lightbox="../media/entities/anatomy-entities-body-structure.png":::

## <a name="demographics"></a>인구 통계

### <a name="entities"></a>엔터티

**연령** - 환자, 가족 구성원 및 기타에 대한 용어를 포함한 모든 연령 용어 및 구입니다. 예를 들어 40살, 51살, 3개월, 성인, 유아, 노인, 청년, 미성년자, 중년이 있습니다.

**성별** - 대상의 성별을 알리는 용어입니다. 예를 들어 남성, 여성, 여자, 신사, 숙녀가 있습니다.

:::image type="content" source="../media/entities/age-entity.png" alt-text="연령 엔터티의 예제입니다." lightbox="../media/entities/age-entity.png":::

## <a name="examinations"></a>검사

### <a name="entities"></a>엔터티

**EXAMINATION_NAME** – 바이탈 사인 및 체격 측정치를 비롯한 진단 절차 및 테스트입니다. 예를 들어 MRI, ECG, HIV 테스트, 헤모글로빈, 혈소판 수, 척도 체계(예: *브리스톨 대변 척도*)가 있습니다.

:::image type="content" source="../media/entities/exam-name-entities.png" alt-text="검사 엔터티의 예제입니다." lightbox="../media/entities/exam-name-entities.png":::

## <a name="external-influence"></a>외부 영향

### <a name="entities"></a>엔터티

**ALLERGEN** – 알레르기 반응을 유발하는 항원입니다. 고양이, 땅콩 등을 예로 들 수 있습니다.

:::image type="content" source="../media/entities/external-influence-allergen.png" alt-text="외부 영향 엔터티의 예제입니다." lightbox="../media/entities/external-influence-allergen.png":::


## <a name="general-attributes"></a>일반 특성

### <a name="entities"></a>엔터티

**COURSE** - 상태 상황(예: 개선, 악화, 해결, 차도), 치료 또는 투약 과정(예: 투약량의 증가)과 같이 시간이 지나면서 나타나는 다른 엔터티의 변화에 대한 설명입니다. 

:::image type="content" source="../media/entities/course-entity.png" alt-text="과정 엔터티의 예제입니다." lightbox="../media/entities/course-entity.png":::

**날짜** - 의학적 상태, 검사, 치료, 약물 또는 관리 이벤트와 관련된 전체 날짜입니다.

:::image type="content" source="../media/entities/date-entity.png" alt-text="날짜 엔터티의 예제입니다." lightbox="../media/entities/date-entity.png":::

**방향** – 신체 구조, 의학적 상태, 검사 또는 치료와 관련될 수 있는 방향성 용어(예: 왼쪽, 측면, 상부, 뒤쪽)입니다.

:::image type="content" source="../media/entities/direction-entity.png" alt-text="방향 엔터티의 예제입니다." lightbox="../media/entities/direction-entity.png":::

**주기** - 의학적 상태, 검사, 치료 또는 약물이 발생했거나, 발생하거나, 발생해야 하는 주기를 설명합니다.

:::image type="content" source="../media/entities/medication-form.png" alt-text="투약 빈도 특성의 예제입니다." lightbox="../media/entities/medication-form.png":::


**시간** - 의학적 상태, 검사, 치료, 약물 또는 관리 이벤트의 시작 및/또는 길이(기간)와 관련된 시간 용어입니다. 

**MEASUREMENT_UNIT** – 검사 또는 의학적 상태 측정과 관련된 측정 단위입니다.

**MEASUREMENT_VALUE** – 검사 또는 의학적 상태 측정과 관련된 값입니다.

:::image type="content" source="../media/entities/measurement-value-entity.png" alt-text="측정값 엔터티의 예제입니다." lightbox="../media/entities/measurement-value-entity.png":::

**RELATIONAL_OPERATOR** - 엔터티와 일부 추가 정보 간의 양적 관계를 표현하는 구입니다.

:::image type="content" source="../media/entities/measurement-unit.png" alt-text="측정 단위 엔터티의 예제입니다." lightbox="../media/entities/measurement-unit.png"::: 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>엔터티

**VARIANT** - 유전자 변형과 돌연변이에 대한 모든 언급입니다. 예를 들어 `c.524C>T`, `(MTRR):r.1462_1557del96`이 있습니다.
  
**GENE_OR_PROTEIN** – 염색체, 염색체 부분 및 단백질과 함께 인간 유전자의 이름과 상징에 대한 모든 언급입니다. 예를 들어 MTRR, F2가 있습니다.

**MUTATION_TYPE** - 형식, 효과, 위치 등 돌연변이에 대한 설명입니다. 삼중 염색체, 생식 계통 돌연변이, 기능 손실을 예로 들 수 있습니다.

:::image type="content" source="../media/entities/genomics-entities.png" alt-text="유전자 엔터티의 예제입니다." lightbox="../media/entities/genomics-entities.png":::

**EXPRESSION** - 유전자 식 수준입니다. 예를 들어, 양성, 음성, 과발현, 높은/낮은 수준으로 감지됨, 정상보다 높은 수치 등이 있습니다.

:::image type="content" source="../media/entities/expression.png" alt-text="유전자 식 엔터티의 예제입니다." lightbox="../media/entities/expression.png":::


## <a name="healthcare"></a>의료

### <a name="entities"></a>엔터티
  
**ADMINISTRATIVE_EVENT** – 의료 시스템에 관련된 이벤트이지만 관리/반 관리 특성을 가지고 있습니다. 예를 들어 등록, 허용, 평가판, 연구 항목, 전송, 방전, 입원, 재원 기간이 있습니다. 

**CARE_ENVIRONMENT** – 환자를 돌보는 환경 또는 위치입니다. 예를 들어 응급실, 진료실, 심장 관련 장치(cardio unit), 호스피스, 병원이 있습니다.

:::image type="content" source="../media/entities/healthcare-event-entity.png" alt-text="의료 이벤트 엔터티의 예제입니다." lightbox="../media/entities/healthcare-event-entity.png" :::

**HEALTHCARE_PROFESSION** – 자격을 소유한 의료 전문가 또는 비면허. 예를 들어 치과의, 병리학자, 신경과 전문의, 방사선 전문의, 약사, 영양사, 물리 치료사, 척추 지압사가 있습니다.

:::image type="content" source="../media/entities/healthcare-profession-entity-2.png" alt-text="의료 환경 엔터티의 다른 예제입니다." lightbox="../media/entities/healthcare-profession-entity-2.png":::

## <a name="medical-condition"></a>의학적 상태

### <a name="entities"></a>엔터티

**진단** – 질병, 증후군, 손상. 예를 들어 유방암, 알츠하이머, HTN, CHF, 척추손상이 있습니다.

**SYMPTOM_OR_SIGN** – 질병 또는 기타 진단의 주관적 또는 객관적인 증거입니다. 예를 들어, 흉통, 두통, 현기증, 발진, SOB, 복부 부드러움, 좋은 장음, 영양 상태 좋음이 있습니다.

:::image type="content" source="../media/entities/medical-condition-entity.png" alt-text="의학적 상태 엔터티의 예제입니다." lightbox="../media/entities/medical-condition-entity.png":::

**CONDITION_QUALIFIER** - 의학적 상태를 설명하는 데 사용되는 질적 용어입니다. 다음 하위 범주는 모두 한정자로 간주됩니다.

* 시간 관련 표현: 급, 급성, 만성, 장기와 같이 시간 차원을 질적으로 설명하는 용어입니다. 
* 품질 표현: 작열감, 뾰족과 같이 의학적 상태의 “유형”을 설명하는 용어입니다.
* 중증도 표현: 심한, 가벼운, 약간, 비조절.
* 포괄성 표현: 국소, 초점, 광범위.

:::image type="content" source="../media/entities/condition-qualifier-diagnosis-3.png" alt-text="이 스크린샷은 진단 엔터티가 포함된 상태 한정자 특성의 다른 예제를 보여 줍니다." lightbox="../media/entities/condition-qualifier-diagnosis-3.png" :::

**CONDITION_SCALE** – 정성적 용어는 순서가 지정된 유한한 값의 목록인 척도로 조건의 특징을 결정합니다.

:::image type="content" source="../media/entities/condition-scale-example.png" alt-text="상태 한정자 특성 및 진단 엔터티의 다른 예제입니다." lightbox="../media/entities/condition-scale-example.png":::

## <a name="medication"></a>약물

### <a name="entities"></a>엔터티

**MEDICATION_CLASS** – 작용의 유사한 메커니즘, 관련된 작용 모드, 유사한 화학 구조 및/또는 동일한 질병을 처리하는 데 사용되는 약물의 세트입니다. 예를 들어 ACE 억제제, 오피오이드, 항생제, 통증 완화제가 있습니다.

:::image type="content" source="../media/entities/medication-entities-class.png" alt-text="약물 등급 엔터티의 예제입니다." lightbox="../media/entities/medication-entities-class.png":::

**MEDICATION_NAME** – 저작권 보호를 받는 브랜드 이름 및 비 브랜드 이름을 비롯한 약물 언급입니다. 예를 들어 이부프로펜이 있습니다.

**용량** - 주문된 약물의 양입니다. 예를 들어 염화나트륨 주입 처방 *1000mL* 가 있습니다.

**MEDICATION_FORM** - 약물의 형식입니다. 예를 들면 처방, 약, 캡슐, 정제, 패치, 겔, 연고, 포말, 스프레이, 방울, 크림, 시럽이 있습니다.

:::image type="content" source="../media/entities/medication-dosage.png" alt-text="약물 용량 특성의 예제입니다." lightbox="../media/entities/medication-dosage.png":::

**MEDICATION_ROUTE** - 약물의 관리 방법입니다. 예를 들어 경구, 국소, 흡입이 있습니다.

:::image type="content" source="../media/entities/medication-form.png" alt-text="약물 형식 특성의 예제입니다." lightbox="../media/entities/medication-form.png"::: 

## <a name="social"></a>소셜

### <a name="entities"></a>엔터티

**FAMILY_RELATION** – 주체의 친족에 관한 언급입니다. 예를 들면 아버지, 딸, 형제, 부모가 있습니다.

:::image type="content" source="../media/entities/family-relation.png" alt-text="가족 관계 엔터티의 예제입니다." lightbox="../media/entities/family-relation.png":::

## <a name="treatment"></a>처리 방법

### <a name="entities"></a>엔터티

**TREATMENT_NAME** – 치료 절차입니다. 예를 들면 무릎 관절 수술, 골수 이식, TAVI, 식이가 있습니다.

:::image type="content" source="../media/entities/treatment-entities-name.png" alt-text="치료 이름 엔터티의 예제입니다." lightbox="../media/entities/treatment-entities-name.png":::


## <a name="supported-assertions"></a>지원되는 어설션

어설션 한정자는 세 가지 범주로 나뉩니다. 각 범주는 서로 다른 측면에 중점을 둡니다.
각 범주에는 상호 배타적인 값 집합이 포함됩니다. 범주당 하나의 값만 각 항목에 할당됩니다. 각 범주에 대한 가장 일반적인 값은 기본값입니다. 서비스의 출력 응답에는 기본값과 다른 어설션 한정자만 포함됩니다.

### <a name="certainty"></a>확신도  

개념의 존재 여부(존재 및 부재)에 대한 정보와 텍스트가 존재와 관련하여 얼마나 확실한지(확실 및 가능)에 대한 정보를 제공합니다.

**Positive**(기본값): 개념이 존재하거나 발생했습니다.

**Negative**: 개념이 현재 존재하지 않거나 발생하지 않았습니다.

:::image type="content" source="../media/entities/negative-entity.png" alt-text="음성 엔터티의 예제입니다." lightbox="../media/entities/negative-entity.png":::

**Positive_Possible**: 개념이 존재할 가능성이 있지만 약간의 불확실성이 있습니다.

:::image type="content" source="../media/entities/positive-possible-entity.png" alt-text="양성 가능 엔터티의 예제입니다." lightbox="../media/entities/positive-possible-entity.png" :::

**Negative_Possible**: 개념의 존재 가능성은 낮지만 약간의 불확실성이 있습니다.

:::image type="content" source="../media/entities/negative-possible-entity.png" alt-text="음성 가능 엔터티의 예제입니다." lightbox="../media/entities/negative-possible-entity.png" :::

**Neutral_Possible**: 개념이 어느 한 쪽에 치우치는 경향이 없이 존재하거나 존재하지 않을 수 있습니다.

:::image type="content" source="../media/entities/neutral-possible-entity.png" alt-text="중립 가능 엔터티의 예제입니다." lightbox="../media/entities/neutral-possible-entity.png":::

### <a name="conditionality"></a>조건부

개념의 존재가 특정 조건에 따라 달라지는지 여부에 대한 정보를 제공합니다. 

**None**(기본값): 개념이 가상이 아니라 사실이며 특정 조건에 의존하지 않습니다.

**Hypothetical**: 개념이 미래에 개발되거나 발생할 수 있습니다.

:::image type="content" source="../media/entities/hypothetical-entity.png" alt-text="가상 엔터티의 예제입니다." lightbox="../media/entities/hypothetical-entity.png":::

**Conditional**: 개념이 존재하거나 특정 조건에서만 발생합니다.

:::image type="content" source="../media/entities/conditional-entity.png" alt-text="조건부 엔터티의 예제입니다." lightbox="../media/entities/conditional-entity.png":::

### <a name="association"></a>연결

개념이 텍스트의 주체 또는 다른 사람과 관련되어 있는지를 설명합니다.

**Subject**(기본값): 개념이 텍스트의 주체, 일반적으로 환자와 관련됩니다.

**Someone_Else**: 개념이 텍스트의 주체가 아닌 사람과 관련이 있습니다.

:::image type="content" source="../media/entities/association-entity.png" alt-text="연결 엔터티의 예제입니다." lightbox="../media/entities/association-entity.png":::



## <a name="next-steps"></a>다음 단계

* [NER 개요](../../named-entity-recognition/overview.md)
