---
title: Azure IoT Central에서 데이터 내보내기 문제 해결 | Microsoft Docs
description: IoT Central에서 데이터 내보내기 관련 문제 해결
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/26/2021
ms.topic: troubleshooting
ms.service: iot-central
ms.openlocfilehash: 0fd283c1f3740e3e06f7a41cc66874596159e8f1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103735"
---
# <a name="troubleshoot-issues-with-data-exports-from-your-azure-iot-central-application"></a>Azure IoT 중앙 응용 프로그램에서 데이터 내보내기 관련 문제 해결

이 문서는 IoT Central 응용 프로그램이 의도 한 대상에 도달 하지 않거나 올바른 형식으로 도착 하지 않은 데이터를 찾는 데 도움이 됩니다.

## <a name="managed-identity-issues"></a>관리 ID 문제

관리 되는 id를 사용 하 여 내보내기 대상에 대 한 연결 권한을 부여 합니다. 데이터가 내보내기 대상에 도착 하지 않습니다.

내보내기 대상을 구성 하거나 사용 하도록 설정 하기 전에 다음 단계를 완료 해야 합니다.

- 응용 프로그램에 관리 되는 id를 사용 하도록 설정 합니다.
- 관리 id에 대 한 사용 권한을 구성 합니다.
- 가상 네트워크, 개인 끝점 및 방화벽 정책을 구성 합니다.

자세히 알아보려면 [데이터 내보내기](howto-export-data.md?tabs=managed-identity)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

도움이 더 필요하면 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)의 Azure 전문가에게 문의하세요. 또는 [Azure 지원 티켓](https://portal.azure.com/#create/Microsoft.Support)을 제출할 수 있습니다.

자세한 내용은 [Azure IoT 지원 및 도움말 옵션](../../iot-fundamentals/iot-support-help.md)을 참조하세요.
