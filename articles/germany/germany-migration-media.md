---
title: Azure 미디어 리소스를 Azure 독일에서 글로벌 Azure로 마이그레이션
description: 이 문서에서는 Azure 미디어 리소스를 Azure 독일에서 글로벌 Azure로 마이그레이션하는 방법을 설명합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: ac7cd5ca55525c11d2b985d931be24d1780899fb
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122528057"
---
# <a name="migrate-media-resources-to-global-azure"></a>미디어 리소스를 글로벌 Azure로 마이그레이션

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

이 문서에는 Azure 미디어 리소스를 Azure 독일에서 글로벌 Azure로 마이그레이션하는 데 도움이 되는 정보가 있습니다.

## <a name="media-services"></a>Media Services

Azure Media Services에서 해당 스토리지 계정과 모든 미디어 자산을 구성합니다. 먼저 글로벌 Azure에서 새 Media Services 계정을 만듭니다. 그런 다음, 해당 미디어 아티팩트를 다시 로드하고 새 Media Services 계정으로 인코딩과 스트리밍을 수행합니다.

추가 정보는 다음 항목을 참조하세요.

- [Media Services 자습서](../media-services/previous/index.yml)를 완료하여 새로운 지식을 습득합니다.
- [Media Services 개요](../media-services/previous/media-services-overview.md)를 검토합니다.
- [Media Services 계정을 만드는](../media-services/previous/media-services-portal-create-account.md) 방법을 알아봅니다.

## <a name="media-player"></a>Media Player

Azure Media Player에서 여러 엔드포인트를 선택할 수 있습니다. Azure 독일 엔드포인트나 글로벌 Azure 엔드포인트에서 콘텐츠를 스트리밍할 수 있습니다.

자세한 내용은 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html)를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스 마이그레이션을 위한 도구, 기술, 권장 사항에 대해 알아봅니다.

- [컴퓨팅](./germany-migration-compute.md)
- [네트워킹](./germany-migration-networking.md)
- [스토리지](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [데이터베이스](./germany-migration-databases.md)
- [분석](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [통합](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [보안](./germany-migration-security.md)
- [관리 도구](./germany-migration-management-tools.md)