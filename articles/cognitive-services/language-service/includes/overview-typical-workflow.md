---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2502698a95671bf2b6ca9960a7f006a12d3a7ce8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029542"
---
## <a name="typical-workflow"></a>일반적인 워크플로

이 기능을 사용하려면 분석을 위해 데이터를 제출하고 애플리케이션에서 API 출력을 처리합니다. 분석은 데이터에 사용되는 모델에 대한 추가 사용자 지정 없이 있는 그대로 수행됩니다.

1. Azure Cognitive Service for Language에서 제공하는 기능에 대한 액세스 권한을 부여하는 Azure 언어 리소스를 만듭니다. API 요청을 인증하는 데 사용하는 암호(키라고 함) 및 엔드포인트 URL을 생성합니다.

2. C#, Java, JavaScript 및 Python에 대한 REST API 또는 클라이언트 라이브러리를 사용하여 요청을 만듭니다. 일괄 처리 요청을 통해 비동기 호출을 보내 여러 기능에 대한 API 요청을 단일 호출로 결합할 수도 있습니다.

3. 데이터가 포함된 비정형 원시 텍스트로 보냅니다. 키와 엔드포인트가 인증에 사용됩니다.

4. 응답을 스트리밍하거나 로컬로 저장합니다. 
