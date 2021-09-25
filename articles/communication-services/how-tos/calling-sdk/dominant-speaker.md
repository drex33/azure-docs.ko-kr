---
title: 활성 화자 받기
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK를 사용하여 통화에서 활성 화자를 렌더링합니다.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: cb2391db2f24eaf4dca4e0463ab508b27361fae0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634293"
---
# <a name="get-active-speakers-within-a-call"></a>통화 내에서 활성 화자 받기

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

활성 통화 중에 다르게 렌더링하거나 표시하기 위해 활성 화자 목록을 얻을 수 있습니다. 방법은 다음과 같습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../quickstarts/create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 사용자 액세스 토큰입니다. 자세한 내용은 [액세스 토큰 만들기 및 관리](../../quickstarts/access-tokens.md)를 참조하세요.
- 선택 사항: 빠른 시작 완료를 완료하여 [애플리케이션에 음성 통화 추가](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

[!INCLUDE [Dominant Speaker JavaScript](./includes/dominant-speaker/dominant-speaker-web.md)]

## <a name="next-steps"></a>다음 단계
- [비디오를 관리하는 방법 알아보기](./manage-video.md)
- [호출을 관리하는 방법 알아보기](./manage-calls.md)
- [호출을 기록하는 방법 알아보기](./record-calls.md)