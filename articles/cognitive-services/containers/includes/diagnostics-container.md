---
titleSuffix: Cognitive Services
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2021
ms.author: aahi
ms.openlocfilehash: 2d26c560cd05aad4962d318e8c2c798c576db811
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "131011659"
---
Cognitive Services 컨테이너를 실행하는 데 문제가 있는 경우 Microsoft 진단 컨테이너를 사용해 볼 수 있습니다. 이 컨테이너를 사용하여 Cognitive Services 컨테이너가 예상대로 작동하지 못하게 할 수 있는 배포 환경의 일반적인 오류를 진단할 수 있습니다.

컨테이너를 가져오려면 다음 Docker `pull` 명령을 사용합니다.

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/diagnostic
```

그런 다음 컨테이너를 실행하고 `{ENDPOINT_URI}`를 엔드포인트로, `{API_KEY}`를 리소스에 대한 키로 바꿉니다.

```bash
docker run --rm mcr.microsoft.com/azure-cognitive-services/diagnostic \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

컨테이너는 청구 엔드포인트에 대한 네트워크 연결을 테스트 합니다.