---
title: 네트워크 프록시용 디바이스 구성 - Azure IoT Edge | Microsoft Docs
description: 프록시 서버를 통해 통신하도록 Azure IoT Edge 런타임과 인터넷 연결 IoT Edge 모듈을 구성하는 방법을 설명합니다.
author: kgremban
ms.author: kgremban
ms.date: 09/03/2020
ms.topic: how-to
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- contperf-fy21q1
ms.openlocfilehash: 85293f72525426cecebc75495f87044c7546d092
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471753"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge 디바이스는 HTTPS 요청을 전송하여 IoT Hub와 통신합니다. 디바이스가 프록시 서버를 사용하는 네트워크에 연결되어 있는 경우에는 해당 서버를 통해 통신하도록 IoT Edge 런타임을 구성해야 합니다. 또한 프록시 서버는 IoT Edge 허브를 통해 라우팅되지 않는 HTTP 또는 HTTPS 요청을 수행하는 개별 IoT Edge 모듈에도 영향을 줄 수 있습니다.

이 문서에서는 프록시 서버 뒤에서 IoT Edge 디바이스를 구성한 후 관리하는 다음 4개 단계를 안내합니다.

1. [**디바이스에 IoT Edge 런타임 설치**](#install-iot-edge-through-a-proxy)

   IoT Edge 설치 스크립트는 인터넷에서 패키지와 파일을 끌어오므로 디바이스는 해당 요청을 수행하려면 프록시 서버를 통해 통신해야 합니다. Windows 디바이스의 경우 설치 스크립트는 오프라인 설치 옵션도 제공합니다.

   이 단계는 IoT Edge 디바이스를 처음 설정할 때 구성하는 일회성 프로세스입니다. IoT Edge 런타임을 업데이트하는 경우에도 동일한 연결이 필요합니다.

2. [**디바이스에서 IoT Edge 및 컨테이너 런타임 구성**](#configure-iot-edge-and-moby)

   IoT Edge는 IoT Hub에 대한 통신을 처리합니다. 컨테이너 런타임은 컨테이너 관리를 처리하므로 컨테이너 레지스트리와 통신합니다. 이러한 구성 요소는 둘 다 프록시 서버를 통해 웹 요청을 수행해야 합니다.

   이 단계는 IoT Edge 디바이스를 처음 설정할 때 구성하는 일회성 프로세스입니다.

3. [**디바이스에서 구성 파일의 IoT Edge 에이전트 속성 구성**](#configure-the-iot-edge-agent)

   IoT Edge 디먼은 처음에 edgeAgent 모듈을 시작합니다. 그런 다음, edgeAgent 모듈은 IoT Hub에서 배포 매니페스트를 검색하고 다른 모든 모듈을 시작합니다. IoT Edge 에이전트가 IoT Hub에 대한 초기 연결을 설정하려면 디바이스 자체에서 수동으로 edgeAgent 모듈 환경 변수를 구성합니다. 초기 연결 후 edgeAgent 모듈을 원격으로 구성할 수 있습니다.

   이 단계는 IoT Edge 디바이스를 처음 설정할 때 구성하는 일회성 프로세스입니다.

4. [**이후 모든 모듈 배포의 경우 프록시를 통해 통신하는 모든 모듈에 대해 환경 변수 설정**](#configure-deployment-manifests)

   IoT Edge 디바이스가 설정되고 프록시 서버를 통해 IoT Hub에 연결된 후에는 이후 모든 모듈 배포에서 연결을 유지 관리해야 합니다.

   이 단계는 모든 새 모듈 또는 배포 업데이트가 프록시 서버를 통해 통신하는 디바이스 기능을 유지 관리하도록 원격으로 수행되는 지속적인 프로세스입니다.

## <a name="know-your-proxy-url"></a>프록시 URL 파악

이 문서의 단계를 시작하기 전에 프록시 URL을 알고 있어야 합니다.

프록시 URL은 다음 형식을 사용합니다. **프로토콜**://**proxy_host**:**proxy_port**

* **프로토콜** 은 HTTP 또는 HTTPS입니다. Docker 디먼은 컨테이너 레지스트리 설정에 따라 프로토콜 중 하나를 사용할 수 있지만, IoT Edge 디먼 및 런타임 컨테이너는 항상 HTTP를 사용하여 프록시에 연결해야 합니다.

* **proxy_host** 는 프록시 서버의 주소입니다. 프록시 서버에 인증이 필요한 경우 **user**:**password**\@**proxy_host** 형식을 사용하여 프록시 호스트의 일부로 자격 증명을 제공할 수 있습니다.

* **proxy_port** 는 프록시가 네트워크 트래픽에 응답하는 네트워크 포트입니다.

## <a name="install-iot-edge-through-a-proxy"></a>프록시를 통해 IoT Edge 설치

IoT Edge 디바이스가 Windows 또는 Linux에서 실행되는지 여부에 관계없이 프록시 서버를 통해 설치 패키지에 액세스해야 합니다. 운영 체제에 따라 프록시 서버를 통해 IoT Edge 런타임을 설치하는 단계를 수행합니다.

### <a name="linux-devices"></a>Linux 디바이스

Linux 디바이스에서 IoT Edge 런타임을 설치 중인 경우 설치 패키지에 액세스하기 위해 프록시 서버로 이동하도록 패키지 관리자를 구성합니다. 예를 들어 [http-proxy를 사용하도록 apt-get을 설정](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy)합니다. 패키지 관리자가 구성된 후 일반적으로 [Azure IoT Edge 런타임 설치](how-to-install-iot-edge.md)의 지침을 따릅니다.

### <a name="windows-devices-using-iot-edge-for-linux-on-windows"></a>IoT Edge for Linux on Windows를 사용하는 Windows 디바이스

IoT Edge for Linux on Windows를 사용하여 IoT Edge 런타임을 설치하는 경우 IoT Edge는 기본적으로 Linux 가상 머신에 설치됩니다. 추가 설치 또는 업데이트 단계가 필요하지 않습니다.

### <a name="windows-devices-using-windows-containers"></a>Windows 컨테이너를 사용하는 Windows 디바이스

Windows 디바이스에서 IoT Edge 런타임을 설치하는 경우 프록시 서버를 두 번 거쳐야 합니다. 첫 번째 연결은 설치 프로그램 스크립트 파일을 다운로드하고 두 번째 연결은 설치 중에 필요한 구성 요소를 다운로드하는 것입니다. Windows 설정에서 프록시 정보를 구성하거나 PowerShell 명령에 프록시 정보를 직접 포함할 수 있습니다.

다음 단계에서는 `-proxy` 인수를 사용하는 Windows 설치의 예제를 보여 줍니다.

1. Invoke-WebRequest 명령에는 설치 프로그램 스크립트에 액세스하려면 프록시 정보가 필요합니다. 그런 다음, Deploy-IoTEdge 명령에는 설치 파일을 다운로드하려면 프록시 정보가 필요합니다.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Initialize-IoTEdge 명령은 프록시 서버를 거칠 필요가 없으므로 두 번째 단계에는 Invoke-WebRequest를 위한 프록시 정보만 필요합니다.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

URL에 포함할 수 없는 프록시 서버에 대한 복잡한 자격 증명이 있는 경우 `-InvokeWebRequestParameters` 내에서 `-ProxyCredential` 매개 변수를 사용합니다. 예제:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

프록시 매개 변수에 대한 자세한 내용은 [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest)를 참조하세요. Windows 설치 매개 변수에 관한 자세한 내용은 [Windows의 IoT Edge에 대한 PowerShell 스크립트](reference-windows-scripts.md)를 참조하세요.

## <a name="configure-iot-edge-and-moby"></a>IoT Edge 및 Moby 구성

IoT Edge는 IoT Edge 디바이스에서 실행되는 두 개의 디먼을 사용합니다. Moby 디먼은 웹 요청을 수행하여 컨테이너 레지스트리에서 컨테이너 이미지를 끌어옵니다. IoT Edge 디먼은 웹 요청을 전송하여 IoT Hub와 통신합니다.

Moby 및 IoT Edge 디먼은 둘 다 지속적인 디바이스 기능을 위해 프록시 서버를 사용하도록 구성해야 합니다. 이 단계는 초기 디바이스 설정 중에 IoT Edge 디바이스에서 수행됩니다.

### <a name="moby-daemon"></a>Moby 디먼

Moby는 Docker를 기반으로 빌드되므로 Docker 설명서를 참조하여 환경 변수를 통해 Moby 디먼을 구성합니다. DockerHub 및 Azure Container Registry를 비롯한 대부분의 컨테이너 레지스트리는 HTTPS 요청을 지원하므로 **HTTPS_PROXY** 매개 변수를 설정해야 합니다. TLS(전송 계층 보안)를 지원하지 않는 레지스트리에서 이미지를 끌어오는 경우에는 **HTTP_PROXY** 매개 변수를 설정해야 합니다.

IoT Edge 디바이스 운영 체제에 적용되는 문서를 선택합니다.

* [Linux에서 Docker 디먼 구성](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Linux 디바이스의 Moby 디먼은 Docker 이름을 유지합니다.
* [Windows에서 Docker 디먼 구성](/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Windows 디바이스의 Moby 디먼을 iotedge-moby라고 합니다. Windows 디바이스에서 Docker Desktop 및 Moby를 둘 다 병렬로 실행할 수 있으므로 이름은 서로 다릅니다.

### <a name="iot-edge-daemon"></a>IoT Edge 디먼

IoT Edge 디먼은 Moby 디먼과 비슷한 방식으로 구성됩니다. 사용 중인 운영 체제에 따라 다음 단계를 수행하여 서비스에 대한 환경 변수를 설정합니다.

IoT Edge 디먼은 항상 HTTPS를 사용하여 IoT Hub에 요청을 보냅니다.

#### <a name="linux"></a>Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

터미널에서 편집기를 열어 IoT Edge 디먼을 구성합니다.

```bash
sudo systemctl edit iotedge
```

다음 텍스트를 입력하여 **\<proxy URL>** 을 프록시 서버 주소 및 포트로 바꿉니다. 그런 다음 입력 내용을 저장하고 편집기를 종료합니다.

```ini
[Service]
Environment=https_proxy=<proxy URL>
```

서비스 관리자를 새로 고쳐 IoT Edge의 새 구성을 선택합니다.

```bash
sudo systemctl daemon-reload
```

IoT Edge를 다시 시작하여 변경 내용을 적용합니다.

```bash
sudo systemctl restart iotedge
```

환경 변수가 생성되었으며 새 구성이 로드되었는지 확인합니다.

```bash
systemctl show --property=Environment iotedge
```
:::moniker-end
<!--end 1.1-->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

터미널에서 편집기를 열어 IoT Edge 디먼을 구성합니다.

```bash
sudo systemctl edit aziot-edged
```

다음 텍스트를 입력하여 **\<proxy URL>** 을 프록시 서버 주소 및 포트로 바꿉니다. 그런 다음 입력 내용을 저장하고 편집기를 종료합니다.

```ini
[Service]
Environment=https_proxy=<proxy URL>
```

버전 1.2부터 IoT Edge는 IoT ID 서비스를 사용하여 IoT Hub 또는 IoT Hub Device Provisioning Service를 통해 디바이스 프로비저닝을 처리합니다. 터미널에서 편집기를 열어 IoT ID 서비스 디먼을 구성합니다.

```bash
sudo systemctl edit aziot-identityd
```

다음 텍스트를 입력하여 **\<proxy URL>** 을 프록시 서버 주소 및 포트로 바꿉니다. 그런 다음 입력 내용을 저장하고 편집기를 종료합니다.

```ini
[Service]
Environment=https_proxy=<proxy URL>
```

서비스 관리자를 새로 고쳐서 새 구성을 선택합니다.

```bash
sudo systemctl daemon-reload
```

두 디먼의 변경 내용을 모두 적용하려면 IoT Edge 시스템 서비스를 다시 시작합니다.

```bash
sudo iotedge system restart
```

환경 변수가 생성되었고 새 구성이 로드되었는지 확인합니다.

```bash
systemctl show --property=Environment aziot-edged
systemctl show --property=Environment aziot-identityd
```
:::moniker-end
<!--end 1.2-->

#### <a name="windows-using-iot-edge-for-linux-on-windows"></a>IoT Edge for Linux on Windows를 사용하는 Windows

IoT Edge for Linux on Windows 가상 머신에 로그인합니다.

```powershell
Connect-EflowVm
```

위 Linux 섹션과 동일한 단계를 수행하여 IoT Edge 디먼을 구성합니다.

#### <a name="windows-using-windows-containers"></a>Windows 컨테이너를 사용하는 Windows

관리자 권한으로 PowerShell 창을 연 후에 다음 명령을 실행해 새 환경 변수를 사용하여 레지스트리를 편집합니다. **\<proxy url>** 을 프록시 서버 주소 및 포트로 바꿉니다.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

IoT Edge를 다시 시작하여 변경 내용을 적용합니다.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>IoT Edge 에이전트 구성

IoT Edge 에이전트는 모든 IoT Edge 디바이스에서 처음으로 시작되는 모듈로, IoT Edge 구성 파일의 정보에 따라 처음으로 시작됩니다. 그런 후에 이 에이전트에서 IoT Hub에 연결하여 배포 매니페스트를 검색합니다. 배포 매니페스트는 디바이스에서 배포해야 하는 다른 모듈을 선언합니다.

이 단계는 초기 디바이스 설정 중에 IoT Edge 디바이스에서 한 번 수행됩니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. IoT Edge 디바이스에서 config.yaml 파일을 엽니다. Linux 시스템에서 이 파일의 위치는 **/etc/iotedge/config.yaml** 입니다. Windows 시스템에서 이 파일의 위치는 **C:\ProgramData\iotedge\config.yaml** 입니다. 구성 파일은 보호되어 있으므로 관리 권한이 있어야 액세스할 수 있습니다. Linux 시스템에서는 원하는 텍스트 편집기에서 파일을 열기 전에 `sudo` 명령을 사용합니다. Windows에서는 관리자 권한으로 메모장과 같은 텍스트 편집기를 연 다음, 파일을 엽니다.

2. config.yaml 파일에서 **Edge Agent module spec** 섹션을 찾습니다. IoT Edge 에이전트 정의에는 **env** 매개 변수가 포함되어 있으며, 이 매개 변수에 환경 변수를 추가할 수 있습니다.

3. env 매개 변수의 자리 표시자인 중괄호를 제거하고 새 줄에 새 변수를 추가합니다. YAML에서 들여쓰기를 하려면 공백 두 개를 추가해야 합니다.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. IoT Edge 런타임은 기본적으로 AMQP를 사용하여 IoT Hub와 통신합니다. 그런데 일부 프록시 서버는 AMQP 포트를 차단합니다. 이 경우에는 WebSocket을 통해 AMQP를 사용하도록 edgeAgent도 구성해야 합니다. 이렇게 하려면 두 번째 환경 변수를 추가합니다.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![환경 변수가 포함된 edgeAgent 정의](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. config.yaml의 변경 내용을 저장하고 편집기를 닫습니다. IoT Edge를 다시 시작하여 변경 내용을 적용합니다.

   * Linux 및 IoT Edge for Linux on Windows:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows 컨테이너를 사용하는 Windows:

      ```powershell
      Restart-Service iotedge
      ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge 디바이스에서 구성 파일 `/etc/aziot/config.toml`을 엽니다. 구성 파일은 보호되어 있으므로 관리 권한이 있어야 액세스할 수 있습니다. Linux 시스템에서는 원하는 텍스트 편집기에서 파일을 열기 전에 `sudo` 명령을 사용합니다.

2. 구성 파일에서 시작 시 사용할 edgeAgent 모듈의 모든 구성 정보가 포함된 `[agent]` 섹션을 찾습니다. IoT Edge 에이전트 정의에는 환경 변수를 추가할 수 있는 `[agent.env]` 하위 섹션이 포함됩니다.

3. 환경 변수 섹션에 **https_proxy** 매개 변수를 추가하고 프록시 URL을 해당 값으로 설정합니다.

   ```toml
   [agent.env]
   # "RuntimeLogLevel" = "debug"
   # "UpstreamProtocol" = "AmqpWs"
   "https_proxy" = "<proxy URL>"
   ```

4. IoT Edge 런타임은 기본적으로 AMQP를 사용하여 IoT Hub와 통신합니다. 그런데 일부 프록시 서버는 AMQP 포트를 차단합니다. 이 경우에는 WebSocket을 통해 AMQP를 사용하도록 edgeAgent도 구성해야 합니다. `UpstreamProtocol` 매개 변수의 주석 처리를 제거합니다.

   ```toml
   [agent.env]
   # "RuntimeLogLevel" = "debug"
   "UpstreamProtocol" = "AmqpWs"
   "https_proxy" = "<proxy URL>"
   ```

5. 변경 내용을 저장하고 편집기를 닫습니다. 최신 변경 내용을 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-deployment-manifests"></a>배포 매니페스트 구성  

IoT Edge 디바이스가 프록시 서버를 사용하도록 구성한 후에는 이후 배포 매니페스트에서 HTTPS_PROXY 환경 변수를 계속 선언해야 합니다. Azure Portal 마법사를 사용하거나 배포 매니페스트 JSON 파일을 편집하여 배포 매니페스트를 편집할 수 있습니다.

두 개의 런타임 모듈인 edgeAgent와 edgeHub가 프록시 서버를 통해 통신하도록 항상 구성하여 IoT Hub와의 연결을 유지할 수 있게 합니다. edgeAgent 모듈에서 프록시 정보를 제거하는 경우 연결을 다시 설정하는 유일한 방법은 이전 섹션에서 설명한 대로 디바이스에서 구성 파일을 편집하는 것입니다.

edgeAgent 및 edgeHub 모듈 외에도 다른 모듈에 프록시 구성이 필요할 수 있습니다. Blob Storage와 같은 IoT Hub 외에 Azure 리소스에 액세스해야 하는 모듈에는 배포 매니페스트 파일에 지정된 HTTPS_PROXY 변수가 있어야 합니다.

다음 절차는 IoT Edge 디바이스의 수명 내내 적용됩니다.

### <a name="azure-portal"></a>Azure portal

**모듈 설정** 마법사를 사용하여 IoT Edge 디바이스의 배포를 만들 때 모든 모듈에는 프록시 서버 연결을 구성할 수 있는 **환경 변수** 섹션에 있습니다.

IoT Edge 에이전트 및 IoT Edge 허브 모듈을 구성하려면 마법사의 첫 단계에서 **런타임 설정** 을 선택합니다.

![고급 Edge 런타임 설정 구성](./media/how-to-configure-proxy-support/configure-runtime.png)

IoT Edge 에이전트 및 IoT Edge 허브 모듈 정의에 모두 **https_proxy** 환경 변수를 추가합니다. IoT Edge 디바이스의 구성 파일에 **UpstreamProtocol** 환경 변수를 포함한 경우에는 IoT Edge 에이전트 모듈 정의에도 해당 환경 변수를 추가합니다.

![Https_proxy 환경 변수 설정](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

배포 매니페스트에 추가하는 기타 모든 모듈은 같은 패턴을 따릅니다.

### <a name="json-deployment-manifest-files"></a>JSON 배포 매니페스트 파일

Visual Studio Code에서 템플릿을 사용하거나 JSON 파일을 직접 만들어 IoT Edge 디바이스용 배포를 만든 경우에는 각 모듈 정의에 환경 변수를 직접 추가할 수 있습니다.

다음 JSON 형식을 사용합니다.

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

환경 변수가 포함된 모듈 정의는 다음 edgeHub 예제와 같이 표시됩니다.

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
        "createOptions": "{}"
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

IoT Edge 디바이스의 confige.yaml 파일에 **UpstreamProtocol** 환경 변수를 포함한 경우에는 IoT Edge 에이전트 모듈 정의에도 해당 환경 변수를 포함합니다.

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="working-with-traffic-inspecting-proxies"></a>트래픽 검사 프록시 사용

사용하려는 프록시가 TLS 보안 연결에서 트래픽 검사를 수행하는 경우 X.509 인증서를 사용하는 인증이 작동하지 않는다는 점에 유의해야 합니다. IoT Edge는 제공된 인증서와 키를 사용하여 엔드투엔드 암호화된 TLS 채널을 설정합니다. 트래픽 검사를 위해 채널이 끊어지면 프록시가 적절한 자격 증명을 사용하여 채널을 다시 설정할 수 없으며 IoT Hub 및 IoT Hub Device Provisioning Service에서 `Unauthorized` 오류를 반환합니다.

트래픽 검사를 수행하는 프록시를 사용하려면, 공유 액세스 서명 인증을 사용하거나 IoT Hub 및 IoT Hub Device Provisioning Service를 허용 목록에 추가하여 검사를 피해야 합니다.

## <a name="next-steps"></a>다음 단계

[IoT Edge 런타임](iot-edge-runtime.md)의 역할에 대해 자세히 알아봅니다.

[Azure IoT Edge와 관련된 일반적인 문제 및 해결 방법](troubleshoot.md)을 사용하여 설치 및 구성 오류가 발생하는 문제를 해결합니다.
