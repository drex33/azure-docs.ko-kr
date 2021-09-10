---
title: Azure Portal을 사용하여 미디어 처리 크기 조정 | Microsoft Docs
description: 이 자습서에서는 Azure Portal을 사용하여 미디어 처리의 크기를 조정하는 단계를 안내합니다.
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1040ffa8bc3af3734a5c37961676b0914f72e991
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835874"
---
# <a name="change-the-reserved-unit-type"></a>예약 단위 유형 변경

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [포털](media-services-portal-scale-media-processing.md)
> * [REST (영문)](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>개요

기본값으로 미디어 예약 단위는 더 이상 사용할 필요가 없으며 Azure Media Services에서 지원하지 않습니다. 호환성을 위해, 현재 Azure Portal에는 MRU를 관리하고 크기를 조정할 수 있는 옵션이 있습니다. 그러나, 기본값으로 설정한 MRU 구성은 인코딩 동시성 또는 성능을 제어하는 데 사용되지 않습니다.

> [!IMPORTANT]
> 미디어 처리 크기 조정에 대해 자세히 알아보려면 이 [개요](media-services-scale-media-processing-overview.md) 항목을 참조하세요.

## <a name="scale-media-processing"></a>미디어 처리 크기 조정
>[!NOTE]
>MRU를 선택하면 Azure Media Services V3의 동시성 또는 성능에 영향을 주지 않습니다. 

예약 단위 유형 및 예약 단위 수를 변경하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. **설정** 창에서 **미디어 예약 단위** 를 선택합니다.
   
    선택한 예약 단위 유형의 예약 단위 수를 변경하려면 화면 위쪽에서 **미디어 예약 단위** 슬라이더를 사용합니다.
   
    **예약된 단위 유형** 을 변경하려면 **예약된 처리 단위의 속도** 표시줄을 클릭합니다. 그런 후, S1, S2 또는 S3 중에서 필요한 가격 책정 계층을 선택합니다.
   
3. 저장 단추를 눌러 변경 내용을 저장합니다.
   
    저장을 누르면 새 예약 단위가 할당됩니다.

## <a name="next-steps"></a>다음 단계
Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
