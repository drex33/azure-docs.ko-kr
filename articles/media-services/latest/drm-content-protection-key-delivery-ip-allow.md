---
title: IP 허용 목록을 사용하여 DRM 라이선스 및 AES 키 배달에 대한 액세스 제한
description: IP 허용 목록을 사용하여 DRM 및 AES 키에 대한 액세스를 제한하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2021
ms.author: johndeu
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: cc0a35c196956d1f6afaf3d3fb27b9f428bb9f6d
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112204807"
---
# <a name="restrict-access-to-drm-license-and-aes-key-delivery-using-ip-allowlists"></a>IP 허용 목록을 사용하여 DRM 라이선스 및 AES 키 배달에 대한 액세스 제한

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services의 [콘텐츠 보호](./drm-content-protection-concept.md) 및 DRM 기능을 사용하여 미디어를 보호하는 경우 라이선스 또는 키 요청 배달을 네트워크에서 클라이언트 디바이스의 특정 IP 범위로 제한해야 하는 시나리오가 발생할 수 있습니다. 콘텐츠 재생 및 키 배달을 제한하기 위해 키 배달에 IP 허용 목록을 사용할 수 있습니다.

또한 허용 목록을 사용하여 키 배달 트래픽에 대한 모든 공용 인터넷 액세스를 완전히 차단하고 개인 네트워크 엔드포인트의 트래픽만 허용할 수 있습니다.

키 배달용 IP 허용 목록은 제공된 IP 허용 목록 범위 내에 있는 클라이언트에 대한 DRM 라이선스 및 AES-128 키의 배달을 제한합니다.

## <a name="setting-the-allowlist-for-key-delivery"></a>키 배달에 대한 허용 목록 설정

키 배달 IP 허용 목록의 설정은 Media Services 계정 리소스에 있습니다. 새 Media Services 계정을 만들 때 [Media Services 계정 리소스](/rest/api/media/mediaservices/create-or-update)의 **KeyDelivery** 속성을 통해 허용되는 IP 범위를 제한할 수 있습니다.

**defaultAction** 속성은 "허용" 또는 "거부"로 설정하여 라이선스 및 키를 허용 목록 범위의 클라이언트로 배달하는 것을 제어할 수 있습니다.

**ipAllowList** 속성은 [CIDR 표기법](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation)을 사용하는 단일 IPv4 주소 및/또는 IPv4 범위의 배열입니다.

## <a name="setting-the-allowlist-in-the-portal"></a>포털에서 허용 목록 설정

Azure Portal은 키 배달용 IP 허용 목록을 구성하고 업데이트하기 위한 방법을 제공합니다.  Media Services 계정으로 이동하여 **설정** 에서 **키 배달** 메뉴에 액세스합니다.

## <a name="next-steps"></a>다음 단계

- [계정 만들기](./account-create-how-to.md)
- [DRM 및 콘텐츠 보호 개요](./drm-content-protection-concept.md)
