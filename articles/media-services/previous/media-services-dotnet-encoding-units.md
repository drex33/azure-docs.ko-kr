---
title: 인코딩 단위를 추가하여 미디어 처리 크기 조정 - Azure | Microsoft Docs
description: 이 문서에서는 Azure Media Services .NET을 사용하여 인코딩 단위를 추가하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.reviewer: milangada
ms.custom: devx-track-csharp
ms.openlocfilehash: b7b7cce9fd33e14175e03486499ec93997617683
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835343"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>.NET SDK를 사용하여 인코딩 크기를 조정하는 방법

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [포털](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST (영문)](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## <a name="overview"></a>개요
> [!IMPORTANT]
> 기본적으로 미디어 예약 단위는 더 이상 사용할 필요가 없으며 Azure Media Services에서 지원하지 않습니다. 미디어 처리 크기 조정에 대해 자세히 알아보려면 해당 [개요](media-services-scale-media-processing-overview.md)를 검토하세요.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
