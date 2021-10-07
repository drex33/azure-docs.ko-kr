---
title: Azure Media Player 개요
description: Azure Media Player에 대해 자세히 알아봅니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: faf5bc8a96cb3781ed1d48e9d7f0d1fd7cbc386e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679582"
---
# <a name="azure-media-player-overview"></a>Azure Media Player 개요 #

Azure Media Player는 다양한 브라우저 및 디바이스의 [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/)에서 미디어 콘텐츠를 재생하는 웹 비디오 플레이어입니다. Azure Media Player는 풍부해진 적응 스트리밍 환경을 제공할 수 있는 HTML5, MSE(미디어 원본 확장) 및 EME(암호화된 미디어 확장)와 같은 업계 표준을 사용합니다.  이러한 표준을 디바이스 또는 브라우저에서 사용할 수 없는 경우, Azure Media Player는 Flash 및 Silverlight를 대체 기술로 사용합니다. 사용되는 재생 기술에 관계없이 개발자는 API에 액세스할 수 있는 통합 JavaScript 인터페이스를 사용하여 Azure Media Services에서 제공하는 콘텐츠를 추가 노력 없이 광범위한 디바이스 및 브라우저에서 재생할 수 있습니다.

Microsoft Azure Media Services에서 컨텐츠를 DASH, 부드러운 스트리밍 및 HLS 스트리밍 형식으로 제공하여 재생할 수 있습니다. Azure Media Player는 이러한 다양한 형식을 고려하여 플랫폼/브라우저 기능에 따라 최상의 링크를 자동으로 재생합니다. Microsoft Azure Media Services는 일반적인 암호화(PlayReady 또는 Widevine) 또는 AES 128 비트 봉투 암호화로 자산의 동적 암호화를 제공합니다. 적절하게 구성된 경우 Azure Media Player를 사용하여 PlayReady의 및 AES 128 비트 암호화된 콘텐츠를 암호 해독할 수 있습니다.  플레이어를 구성하는 방법을 이해하려면 [보호된 콘텐츠](azure-media-player-protected-content.md) 섹션을 참조하세요.

특정 문제, 질문이 있거나 버그를 찾은 경우 클라이언트 재생 범주를 사용하여 [지원 티켓을 제출](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)하세요.

> [!NOTE]
> Azure Media Player는 Azure Media Services의 미디어 스트림만 지원합니다.

## <a name="license"></a>License ##

Azure Media Player는 라이선스가 부여되었으며 Azure Media Player에 대한 Microsoft 소프트웨어 사용 약관에 설명된 조건에 따릅니다. 전체 사용 약관은 [라이선스 파일](/legal/azure-media-player/azure-media-player-license)을 참조하세요. 자세한 내용은 [개인정보처리방침](https://www.microsoft.com/en-us/privacystatement/default.aspx)을 참조하세요.

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)
