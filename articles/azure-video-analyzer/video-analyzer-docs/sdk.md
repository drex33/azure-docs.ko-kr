---
title: Azure Video Analyzer SDK 및 리소스
description: Azure Video Analyzer SDK에 대해 알아보기
author: bennage
ms.author: christb
ms.topic: reference
ms.date: 11/04/2021
ms.openlocfilehash: bc85e9b796f948d9fdee5a96555590084dff215c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562051"
---
# <a name="azure-video-analyzer-sdks"></a>Azure Video Analyzer SDK

Azure Video Analyzer에는 두 개의 SDK 그룹이 포함되어 있습니다. 관리 SDK는 Azure 리소스를 관리하는 데 사용되며 클라이언트 SDK는 에지 모듈과 상호 작용하는 데 사용됩니다.

## <a name="management-sdks"></a>관리 SDK

관리 SDK를 사용하면 Azure Resource Manager에 의해 노출되는 리소스와 상호 작용할 수 있습니다. Video Analyzer 계정을 만들고, 에지 모듈의 프로비저닝 토큰을 생성하고, 비디오에 대한 액세스 정책을 관리할 수 있습니다. SDK는 기본 [REST API](/rest/api/videoanalyzer/?branch=video)를 기반으로 빌드됩니다.

지원되는 플랫폼은 다음과 같습니다.

- [.NET](https://aka.ms/ava/sdk/mgt/net)
- [Java](https://aka.ms/ava/sdk/mgt/java)
- [Python](https://aka.ms/ava/sdk/mgt/python)

## <a name="client-sdks"></a>클라이언트 SDK

클라이언트 SDK를 사용하면 Video Analyzer 에지 모듈의 [직접 메서드][docs-direct-methods]와 상호 작용할 수 있습니다. 이러한 SDK는 [Azure IoT Hub SDK][docs-iot-hub-sdks]와 함께 사용하도록 설계되었습니다. IoT Hub SDK를 사용하여 에지 모듈로 전송할 수 있는 직접 메서드를 나타내는 개체 구성을 지원합니다.

지원되는 플랫폼은 다음과 같습니다.

- [.NET](https://aka.ms/ava/sdk/client/net)
- [Python](https://aka.ms/ava/sdk/client/python)
- [Java](https://aka.ms/ava/sdk/client/java)

## <a name="see-also"></a>참고 항목

- [Azure Video Analyzer API](/rest/api/videoanalyzer/)를 참조할 수도 있습니다.

<!-- links -->
[docs-direct-methods]: direct-methods.md
[docs-iot-hub-sdks]: ../../iot-hub/iot-hub-devguide-sdks.md

[REST API]: https://aka.ms/ava/api/rest
