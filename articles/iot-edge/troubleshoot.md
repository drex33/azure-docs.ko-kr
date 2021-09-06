---
title: 문제 해결 - Azure IoT Edge | Microsoft Docs
description: 이 문서에서는 구성 요소 상태 및 로그 검색과 같은 Azure IoT Edge의 표준 진단 기술을 알아봅니다.
author: kgremban
ms.author: kgremban
ms.date: 05/04/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3bca470114b5fb22409d86c333e92f93155759f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528661"
---
# <a name="troubleshoot-your-iot-edge-device"></a>IoT Edge 디바이스 문제 해결

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

작업 환경에서 Azure IoT Edge를 실행하는 동안 문제가 발생할 경우 이 문서를 문제 해결 및 진단에 대한 가이드로 사용합니다.

## <a name="run-the-check-command"></a>'check' 명령 실행

IoT Edge 문제를 해결하는 첫 번째 단계는 일반적인 문제에 대한 구성 및 연결 테스트 컬렉션을 실행하는 `check` 명령을 사용하는 것입니다. `check` 명령은 [release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) 이상에서 사용할 수 있습니다.

>[!NOTE]
>문제 해결 도구는 IoT Edge 디바이스가 프록시 서버 뒤에 있는 경우 연결 확인을 실행할 수 없습니다.

다음과 같이 `check` 명령을 실행하거나 `--help` 플래그를 포함하여 전체 옵션 목록을 볼 수 있습니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Linux에서:

```bash
sudo iotedge check
```

Windows에서:

```powershell
iotedge check
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.1 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge check
```

:::moniker-end
<!-- end 1.2 -->

문제 해결 도구는 다음과 같은 세 가지 범주로 정렬된 많은 검사를 실행합니다.

* *구성 검사* 는 구성 파일 및 컨테이너 엔진과 관련된 문제를 포함하여 IoT Edge 디바이스가 클라우드에 연결되지 못하게 하는 세부 정보를 검사합니다.
* *연결 확인* 은 IoT Edge 런타임이 호스트 디바이스의 포트에 액세스할 수 있고 모든 IoT Edge 구성 요소가 IoT Hub에 연결할 수 있는지 확인합니다. IoT Edge 디바이스가 프록시 뒤에 있는 경우 이 검사 집합은 오류를 반환합니다.
* *프로덕션 준비 검사* 는 디바이스 CA(인증 기관) 인증서 및 모듈 로그 파일 구성의 상태와 같은 권장 프로덕션 모범 사례를 확인합니다.

