---
title: 일반 오류 - Azure IoT Edge | Microsoft Docs
description: 이 문서에서는 IoT Edge 솔루션을 배포할 때 발생하는 일반적인 문제를 해결할 수 있습니다.
author: kgremban
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 8d0ccbbf15f79d9e3475654094d45ed1518a2e89
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132942083"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge에 대한 일반적인 문제 및 해결 방법

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

이 문서에서는 IoT Edge 솔루션을 배포할 때 발생할 수 있는 일반적인 문제의 해결 단계를 찾아볼 수 있습니다. IoT Edge 서비스에서 로그 및 오류를 발견하는 방법에 대한 자세한 내용은 [IoT Edge 디바이스 문제 해결](troubleshoot.md)을 참조하세요.

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge 에이전트가 약 1분 후 중지됨

**관찰된 동작:**

edgeAgent 모듈이 시작되고 1분 정도 성공적으로 실행되고 나서 중지됩니다. 로그에는 IoT Edge 에이전트가 AMQP를 통해 IoT 허브에 연결하려고 시도한 후 WebSocket을 통해 AMQP를 사용하여 연결하려고 시도한다고 표시됩니다. 이것이 실패했을 때 IoT Edge 에이전트가 종료됩니다.

예제 edgeAgent 로그:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**근본 원인:**

호스트 네트워크의 네트워킹 구성 때문에 IoT Edge 에이전트가 네트워크에 연결하지 못합니다. 에이전트는 AMQP(포트 5671)를 통해 먼저 연결을 시도합니다. 이 연결이 실패하면 WebSockets(포트 443)을 시도합니다.

IoT Edge 런타임은 각 모듈이 통신할 네트워크를 설정합니다. Linux에서 이 네트워크는 브리지 네트워크입니다. Windows에서는 NAT를 사용합니다. 이 문제는 NAT 네트워크를 사용하는 Windows 컨테이너를 사용하는 Windows 디바이스에서 좀 더 자주 발생합니다.

**해결 방법:**

이 브리지/NAT 네트워크에 할당된 IP 주소가 인터넷에 연결되는 경로가 있는지 확인합니다. 경우에 따라 호스트의 VPN 구성은 IoT Edge 네트워크를 재정의합니다.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge 에이전트가 모듈의 이미지에 액세스할 수 없음(403)

**관찰된 동작:**

컨테이너가 실행되지 못하고 edgeAgent 로그에 403 오류가 표시됩니다.

**근본 원인:**

IoT Edge 에이전트가 모듈의 이미지에 액세스할 수 있는 권한이 없습니다.

**해결 방법:**

레지스트리 자격 증명이 배포 매니페스트에서 제대로 지정되었는지 확인합니다.

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge 에이전트 모듈이 ‘빈 구성 파일’을 보고하고 모듈이 디바이스에서 시작되지 않음

**관찰된 동작:**

디바이스가 배포에 정의된 모듈을 시작하는 데 문제가 있습니다. edgeAgent가 실행 중이지만 계속해서 ‘빈 구성 파일...’을 보고합니다.

**근본 원인:**

기본적으로 IoT Edge는 자신의 격리된 컨테이너 네트워크에서 모듈을 시작합니다. 디바이스가 이 비공개 네트워크 내에서 DNS 이름을 확인하는 데 문제가 발생할 수 있습니다.

**해결 방법:**

**옵션 1: 컨테이너 엔진 설정에서 DNS 서버 설정**

컨테이너 엔진 설정에서 해당 환경에 대해 DNS 서버를 지정합니다. 이것은 엔진에서 시작되는 모든 컨테이너 모듈에 적용됩니다. 사용할 DNS 서버를 지정하는 `daemon.json`이라는 파일을 만듭니다. 예:

```json
{
    "dns": ["1.1.1.1"]
}
```

위 예제는 DNS 서버를 공개적으로 액세스할 수 있는 DNS 서비스로 설정합니다. 에지 디바이스가 해당 환경에서 이 IP에 액세스할 수 없으면 이를 액세스 가능한 DNS 서버 주소로 바꿉니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
`daemon.json`을 플랫폼에 올바른 위치에 배치합니다.

