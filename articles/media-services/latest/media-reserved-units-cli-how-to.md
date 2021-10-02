---
title: 미디어 예약 단위(MRU) 스케일링 CLI
description: 이 토픽에서는 CLI를 사용하여 Azure Media Services에서 미디어 처리 크기를 조정하는 방법을 보여줍니다.
services: media-services
author: jiayali-ms
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 2525417516691fa4ffb6d681fc23b394f4588649
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129364103"
---
# <a name="how-to-scale-media-reserved-units-legacy"></a>미디어 예약 단위를 스케일링하는 방법(레거시)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 더 빠른 인코딩을 위해 MRU(미디어 예약 단위)를 스케일링하는 방법을 보여 줍니다.

> [!WARNING]
> 이 명령은 2020-05-01 이상 버전의 API 이상에서 만든 Media Services 계정에 대해 더 이상 작동 하지 않습니다. 계정에서는 부하에 따라 시스템이 자동으로 스케일 업/다운되기 때문에 미디어 예약 단위가 더 이상 필요하지 않습니다. Azure Portal에서 MRU를 관리하는 옵션이 표시되지 않으면 2020-05-01 API 이상으로 만든 계정을 사용하고 있는 것입니다.
> 이 문서의 목적은 MRU를 사용하는 레거시 프로세스를 문서화하는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

[Media Services 계정 만들기](./account-create-how-to.md)

[미디어 예약 단위](concept-media-reserved-units.md)를 이해합니다.

## <a name="scale-media-reserved-units-with-cli"></a>CLI를 사용하여 미디어 예약 단위 크기 조정

`mru` 명령을 실행합니다.

다음 [az ams account mru](/cli/azure/ams/account/mru) 명령은 **count** 및 **type** 매개 변수를 사용하여 "amsaccount" 계정에서 미디어 예약 단위를 설정합니다.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>결제

 이전에는 미디어 예약 단위에 대한 요금이 있었지만 2021년 4월 17일부터 미디어 예약 단위에 대한 구성이 있는 계정에 대한 요금은 더 이상 청구되지 않습니다.

## <a name="see-also"></a>참고 항목

* [Media Services v2에서 v3으로 마이그레이션](migrate-v-2-v-3-migration-introduction.md)
