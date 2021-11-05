---
title: 액세스 및 애플리케이션 제어 자습서 - Microsoft Defender for Cloud
description: 이 자습서에서는 Just-In-Time VM 액세스 정책과 애플리케이션 제어 정책을 구성하는 방법을 보여 줍니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.custom: mvc, ignite-fall-2021
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 9ebbe0e03f7bd701ce83e0edb816a7308f9ff766
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037362"
---
# <a name="tutorial-protect-your-resources-with-microsoft-defender-for-cloud"></a>자습서: Microsoft Defender for Cloud를 사용하여 리소스 보호

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud는 액세스 및 애플리케이션 제어를 통해 악성 활동을 차단하여 위협에 대한 노출을 제한합니다. JIT(Just-In-Time) VM(가상 머신) 액세스는 지속적인 VM 액세스를 거부할 수 있도록 하여 공격에 대한 노출을 줄입니다. 대신, 필요한 경우에만 VM에 대한 제어 및 감사된 액세스를 제공합니다. 적응형 애플리케이션 제어는 VM에서 실행할 수 있는 애플리케이션을 제어하여 맬웨어로부터 VM을 강화합니다. Defender for Cloud는 기계 학습을 통해 VM에서 실행 중인 프로세스를 분석하고 이러한 인텔리전스를 사용하여 허용 목록 규칙을 적용할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Just-In-Time VM 액세스 정책 구성
> * 애플리케이션 제어 정책 구성

## <a name="prerequisites"></a>사전 요구 사항
이 자습서에서 설명하는 기능을 단계별로 실행하려면 Defender for Cloud의 향상된 보안 기능을 사용하도록 설정해야 합니다. 평가판을 사용할 수 있습니다. 업그레이드하려면 [향상된 보호 사용](enable-enhanced-security.md)을 참조하세요.

## <a name="manage-vm-access"></a>VM 액세스 관리
JIT VM 액세스를 사용하면 Azure VM에 대한 인바운드 트래픽을 잠가 공격에 대한 노출을 줄이는 동시에 VM에 쉽게 액세스하여 필요할 때 연결할 수 있습니다.

관리 포트는 항상 열려 있을 필요가 없습니다. 예를 들어 관리 또는 유지 관리 작업을 수행하기 위해 VM에 연결되는 동안에만 열려 있어야 합니다. Just-In-Time을 사용하는 경우 Defender for Cloud는 관리 포트에 대한 액세스를 제한하는 NSG(네트워크 보안 그룹) 규칙을 사용하여 공격자의 대상이 되지 않도록 합니다.

[Just-In-Time 액세스를 사용하여 관리 포트 보호](just-in-time-access-usage.md)의 지침을 따르세요.

## <a name="harden-vms-against-malware"></a>맬웨어로부터 VM 강화
적응형 애플리케이션 제어는 구성된 리소스 그룹에서 실행할 수 있는 애플리케이션 집합을 정의하는 데 도움이 되며, 이로 인해 특히 맬웨어로부터 VM을 강화할 수 있는 혜택이 있습니다. Defender for Cloud는 기계 학습을 통해 VM에서 실행 중인 프로세스를 분석하고 이러한 인텔리전스를 사용하여 허용 목록 규칙을 적용할 수 있습니다.

[적응형 애플리케이션 제어를 사용하여 머신의 공격 표면 축소](adaptive-application-controls.md)의 지침을 따르세요.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음을 통해 위협에 대한 노출을 제한하는 방법을 살펴보았습니다.

> [!div class="checklist"]
> * Just-In-Time VM 액세스 정책을 구성하여 필요할 때만 VM에 대한 제어 및 감사된 액세스 제공
> * 적응형 애플리케이션 제어를 구성하여 VM에서 실행할 수 있는 애플리케이션 제어

보안 인시던트에 대응하는 방법을 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [자습서: 보안 인시던트에 대응](tutorial-security-incident.md)