| 플랫폼 | 위치 |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows 컨테이너가 포함된 Windows 호스트 | `C:\ProgramData\iotedge-moby\config` |

위치에 이미 `daemon.json` 파일이 포함되어 있으면 **dns** 키를 여기에 추가하고 파일을 저장합니다.

업데이트를 적용하기 위해 컨테이너 엔진을 다시 시작합니다.

| 플랫폼 | 명령 |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows(관리자 PowerShell) | `Restart-Service iotedge-moby -Force` |

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
`daemon.json`을 디바이스의 `/etc/docker` 디렉터리에 놓습니다.

위치에 이미 `daemon.json` 파일이 있으면 **dns** 키를 여기에 추가하고 파일을 저장합니다.

업데이트를 적용하기 위해 컨테이너 엔진을 다시 시작합니다.

```bash
sudo systemctl restart docker
```

:::moniker-end
<!-- end 1.2 -->

**옵션 2: 모듈별로 IoT Edge 배포에서 DNS 서버 설정**

IoT Edge 배포에서 각 모듈의 *createOptions* 에 대해 DNS 서버를 설정할 수 있습니다. 예:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

*edgeAgent* 및 *edgeHub* 모듈에 대해서도 이 구성을 설정해야 합니다.

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge 허브 시작 실패

**관찰된 동작:**

edgeHub 모듈이 시작되지 못합니다. 로그에 다음 오류 중 하나와 비슷한 메시지가 표시될 수 있습니다.

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

또는

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**근본 원인:**

edgeHub 모듈이 바인딩하려고 시도 중인 포트를 호스트 컴퓨터에 있는 다른 일부 프로세스가 바인딩했습니다. IoT Edge 허브는 게이트웨이 시나리오에서 사용하기 위해 포트 443, 5671 및 8883을 매핑합니다. 다른 프로세스가 이러한 포트 중 하나를 이미 바인딩했으면 모듈이 시작되지 못합니다.

**해결 방법:**

이 문제는 두 가지 방법으로 해결할 수 있습니다.

IoT Edge 디바이스가 게이트웨이 디바이스로 작동하는 경우 포트 443, 5671 또는 8883을 사용 중인 프로세스를 찾아서 중지해야 합니다. 포트 443 오류는 일반적으로 다른 프로세스가 웹 서버임을 의미합니다.

IoT Edge 디바이스를 게이트웨이로 사용할 필요가 없으면 edgeHub의 모듈 만들기 옵션에서 포트 바인딩을 제거할 수 있습니다. Azure Portal에서 또는 deployment.json 파일에서 만들기 옵션을 변경할 수 있습니다.

Azure Portal에서 다음을 수행합니다.

1. IoT 허브로 이동하고 **IoT Edge** 를 선택합니다.

2. 업데이트하려는 IoT Edge 디바이스를 선택합니다.

3. **모듈 설정** 을 선택합니다.

4. **런타임 설정** 을 선택합니다.

5. **Edge 허브** 모듈 설정에서 **만들기 옵션** 텍스트 상자에 있는 모든 항목을 삭제합니다.

6. 변경 사항을 저장하고 배포를 만듭니다.

deployment.json 파일에서 다음을 수행합니다.

1. IoT Edge 디바이스에 적용한 deployment.json 파일을 엽니다.

2. edgeAgent의 원하는 속성 섹션에서 `edgeHub` 설정을 찾습니다.

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. `createOptions` 줄을 제거하고 그 앞의 `image` 줄의 끝에 있는 후행 쉼표를 제거합니다.

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. 파일을 저장하고 IoT Edge 디바이스에 다시 적용합니다.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge 보안 디먼이 유효하지 않은 호스트 이름으로 실패합니다.

**관찰된 동작:**

[IoT Edge 보안 관리자 로그를 확인](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)하려는 시도가 실패하고 다음 메시지가 출력됩니다.

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**근본 원인:**

