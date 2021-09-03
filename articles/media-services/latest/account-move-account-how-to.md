---
title: Azure Media Services v3 계정 관리
description: Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. 이 문서에서는 Azure Media Services v3 계정을 관리하는 방법을 설명합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: inhenkel
ms.openlocfilehash: cbda31a3aaaf741424db174aa6a676858b27a23c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471215"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Azure Media Services v3 계정 관리

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 스토리지 계정은 Media Services 계정에 연결됩니다. Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. 자세한 내용은 [스토리지 계정](storage-account-concept.md)을 참조하세요.

[!INCLUDE [account creation note](./includes/note-2020-05-01-account-creation.md)]

## <a name="media-services-account-names"></a>Media Services 계정 이름

Media Services 계정 이름은 공백 없이 모두 소문자로 이루어진 3-24자의 숫자 또는 문자여야 합니다. Media Services 계정 이름은 Azure 위치 내에서 고유해야 합니다.

Media Services 계정이 삭제되면 계정 이름은 1년 동안 예약됩니다. 계정이 삭제된 후 1년 동안 계정 이름은 원래 계정이 포함된 구독에 의해 동일한 Azure 위치에서만 재사용될 수 있습니다.

Media Services 계정 이름은 키 배달, 라이브 이벤트 및 스트리밍 엔드포인트 이름을 포함하여 DNS 이름에 사용됩니다. Media Services DNS 이름을 허용하도록 방화벽이나 프록시를 구성한 경우 Media Services 계정을 삭제한 후 1년 이내에 이러한 구성을 제거해야 합니다.

## <a name="moving-a-media-services-account-between-subscriptions"></a>구독 간에 Media Services 계정 이동

Media Services 계정을 새 구독으로 이동해야 하는 경우 먼저 Media Services 계정을 포함한 전체 리소스 그룹을 새 구독으로 이동해야 합니다. Azure Storage 계정, Azure CDN 프로필 등 연결된 모든 리소스를 이동해야 합니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요. Azure의 모든 리소스와 마찬가지로 리소스 그룹 이동은 완료하는 데 다소 시간이 걸릴 수 있습니다.

### <a name="considerations"></a>고려 사항

* 다른 구독으로 마이그레이션하기 전에 계정에 있는 모든 데이터의 백업을 만듭니다.
* 모든 스트리밍 엔드포인트와 라이브 스트리밍 리소스를 중지해야 합니다. 사용자는 리소스 그룹 이동 기간 동안 콘텐츠에 액세스할 수 없습니다. 

> [!IMPORTANT]
> 이동이 성공적으로 완료될 때까지 스트리밍 엔드포인트를 시작하지 마세요.

### <a name="troubleshoot"></a>문제 해결

리소스 그룹 이동 후에 Media Services 계정 또는 연결된 Azure Storage 계정이 "연결 끊김" 상태가 되면 스토리지 계정 키를 전환해 보세요. 스토리지 계정 키를 전환해도 Media Services 계정의 "연결 끊김" 상태가 해결되지 않으면 Media Services 계정의 "지원 + 문제 해결" 메뉴에서 새로운 지원 요청을 파일에 추가합니다.  

## <a name="next-steps"></a>다음 단계

[계정 만들기](./account-create-how-to.md)
