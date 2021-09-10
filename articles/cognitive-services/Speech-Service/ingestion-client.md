---
title: 수집 클라이언트 - Speech Service
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 고객이 Speech Service에 오디오 파일을 쉽고 빠르게 푸시할 수 있도록 하는, GitHub에 릴리스된 도구에 대해 설명합니다.
services: cognitive-services
author: panosperiorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 069c0b587fd3ff69ca8e883850d44b84ab12486f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536509"
---
# <a name="ingestion-client-for-the-speech-service"></a>Speech Service용 수집 클라이언트

수집 클라이언트는 고객이 개발 작업을 거의 또는 전혀 수행하지 않고도 Speech Service를 통해 오디오 파일을 빠르게 전사할 수 있도록 하는, [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/ingestion)에 릴리스된 도구입니다. 전사 요청을 서비스에 전달하기 위해 [REST API](https://azure.microsoft.com/services/functions/) 또는 [SDK](speech-sdk.md)를 서버리스 방식으로 사용하는 사용자 지정 [Azure Functions](rest-speech-to-text.md)에 전용 [Azure Storage](https://azure.microsoft.com/product-categories/storage/) 계정을 연결하여 작동합니다.  

## <a name="architecture"></a>아키텍처

이 도구는 사전 개발 투자 없이 음성 텍스트의 품질을 확인하려는 고객에게 도움이 됩니다. 도구는 몇 가지 리소스를 연결하여 전용 [Azure Storage 컨테이너](https://azure.microsoft.com/product-categories/storage/)에 저장되는 오디오 파일을 전사합니다.

내부적으로 도구는 V3.0 Batch API 또는 SDK를 사용하고, 모범 사례에 따라 스케일 업, 재시도, 장애 조치(failover)를 처리합니다. 다음 계통도는 리소스와 연결을 설명합니다.

:::image type="content" source="media/ingestion-client/architecture-1.png" alt-text="수집 클라이언트 아키텍처":::

[수집 클라이언트 시작 가이드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/ingestion/ingestion-client/Setup/guide.md)에서는 도구를 설정하고 사용하는 방법을 설명합니다.

> [!IMPORTANT]
> 가격 책정은 선택한 Azure 함수 SKU뿐만 아니라 작업 모드(일괄 처리 대 실시간)에 따라 달라집니다. 기본적으로 도구는 대용량을 처리하는 프리미엄 Azure 함수 SKU를 만듭니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/functions/) 페이지를 참조하세요.

Microsoft [Speech SDK](speech-sdk.md)와 [음성 텍스트 변환 REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) 모두 음성 텍스트를 얻는 데 사용될 수 있습니다. 이 결정은 가이드에 설명된 대로 전체 비용에 영향을 미칩니다. 

> [!TIP]
> 프로덕션에서 도구와 결과 솔루션을 사용하여 대용량 오디오를 처리할 수 있습니다.

## <a name="tool-customization"></a>도구 사용자 지정

이 도구는 고객에게 결과를 빠르게 표시하도록 빌드되었습니다. 원하는 SKU와 설정에 맞게 도구를 사용자 지정할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 SKU를 편집할 수 있으며, [코드 자체는 GitHub에서 확인할 수 있습니다](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch).

> [!NOTE]
> 보다 쉽게 비용을 파악하고 추적할 수 있도록 동일한 전용 리소스 그룹에 리소스를 만드는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [Speech Service 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [Speech SDK에 대한 자세한 정보](./speech-sdk.md)
* [Speech CLI 도구에 대한 자세한 정보](./spx-overview.md)