IoT Edge 런타임은 64자 미만인 호스트 이름만을 지원할 수 있습니다. 물리적 머신은 일반적으로 긴 호스트 이름을 사용하지 않지만 문제는 가상 머신에서 자주 발생합니다. 특히 Azure에서 호스팅되는 Windows 가상 머신에 자동으로 생성된 호스트 이름이 너무 긴 경향이 있습니다.

**해결 방법:**

이 오류를 표시하는 경우 가상 머신의 DNS 이름을 구성한 다음, 설정 명령에서 DNS 이름을 호스트 이름으로 설정하여 해결할 수 있습니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Azure Portal에서 가상 머신의 개요 페이지로 이동합니다.
2. DNS 이름에서 **구성** 을 선택합니다. 가상 머신에 DNS 이름이 이미 구성되어 있으면 새 이름을 구성할 필요가 없습니다.

   ![가상 머신의 DNS 이름 구성](./media/troubleshoot/configure-dns.png)

3. **DNS 이름 레이블** 에 대한 값을 입력하고 **저장** 을 선택합니다.
4. 새 DNS 이름을 복사합니다. 이 이름은 **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com** 형식입니다.
5. 가상 머신 내에서 다음 명령을 사용하여 DNS 이름으로 IoT Edge 런타임을 설정합니다.

   * Linux에서:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * Windows에서:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Azure Portal에서 가상 머신의 개요 페이지로 이동합니다.

2. DNS 이름에서 **구성** 을 선택합니다. 가상 머신에 DNS 이름이 이미 구성되어 있으면 새 이름을 구성할 필요가 없습니다.

   ![가상 머신의 DNS 이름 구성](./media/troubleshoot/configure-dns.png)

3. **DNS 이름 레이블** 에 대한 값을 입력하고 **저장** 을 선택합니다.

4. 새 DNS 이름을 복사합니다. 이 이름은 **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com** 형식입니다.

5. IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

6. `hostname`의 값을 DNS 이름으로 바꿉니다.

7. 파일을 저장하고 닫은 후 변경 사항을 IoT Edge에 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Windows에서 IoT Edge 디먼 로그를 가져올 수 없습니다.

**관찰된 동작:**

Windows에서 `Get-WinEvent`를 사용할 때 EventLogException이 발생합니다.

**근본 원인:**

`Get-WinEvent` PowerShell 명령은 특정 `ProviderName`별로 로그를 찾기 위해 표시된 레지스트리 항목을 사용합니다.

**해결 방법:**

IoT Edge 디먼에 대한 레지스트리 항목을 설정합니다. 다음 콘텐츠를 사용하여 **iotedge.reg** 파일을 만들고, 파일을 두 번 클릭하거나 `reg import iotedge.reg` 명령을 사용하여 Windows 레지스트리로 가져옵니다.

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

:::moniker-end
<!-- end 1.1 -->

## <a name="stability-issues-on-smaller-devices"></a>작은 디바이스에서의 안정성 문제

**관찰된 동작:**

특히 게이트웨이로 사용될 때 Raspberry Pi와 같은 리소스가 제한된 디바이스에서 안정성 문제가 발생할 수 있습니다. 증상에는 IoT Edge 허브 모듈의 메모리 부족 예외, 다운스트림 디바이스의 연결 실패 또는 몇 시간 후 디바이스의 원격 분석 메시지 전송 실패가 포함됩니다.

**근본 원인:**

IoT Edge 런타임에 속하는 IoT Edge 허브는 기본적으로 성능에 최적화되어 있으며 많은 양의 메모리를 할당하려고 합니다. 이 최적화는 제한된 에지 디바이스에 대해 이상적이지 않으며 안정성 문제를 유발할 수 있습니다.

**해결 방법:**

IoT Edge 허브의 경우 **OptimizeForPerformance** 환경 변수를 **false** 로 설정합니다. 환경 변수를 설정하는 방법은 두 가지입니다.

Azure Portal에서 다음을 수행합니다.

