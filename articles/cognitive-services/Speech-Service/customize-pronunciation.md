---
title: 발음 사용자 지정
description: 음소를 사용 하 여 음성 텍스트에서 단어의 발음을 사용자 지정할 수 있습니다.
author: ut-karsh
ms.author: umaheshwari
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/19/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 6b8f2339668656ec067bae234d21ec4bf2d5741c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052871"
---
# <a name="customize-pronunciation"></a>발음 사용자 지정

사용자 지정 음성을 사용 하면 유니버설 전화 집합을 사용 하 여 특정 단어에 대해 다른 발음를 제공할 수 있습니다. UPS (Universal 전화 set)는 국제 Phonetic (IPA)를 기반으로 하는 컴퓨터에서 읽을 수 있는 전화 번호 집합입니다. IPA는 linguists 세계 전체에서 사용 되며 표준으로 승인 됩니다.

UPS 발음는 각각 공백으로 구분 된 UPS의 문자열로 구성 됩니다. 전화 집합은 대/소문자를 구분 합니다. UPS 전화 레이블은 모두 ASCII 문자열을 사용 하 여 정의 됩니다.

UPS 구현에 대 한 단계는 [교육 전화 집합을 위한 구조적 텍스트 데이터](how-to-custom-speech-test-and-train.md#structured-text-data-for-training-public-preview) 를 참조 하세요.

이 구조화 된 텍스트 데이터는 [발음 파일과](how-to-custom-speech-test-and-train.md#pronunciation-data-for-training)동일 하지 않으며 함께 사용할 수 없습니다.

## <a name="languages-supported"></a>지원되는 언어

아래 표를 사용 하 여 해당 언어의 UPS로 이동 합니다.

| 언어                | Locale  |
|-------------------------|---------|
| [영어(미국)](phone-sets.md) | `en-US` |


## <a name="next-steps"></a>다음 단계

* [Custom Speech에 UPS 발음 제공](how-to-custom-speech-test-and-train.md#structured-text-data-for-training-public-preview)