IoT Edge 검사 도구는 컨테이너를 사용하여 진단을 실행합니다. 컨테이너 이미지 `mcr.microsoft.com/azureiotedge-diagnostics:latest`는 [Microsoft Container Registry](https://github.com/microsoft/containerregistry)를 통해 사용할 수 있습니다. 인터넷에 직접 액세스하지 않고 디바이스에서 검사를 실행해야 하는 경우 디바이스에서 컨테이너 이미지에 액세스해야 합니다.

<!-- <1.2> -->
:::moniker range=">=iotedge-2020-11"

중첩된 IoT Edge 디바이스를 사용하는 시나리오에서는 부모 디바이스를 통해 이미지 풀을 라우팅하여 자식 디바이스에 있는 진단 이미지에 액세스할 수 있습니다.

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:<port_for_api_proxy_module>/azureiotedge-diagnostics:1.2
```

<!-- </1.2> -->
:::moniker-end

오류 또는 경고가 발생하는 경우 수행할 작업을 포함하여 이 도구를 실행하는 각 진단 검사에 대한 자세한 내용은 [IoT Edge 문제 해결 확인](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)을 참조하세요.

## <a name="gather-debug-information-with-support-bundle-command"></a>'support-bundle' 명령을 사용하여 디버그 정보 수집

IoT Edge 디바이스에서 로그를 수집해야 하는 경우 가장 편리한 방법은 `support-bundle` 명령을 사용하는 것입니다. 기본적으로 이 명령은 모듈, IoT Edge 보안 관리자 및 컨테이너 엔진 로그, `iotedge check` JSON 출력 및 기타 유용한 디버그 정보를 수집합니다. 쉽게 공유할 수 있도록 단일 파일로 압축합니다. `support-bundle` 명령은 [release 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) 이상에서 사용할 수 있습니다.

`--since` 플래그를 사용하여 `support-bundle` 명령을 실행하여 과거부터 로그를 가져올 기간을 지정합니다. 예를 들어 `6h`는 지난 6시간 이후, `6d`는 지난 6일 이후, `6m`은 지난 6분 후의 로그를 가져옵니다. `--help` 플래그를 포함하여 전체 옵션 목록을 표시합니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Linux에서:

```bash
sudo iotedge support-bundle --since 6h
```

Windows에서:

```powershell
iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.2 -->

기본적으로 `support-bundle` 명령은 명령이 호출되는 디렉터리에 **support_bundle.zip** 이라는 zip 파일을 만듭니다. `--output` 플래그를 사용하여 출력에 대해 다른 경로 또는 파일 이름을 지정합니다.

명령에 대한 자세한 내용은 해당 도움말 정보를 확인하세요.

```bash/cmd
iotedge support-bundle --help
```

기본 제공되는 직접 메서드 호출 [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)을 사용하여 Azure Blob Storage에 대한 support-bundle 명령의 출력을 업로드할 수도 있습니다.

> [!WARNING]
> `support-bundle` 명령의 출력에는 호스트, 디바이스 및 모듈 이름, 모듈에 의해 기록된 정보 등이 포함될 수 있습니다. 공개 포럼의 출력을 공유하는 경우 이 점에 유의하세요.

## <a name="review-metrics-collected-from-the-runtime"></a>런타임에서 수집된 메트릭 검토

IoT Edge 런타임 모듈은 IoT Edge 디바이스의 상태를 모니터링하고 이해하는 데 도움이 되는 메트릭을 생성합니다. **메트릭 수집기** 모듈을 배포에 추가하여 이러한 메트릭 수집을 처리하고 더 쉽게 모니터링할 수 있도록 클라우드에 보냅니다.

자세한 내용은 [메트릭 수집 및 전송](how-to-collect-and-transport-metrics.md)을 참조하세요.

## <a name="check-your-iot-edge-version"></a>IoT Edge 버전 확인

이전 버전의 IoT Edge를 실행하는 경우 업그레이드하면 문제가 해결될 수 있습니다. `iotedge check` 도구는 IoT Edge 보안 디먼이 최신 버전인지 확인하지만, IoT Edge 허브 및 에이전트 모듈의 버전을 확인하지는 않습니다. 디바이스에서 런타임 모듈의 버전을 확인하려면 `iotedge logs edgeAgent` 및 `iotedge logs edgeHub` 명령을 사용합니다. 버전 번호는 모듈이 시작될 때 로그에서 선언됩니다.

디바이스를 업데이트하는 방법에 대한 지침은 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>디바이스에 IoT Edge 설치 확인

[edgeAgent 모듈 쌍을 모니터링](./how-to-monitor-module-twins.md)하여 디바이스에 IoT Edge 설치를 확인할 수 있습니다.

최신 edgeAgent 모듈 쌍을 가져오려면 [Azure Cloud Shell](https://shell.azure.com/)에서 다음 명령을 실행합니다.

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name>
   ```

이 명령은 모든 edgeAgent [reported 속성](./module-edgeagent-edgehub.md)을 출력합니다. 다음은 디바이스 상태를 모니터링하는 데 도움이 되는 몇 가지 유용한 항목입니다.

* 런타임 상태
* 런타임 시작 시간
* 런타임 마지막 종료 시간
* 런타임 다시 시작 횟수

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>IoT Edge 보안 관리자의 상태 및 해당 로그 확인

[IoT Edge 보안 관리자](iot-edge-security-manager.md)는 시작 및 프로비저닝 디바이스에서 IoT Edge 시스템을 초기화하는 등의 작업을 담당합니다. IoT Edge를 시작하지 않는 경우 보안 관리자 로그에서 유용한 정보를 제공할 수 있습니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Linux에서:

* IoT Edge 보안 관리자의 상태를 확인합니다.

   ```bash
   sudo systemctl status iotedge
   ```

* IoT Edge 보안 관리자의 로그를 확인합니다.

   ```bash
   sudo journalctl -u iotedge -f
   ```

* IoT Edge 보안 관리자의 더욱 자세한 로그를 확인합니다.

  1. IoT Edge 디먼 설정을 편집합니다.

     ```bash
     sudo systemctl edit iotedge.service
     ```

  2. 다음 줄을 업데이트합니다.

     ```bash
     [Service]
     Environment=IOTEDGE_LOG=debug
     ```

  3. IoT Edge 보안 디먼을 다시 시작합니다.

     ```bash
     sudo systemctl cat iotedge.service
     sudo systemctl daemon-reload
     sudo systemctl restart iotedge
     ```

Windows에서:

* IoT Edge 보안 관리자의 상태를 확인합니다.

   ```powershell
   Get-Service iotedge
   ```

* IoT Edge 보안 관리자의 로그를 확인합니다.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* IoT Edge 보안 관리자 로그의 최근 5분만 확인합니다.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* IoT Edge 보안 관리자의 더욱 자세한 로그를 확인합니다.

  1. 시스템 수준 환경 변수를 추가합니다.

     ```powershell
     [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
     ```

  2. IoT Edge 보안 디먼을 다시 시작합니다.

     ```powershell
     Restart-Service iotedge
     ```

:::moniker-end
<!--end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* IoT Edge 시스템 서비스의 상태를 확인합니다.

   ```bash
   sudo iotedge system status
   ```

* IoT Edge 시스템 서비스의 로그를 확인합니다.

   ```bash
   sudo iotedge system logs -- -f
   ```

* 디버그 수준 로그를 사용하여 IoT Edge 시스템 서비스에 대한 자세한 로그를 확인합니다.

  1. 디버그 수준 로그를 사용하도록 설정합니다.

     ```bash
     sudo iotedge system set-log-level debug
     sudo iotedge system restart
     ```

  1. 디버깅 후 기본 정보 수준 로그로 다시 전환합니다.

     ```bash
     sudo iotedge system set-log-level info
     sudo iotedge system restart
     ```

:::moniker-end
<!-- end 1.2 -->

## <a name="check-container-logs-for-issues"></a>문제에 대한 컨테이너 로그 확인

IoT Edge 보안 디먼이 실행되면 컨테이너의 로그를 확인하여 문제를 검색합니다. 배포된 컨테이너부터 살펴본 후, IoT Edge 런타임을 구성하는 컨테이너(edgeAgent 및 edgeHub)를 살펴봅니다. IoT Edge 에이전트 로그는 일반적으로 각 컨테이너의 수명 주기에 대한 정보를 제공합니다. IoT Edge 허브 로그는 메시징 및 라우팅에 대한 정보를 제공합니다.

컨테이너 로그는 여러 위치에서 검색할 수 있습니다.

* IoT Edge 디바이스에서 다음 명령을 실행하여 로그를 확인합니다.

  ```cmd
  iotedge logs <container name>
  ```

* Azure Portal에서 기본 제공 문제 해결 도구를 사용합니다. [Azure Portal에서 IoT Edge 디바이스 모니터링 및 문제 해결](troubleshoot-in-portal.md)

* [UploadModuleLogs 직접 메서드](how-to-retrieve-iot-edge-logs.md#upload-module-logs)를 사용하여 모듈의 로그를 Azure Blob Storage에 업로드합니다.

## <a name="clean-up-container-logs"></a>컨테이너 로그 정리

기본적으로 Moby 컨테이너 엔진은 컨테이너 로그 크기 제한을 설정하지 않습니다. 시간이 지남에 따라 로그를 채우고 디스크 공간이 부족해질 수 있습니다. 컨테이너 로그가 커서 IoT Edge 디바이스 성능에 영향이 있는 경우 다음 명령을 사용하여 컨테이너와 관련 로그를 강제 제거합니다.

그래도 문제가 있으면 컨테이너 로그를 검사한 뒤 잠시 기다렸다가 이 단계를 수행합니다.

>[!WARNING]
>배달되지 않은 메시지 백로그가 있고 [호스트 스토리지가](how-to-access-host-storage-from-module.md) 설정되지 않은 상태에서 edgeHub 컨테이너를 강제로 제거하면 배달되지 않은 메시지가 손실됩니다.

```cmd
docker rm --force <container name>
```

진행 중인 로그 유지 관리 및 프로덕션 시나리오의 경우 [로그 크기에 제한을 적용합니다.](production-checklist.md#place-limits-on-log-size)

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>IoT Edge 허브를 따라 메시지 확인

<!--1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge 허브에 표시되는 메시지를 확인하고, 런타임 컨테이너의 자세한 로그에서 인사이트를 수집할 수 있습니다. 이러한 컨테이너에서 자세한 로그를 켜려면 yaml 구성 파일에서 `RuntimeLogLevel`을 설정합니다. 파일을 열려면:

Linux에서:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows에서:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

기본적으로 `agent` 요소는 다음 예제와 같습니다.

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.1
       auth: {}
   ```

`env: {}`을 다음으로 바꿉니다.

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML 파일은 들여쓰기로 탭을 포함할 수 없습니다. 2 공백을 대신 사용합니다. 최상위 항목에는 선행 공백이 있을 수 없습니다.

파일을 저장하고 IoT Edge 보안 관리자를 다시 시작합니다.

<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 허브에 표시되는 메시지를 확인하고, 런타임 컨테이너의 자세한 로그에서 인사이트를 수집할 수 있습니다. 이러한 컨테이너에서 자세한 로그를 켜려면 배포 매니페스트에서 `RuntimeLogLevel` 환경 변수를 설정합니다.

IoT Edge 허브에 표시되는 메시지를 보려면 edgeHub 모듈에 대해 `debug`로 `RuntimeLogLevel` 환경 변수를 설정합니다.

edgeHub 및 edgeAgent 모듈에는 모두 이 런타임 로그 환경 변수가 있으며 기본값은 `info`로 설정됩니다. 이 환경 변수는 다음 값을 사용할 수 있습니다.

* 심각한
* error
* warning
* 정보
* debug
* verbose

<!-- end 1.2 -->
:::moniker-end

IoT Hub 및 IoT 디바이스 간에 전송되는 메시지를 확인할 수도 있습니다. [Visual Studio Code용 Azure IoT Hub 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 이러한 메시지를 확인합니다. 자세한 내용은 [Azure IoT로 개발할 때 사용할 수 있는 편리한 도구](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)를 참조하세요.

## <a name="restart-containers"></a>컨테이너 다시 시작

로그 및 메시지에서 정보를 검토한 후에는 컨테이너를 다시 시작할 수 있습니다.

IoT Edge 디바이스에서 다음 명령을 사용하여 모듈을 다시 시작합니다.

```cmd
iotedge restart <container name>
```

IoT Edge 런타임 컨테이너를 다시 시작합니다.

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

Azure Portal에서 원격으로 모듈을 다시 시작할 수도 있습니다. 자세한 내용은 [Azure Portal에서 IoT Edge 디바이스 모니터링 및 문제 해결](troubleshoot-in-portal.md)을 참조하세요.

## <a name="check-your-firewall-and-port-configuration-rules"></a>방화벽 및 포트 구성 규칙 확인

Azure IoT Edge는 지원되는 IoT Hub 프로토콜을 사용하여 온-프레미스 서버에서 Azure 클라우드로의 통신을 허용합니다. [통신 프로토콜 선택](../iot-hub/iot-hub-devguide-protocols.md)을 참조하세요. 보안 향상된을 위해 Azure IoT Edge 및 Azure IoT Hub 간의 통신 채널은 항상 아웃바운드되도록 구성됩니다. 이 구성은 [서비스 보조 통신 패턴](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices)을 기반으로 합니다. 그러면 탐색할 악의적인 엔터티에 대한 공격 노출 영역을 최소화합니다. 인바운드 통신은 Azure IoT Hub에서 메시지를 Azure IoT Edge 디바이스에 푸시해야 하는 특정 시나리오에만 필요합니다. 클라우드-디바이스 메시지는 보안 TLS 채널을 사용하여 보호되고 X.509 인증서 및 TPM 디바이스 모듈을 사용하여 추가로 보호될 수 있습니다. Azure IoT Edge 보안 관리자는 이 통신을 설정하는 방법을 관리합니다. [IoT Edge 보안 관리자](../iot-edge/iot-edge-security-manager.md)를 참조하세요.

IoT Edge는 Azure IoT Edge 런타임 및 배포된 모듈을 보호하기 위해 향상된 구성을 제공하지만, 기본 컴퓨터 및 네트워크 구성에 여전히 종속됩니다. 따라서 보안 에지-클라우드 통신을 위해 적절한 네트워크 및 방화벽 규칙을 설정해야 합니다. Azure IoT Edge 런타임이 호스트되는 기본 서버의 방화벽 규칙을 구성할 때 다음 표를 참조할 수 있습니다.

|프로토콜|포트|수신|나가는 포트|지침|
|--|--|--|--|--|
|MQTT|8883|BLOCKED(기본값)|BLOCKED(기본값)|<ul> <li>통신 프로토콜로 MQTT를 사용하는 경우 발신(아웃바운드)이 Open이 되도록 구성합니다.<li>MQTT에 대한 1883은 IoT Edge에서 지원되지 않습니다. <li>수신(인바운드) 연결을 차단해야 합니다.</ul>|
|AMQP|5671|BLOCKED(기본값)|OPEN(기본값)|<ul> <li>IoT Edge의 기본 통신 프로토콜입니다. <li> Azure IoT Edge는 지원되는 다른 프로토콜에 대해 구성되지 않았거나 AMQP가 원하는 통신 프로토콜인 경우 Open으로 구성해야 합니다.<li>AMQP에 대한 5672는 IoT Edge에서 지원되지 않습니다.<li>Azure IoT Edge가 다른 IoT Hub 지원 프로토콜을 사용하는 경우 이 포트를 차단합니다.<li>수신(인바운드) 연결을 차단해야 합니다.</ul></ul>|
|HTTPS|443|BLOCKED(기본값)|OPEN(기본값)|<ul> <li>IoT Edge 프로비전을 위해 443에서 발신(아웃바운드)을 보내는 열기로 구성합니다. 수동 스크립트 또는 Azure IoT DPS(디바이스 프로비저닝 서비스)를 사용하는 경우 이 구성이 필요합니다. <li>다음과 같은 특정 시나리오의 경우에만 수신(인바운드) 연결이 Open 상태여야 합니다. <ul> <li>  메서드 요청을 보낼 수 있는 리프 디바이스에 대한 투명 게이트웨이가 있는 경우. 이 경우 IoTHub에 연결하거나 Azure IoT Edge를 통해 IoTHub 서비스를 제공하기 위해 포트 443을 외부 네트워크로 열어둘 필요가 없습니다. 따라서 수신 규칙은 내부 네트워크에서의 수신(인바운드)만 열도록 제한될 수 있습니다. <li> 클라이언트-디바이스(C2D) 시나리오의 경우</ul><li>HTTP에 대한 80은 IoT Edge에서 지원되지 않습니다.<li>비 HTTP 프로토콜(예: AMQP 또는 MQTT)을 엔터프라이즈에서 구성할 수 없는 경우 WebSockets을 통해 메시지를 보낼 수 있습니다. 이 경우 WebSocket 통신에 포트 443이 사용됩니다.</ul>|

## <a name="next-steps"></a>다음 단계

IoT Edge 플랫폼에서 버그를 찾았나요? 지속적인 제품 개선을 위해 [문제를 제출](https://github.com/Azure/iotedge/issues)하세요.

추가 질문이 있으면 [지원 요청](https://portal.azure.com/#create/Microsoft.Support)을 만들어 도움을 받으세요.
