---
title: Azure Virtual Desktop의 서비스 경고 설정 - Azure
description: Azure Virtual Desktop에 대한 서비스 알림을 수신하도록 Azure Service Health를 설정하는 방법입니다.
author: Heidilohr
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: da6923d77c866d7123599dc9f242dd18f45964eb
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113033842"
---
# <a name="tutorial-set-up-service-alerts"></a>자습서: 서비스 경고 설정

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/set-up-service-alerts-2019.md)를 참조하세요.

Azure Service Health를 사용하여 Azure Virtual Desktop에 대한 서비스 문제 및 상태 권고를 모니터링할 수 있습니다. Azure Service Health는 이메일 또는 SMS와 같은 다양한 경고 유형을 사용하여 알려주므로 문제의 영향을 이해할 수 있고 문제가 해결되면 업데이트 사항을 알 수 있습니다. 또한 Azure Service Health는 가동 중지 시간을 완화하고 리소스의 가용성에 영향을 줄 수 있는 변경 내용 및 계획된 유지 관리를 준비하는 데 도움이 될 수 있습니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * 서비스 경고를 만들고 구성합니다.

Azure Service Health에 대한 자세한 내용은 [Azure Health 설명서](../service-health/index.yml)를 참조하세요.

## <a name="create-service-alerts"></a>서비스 경고 만들기

이 섹션에서는 Azure Service Health를 구성하는 방법 및 Azure Portal에서 액세스할 수 있는 알림을 설정하는 방법을 보여 줍니다. 다양한 유형의 경고를 적시에 알리도록 설정하고 예약할 수 있습니다.

### <a name="recommended-service-alerts"></a>권장 서비스 경고

다음 상태 이벤트 유형에 대한 서비스 경고를 만드는 것이 좋습니다.

- **서비스 문제:** Azure Virtual Desktop 테넌트를 관리하는 서비스 또는 기능을 사용하여 사용자의 연결에 영향을 미치는 주요 문제에 대한 알림을 받습니다.
- **상태 권고:** 주의가 필요한 알림을 받습니다. 다음은 이 알림 유형의 몇 가지 예입니다.
    - VM(Virtual Machines)이 열려 있는 포트 3389로 안전하게 구성되지 않음
    - 기능 사용 중단

### <a name="configure-service-alerts"></a>서비스 경고 구성

서비스 경고를 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Service Health** 를 선택합니다.
3. [서비스 알림에 대한 활동 로그 경고 만들기](../service-health/alerts-activity-log-service-notifications-portal.md?toc=%2fazure%2fservice-health%2ftoc.json)의 지침을 따라 경고 및 알림을 설정합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Service Health를 설정하고 사용하여 Azure Virtual Desktop에 대한 서비스 문제 및 상태 권고를 모니터링하는 방법을 알아보았습니다. Azure Virtual Desktop에 로그인하는 방법을 알아보려면 Azure Virtual Desktop에 연결하는 방법을 참고하세요.

> [!div class="nextstepaction"]
> [Windows 7 및 Windows 10에서 원격 데스크톱 클라이언트에 연결](./user-documentation/connect-windows-7-10.md)