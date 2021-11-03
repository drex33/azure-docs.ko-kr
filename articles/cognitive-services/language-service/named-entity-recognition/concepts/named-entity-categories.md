---
title: Azure Cognitive Service for Language에서 명명된 엔터티 인식 인식되는 엔터티 범주
titleSuffix: Azure Cognitive Services
description: NER 기능이 비정형 텍스트에서 인식할 수 있는 엔터티에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: 17c911dadbe0ce741e6e33fd51e0133ff1a67345
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103191"
---
# <a name="supported-named-entity-recognition-ner-entity-categories"></a>NER(지원되는 명명된 엔터티 인식) 엔터티 범주

이 문서를 사용하면 [NER(명명된 엔터티 인식)](../how-to-call.md)에서 반환할 수 있는 엔터티 범주를 찾을 수 있습니다. NER은 예측 모델을 실행하여 입력 문서에서 명명된 엔터티를 식별하고 분류합니다.

## <a name="category-person"></a>범주: 사람

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        사람

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        사용자의 이름.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

## <a name="category-persontype"></a>범주: PersonType

이 범주에는 다음 엔터티가 포함됩니다.


:::row:::
    :::column span="":::
        **엔터티**

        PersonType

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        사용자가 보유한 작업 유형 또는 역할
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

## <a name="category-location"></a>범주: 위치

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        위치

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        자연 및 인간이 만든 랜드마크, 구조, 지리적 기능 및 지정학적 엔터티.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>하위 범주

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        지정학적 엔터티(GPE)

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        도시, 국가/지역, 주.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Structural

    :::column-end:::
    :::column span="2":::

        인공 구조물. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        지리적

    :::column-end:::
    :::column span="2":::

        강, 바다 및 사막과 같은 지리적 및 자연적 특징.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

## <a name="category-organization"></a>범주: 조직

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        조직

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        회사, 정치 그룹, 음악 밴드, 스포츠 클럽, 정부 기관, 공공 단체. 국적 및 종교는 이 엔터티 형식에 포함되지 않습니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>하위 범주

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        의료

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        의료 회사 및 그룹.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        증권 거래소

    :::column-end:::
    :::column span="2":::

        증권 거래소 그룹. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        스포츠

    :::column-end:::
    :::column span="2":::

        스포츠 관련 단체.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

## <a name="category-event"></a>범주: 이벤트

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        이벤트

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        역사적, 사회적 및 자연적으로 발생하는 이벤트입니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` 및 `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>하위 범주

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        문화권

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        문화권 이벤트 및 휴일.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Natural

    :::column-end:::
    :::column span="2":::

        자연적으로 발생하는 이벤트.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        스포츠

    :::column-end:::
    :::column span="2":::

        스포츠 이벤트.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

## <a name="category-product"></a>범주: 제품

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        제품

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        다양한 범주의 물리적 개체.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>하위 범주

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        컴퓨팅 제품
    :::column-end:::
    :::column span="2":::
        **세부 정보**

        컴퓨팅 제품.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`   
      
   :::column-end:::
:::row-end:::

## <a name="category-skill"></a>범주: 기술

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        기술

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        기능, 기술 또는 전문 지식.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en` , `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br` 
      
   :::column-end:::
:::row-end:::

## <a name="category-address"></a>범주: 주소

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        주소

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        전체 우편 주소.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

## <a name="category-phonenumber"></a>범주: PhoneNumber

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        전화 번호(미국과 유럽 전화 번호만 해당).
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

## <a name="category-email"></a>범주: 이메일

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        메일

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이메일 주소.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

## <a name="category-url"></a>범주: URL

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        URL

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        웹 사이트에 대한 URL입니다. 
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

## <a name="category-ip"></a>범주: IP

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        IP

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        네트워크 IP 주소. 
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

## <a name="category-datetime"></a>범주: DateTime

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        DateTime

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        날짜 및 하루 중 시간. 
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

#### <a name="subcategories"></a>하위 범주

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        Date

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        달력 날짜.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        시간

    :::column-end:::
    :::column span="2":::

        하루 중 시간.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        DateRange

    :::column-end:::
    :::column span="2":::

        날짜 범위.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        TimeRange

    :::column-end:::
    :::column span="2":::

        시간 범위.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Duration

    :::column-end:::
    :::column span="2":::

        기간
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        설정

    :::column-end:::
    :::column span="2":::

        반복 횟수를 설정합니다.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

## <a name="category-quantity"></a>범주: 수량

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        수량

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        숫자 및 숫자 수량.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>하위 범주

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        숫자

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        숫자
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        백분율

    :::column-end:::
    :::column span="2":::

        백분율
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        서수 번호

    :::column-end:::
    :::column span="2":::

        서수 번호.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        나이

    :::column-end:::
    :::column span="2":::

        나이.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        통화

    :::column-end:::
    :::column span="2":::

        통화
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        차원

    :::column-end:::
    :::column span="2":::

        차원 및 측정.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        온도

    :::column-end:::
    :::column span="2":::

        온도.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::


## <a name="next-steps"></a>다음 단계

* [NER 개요](../overview.md)