IoT 허브에서 IoT Edge 디바이스를 선택하고 디바이스 세부 정보 페이지에서 **모듈 선택** > **런타임 설정** 을 선택합니다. *false* 로 설정된 *OptimizeForPerformance* 라는 IoT Edge 허브 모듈에 대해 환경 변수를 만듭니다.

![OptimizeForPerformance를 false로 설정](./media/troubleshoot/optimizeforperformance-false.png)

배포 매니페스트의 경우:

```json
"edgeHub": {
  "type": "docker",
  "settings": {
    "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
    "createOptions": <snipped>
  },
  "env": {
    "OptimizeForPerformance": {
      "value": "false"
    }
  },
```

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>404 오류로 IoT Edge 모듈이 edgeHub에 메시지를 보내지 못함

**관찰된 동작:**

사용자 지정 IoT Edge 모듈이 404 `Module not found` 오류로 인해 IoT Edge 허브에 메시지를 보내지 못합니다. IoT Edge 디먼은 로그에 다음 메시지를 출력합니다.

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**근본 원인:**

IoT Edge 디먼은 보안상의 이유로 edgeHub에 연결하는 모든 모듈에 대해 프로세스 확인을 적용합니다. 이 디먼은 모듈이 전송하는 모든 메시지가 해당 모듈의 기본 프로세스 ID에서 온 것인지 확인합니다. 메시지가 처음에 설정한 것과는 다른 프로세스 ID의 모듈에서 전송될 경우 404 오류 메시지가 표시되며 거부됩니다.

**해결 방법:**

1\.0.7 버전부터는 모든 모듈 프로세스에 연결 권한이 부여됩니다. 자세한 내용은 [1.0.7 릴리스 변경 로그](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1)를 참조하세요.

1\.0.7로 업그레이드할 수 없으면 다음 단계를 완료합니다. 사용자 지정 IoT Edge 모듈이 edgeHub에 메시지를 보내는 데 항상 동일한 프로세스 ID를 사용하는지 확인합니다. 예를 들어 Docker 파일에서 `CMD` 명령 대신 `ENTRYPOINT` 명령을 사용합니다. `CMD` 명령은 모듈에 대한 프로세스 ID 하나와 기본 프로그램에서 실행되는 bash 명령에 대한 또 다른 프로세스 ID로 이어지지만 `ENTRYPOINT` 명령은 단일 프로세스 ID로 이어집니다.

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>IoT Edge 모듈이 성공적으로 배포된 후 디바이스에서 사라짐

**관찰된 동작:**

IoT Edge 서비스에 대해 모듈을 설정한 후 모듈이 성공적으로 배포되지만 몇 분 후 디바이스 및 Azure Portal의 디바이스 세부 정보에서 사라집니다. 정의된 것 이외의 다른 모듈이 디바이스에 표시될 수도 있습니다.

**근본 원인:**

자동 배포가 대상 디바이스를 선택하는 경우 단일 디바이스에 대해 모듈을 수동으로 설정하는 것보다 우선 적용됩니다. Azure Portal의 **모듈 설정** 기능 또는 Visual Studio Code의 **단일 디바이스에 대한 배포 만들기** 기능이 잠시 동안 적용됩니다. 정의한 모듈이 디바이스에서 시작되는 것을 확인할 수 있습니다. 그런 후 자동 배포가 우선 적용되면서 디바이스의 원하는 속성을 덮어씁니다.

**해결 방법:**

자동 배포 또는 개별 디바이스 배포 중에서 배포 메커니즘을 디바이스당 한 가지 유형만 사용해야 합니다. 한 디바이스를 대상으로 하는 자동 배포가 여럿인 경우 올바른 항목이 지정된 디바이스에 적용되도록 우선 순위 또는 대상 설명을 변경할 수 있습니다. 또한 자동 배포의 대상 설명과 더 이상 일치하지 않도록 디바이스 트윈을 업데이트할 수 있습니다.

자세한 내용은 [단일 디바이스 또는 대규모 IoT Edge 자동 배포에 대한 이해](module-deployment-monitoring.md)를 참조하세요.

