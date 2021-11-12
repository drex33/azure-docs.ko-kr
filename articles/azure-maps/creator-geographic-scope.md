---
title: Azure Maps 작성자 서비스 지리적 범위
description: Azure Maps에서 Azure Maps 작성자 서비스의 지리적 매핑에 대해 알아보기
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: mvc, references_regions
ms.openlocfilehash: 9941db10efb39a5b68181224f06f1096bce5be7f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310498"
---
# <a name="creator-service-geographic-scope"></a>작성자 서비스 지리적 범위

Azure Maps 작성자는 지리적으로 범위가 지정된 서비스입니다. 작성자는 Azure 지역이 지정된 리소스 공급자 API를 제공하여 지리적 수준에서 배포된 작성자 데이터의 인스턴스를 만듭니다. Azure 지역에서 지리적 위치로의 매핑은 아래 표에 설명된 대로 백그라운드에서 수행합니다. Azure 지역 및 지리적 위치에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies)을 참조하세요.

## <a name="data-locations"></a>데이터 위치

재해 복구 및 고가용성을 위해 Microsoft는 동일한 지리적 영역 내의 다른 지역에만 고객 데이터를 복제할 수 있습니다. 예를 들어 서유럽의 데이터는 미국이 아닌 북유럽에 복제될 수 있습니다.  Microsoft는 고객이 선택한 지리적 위치에 관계없이 고객 또는 최종 사용자가 Azure Maps API를 통해 고객 데이터에 액세스할 수 있는 위치를 제어하거나 제한하지 않습니다.  

## <a name="geographic-and-regional-mapping"></a>지리적 및 지역 매핑

다음 표에서는 지리적 위치와 지원되는 Azure 지역 간의 매핑 및 해당 지역 API 엔드포인트에 대해 설명합니다. 예를 들어 작성자 계정이 미국 지리에 속하는 미국 서부 2 지역에 프로비전된 경우, 전환 서비스에 대한 모든 API 호출은 `us.atlas.microsoft.com/conversion/convert`에 대해 수행되어야 합니다.


| Azure 지리적 영역(지리적 위치) | Azure 데이터 센터(지역) | API 지리적 엔드포인트 |
|------------------------|----------------------|-------------|
| 유럽| 서유럽, 북유럽 | eu.atlas.microsoft.com |
|미국 | 미국 서부 2, 미국 동부 2 | us.atlas.microsoft.com |
