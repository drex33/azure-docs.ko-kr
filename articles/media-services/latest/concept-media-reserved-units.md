---
title: 미디어 예약 단위 - Azure
description: 미디어 예약 단위를 사용하면 미디어 프로세스를 스케일링하고 미디어 처리 작업의 속도를 결정할 수 있습니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 44205bc628a839dd28cd574dbd19a22e9856d999
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114719408"
---
# <a name="media-reserved-units"></a>미디어 예약 단위

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services를 사용하면 MRU(미디어 예약 단위)를 관리하여 미디어 처리를 스케일링할 수 있습니다. MRU는 미디어 인코딩에 필요한 추가 계산 용량을 제공합니다. MRU 수는 미디어 작업이 처리되는 속도와 계정에서 동시에 처리할 수 있는 미디어 작업의 수를 결정합니다. 예를 들어 계정에 5개의 MRU가 있고 처리할 작업들이 있는 경우 5개의 미디어 작업이 동시에 실행될 수 있습니다. 나머지 작업은 큐에 대기되며 실행 중인 작업이 완료되면 순차적으로 처리하도록 선택될 수 있습니다. 프로비저닝하는 각 MRU는 용량 예약을 생성하지만 전용 리소스를 제공하지는 않습니다. 요구가 매우 많은 때에는 모든 MRU의 처리를 즉시 시작하지 않을 수 있습니다.

작업은 자산에 대한 개별 작업(예: 적응 스트리밍 인코딩)입니다. 작업(job)을 제출할 때 Media Services는 별도의 MRU에 연결될 개별 작업, 즉 작업(task)으로 작업을 분리합니다.

## <a name="choosing-between-different-reserved-unit-types"></a>여러 예약 단위 유형 중에서 선택

다음 표를 참조하여 여러 인코딩 속도 중에서 선택할 때 적절한 속도를 결정할 수 있습니다.  사용된 MRU에 따라 1080p 비디오에 대해 7분의 인코딩 기간을 보여 줍니다.

|RU 유형|시나리오|7분 1080p 비디오에 대한 결과 예 |
|---|---|---|
| **S1**|단일 비트 전송률 인코딩 <br/>SD 이하 해상도의 파일, 시간이 중요하지 않은 인코딩, 저가형 비디오.|“H264 단일 비트 전송률 SD 16x9”를 사용하여 단일 비트 전송률 SD 해상도 MP4 파일로 인코딩하는 데 약 7분이 걸립니다.|
| **S2**|단일 비트 전송률 및 다중 비트 전송률 인코딩.<br/>SD 및 HD 인코딩에서 모두 일반적으로 사용됨|“H264 단일 비트 전송률 720p” 사전 설정을 사용하여 인코딩하는 데 약 6분이 걸립니다.<br/><br/>“H264 다중 비트 전송률 720p” 사전 설정을 사용하여 인코딩하는 데 약 12분이 걸립니다.|
| **S3**|단일 비트 전송률 및 다중 비트 전송률 인코딩.<br/>Full HD 및 4K 해상도 비디오. 시간이 중요하며 소요 시간이 짧은 인코딩|“H264 단일 비트 전송률 1080p” 사전 설정을 사용하여 인코딩하는 데 약 3분이 걸립니다.<br/><br/>"H264 다중 비트 전송률 1080p" 미리 설정을 사용하여 인코딩할 때 약 8분이 걸립니다.|

> [!NOTE]
> 계정에 대한 MRU를 프로비저닝하지 않으면 미디어 작업은 S1 MRU의 성능으로 처리되고 작업은 순차적으로 선택됩니다. 처리 용량이 예약되어 있지 않으므로 한 작업을 완료하고 그 다음 작업을 시작하는 데 걸리는 대기 시간은 시스템의 리소스 가용성에 따라 달라집니다.

## <a name="considerations"></a>고려 사항

* Media Services v3 또는 Azure Video Analyzer for Media에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 10개의 S3 단위를 사용하여 계정을 프로비저닝하는 것이 좋습니다. 10개가 넘는 S3 MRU가 필요한 경우 [Azure Portal](https://portal.azure.com/)을 사용하여 지원 티켓을 엽니다.
* MRU가 없는 인코딩 작업의 경우 작업이 대기 상태에 소요되는 시간에 대한 상한이 없으며 한 번에 최대 하나의 작업만 실행됩니다.

## <a name="billing"></a>결제

실행 중인 작업이 있는지 여부에 관계 없이 계정에서 미디어 예약 단위가 프로비저닝되는 시간(분)을 기준으로 요금이 청구됩니다. 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 페이지에서 FAQ 섹션을 참조하세요.

## <a name="next-step"></a>다음 단계
[CLI를 사용하여 미디어 예약 단위 스케일링](media-reserved-units-cli-how-to.md)
[비디오 분석](analyze-videos-tutorial.md)

## <a name="see-also"></a>참고 항목

* [할당량 및 제한](limits-quotas-constraints-reference.md)