## <a name="iot-edge-module-reports-connectivity-errors"></a>IoT Edge 모듈에서 연결 오류를 보고합니다.

**관찰된 동작:**

런타임 모듈을 포함하여 클라우드 서비스에 직접 연결하는 모듈을 IoT Edge 예상대로 작동하지 않고 연결 또는 네트워킹 오류와 관련된 오류를 반환합니다.

**근본 원인:**

컨테이너는 클라우드 서비스와 통신할 수 있도록 IP 패킷 전달을 사용하여 인터넷에 연결합니다. IP 패킷 전달은 Docker에서 기본적으로 사용하도록 설정되지만 사용하지 않도록 설정되면 클라우드 서비스에 연결하는 모든 모듈이 예상대로 작동하지 않습니다. 자세한 내용은 Docker 설명서의 [컨테이너 통신 이해를](https://apimirror.com/docker~1.12/engine/userguide/networking/default_network/container-communication/index) 참조하세요.

**해결 방법:**

다음 단계를 사용하여 IP 패킷 전달을 사용하도록 설정합니다.

<!--1.1-->
:::moniker range="iotedge-2018-06"

Windows에서:

1. 실행 애플리케이션을 **엽니다.**

1. `regedit`텍스트 상자에 를 입력하고 **확인을** 선택합니다.

1. 레지스트리 **편집기** 창에서 **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters** 으로 찾습니다.

1. **IPEnableRouter 매개 변수를 찾습니다.**

   1. 매개 변수가 있는 경우 매개 변수 값을 **1로** 설정합니다.

   1. 매개 변수가 없는 경우 다음 설정을 통해 새 매개 변수로 추가합니다.

      | 설정 | 값 |
      | ------- | ----- |
      | 이름    | IPEnableRouter |
      | Type    | REG_DWORD |
      | 값   | 1 |

1. 레지스트리 편집기 창을 닫습니다.

1. 시스템을 다시 시작하여 변경 내용을 적용합니다.

Linux에서:
:::moniker-end
<!-- end -->

1. **sysctl.conf** 파일을 엽니다.

   ```bash
   sudo nano /etc/sysctl.conf
   ```

1. 파일에 다음 줄을 추가합니다.

   ```input
   net.ipv4.ip_forward=1
   ```

1. 파일을 저장하고 닫습니다.

1. 네트워크 서비스 및 Docker 서비스를 다시 시작하여 변경 내용을 적용합니다.

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

## <a name="iot-edge-behind-a-gateway-cannot-perform-http-requests-and-start-edgeagent-module"></a>게이트웨이 뒤의 IoT Edge가 HTTP 요청을 수행하고 edgeAgent 모듈을 시작할 수 없음

**관찰된 동작:**

IoT Edge 데몬이 유효한 구성 파일로 활성 상태이지만 edgeAgent 모듈을 시작할 수 없습니다. `iotedge list` 명령이 빈 목록을 반환합니다. IoT Edge 데몬 로그가 `Could not perform HTTP request`를 보고합니다.

**근본 원인:**

게이트웨이 뒤의 IoT Edge 디바이스가 구성 파일의 `parent_hostname` 필드에 지정된 부모 IoT Edge 디바이스에서 모듈 이미지를 가져옵니다. `Could not perform HTTP request` 오류는 자식 디바이스가 HTTP를 통해 부모 디바이스에 연결할 수 없음을 의미합니다.

**해결 방법:**

부모 IoT Edge 서비스가 자식 IoT Edge 디바이스에서 수신 요청을 수신할 수 있는지 확인합니다. 자식 디바이스에서 들어오는 요청에 대해 포트 443 및 6617에서 네트워크 트래픽을 엽니다.

:::moniker-end
<!-- end 1.2 -->

## <a name="next-steps"></a>다음 단계

IoT Edge 플랫폼에서 버그를 찾았나요? 지속적인 제품 개선을 위해 [문제를 제출](https://github.com/Azure/iotedge/issues)하세요.

추가 질문이 있으면 [지원 요청](https://portal.azure.com/#create/Microsoft.Support)을 만들어 도움을 받으세요.
