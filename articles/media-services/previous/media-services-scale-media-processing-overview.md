---
title: 미디어 예약 단위 개요 | Microsoft Docs
description: 이 문서에서는 Azure Media Services를 사용하여 미디어 처리를 스케일링하는 방법을 대략적으로 설명합니다.
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: 19454d90b55ab92c92d2e9132198f918f38c6e2c
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835385"
---
# <a name="media-reserved-units"></a>미디어 예약 단위

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

MRU(미디어 예약 단위)는 이전에 인코딩 동시성과 성능을 제어하는 데 사용되었습니다. MRU는 곧 사용 중단될 다음 레거시 미디어 프로세서에만 사용됩니다. 다음 레거시 프로세서에 대한 사용 중지 정보는 [Azure Media Services 레거시 구성 요소](legacy-components.md)를 참조하세요.

* 미디어 인코더 Premium 워크플로
* Media Indexer V1 및 V2

다른 모든 미디어 프로세서의 경우 시스템이 부하에 따라 자동으로 스케일 업 및 스케일 다운되므로 더 이상 MRU를 관리하거나 미디어 서비스 계정에 대한 할당량 증가를 요청할 필요가 없습니다. 또한 MRU를 사용할 때와 같거나 향상된 성능을 볼 수 있습니다.

## <a name="billing"></a>결제

이전에는 미디어 예약 단위에 대한 요금이 있었지만 2021년 4월 17일부터 미디어 예약 단위에 대한 구성이 있는 계정에 대한 요금은 더 이상 청구되지 않습니다.

## <a name="scaling-mrus"></a>MRU 크기 조정

호환성을 위해 계속해서 Azure Portal 또는 다음 API를 사용하여 MRU를 관리하고 크기 조정할 수 있습니다.

[.NET](media-services-dotnet-encoding-units.md)
[Portal](media-services-portal-scale-media-processing.md)
[REST](/rest/api/media/operations/encodingreservedunittype)
[Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
[PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

그러나 기본적으로 설정한 MRU 구성은 인코딩 동시성 또는 성능을 제어하는 데 사용되지 않습니다. 이 구성의 유일한 예외는 Media Encoder Premium Workflow 또는 Media Indexer V1과 같은 레거시 미디어 프로세서 중 하나로 인코딩하는 경우입니다.  
