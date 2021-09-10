---
title: 미디어 예약 단위 - Azure
description: 미디어 예약 단위를 사용하면 미디어 프로세스를 스케일링하고 미디어 처리 작업의 속도를 결정할 수 있습니다.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 3fc68505712bc5cae0defd216b30a3c5913c3c77
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866439"
---
# <a name="media-reserved-units"></a>미디어 예약 단위

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

MRU(미디어 예약 단위)는 이전에 Azure Media Services v2에서 인코딩 동시성 및 성능을 제어하는 데 사용되었습니다. 시스템이 부하에 따라 자동으로 스케일 업 및 다운되므로 더 이상 MRU를 관리하거나 미디어 서비스 계정에 대한 할당량 증가를 요청할 필요가 없습니다. 또한 MRU를 사용할 때와 같거나 향상된 성능을 볼 수 있습니다. 

2020-05-01 API 이전 버전을 사용하여 만든 계정이 있는 경우 MRU 관리를 위한 API에 계속 액세스할 수 있지만 설정한 MRU 구성은 인코딩 동시성 또는 성능을 제어하는 데 사용되지 않습니다. Azure Portal에서 MRU를 관리하는 옵션이 표시되지 않으면 2020-05-01 API 이상으로 만든 계정이 있는 것입니다. 

## <a name="billing"></a>결제

이전에는 미디어 예약 단위에 대한 요금이 있었지만 2021년 4월 17일부터 미디어 예약 단위에 대한 구성이 있는 계정에 대한 요금은 더 이상 청구되지 않습니다. 인코딩 작업 청구에 대한 자세한 내용은 [미디어 서비스로 동영상 및 오디오 인코딩](encoding-concept.md)을 참조하세요.

API(즉, v3 버전)의 **2020-05-01** 버전 또는 Azure Portal을 통해 생성된 계정의 경우 크기 조정 및 미디어 예약 단위가 더 이상 필요하지 않습니다. 이제 크기 조정은 서비스에서 내부적으로 자동으로 처리됩니다. 미디어 예약 단위는 더 이상 Azure Media Services 계정에 필요하지 않거나 지원되지 않습니다. 자세한 내용은 [미디어 예약 단위(레거시)](concept-media-reserved-units.md)를 참조하세요.

## <a name="see-also"></a>추가 정보

* [Media Services v2에서 v3으로 마이그레이션](migrate-v-2-v-3-migration-introduction.md)
* [CLI를 사용하여 미디어 예약 단위 크기 조정](media-reserved-units-cli-how-to.md)
