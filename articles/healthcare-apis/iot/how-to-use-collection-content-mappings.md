---
title: IoT Connector 장치 매핑의 CollectionContentTemplate 매핑-Azure 의료 api
description: 이 문서에서는 IoT 커넥터 장치 매핑과 함께 CollectionContentTemplate 매핑을 사용 하는 방법을 설명 합니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 5fc15f07e2263598b758fe18945ae1b6f1323292
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733549"
---
# <a name="how-to-use-collectioncontenttemplate-mappings"></a>CollectionContentTemplate 매핑을 사용 하는 방법

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

> [!TIP]
> IoT 커넥터 장치 및 FHIR 대상 매핑의 편집, 테스트 및 문제 해결을 위해 [IoMT 커넥터 데이터 매퍼](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) 도구를 확인 하세요. Azure Portal에서 IoT connector로 업로드 하기 위한 매핑을 내보내거나, IoT 커넥터의 [오픈 소스 버전과](https://github.com/microsoft/iomt-fhir) 함께 사용 합니다.

이 문서에서는 IoT 커넥터 장치 매핑 템플릿과 함께 CollectionContentTemplate 매핑을 사용 하는 방법을 설명 합니다.

## <a name="collectioncontenttemplate"></a>CollectionContentTemplate

CollectionContentTemplate는 정규화 중에 사용 되는 템플릿 목록을 나타내는 데 사용할 수 있습니다.
                                                             
### <a name="example"></a>예제

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    },
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.steps",
            "valueName": "steps"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 장치 매핑을 사용 하는 방법을 알아보았습니다. FHIR 대상 매핑을 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

>[!div class="nextstepaction"]
>[FHIR 대상 매핑을 사용 하는 방법](how-to-use-fhir-mappings.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.
