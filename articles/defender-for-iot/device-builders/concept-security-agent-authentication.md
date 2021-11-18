---
title: 보안 에이전트 인증(미리 보기)
description: 가능한 두 가지 방법을 사용하여 마이크로 에이전트 인증을 수행합니다.
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: ab7d624ef206120751d273781384d440cea5802d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709539"
---
# <a name="micro-agent-authentication-methods-preview"></a>마이크로 에이전트 인증 방법(미리 보기)

Defender for IoT 마이크로 에이전트를 사용하는 인증에는 다음 두 가지 옵션이 있습니다. 

- 연결 문자열 

- 인증서 

## <a name="authentication-using-a-connection-string"></a>연결 문자열을 사용하여 인증 

연결 문자열을 사용하려면 이름이 인 파일의 Defender for Cloud 에이전트 디렉터리에 utf-8로 인코딩된 연결 문자열을 사용하는 파일을 추가해야 `connection_string.txt` 합니다. 예제:

```azurecli
echo “<connection string>” > connection_string.txt 
```

이 작업을 완료한 후에는 이 명령을 사용하여 서비스를 다시 시작해야 합니다.

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>인증서를 사용하여 인증 


인증서를 사용하여 인증을 수행하려면 

1. PEM으로 인코딩된 인증서의 공용 부분을 라는 파일의 Defender for Cloud 에이전트 디렉터리에 `certificate_public.pem` 배치합니다.
1. PEM으로 인코딩된 프라이빗 키를 라는 파일의 Defender for Cloud 에이전트 디렉터리에 `certificate_private.pem` 배치합니다.
1. 적절한 연결 문자열을 `connection_string.txt` 파일에 저장합니다. 예제:

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    이 작업은 Defender for Cloud 에이전트에서 인증을 위해 인증서가 제공될 것으로 예상함을 나타냅니다. 

1. 다음 코드를 사용하여 서비스를 다시 시작합니다. 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>마이크로 에이전트가 제대로 실행되고 있는지 확인합니다. 

1. 다음 명령 실행: 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. 서비스가 **활성** 인지 확인하여 서비스가 안정적이고, 프로세스의 작동 시간이 적절한지 확인합니다. 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="서비스가 활성 상태를 유지하도록 하여 안정적으로 만듭니다.":::

## <a name="next-steps"></a>다음 단계

[보안 상태 – CIS 벤치마크](concept-security-posture.md)를 확인합니다.
