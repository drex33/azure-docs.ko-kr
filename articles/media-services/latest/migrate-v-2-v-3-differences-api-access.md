---
title: Media Services V2 대 v3 API 액세스
description: 이 문서에서는 Azure Media Services V2와 V3 간의 API 액세스 차이점에 대해 설명합니다.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 594f4d68669b216e4606806a9ac3ef9a5b2f0b5c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358300"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Azure Media Services V2에서 v3 API로의 API 액세스 차이점

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2단계](./media/migration-guide/steps-2.svg)

이 문서에서는 Azure Media Services V2와 V3 간의 API 액세스 차이점에 대해 설명합니다.

## <a name="api-access"></a>API 액세스

모든 Media Services 계정은 V3 API에 액세스할 수 있습니다. 그러나 기존 V2 계정에 업데이트된 코드를 적용하기 전에 최신 계정으로 마이그레이션 개발을 수행하는 것이 좋습니다. V3 엔터티는 이전 버전에서 V2와 호환되지 않기 때문입니다. 자산과 같은 일부 V2 엔터티는 V3과의 이전 버전과 호환됩니다.
V2 및 V3 API를 혼합하지 않은 경우에도 기존 계정을 계속 사용할 수 있습니다. 그런 다음, V2로 돌아가서 다시 시도하지 않는 것이 좋습니다.

2024에서 사용이 중지될 때까지 V2 API에 대한 액세스를 사용할 수 있습니다.

## <a name="create-a-v3-account"></a>V3 계정 생성

마이그레이션하는 동안 계속 V2에 액세스할 수 있는 V3 계정을 만들 수 있습니다.  계정을 만드는 작업은 다음 과정을 통해 수행할 수 있습니다.

- REST API 및 이전 버전
- 포털에서 확인란을 선택합니다.

> [!div class="mx-imgBorder"]
> [ ![포털에서 계정 만들기](./media/migration-guide/v-3-v-2-access-account-creation-small.png) ](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

모든 .NET, CLI 및 기타 SDK는 최신 2020-05-01 API를 대상으로 하므로 이전 API 버전을 찾거나 구성하세요.

> [!NOTE]
> 2020-05-01 이상 API를 사용 하 여 만든 새 계정은 V2 Api를 사용할 수 없습니다.
