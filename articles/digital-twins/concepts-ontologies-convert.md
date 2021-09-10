---
title: 온톨로지 변환
titleSuffix: Azure Digital Twins
description: Azure Digital Twins에 대해 업계 표준 모델을 DTDL로 변환하는 프로세스 이해
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d95e9e6ed85e170efeb570fa46f634a9071c687c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772396"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Azure Digital Twins에 대해 업계 표준 온톨로지를 DTDL로 변환

대부분의 [온톨로지](concepts-ontologies.md)는 [OWL](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)및 [RDFS](https://www.w3.org/2001/sw/wiki/RDFS)와 같은 의미 체계 웹 표준을 기반으로 합니다. 

Azure Digital Twins에서 모델을 사용하려면 DTDL 형식이어야 합니다. 이 문서에서는 Azure Digital Twins에서 사용할 수 있도록 RDF 기반 모델을 DTDL로 변환하기 위한 **변환 패턴** 형식의 일반 디자인 지침에 대해 설명합니다. 

이 문서에는 빌딩 업계의 다른 스키마에 대해 확장될 수 있는 RDF 및 OWL 변환기에 대한 [샘플 변환기 코드](#converter-samples)도 포함되어 있습니다.

## <a name="conversion-pattern"></a>변환 패턴

RDF 기반 모델을 DTDL로 변환할 때 사용할 수 있는 여러 타사 라이브러리가 있습니다. 이러한 라이브러리 중 일부를 사용하여 그래프에 모델 파일을 로드할 수 있습니다. 그래프를 반복하여 특정 RDFS 및 OWL 구문을 찾고 이를 DTDL로 변환할 수 있습니다.   

다음 표는 RDFS 및 OWL 구문을 DTDL에 매핑할 수 있는 방법의 예제입니다. 

| RDFS/OWL 개념 | RDFS/OWL 구문 | DTDL 개념 | DTDL 구문 |
| --- | --- | --- | --- |
| 클래스 | `owl:Class`<br>IRI 접미사<br>``rdfs:label``<br>``rdfs:comment`` | 인터페이스 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| 를 서브클래싱합니다. | `owl:Class`<br>IRI 접미사<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | 인터페이스 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Datatype 속성 | `owl:DatatypeProperty`<br>`rdfs:label` 또는 `INode`<br>`rdfs:label`<br>`rdfs:range` | 인터페이스 속성 | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| 개체 속성 | `owl:ObjectProperty`<br>`rdfs:label` 또는 `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | 관계 | `type:Relationship`<br>`name`<br>`target`(또는 `rdfs:range`가 아닌 경우 생략됨)<br>`comment`<br>`displayName`<br>

다음 C# 코드 조각에서는 [dotNetRDF](https://www.dotnetrdf.org/) 라이브러리를 사용하여 RDF 모델 파일이 그래프로 로드되고 DTDL로 변환되는 방법을 보여 줍니다. 

:::code language="csharp" source="~/digital-twins-docs-samples/other/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>변환기 샘플

### <a name="rdf-converter-application"></a>RDF 변환기 애플리케이션 

RDF 기반 모델 파일을 [DTDL(버전 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)로 변환하여 Azure Digital Twins 서비스에서 사용할 수 있는 샘플 애플리케이션이 있습니다. 이는 [Brick](https://brickschema.org/ontology/) 스키마에 대해 유효성이 검사되었으며, 빌딩 업계의 다른 스키마(예: [빌딩 토폴로지 온톨로지(BOT)](https://w3c-lbd-cg.github.io/bot/), [의미 체계 센서 네트워크](https://www.w3.org/TR/vocab-ssn/) 또는 [buildingSmart IFC(Industry Foundation Classes)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/))에 대해 확장할 수 있습니다.

이 샘플은 [RdfToDtdlConverter라는 .NET Core 명령줄 애플리케이션](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)입니다.

머신에 코드를 다운로드하려면 샘플 페이지의 제목 아래에 있는 **코드 찾아보기** 단추를 선택하여 샘플에 대한 GitHub 리포지토리로 이동합니다. **코드** 단추와 **ZIP 다운로드** 를 선택하여 샘플을 *RdfToDtdlConverter-main.zip* 이라는 .zip 파일로 다운로드합니다. 그러면 파일의 압축을 풀고 코드를 탐색할 수 있습니다.

:::image type="content" source="media/concepts-ontologies-convert/download-repo-zip.png" alt-text="GitHub의 RdfToDtdlConverter 리포지토리 스크린샷. 코드 단추를 선택하면 ZIP 다운로드 단추가 강조 표시된 대화 상자가 생성됩니다." lightbox="media/concepts-ontologies-convert/download-repo-zip.png":::

이 샘플을 사용하여 컨텍스트에서 변환 패턴을 확인하고, 고유한 특정 요구 사항에 따라 모델 변환을 수행하는 사용자 고유의 애플리케이션에 대한 빌딩 블록으로 사용할 수 있습니다.

### <a name="owl2dtdl-converter"></a>OWL2DTDL 변환기 

[OWL2DTDL 변환기](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL)는 OWL 온톨로지를 Azure Digital Twins 서비스와 함께 사용할 수 있는 DTDL 인터페이스 선언 집합으로 변환하는 샘플입니다. 또한 `owl:imports` 선언을 통해 다른 온톨로지를 재사용하는 하나의 루트 온톨로지로 구성된 온톨로지 네트워크에서도 작동합니다.

이 변환기는 [부동산 코어 온톨로지](https://doc.realestatecore.io/3.1/full.html)를 DTDL로 변환하는 데 사용되었으며, 모든 OWL 기반 온톨로지에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계 

* 사양에 맞게 업계 표준 온톨로지를 확장하는 것에 대해 자세히 알아봅니다([업계 온톨로지 확장](concepts-ontologies-extend.md)).

* 또는 온톨로지 기반의 모델 개발을 위해 경로를 계속합니다([모델 개발 경로에서 온톨로지 전략 사용](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)).