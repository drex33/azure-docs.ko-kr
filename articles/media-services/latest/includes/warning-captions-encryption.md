---
author: johndeu
ms.service: media-services
ms.topic: include
ms.date: 10/09/21
ms.author: johndeu
ms.custom: concept, captions, encryption
ms.openlocfilehash: b7635c9a0b1340f4b0d1b21311718f8b2165f6af
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "133071492"
---
## <a name="security-considerations-for-closed-captions-subtitles-and-timed-metadata-delivery"></a>선택 자막, 자막 및 시간 제한 메타데이터 전달에 대한 보안 고려 사항

Azure Media Services의 동적 암호화 및 DRM 기능에는 라이브 전사, 캡션, 자막 또는 시간 제한 메타데이터를 포함한 콘텐츠 전송을 보호하려고 할 때 고려해야 할 제한 사항이 있습니다.
PlayReady, FairPlay 및 Widevine을 포함한 DRM 하위 시스템은 텍스트 트랙의 암호화 및 라이선스를 지원하지 않습니다.  텍스트 트랙에 대한 DRM 암호화가 없으면 라이브 전사, 수동 삽입 캡션, 업로드된 자막 또는 시간 제한 메타데이터 신호(별도 트랙으로 삽입될 수 있음)의 콘텐츠를 보호하는 기능이 제한됩니다.

캡션, 자막 또는 시간 제한 메타데이터 트랙을 보호하려면 다음 지침 중 하나를 따르는 것이 좋습니다.

1. AES-128 키 암호화 지우기를 사용합니다.  AES-128 키 암호화 지우기를 사용하도록 설정하면 오디오 및 비디오 세그먼트와 동일한 암호화 패턴을 따르는 전체 "봉투" 암호화 기술을 사용하여 텍스트 트랙을 암호화하도록 구성할 수 있습니다. 그런 다음, 인증된 JWT 토큰을 사용하여 Media Services Key Delivery 서비스에서 암호 해독 키를 요청한 후 클라이언트 애플리케이션에서 이러한 세그먼트를 암호 해독할 수 있습니다.  이 메서드는 Azure Media Player에서 지원되지만 모든 디바이스에서 지원되지 않을 수 있으며 모든 플랫폼에서 성공하도록 일부 클라이언트 쪽 개발 작업이 필요할 수 있습니다.
1. CDN 토큰 인증을 사용하여 CDN 포털에서 지역, IP 또는 기타 구성 가능한 설정으로 제한된 짧은 형식의 토큰화된 URL로 전달되는 텍스트(자막, 캡션, 메타데이터) 트랙을 보호합니다.  Verizon Premium CDN 또는 Media Services 스트리밍 엔드포인트에 연결하도록 구성된 다른 타사 CDN을 사용하여 CDN 보안 기능을 사용하도록 설정합니다.

> [!WARNING]
> 위의 지침 중 하나를 따르지 않을 경우 자막, 캡션 또는 시간 제한 메타데이터 텍스트는 원하는 클라이언트 전달 경로 외부에서 가로채거나 공유할 수 있는 암호화되지 않은 콘텐츠로 액세스할 수 있습니다. 이로 인해 정보가 유출될 수 있습니다. 안전한 전송 시나리오에서 캡션 또는 자막의 콘텐츠가 유출되는 것이 우려되는 경우 콘텐츠 전송 보안을 위한 위의 지침에 대한 자세한 내용은 Media Services 지원 팀에 문의하세요.