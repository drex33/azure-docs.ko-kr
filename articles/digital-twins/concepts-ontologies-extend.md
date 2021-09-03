---
title: 온톨로지 확장
titleSuffix: Azure Digital Twins
description: 온톨로지 확장의 이유 및 전략에 대해 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 4051f8fe4b338f223b36c3a87ebcc3e3d3e51979
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472976"
---
# <a name="extending-ontologies"></a>온톨로지 확장 

[스마트 빌드를 위한 DTDL 기반 RealEstateCore 온톨로지](https://github.com/Azure/opendigitaltwins-building)와 같은 업계 표준 [온톨로지](concepts-ontologies.md)는 IoT 솔루션 빌드를 시작하는 좋은 방법입니다. 업계 온톨로지는 사용자 도메인에 맞게 설계되었고 Azure Digital Twins와 같은 Azure IoT 서비스에서 즉시 사용할 수 있도록 설계된 다양한 기능의 기본 인터페이스를 제공합니다. 

하지만 특정 솔루션에 따라 업계 토폴로지로 지원되지 않는 특별한 요구 사항이 존재할 수 있습니다. 예를 들어 디지털 트윈을 별도 시스템에 저장된 3D 모델에 연결해야 할 수 있습니다. 이 경우 원래 온톨로지의 이점을 모두 활용하면서도 고유 기능을 추가하도록 온톨로지 중 하나를 확장할 수 있습니다.

이 문서에서는 DTDL 기반 [RealEstateCore](https://www.realestatecore.io/) 온톨로지를 새로운 DTDL 속성을 사용한 온톨로지 확장의 기본 예시로 사용합니다. 하지만 여기에 설명된 기법은 일반적인 내용이며 DTDL 기능(원격 분석, 속성, 관계, 구성 요소 또는 명령)을 포함하는 DTDL 기반 온톨로지 중 일부에 적용될 수 있습니다. 

## <a name="realestatecore-space-hierarchy"></a>RealEstateCore 공간 계층 구조 

DTDL 기반 RealEstateCore 온톨로지에서 Space 계층 구조는 Room, Building, Zone 등 여러 유형의 공간을 정의하기 위해 사용됩니다. 이 계층 구조는 이러한 각 모델로부터 확장되어 여러 유형의 Room, Building 및 Zone을 정의합니다. 

계층 구조의 일부는 아래 다이어그램과 같이 표시됩니다. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-original.png" alt-text="RealEstateCore 공간 계층 구조의 일부를 보여주는 다이어그램. Space, Room, ConferenceRoom 및 Office의 요소를 보여줍니다."::: 

RealEstateCore 온톨로지에 대한 자세한 내용은 [업계 표준 온톨로지 채택](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology)을 참조하세요.

## <a name="extending-the-realestatecore-space-hierarchy"></a>RealEstateCore 공간 계층 구조 확장 

일부 경우에는 솔루션에 업계 온톨로지로 지원되지 않는 특별한 요구 사항이 존재할 수 있습니다. 이 경우 계층 구조를 확장하면 업계 온톨로지를 계속 사용하면서도 특정 요구 사항에 맞게 사용자 지정할 수 있습니다. 

이 문서에서는 온톨로지의 계층 구조를 확장하는 것이 유용한 두 가지 사례를 살펴봅니다. 

* 업계 온톨로지에 없는 개념을 위한 새로운 인터페이스 추가. 
* 기존 인터페이스에 추가적인 속성(또는 관계, 구성 요소, 원격 분석 또는 명령) 추가.

### <a name="add-new-interfaces-for-new-concepts"></a>새로운 개념을 위한 새로운 인터페이스 추가 

이 경우 솔루션에 필요하지만 업계 온톨로지에 없는 개념에 대해 인터페이스를 추가해야 합니다. 예를 들어 DTDL 기반 RealEstateCore 온톨로지에 없는 다른 룸 유형이 솔루션에 포함된 경우 RealEstateCore 인터페이스에서 직접 확장하여 이를 추가할 수 있습니다. 

아래 예제에서는 RealEstateCore 온톨로지에 없는 “포커스 룸”을 표현해야 하는 솔루션을 보여줍니다. 포커스 룸은 여러 사람이 한 번에 몇 시간씩 특정 작업에 집중할 수 있도록 설계된 작은 공간입니다. 

이 새로운 개념으로 업계 온톨로지를 확장하려면 업계 인터페이스로부터 [확장되는](concepts-models.md#model-inheritance) 새로운 인터페이스를 만듭니다. 

포커스 룸 인터페이스를 추가한 후에는 확장된 계층 구조에 새로운 룸 유형이 표시됩니다. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-1.png" alt-text="새로운 추가 사항을 포함하여 RealEstateCore 공간 계층 구조의 일부를 보여주는 다이어그램."::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>기존 인터페이스에 추가 기능 추가 

여기에서는 업계 온톨로지에 있는 인터페이스에 더 많은 속성(또는 관계, 구성 요소, 원격 분석 또는 명령)을 추가하려고 합니다.

이 섹션에서는 두 가지 예제를 보여줍니다. 
* 이미 기존 시스템이 있는 공간의 3D 도면을 표시하는 솔루션을 빌드하는 경우, 솔루션에 공간에 대한 정보가 표시될 때 기존 시스템의 3D 도면도 검색할 수 있도록 각 디지털 트윈을 해당 3D 도면에 ID로 연결해야 할 수 있습니다. 
* 솔루션이 컨퍼런스 룸의 온라인/오프라인 상태를 추적해야 할 경우 표시 또는 쿼리에 사용할 수 있도록 컨퍼런스 룸 상태를 추적해야 할 수 있습니다. 

두 예제 모두 새로운 속성을 사용하여 구현될 수 있습니다. `drawingId` 속성은 3D 도면을 디지털 트윈 및 “온라인” 속성에 연결하여 컨퍼런스 룸이 온라인 상태인지 여부를 나타냅니다. 

일반적으로 이후 솔루션에서 업데이트를 통합할 수 있기를 원하므로(추가 항목 덮어쓰기) 업계 온톨로지를 직접 수정하지는 않습니다. 대신 이러한 종류의 추가는 DTDL 기반 RealEstateCore 온톨로지로부터 확장되는 고유 인터페이스 계층 구조에서 수행될 수 있습니다. 생성되는 각 인터페이스에는 확장된 인터페이스 계층 구조로부터 상위 RealEstateCore 인터페이스 및 상위 인터페이스를 확장하기 위해 여러 인터페이스 상속성이 사용됩니다. 이 접근 방식을 사용하면 업계 온톨로지 및 추가 항목을 함께 사용할 수 있습니다. 

업계 온톨로지를 확장하려면 업계 온톨로지의 인터페이스에서 확장되는 고유 인터페이스를 만들고 새 기능을 확장된 인터페이스에 추가합니다. 확장하려는 각 인터페이스에 대해 새 인터페이스를 만듭니다. 확장된 인터페이스는 DTDL로 작성됩니다(이 문서 뒷부분의 확장된 인터페이스 섹션에서 DTDL 참조). 

위에 표시된 계층 구조의 일부를 확장한 후 확장된 계층 구조는 아래 다이어그램과 같이 표시됩니다. 여기에서 확장된 Space 인터페이스는 디지털 트윈을 3D 도면과 연결하는 ID가 포함된 `drawingId` 속성을 추가합니다. 또한 ConferenceRoom 인터페이스는 컨퍼런스 룸의 온라인 상태를 포함하는 “온라인” 속성을 추가합니다. 상속성을 통해 ConferenceRoom 인터페이스는 확장된 Space 인터페이스의 모든 기능은 물론 RealEstateCore ConferenceRoom 인터페이스의 모든 기능을 포함합니다. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-2.png" alt-text="확장된 RealEstateCore 공간 계층 구조를 보여주는 다이어그램으로, 설명된 대로 더 많은 새로운 추가 사항이 있습니다."::: 

## <a name="using-the-extended-space-hierarchy"></a>확장된 공간 계층 구조 사용 

확장된 Space 계층 구조를 사용하여 디지털 트윈을 만들 때 각 디지털 트윈의 모델은 원래 업계 토폴로지 대신 확장된 Space 계층 구조에서 가져온 것이며, 인터페이스 상속성을 통해 업계 온톨로지 및 확장된 인터페이스의 모든 기능을 포함합니다.

각 디지털 트윈의 모델은 아래 다이어그램에 표시된 확장된 계층 구조의 인터페이스입니다. 
 
:::image type="content" source="media/concepts-ontologies-extend/ontology-with-models.png" alt-text="연결된 모델 Space, Room, ConferenceRoom, Office 및 FocusRoom을 포함하여 확장된 RealEstateCore 공간 계층 구조를 보여주는 다이어그램."::: 

모델 ID(`IS_OF_MODEL` 연산자)를 사용하여 디지털 트윈을 쿼리할 때는 확장된 계층 구조의 모델 ID를 사용해야 합니다. 예들 들어 `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`입니다. 

## <a name="contributing-back-to-the-original-ontology"></a>원래 온톨로지에 다시 기여 

일부 경우에는 대부분의 온톨로지 사용자에게 포괄적으로 유용한 방식으로 업계 온톨로지를 확장합니다. 이 경우 확장을 다시 원래 온톨로지에 기여하는 것을 고려해야 합니다. 각 온톨로지에는 서로 다른 기여 프로세스가 사용되므로 온톨로지의 GitHub 리포지토리에서 기여 세부 정보를 확인해야 합니다. 

## <a name="dtdl-for-new-interfaces"></a>새 인터페이스를 위한 DTDL 

업계 온톨로지에서 직접 확장되는 새 인터페이스의 DTDL은 다음과 같이 표시됩니다. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>확장 인터페이스를 위한 DTDL 

위에 설명된 부분에 국한하여 확장된 인터페이스의 DTDL은 다음과 같이 표시됩니다. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>다음 단계

온톨로지 기반의 모델 개발 경로를 계속합니다. [모델 개발 경로에서 온톨로지 전략 사용](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).