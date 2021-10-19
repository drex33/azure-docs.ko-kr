---
title: FHIR 서비스에 대해 식별 되지 않은 데이터 (미리 보기) 내보내기
description: 이 문서에서는 식별 되지 않은 내보내기를 설정 하 고 사용 하는 방법을 설명 합니다.
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: ranku
ms.openlocfilehash: b0f8a1a9ddce7648d26bf73ebb0eadead06dba6c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435952"
---
# <a name="exporting-de-identified-data-preview"></a>식별 되지 않은 데이터 내보내기 (미리 보기)

> [!Note] 
> 확인 되지 않은 내보내기를 사용 하는 경우의 결과는 데이터 입력 및 고객이 선택한 함수에 따라 달라 집니다. Microsoft는 확인 되지 않은 내보내기 출력을 평가 하거나 고객의 사용 사례 및 규정 준수 요구 사항에 대 한 acceptability를 확인할 수 없습니다. 식별 되지 않은 내보내기는 특정 법률, 규정 또는 규정 준수 요구 사항을 충족 하는 것이 보장 되지 않습니다.

$Export 명령을 사용 하 여 FHIR 서버에서 식별 되지 않은 데이터를 내보낼 수도 있습니다. [익명화에 대 한 Fhir 도구의](https://github.com/microsoft/FHIR-Tools-for-Anonymization)익명화 엔진을 사용 하 고 쿼리 매개 변수에서 익명화 구성 세부 정보를 가져옵니다. 사용자 고유의 익명화 config 파일을 만들거나 HIPAA 금고 Harbor 메서드에 대 한 [샘플 구성 파일](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) 을 시작 점으로 사용할 수 있습니다. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> 현재 FHIR 서비스는 시스템 수준 ($export)에서 확인 되지 않은 내보내기를 지원 합니다.

|쿼리 매개 변수            | 예제 |옵션| 설명|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig. json|식별 되지 않은 내보내기에 필요 |구성 파일의 이름입니다. [여기](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)에서 구성 파일 형식을 참조 하세요. 이 파일은 내보내기 위치로 구성 된 동일한 Azure storage 계정 내의 **익명화** 이라는 컨테이너 내에 보관 되어야 합니다. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|식별 되지 않은 내보내기의 경우 선택 사항|이는 구성 파일의 Etag입니다. blob 속성에서 Azure Storage Explorer를 사용 하 여 Etag를 가져올 수 있습니다.|

> [!IMPORTANT]
> 원시 내보내기와 식별 되지 않은 내보내기는 모두 내보내기 구성의 일부로 지정 된 동일한 Azure storage 계정에 기록 합니다. 다른 식별 되지 않은 구성에 해당 하는 다른 컨테이너를 사용 하 고 컨테이너 수준에서 사용자 액세스를 관리 하는 것이 좋습니다.
