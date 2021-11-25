---
title: 매핑 데이터 흐름의 Flowlet 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics 파이프라인의 매핑 데이터 흐름 내에서 flowlet 변환을 실행하는 방법을 알아봅니다.
author: joshuha-msft
ms.author: joowen
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: 19feba2b0f98e09df8399f88328c61f033474c47
ms.sourcegitcommit: e9e332a512ed615a3c8ad5a11baa21649f14116d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133098193"
---
# <a name="flowlet-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 Flowlet 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

flowlet 변환을 사용하여 이전에 매핑 데이터 흐름 흐름 만들기를 실행합니다. flowlet에 대한 개요는 [매핑 데이터 흐름 | Flowlet을 참조하세요. Microsoft Docs](concepts-data-flow-flowlet.md)

> [!NOTE] 
> Azure Data Factory 및 Synapse Analytics 파이프라인의 flowlet 변환은 현재 공개 미리 보기로 제공됩니다.


## <a name="configuration"></a>Configuration

flowlet 변환에는 다음 구성 설정이 포함됩니다.

:::image type="content" source="media/data-flow-flowlet/flowlet-settings.png" alt-text="Flowlet 설정 구성을 보여주는 스크린샷.":::

### <a name="flowlet"></a>Flowlet

실행할 flowlet을 선택합니다. flowlet이 선택되면 매핑 탭에서 입력 열(있는 경우)을 매핑할 수 있습니다.

### <a name="mapping"></a>매핑

:::image type="content" source="media/data-flow-flowlet/flowlet-mapping.png" alt-text="flowlet 입력에 열을 매핑하는 방법을 보여 주는 스크린샷.":::

선택한 flowlet에 입력 열이 있는 경우 입력 스트림의 열을 flowlet의 예상 입력 열에 매핑할 수 있습니다. Flowlet에 데이터 흐름 열을 매핑하는 이 매핑을 통해 flowlet은 잠재적으로 많은 매핑 데이터 흐름에서 데이터 흐름 논리를 다시 사용할 수 있는 조각으로 사용할 수 있습니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
<transformation> ~> <transformationName>
<outputStream>
```

### <a name="example"></a>예제

```
source1 derive(Test = "test") ~> DerivedColumn1
DerivedColumn1 output() ~> output1 
```    

