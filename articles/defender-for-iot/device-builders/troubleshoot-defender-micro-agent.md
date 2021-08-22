---
title: Defender IoT 마이크로 에이전트 문제 해결(미리 보기)
description: 예기치 않은 오류 또는 설명할 수 없는 오류를 처리하는 방법을 알아봅니다.
ms.date: 4/5/2021
ms.topic: reference
ms.openlocfilehash: 23722195f4a7f7a19f10962ce648bc1a6e2a4012
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018748"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Defender IoT 마이크로 에이전트 문제 해결(미리 보기)

예기치 않은 오류가 발생하는 경우 이러한 문제 해결 방법을 사용하여 문제를 해결할 수 있습니다. 또한 필요에 따라 Azure Defender for IoT 제품 팀에 연락하여 도움을 받을 수 있습니다.   

## <a name="service-status"></a>서비스 상태 

서비스의 상태를 보려면 다음을 수행합니다. 

1. 다음 명령을 실행합니다.

    ```bash
    systemctl status defender-iot-micro-agent.service 
    ```

1. 서비스가 `active`인지 확인하여 서비스가 안정적이고, 프로세스의 작동 시간이 적절한지 확인합니다.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="서비스가 active 상태인지 확인하여 서비스가 안정적이고, 프로세스의 작동 시간이 적절한지 확인합니다.":::

서비스가 `inactive`로 표시되는 경우 다음 명령을 사용하여 서비스를 시작합니다.

```bash
systemctl start defender-iot-micro-agent.service 
```

프로세스 작동 시간이 2분 미만이면 서비스가 충돌하는 것을 알 수 있습니다. 이 문제를 해결하려면 [로그를 검토](#review-the-logs)해야 합니다.

## <a name="validate-micro-agent-root-privileges"></a>마이크로 에이전트 루트 권한 유효성 검사

다음 명령을 사용하여 루트 권한으로 Defender IoT 마이크로 에이전트 서비스가 실행 중인지 확인합니다.

```bash
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Defender for IoT 마이크로 에이전트 서비스가 루트 권한으로 실행되고 있는지 확인합니다.":::
## <a name="review-the-logs"></a>로그 검토 

로그를 검토하려면 다음 명령을 사용합니다.  

```bash
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>빠른 로그 검토

마이크로 에이전트를 실행할 때 문제가 발생하면 다음 명령을 사용하여 로그를 볼 수 있는 임시 상태에서 마이크로 에이전트를 실행할 수 있습니다.

```bash
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>서비스를 다시 시작합니다.

서비스를 다시 시작하려면 다음 명령을 사용합니다. 

```bash
sudo systemctl restart defender-iot-micro-agent 
```

## <a name="next-steps"></a>다음 단계

[기능 지원 및 사용 중지](edge-security-module-deprecation.md)를 확인하세요.
