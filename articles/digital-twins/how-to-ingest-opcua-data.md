---
title: Azure Digital Twins를 사용하여 OPC UA 데이터 수집
titleSuffix: Azure Digital Twins
description: Azure OPC UA 데이터를 Azure Digital Twins로 가져오는 단계
author: danhellem
ms.author: dahellem
ms.date: 5/20/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b191bdb1303ae0210573d295ffb5b371cc81ddfb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567274"
---
# <a name="ingesting-opc-ua-data-with-azure-digital-twins"></a>Azure Digital Twins를 사용하여 OPC UA 데이터 수집

[OPC UA(OPC 통합 아키텍처)](https://opcfoundation.org/about/opc-technologies/opc-ua/)는 제조 공간의 플랫폼 독립적 서비스 지향 아키텍처입니다. 디바이스에서 원격 분석 데이터를 가져오는 데 사용됩니다. 

OPC UA 서버 데이터를 Azure Digital Twins에 흐르게 하려면 여러 디바이스에 여러 구성 요소를 설치해야 하고 일부 사용자 지정 코드와 설정을 구성해야 합니다. 

이 문서에서는 이러한 모든 부분을 함께 연결하여 OPC UA 노드를 Azure Digital Twins로 가져오는 방법을 보여 줍니다. 이 지침을 통해 사용자 고유의 솔루션에 대한 빌드를 계속할 수 있습니다.

> [!NOTE]
> 이 문서에서는 OPC UA 노드를 DTDL로 변환하지 않습니다. OPC UA 서버에서 기존 Azure Digital Twins로 원격 분석을 가져오는 것만을 다룹니다. OPC UA 데이터에서 DTDL 모델을 생성하는 데 관심이 있다면 [UANodeSetWebViewer](https://github.com/barnstee/UANodesetWebViewer) 및 [OPCUA2DTDL](https://github.com/khilscher/OPCUA2DTDL) 리포지토리를 방문하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하기 전에 다음 필수 구성 요소를 완료하세요.
* **샘플 리포지토리 다운로드**: 이 문서에서는 [OPC UA에서 Azure Digital Twins GitHub 리포지토리로](https://github.com/Azure-Samples/opcua-to-azure-digital-twins) [DTDL 모델](concepts-models.md) 파일 및 Azure 함수 본문을 사용합니다. 먼저 컴퓨터에 샘플 리포지토리를 다운로드합니다. 리포지토리의 **코드** 단추를 선택하여 리포지토리를 복제하거나 컴퓨터에 .zip 파일로 다운로드할 수 있습니다.

    :::image type="content" source="media/how-to-ingest-opcua-data/download-repo.png" alt-text="GitHub에 대한 digital-twins-samples 리포지토리의 스크린샷. 코드를 복제 또는 다운로드하는 단계를 강조 표시합니다." lightbox="media/how-to-ingest-opcua-data/download-repo.png":::
    
    리포지토리를 .zip으로 다운로드하는 경우 파일의 압축을 풀고 파일을 추출해야 합니다.
* **Visual Studio 다운로드**: 이 문서에서는 Visual Studio를 사용하여 Azure 함수를 게시합니다. [Visual Studio 다운로드](https://visualstudio.microsoft.com/downloads/)에서 최신 버전의 Visual Studio를 다운로드할 수 있습니다.

## <a name="architecture"></a>Architecture

이 솔루션에 포함될 구성 요소는 다음과 같습니다.

 :::image type="content" source="media/how-to-ingest-opcua-data/opcua-to-adt-diagram-1.png" alt-text="Azure Digital Twins 아키텍처로의 OPC UA 그림" lightbox="media/how-to-ingest-opcua-data/opcua-to-adt-diagram-1.png":::    

| 구성 요소 | Description |
| --- | --- |
| OPC UA 서버 | OPC UA 데이터를 시뮬레이트하는 [ProSys](https://www.prosysopc.com/products/opc-ua-simulation-server/) 또는 [Kepware](https://www.kepware.com/en-us/products/#KEPServerEX)의 OPC UA 서버입니다. |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) | IoT Edge는 로컬 Linux 게이트웨이 디바이스에 설치되는 IoT Hub 서비스입니다. OPC 게시자 모듈을 실행하고 IoT Hub에 데이터를 전송하는 데 필요합니다. |
| [OPC 게시자](https://github.com/Azure/iot-edge-opc-publisher) | Azure 산업용 IoT 팀에서 빌드한 IoT Edge 모듈입니다. 이 모듈은 OPC UA 서버에 연결하여 노드 데이터를 Azure IoT Hub로 보냅니다. |
| [Azure IoT Hub](../iot-hub/about-iot-hub.md) | OPC 게시자는 Azure IoT Hub에 OPC UA 원격 분석을 보냅니다. 여기에서부터 Azure Function 및 Azure Digital Twins를 통해 데이터를 처리할 수 있습니다. |
| Azure Digital Twins | 실제 사물, 장소, 비즈니스 프로세스 및 사람들의 디지털 표현을 만들 수 있는 플랫폼입니다. |
| [Azure 함수](../azure-functions/functions-overview.md) | 사용자 지정 Azure 함수를 사용하여 Azure IoT Hub에서 Azure Digital Twins의 적절한 트윈 및 속성으로 흐르는 원격 분석을 처리합니다. |

## <a name="set-up-edge-components"></a>에지 구성 요소 설정

첫 번째 단계는 에지에 설정된 디바이스 및 소프트웨어를 가져오는 것입니다. 다음은 순서대로 설정할 에지 구성 요소입니다.
1. [OPC UA 시뮬레이션 서버](#set-up-opc-ua-server)
1. [IoT Hub 및 IoT Edge 디바이스](#set-up-iot-edge-device)
1. [게이트웨이 디바이스](#set-up-gateway-device)

이 섹션에서는 각각에 대한 간략한 설정을 안내합니다. 

이러한 각 부분을 설치하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.
* [Azure IoT Edge에서 OPC 게시자를 설치하기 위한 단계별 가이드](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher) 
* [Linux에 IoT Edge 설치](../iot-edge/how-to-install-iot-edge.md) 
* [GitHub의 OPC 게시자](https://github.com/Azure/iot-edge-opc-publisher)
* [OPC 게시자 구성](/previous-versions/azure/iot-accelerators/howto-opc-publisher-configure)

### <a name="set-up-opc-ua-server"></a>OPC UA 서버 설정

이 문서에서는 실제 OPC UA 서버를 실행하는 물리적 디바이스에 액세스하지 않아도 됩니다. 대신, OPC UA 데이터를 생성하기 위해 Windows VM에 무료 [Prosys OPC UA 시뮬레이션 서버](https://www.prosysopc.com/products/opc-ua-simulation-server/)를 설치할 수 있습니다. 이 섹션에서는 이러한 과정을 안내합니다.

실제 OPC UA 디바이스 또는 사용하려는 다른 OPC UA 시뮬레이션 서버가 이미 있는 경우 다음 섹션인 [IoT Edge 디바이스 설정](#set-up-iot-edge-device)으로 이동할 수 있습니다.

#### <a name="create-windows-10-virtual-machine"></a>Windows 10 가상 머신 만들기

Prosys 소프트웨어에는 간단한 가상 리소스가 필요합니다. [Azure Portal](https://portal.azure.com)을 사용하여 다음 사양의 [Windows 10 VM(가상 머신)을 만듭니다](../virtual-machines/windows/quick-create-portal.md).
* **가용성 옵션**: 인프라 중복 필요 없음
* **이미지**: Windows 10 Pro, 버전 2004 - Gen2
* **크기**: Standard_B1s - 1 vcpu, 1GiB 메모리

:::image type="content" source="media/how-to-ingest-opcua-data/create-windows-virtual-machine-1.png" alt-text="Windows 가상 머신 설정의 기본 탭을 보여 주는 Azure Portal의 스크린샷." lightbox="media/how-to-ingest-opcua-data/create-windows-virtual-machine-1.png":::

인터넷을 통해 VM에 다시 연결할 수 있어야 합니다. 이 연습에서는 간단한 설명을 위해 모든 포트를 열고 VM에 공용 IP 주소를 할당할 수 있습니다. 가상 머신 설정의 **네트워킹** 탭에서 이 작업을 수행합니다.

:::image type="content" source="media/how-to-ingest-opcua-data/create-windows-virtual-machine-2.png" alt-text="Windows 가상 머신 설정의 네트워킹 탭을 보여 주는 Azure Portal의 스크린샷.":::

> [!WARNING]
> 인터넷에 대한 모든 포트를 여는 것은 보안 위험을 초래할 수 있으므로 프로덕션 솔루션에는 권장되지 않습니다. 사용자 환경에 대한 다른 보안 전략을 고려해 볼 수 있습니다.

다음 단계에서 사용할 **공용 IP** 값을 수집합니다.
 
VM 설정을 마칩니다.

#### <a name="install-opc-ua-simulation-software"></a>OPC UA 시뮬레이션 소프트웨어 설치

새 Windows 가상 머신에서 [Prosys OPC UA 시뮬레이션 서버](https://www.prosysopc.com/products/opc-ua-simulation-server/)를 설치합니다.

다운로드 및 설치가 완료되면 서버를 시작합니다. OPC UA 서버를 시작하는 데 몇 분 정도 걸릴 수 있습니다. 준비가 되면 서버 상태는 **실행 중** 으로 표시되어야 합니다.

다음으로, **연결 주소(UA TCP)** 의 값을 복사합니다. 나중에 사용할 수 있도록 안전한 위치에 붙여넣습니다. 붙여넣은 값에서 주소의 머신 이름 부분을 앞에서 설명한 VM의 **공용 IP** 로 바꿉니다. 예를 들면 다음과 같습니다. 

`opc.tcp://<ip-address>:53530/OPCUA/SimulationServer`

이 문서의 뒷부분에서 이 업데이트된 값을 사용할 것입니다.

마지막으로 **개체** 탭을 선택하고 Objects::FolderType 및 Simulation::FolderType 폴더를 확장하여 서버와 함께 기본적으로 제공되는 시뮬레이션 노드를 봅니다. 각각 고유한 `NodeId` 값을 포함한 시뮬레이션 노드가 표시됩니다. 

게시하려는 시뮬레이트된 노드의 `NodeId` 값을 캡처합니다. 이러한 ID는 이 문서의 뒷부분에서 이러한 노드의 데이터를 시뮬레이션하는 데 필요합니다.

> [!TIP]
> [Azure IoT Edge에 OPC 게시자를 설치하기 위한 단계별 가이드](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher)의 “OPC UA 서비스가 실행 중이고 연결 가능한지 확인”하는 단계를 수행하여 OPC UA 서버에 액세스할 수 있는지 확인합니다.

#### <a name="verify-completion"></a>확인 완료

이 섹션에서는 데이터를 시뮬레이트하기 위해 OPC UA 서버를 설정합니다. 다음 검사 목록을 완료했는지 확인합니다.

> [!div class="checklist"]
> * Prosys 시뮬레이션 서버가 설정되어 실행되고 있습니다.
> * UA TCP 연결 주소(`opc.tcp://<ip-address>:53530/OPCUA/SimulationServer`)를 복사했습니다.
> * 게시하려는 시뮬레이트된 노드에 대한 `NodeId` 목록을 캡처했습니다. 

### <a name="set-up-iot-edge-device"></a>IoT Edge 디바이스 설정

이 섹션에서는 IoT Hub 인스턴스와 IoT Edge 디바이스를 설정합니다. 

먼저 [Azure IoT Hub 인스턴스를 만듭니다](../iot-hub/iot-hub-create-through-portal.md). 이 문서의 경우 **F1 키를 사용하지 않는** 계층에서 인스턴스를 만들 수 있습니다.

:::image type="content" source="media/how-to-ingest-opcua-data/iot-hub.png" alt-text="IoT Hub의 속성을 보여 주는 Azure Portal의 스크린샷.":::

Azure IoT Hub 인스턴스를 만든 후 인스턴스의 왼쪽 탐색 메뉴에서 **IoT Edge** 를 선택하고 **IoT Edge 디바이스 추가** 를 선택합니다.

:::image type="content" source="media/how-to-ingest-opcua-data/iot-edge-1.png" alt-text="Azure Portal에서 IoT Edge 디바이스를 추가하는 스크린샷.":::

표시되는 메시지에 따라 새 디바이스를 만듭니다. 

디바이스를 만든 후에는 **기본 연결 문자열** 또는 **보조 연결 문자열** 값을 복사합니다. 이러한 작업은 나중에 에지 디바이스를 설정할 때 필요합니다.

:::image type="content" source="media/how-to-ingest-opcua-data/iot-edge-2.png" alt-text="IoT Edge 디바이스 연결 문자열을 보여 주는 Azure Portal의 스크린샷.":::

#### <a name="verify-completion"></a>확인 완료

이 섹션에서는 게이트웨이 디바이스를 만들기 위해 IoT Edge 및 IoT Hub를 설정합니다. 다음 검사 목록을 완료했는지 확인합니다.
> [!div class="checklist"]
> * IoT Hub 인스턴스가 생성되었습니다.
> * IoT Edge 디바이스를 프로비전했습니다.

### <a name="set-up-gateway-device"></a>게이트웨이 디바이스 설정

OPC UA 서버 데이터를 IoT Hub로 가져오려면 OPC 게시자 모듈을 사용하여 IoT Edge를 실행하는 디바이스가 필요합니다. OPC 게시자는 OPC UA 노드 업데이트를 수신 대기하고 IoT Hub에 원격 분석을 JSON 형식으로 게시합니다.

#### <a name="create-ubuntu-server-virtual-machine"></a>Ubuntu Server 가상 머신 만들기

[Azure Portal](https://portal.azure.com)을 사용하여 다음 사양의 Ubuntu Server 가상 머신을 만듭니다.
* **가용성 옵션**: 인프라 중복 필요 없음
* **이미지**: Ubuntu Server 18.04 LTS - Gen1
* **크기**: Standard_B1ms - 1 vcpu, 2GiB 메모리
    - 기본 크기(Standard_b1s – vcpu, 1GiB 메모리)는 RDP에 대해 너무 느립니다. 2GiB 메모리로 업데이트하면 더 나은 RDP 환경이 제공됩니다.

:::image type="content" source="media/how-to-ingest-opcua-data/ubuntu-virtual-machine.png" alt-text="Ubuntu 가상 머신 설정을 보여 주는 Azure Portal의 스크린샷.":::

> [!NOTE]
> Ubuntu VM으로의 RDP를 선택하는 경우 지침에 따라 [Ubuntu에서 원격 데스크톱을 사용하도록 xrdp를 설치 및 구성](../virtual-machines/linux/use-remote-desktop.md)할 수 있습니다.

#### <a name="install-iot-edge-container"></a>IoT Edge 컨테이너 설치

지침에 따라 [Linux에 IoT Edge를 설치](../iot-edge/how-to-install-iot-edge.md)합니다.

설치가 완료되면 다음 명령을 실행하여 설치 상태를 확인합니다.

```bash
admin@gateway:~$ sudo iotedge check
```

이 명령은 여러 테스트를 실행하여 설치를 진행할 준비가 되었는지 확인합니다.

#### <a name="install-opc-publisher-module"></a>OPC 게시자 모듈 설치

다음으로, 게이트웨이 디바이스에 OPC 게시자 모듈을 설치합니다. 

먼저 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.iotedge-opc-publisher)에서 모듈을 가져옵니다.

:::image type="content" source="media/how-to-ingest-opcua-data/opc-publisher-1.png" alt-text="Azure Marketplace의 OPC 게시자 스크린샷.":::

그런 다음, [OPC 게시자 GitHub 리포지토리](https://github.com/Azure/iot-edge-opc-publisher)에 설명된 설치 단계에 따라 Ubuntu VM에 모듈을 설치합니다.

[컨테이너 만들기 옵션을 지정](https://github.com/Azure/iot-edge-opc-publisher#specifying-container-create-options-in-the-azure-portal)하는 단계에서 다음 json을 추가해야 합니다.

```JSON
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=/appdata/publishednodes.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

:::image type="content" source="media/how-to-ingest-opcua-data/opc-publisher-2.png" alt-text="OPC 게시자 컨테이너 만들기 옵션의 스크린샷.":::

>[!NOTE]
>위의 만들기 옵션은 대부분의 경우 변경 없이 작동해야 하지만, 지금까지 문서 지침과는 다른 사용자 고유의 게이트웨이 디바이스를 사용하는 경우에는 해당 상황에 맞게 설정을 조정해야 할 수도 있습니다.

표시되는 메시지의 나머지 부분을 따라 모듈을 만듭니다. 

약 15초 후에 IoT Edge 디바이스에서 실행되는 모든 모듈을 나열하는 게이트웨이 디바이스에서 `iotedge list` 명령을 실행할 수 있습니다. OPCPublisher 모듈이 작동하고 실행되고 있는 것을 확인할 수 있습니다.

:::image type="content" source="media/how-to-ingest-opcua-data/iotedge-list.png" alt-text="iotedge 목록 결과의 스크린샷.":::

마지막으로 `/iiotedge` 디렉터리로 이동하여 *publishednodes.json* 파일을 만듭니다. 파일의 ID는 [이전에 OPC 서버에서 수집한](#install-opc-ua-simulation-software) `NodeId` 값과 일치해야 합니다. 파일은 다음과 같아야 합니다.

```JSON
[
    {
        "EndpointUrl": "opc.tcp://20.185.195.172:53530/OPCUA/SimulationServer",
        "UseSecurity": false,
        "OpcNodes": [
            {
                "Id": "ns=3;i=1001"
            },
            {
                "Id": "ns=3;i=1002"
            },
            {
                "Id": "ns=3;i=1003"
            },
            {
                "Id": "ns=3;i=1004"
            },
            {
                "Id": "ns=3;i=1005"
            },
            {
                "Id": "ns=3;i=1006"
            }
        ]
    }
]
```

*publishednodes.json* 파일에 대한 변경 내용을 저장합니다.

그런 후 다음 명령을 실행합니다.

```bash
sudo iotedge logs OPCPublisher -f
```

명령은 OPC 게시자 로그의 출력을 생성합니다. 모든 항목이 올바르게 구성되고 실행되면 다음과 같은 내용이 표시됩니다.

:::image type="content" source="media/how-to-ingest-opcua-data/iotedge-logs.png" alt-text="터미널의 iotedge 로그 스크린샷. 왼쪽에는 진단 정보 필드 열이 있고 오른쪽에는 값 열이 있습니다.":::

이제 OPC UA 서버에서 IoT Hub로 데이터를 이동해야 합니다.

Azure IoT 허브로 이동하는 메시지를 모니터링하려면 다음 명령을 사용할 수 있습니다.

```azurecli-interactive
az iot hub monitor-events -n <iot-hub-instance> -t 0
```

> [!TIP]
> [Azure IoT 탐색기](../iot-fundamentals/howto-use-iot-explorer.md)를 사용하여 IoT Hub 메시지를 모니터링해 보세요.

#### <a name="verify-completion"></a>확인 완료

이 섹션에서는 OPC UA 서버에서 데이터를 수신하는 IoT Edge를 실행하는 게이트웨이 디바이스를 설정합니다. 다음 검사 목록을 완료했는지 확인합니다.
> [!div class="checklist"]
> * Ubuntu Server VM이 생성되었습니다.
> * IoT Edge가 설치되었으며 Ubuntu VM에 있습니다.
> * OPC 게시자 모듈이 설치되었습니다.
> * *publishednodes.json* 파일이 생성 및 구성되었습니다.
> * OPC 게시자 모듈이 실행 중이며 원격 분석 데이터가 IoT Hub로 이동하고 있습니다.

다음 단계에서는 Azure Digital Twins로 이 원격 분석 데이터를 가져옵니다.

## <a name="set-up-azure-digital-twins"></a>Azure Digital Twins 설정

OPC UA 서버에서 Azure IoT Hub로 이동하는 데이터를 만들었으므로 다음 단계는 Azure Digital Twins를 설정하고 구성하는 것입니다. 

이 예제에서는 단일 모델과 단일 트윈 인스턴스를 사용하여 시뮬레이션 서버의 속성과 일치시킵니다. 

>[!TIP]
>더 많은 모델과 트윈이 있는 더 복잡한 시나리오가 궁금하다면 [OPC UA에서 Azure Digital Twins GitHub 리포지토리로의](https://github.com/Azure-Samples/opcua-to-azure-digital-twins) 초콜릿 공장 예제를 확인하세요.

### <a name="create-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 만들기

먼저 [인스턴스 및 인증 설정](how-to-set-up-instance-portal.md)의 지침을 사용하여 새 Azure Digital Twins 인스턴스를 배포합니다.

### <a name="upload-model-and-create-twin"></a>모델 업로드 및 트윈 만들기

다음으로, 모델 및 트윈을 인스턴스에 추가합니다. 인스턴스에 업로드할 모델 파일은 [필수 구성 요소](#prerequisites) 섹션에서 다운로드한 샘플 프로젝트의 일부이며, *Simulation Example/simulation-dtdl-model.json* 에 있습니다.

[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md)를 사용하여 시뮬레이션 모델을 업로드하고, **simulation-1** 이라는 새 트윈을 만들 수 있습니다.

:::image type="content" source="media/how-to-ingest-opcua-data/azure-digital-twins-explorer.png" alt-text="시뮬레이션 모델 및 simulation-1 트윈을 보여 주는 Azure Digital Twins Explorer의 스크린샷.":::

### <a name="verify-completion"></a>확인 완료

이 섹션에서는 모델 및 트윈을 포함하는 Azure Digital Twins 인스턴스를 설정합니다. 다음 검사 목록을 완료했는지 확인합니다.
> [!div class="checklist"]
> * Azure Digital Twins 인스턴스가 배포되었습니다.
> * 시뮬레이션 모델이 Azure Digital Twins 인스턴스에 업로드되었습니다.
> * simulation-1 트윈이 시뮬레이션 모델에서 만들어졌습니다.

## <a name="set-up-azure-function"></a>Azure 함수 설정

이제 OPC UA 노드가 데이터를 IoT Hub로 전송하고 Azure Digital Twins 인스턴스에서 데이터를 받을 준비가 되었으므로 Azure Digital Twins의 올바른 트윈 및 속성에 데이터를 매핑하고 저장해야 합니다. 이 섹션에서는 Azure 함수 및 *opcua-mapping.json* 파일을 사용하여 이를 설정합니다.

이 섹션의 데이터 흐름은 다음 단계를 포함합니다.

1. Azure 함수는 이벤트 구독을 사용하여 IoT Hub에서 오는 메시지를 수신합니다.
1. Azure 함수는 도착하는 각 원격 분석 이벤트를 처리합니다. 이벤트에서 `NodeId`를 추출하고 *opcua-mapping.json 파일* 에 있는 항목을 찾습니다. 파일은 각 `NodeId`를 특정 `twinId` 및 노드 값을 저장해야 하는 Azure Digital Twins의 속성에 매핑합니다.
1. Azure 함수는 적절한 패치 문서를 생성하여 해당 디지털 트윈을 업데이트하고 트윈 속성 업데이트 명령을 실행합니다.

### <a name="create-opcua-mappingjson-file"></a>opcua-mapping.json 파일 만들기

먼저 *opcua-mapping.json* 파일을 만듭니다. 빈 JSON 파일을 시작하고, 아래 예제 및 스키마에 따라 `NodeId` 값을 `twinId` 값 및 Azure Digital Twins의 속성에 매핑하는 항목을 채웁니다. 모든 `NodeId`에 대해 매핑 항목을 만들어야 합니다.

```JSON
[
    {
        "NodeId": "1001",
        "TwinId": "simulation",
        "Property": "Counter",
        "ModelId": "dtmi:com:microsoft:iot:opcua:simulation;1"
    },
    ...
]
```

항목에 대한 스키마는 다음과 같습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| NodeId | OPC UA 노드의 값입니다. 예: ns=3;i={value} | ✔ |
| TwinId | 원격 분석 값을 저장하려는 트윈의 TwinId($dtId)입니다. | ✔ |
| 속성 | 원격 분석 값을 저장하려는 트윈의 속성 이름입니다. | ✔ |
| ModelId  | TwinId가 없는 경우 트윈을 만들 modelId입니다. |  |

> [!TIP]
> *opcua-mapping.json* 파일의 전체 예제는 [OPC UA에서 Azure Digital Twins GitHub 리포지토리로](https://github.com/Azure-Samples/opcua-to-azure-digital-twins)를 참조하세요.

매핑 추가가 완료되면 파일을 저장합니다.

이제 매핑 파일이 있으니 Azure 함수가 액세스할 수 있는 위치에 파일을 저장해야 합니다. 이러한 위치 중 하나는 [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)입니다.

지침에 따라 [스토리지 컨테이너를 만들고](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) *opcua-mapping.json* 파일을 컨테이너로 가져옵니다. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 스토리지 관리 이벤트를 수행할 수도 있습니다. 

다음으로, [컨테이너에 대한 공유 액세스 서명](../storage/common/storage-sas-overview.md)을 만들고 해당 URL을 저장합니다. 나중에 저장된 파일에 액세스할 수 있도록 Azure 함수에 URL을 제공합니다.

:::image type="content" source="media/how-to-ingest-opcua-data/azure-storage-explorer.png" alt-text="SAS 토큰을 만드는 대화 상자를 보여 주는 Azure Storage Explorer의 스크린샷.":::

### <a name="publish-azure-function"></a>Azure 함수 게시

이 섹션에서는 OPC UA 데이터를 처리하고 Azure Digital Twins를 업데이트하는 [필수 구성 요소](#prerequisites)에서 다운로드한 Azure 함수를 게시합니다.

1. 로컬 컴퓨터에 다운로드한 [OPC UA에서 Azure Digital Twins로](https://github.com/Azure-Samples/opcua-to-azure-digital-twins) 프로젝트로 이동한 후 *Azure Functions/OPCUAFunctions* 폴더로 이동합니다. Visual Studio에서 **OPCUAFunctions.sln** 솔루션을 엽니다.
2. Azure의 함수 앱에 프로젝트를 게시합니다. 이 작업을 수행하는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#publish-to-azure)을 참조하세요.

#### <a name="configure-the-function-app"></a>함수 앱 구성

다음으로, 함수에 대한 **액세스 역할을 할당** 하고 **애플리케이션 설정을 구성** 하여 Azure Digital twins 인스턴스에 액세스할 수 있도록 합니다.

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

#### <a name="add-application-settings"></a>애플리케이션 설정 추가

또한 환경 및 Azure 함수를 완전히 설정하려면 일부 애플리케이션 설정을 추가해야 합니다. [Azure Portal](https://portal.azure.com)로 이동하여 포털 검색 창에서 해당 이름을 검색하여 새로 만든 Azure 함수로 이동합니다.

함수의 왼쪽 탐색 메뉴에서 구성을 선택합니다. **+ 새 애플리케이션 설정** 단추를 사용하여 새 설정 만들기를 시작합니다.

:::image type="content" source="media/how-to-ingest-opcua-data/azure-function-settings-1.png" alt-text="Azure Portal의 Azure 함수에 애플리케이션 설정을 추가하는 스크린샷.":::

세 가지 애플리케이션 설정을 만들어야 합니다.

| 설정 | 설명 | 필수 |
| --- | --- | --- |
| ADT_SERVICE_URL | Azure Digital Twins 인스턴스에 대한 URL입니다. 예: `https://example.api.eus.digitaltwins.azure.net` | ✔ |
| JSON_MAPPINGFILE_URL | opcua-mapping.json에 대한 공유 액세스 서명의 URL | ✔ |
| LOG_LEVEL | 로그 수준 세부 정보 표시입니다. 기본값은 100입니다. 세부 정보 표시는 300입니다. | |

:::image type="content" source="media/how-to-ingest-opcua-data/azure-function-settings-2.png" alt-text="Azure Portal의 Azure 함수에 대한 애플리케이션 설정 스크린샷. 위의 설정이 추가되었습니다.":::

> [!TIP]
> 더 자세한 로깅 환경을 위해 함수의 `LOG_LEVEL` 애플리케이션 설정을 300으로 설정합니다. 

### <a name="create-event-subscription"></a>이벤트 구독 만들기

마지막으로 함수 앱 및 *ProcessOPCPublisherEventsToADT* 함수를 IoT Hub에 연결하는 이벤트 구독을 생성합니다. 게이트웨이 디바이스에서 함수를 통해 IoT Hub로 데이터가 흐를 수 있도록 이벤트 구독이 필요합니다. 그러면 Azure Digital Twins가 업데이트됩니다.

지침은 Azure Digital Twins *자습서: 엔드투엔드 솔루션 연결* 에서 [Azure 함수에 IoT 허브 연결](tutorial-end-to-end.md#connect-the-iot-hub-to-the-azure-function)에 사용된 것과 동일한 단계를 따릅니다.

이벤트 구독에는 엔드포인트 유형의 **Azure 함수** 및 **ProcessOPCPublisherEventsToADT** 의 엔드포인트가 있습니다.

:::image type="content" source="media/how-to-ingest-opcua-data/event-subscription.png" alt-text="새 이벤트 구독 만들기를 보여 주는 Azure Portal 스크린샷.":::

이 단계가 끝나면 모든 필수 구성 요소를 설치하고 실행해야 합니다. 데이터는 OPC UA 시뮬레이션 서버에서 Azure IoT Hub를 통해 Azure Digital Twins 인스턴스로 흐릅니다. 

### <a name="verify-completion"></a>확인 완료

이 섹션에서는 OPC UA 데이터를 Azure Digital Twins에 연결하는 Azure 함수를 설정합니다. 다음 검사 목록을 완료했는지 확인합니다.
> [!div class="checklist"]
> * *opcua-mapping.json* 파일을 만들고 Blob Storage 컨테이너로 가져왔습니다. 
> * 샘플 함수 *ProcessOPCPublisherEventsToADT* 를 Azure의 함수 앱에 게시했습니다.
> * Azure Functions 앱에 세 가지 새 애플리케이션 설정이 추가되었습니다.
> * 함수 앱에 IoT Hub 이벤트를 보내는 이벤트 구독을 만들었습니다.

다음 섹션에서는 이벤트를 모니터링하고 모든 것이 성공적으로 작동하는지 확인하기 위해 실행할 수 있는 몇 가지 Azure CLI 명령을 제공합니다.

## <a name="verify-and-monitor"></a>확인 및 모니터링

이 섹션의 명령은 [Azure Cloud Shell](https://shell.azure.com)또는 [로컬 Azure CLI 창](/cli/azure/install-azure-cli)에서 실행할 수 있습니다.

다음 명령을 실행하여 IoT Hub 이벤트를 모니터링합니다.
```azurecli-interactive
az iot hub monitor-events -n <IoT-hub-name> -t 0
```

Azure 함수 이벤트 처리를 모니터링하려면 다음 명령을 실행합니다.
```azurecli-interactive
az webapp log tail –name <function-name> --resource-group <resource-group-name>
```

마지막으로 Azure Digital Twins Explorer를 사용하여 트윈 속성 업데이트를 수동으로 모니터링할 수 있습니다. 

:::image type="content" source="media/how-to-ingest-opcua-data/adt-explorer-2.png" alt-text="Azure Digital Twins Explorer를 사용하여 트윈 속성 업데이트를 모니터링하는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 시뮬레이션된 OPC UA 서버 데이터를 디지털 트윈의 속성을 업데이트하는 Azure Digital Twins로 보내기 위해 전체 데이터 흐름을 설정합니다.

다음으로, 다음 리소스를 사용하여 이 문서에서 사용된 지원 도구 및 프로세스에 대해 자세히 읽어보세요.

* [Azure IoT Edge에서 OPC 게시자를 설치하기 위한 단계별 가이드](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher) 
* [Linux에 IoT Edge 설치](../iot-edge/how-to-install-iot-edge.md) 
* [OPC 게시자](https://github.com/Azure/iot-edge-opc-publisher)
* [OPC 게시자 구성](/previous-versions/azure/iot-accelerators/howto-opc-publisher-configure)
* [UANodeSetWebViewer](https://github.com/barnstee/UANodesetWebViewer) 
* [OPCUA2DTDL](https://github.com/khilscher/OPCUA2DTDL)