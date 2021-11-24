---
title: '빠른 시작: IoT 솔루션에 Azure 리소스 추가'
description: 이 빠른 시작에서는 Microsoft Defender for IoT를 사용하여 엔드투엔드 IoT 솔루션을 구성하는 방법을 알아봅니다.
ms.topic: quickstart
ms.date: 11/09/2021
ms.custom: mode-other
ms.openlocfilehash: 1829d7ef152880c36f83a63c421b9ccc38fe462c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133069960"
---
# <a name="quickstart-configure-your-microsoft-defender-for-iot-solution"></a>빠른 시작: Microsoft Defender for IoT 솔루션 구성

이 문서에서는 처음으로 Defender for IoT를 사용하여 IoT 보안 솔루션을 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 없음

## <a name="what-is-defender-for-iot"></a>Defender for IoT란?

Defender for IoT는 Azure 기반 IoT 솔루션에 포괄적인 엔드투엔드 보안 기능을 제공합니다.

Defender for IoT를 사용하면 하나의 대시보드에서 전체 IoT 솔루션을 모니터링하여 Azure의 IoT 디바이스, IoT 플랫폼 및 백 엔드 리소스를 모두 검색할 수 있습니다.

IoT Hub에서 Defender for IoT를 사용하면 Defender for IoT는 자동으로 다른 Azure 서비스를 식별하고 IoT 솔루션과 관련된 관련 서비스에 연결됩니다.

또한 IoT 솔루션의 일부인 다른 Azure 리소스 그룹을 선택할 수 있습니다.

선택하는 경우 전체 구독, 리소스 그룹 또는 단일 리소스를 추가할 수 있습니다.

모든 리소스 관계가 정의되면 Defender for IoT는 Defender for Cloud를 사용하여 이러한 리소스의 보안 권장 사항과 경고를 제공합니다.

## <a name="add-azure-resources-to-your-iot-solution"></a>IoT 솔루션에 Azure 리소스 추가

**새 리소스를 IoT 솔루션에 추가하려면 다음을 수행합니다**.

1. Azure Portal에서 **IoT Hub** 를 검색하고 선택합니다.

1. 보안 섹션에서 **설정** > **모니터링되는 리소스** 를 선택합니다.

1. **편집** 을 선택하고 IoT 솔루션에 속하는 모니터링되는 리소스를 선택합니다.

1. **추가** 를 선택합니다.

이제 새 리소스 그룹이 IoT 솔루션에 추가됩니다.

이제 Defender for IoT에서 새로 추가된 리소스 그룹을 모니터링하고 IoT 솔루션의 일부로 관련 보안 권장 사항과 경고를 표시합니다.

## <a name="next-steps"></a>다음 단계

Defender-IoT-micro-agents를 만드는 방법을 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Defender-IoT-micro-agents 만들기](quickstart-create-security-twin.md)
