---
title: 자습서 - IoT Edge 디바이스의 계층 구조 만들기 - Azure IoT Edge
description: 이 자습서에서는 게이트웨이를 사용하여 IoT Edge 디바이스의 계층 구조를 만드는 방법을 보여줍니다.
author: v-tcassi
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 5c204a07a0457852d2faefacdd77740f147b9d88
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225994"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>자습서: IoT Edge 디바이스의 계층 구조 만들기

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

계층 구조의 계층으로 구성된 네트워크에 Azure IoT Edge 노드를 배포합니다. 계층 구조의 각 계층은 그 아래 계층에 있는 디바이스의 메시지와 요청을 처리하는 게이트웨이 디바이스입니다.

최상위 계층만 클라우드에 연결되고 하위 계층은 인접한 북쪽 및 남쪽 계층과만 통신할 수 있도록 디바이스의 계층 구조를 구성할 수 있습니다. 이러한 네트워크 계층화는 [ISA-95 standard](https://en.wikipedia.org/wiki/ANSI/ISA-95)를 따르는 대부분의 산업 네트워크의 기초입니다.

이 자습서의 목표는 간소화된 프로덕션 환경을 시뮬레이션하는 IoT Edge 디바이스의 계층 구조를 만드는 것입니다. 끝에는 [시뮬레이션된 온도 센서 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor)을 계층 구조를 통해 컨테이너 이미지를 다운로드하여 인터넷 액세스 없이 하위 계층 디바이스에 배포합니다.

이러한 목표를 달성하기 위해 이 자습서에서는 IoT Edge 디바이스의 계층 구조를 만들고, 디바이스에 IoT Edge 런타임 컨테이너를 배포하고, 로컬에서 디바이스를 구성하는 과정을 안내합니다. 이 자습서에서는 자동화된 구성 도구를 사용하여 다음을 수행합니다.

> [!div class="checklist"]
>
> * IoT Edge 디바이스의 계층 구조에서 관계를 만들고 정의합니다.
> * 계층 구조의 디바이스에서 IoT Edge 런타임을 구성합니다.
> * 디바이스 계층 구조 전체에 일관된 인증서를 설치합니다.
> * 계층 구조의 디바이스에 워크로드를 추가합니다.
> * [IoT Edge API 프록시 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview)을 사용하여 하위 계층 디바이스에서 단일 포트를 통해 HTTP 트래픽을 안전하게 라우팅합니다.

>[!TIP]
>이 자습서에는 중첩된 IoT Edge 기능을 소개하는 수동 및 자동 단계가 함께 포함되어 있습니다.
>
>IoT Edge 디바이스의 계층 구조 설정을 완전히 자동화하려면 스크립팅된 [산업용 IoT용 Azure IoT Edge 샘플](https://aka.ms/iotedge-nested-sample)을 따라 하면 됩니다. 이 스크립팅된 시나리오는 Azure 가상 머신을 미리 구성된 디바이스로 배포하여 공장 환경을 시뮬레이션합니다.
>
>IoT Edge 디바이스 계층 구조를 만들고 관리하는 수동 단계를 자세히 확인하려면 [IoT Edge 디바이스 게이트웨이 계층 구조에 대한 방법 가이드](how-to-connect-downstream-iot-edge-device.md)를 참조하세요.

이 자습서에서는 다음 네트워크 계층이 정의됩니다.

* **최상위 계층**: 이 계층의 IoT Edge 디바이스는 클라우드에 직접 연결할 수 있습니다.

* **하위 계층**: 최상위 계층 아래에 있는 계층의 IoT Edge 디바이스는 클라우드에 직접 연결할 수 없습니다. 데이터를 보내고 받으려면 하나 이상의 중간 IoT Edge 디바이스를 거쳐야 합니다.

이 자습서에서는 아래 그림과 같이 간단하게 두 개의 디바이스 계층 구조를 사용합니다. **최상위 계층 디바이스** 는 계층 구조의 최상위 계층에 있는 디바이스를 나타내며 클라우드에 직접 연결할 수 있습니다. 이 디바이스를 **부모 디바이스** 라고도 합니다. **하위 계층 디바이스** 는 계층 구조의 하위 계층에 있는 디바이스를 나타내며 클라우드에 직접 연결할 수 없습니다. 프로덕션 환경을 나타내는 하위 계층 디바이스를 필요에 따라 더 추가할 수 있습니다. 하위 계층의 디바이스를 **자식 디바이스** 라고도 합니다.

![두 개의 디바이스(최상위 계층 디바이스 및 하위 계층 디바이스)를 포함하는 자습서 계층 구조](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>필수 구성 요소

IoT Edge 디바이스의 계층 구조를 만들려면 다음이 필요합니다.

* 인터넷에 연결된 컴퓨터(Windows 또는 Linux)
* 유효한 구독이 있는 Azure 계정. [Azure 구독](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Azure IoT 확장 v0.10.6 이상이 설치된 Azure CLI v2.3.1을 사용하는 Azure Cloud Shell의 Bash 셸. 이 자습서에서는 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다. Azure Cloud Shell에 익숙하지 않은 경우 [빠른 시작에서 자세한 내용을 확인하세요.](./quickstart-linux.md#prerequisites)
  * Azure CLI 모듈 및 확장의 현재 버전을 보려면 [az version](/cli/azure/reference-index?#az_version)을 실행합니다.
* 계층의 각 디바이스에 대한 IoT Edge 디바이스로 구성할 Linux 디바이스입니다. 이 자습서에는 디바이스가 두 개 사용됩니다. 사용 가능한 디바이스가 없는 경우 아래 명령을 사용하여 계층 구조의 각 디바이스를 위한 Azure 가상 머신을 만들 수 있습니다.

   다음 명령에서 자리 표시자 텍스트를 바꾸고 각 가상 머신당 한 번씩, 총 두 번 실행합니다. 각 가상 머신에는 고유한 DNS 접두사가 필요하며, 이는 이름으로도 사용됩니다. DNS 접두사는 `[a-z][a-z0-9-]{1,61}[a-z0-9]` 정규식을 따라야 합니다.

   ```azurecli
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   가상 머신은 사용자를 인증하는 데 SSH 키를 사용합니다. SSH 키를 만들고 사용하는 방법을 잘 모르는 경우 [Azure의 Linux VM용 SSH 공개-프라이빗 키 쌍에 대한 지침](../virtual-machines/linux/mac-create-ssh-keys.md)을 따를 수 있습니다.

   이 ARM 템플릿에는 IoT Edge 버전 1.2가 미리 설치되어 있으므로 가상 머신에 수동으로 자산을 설치할 필요가 없습니다. 사용자 고유의 디바이스에 IoT Edge를 설치하는 경우 [Linux용 Azure IoT Edge 설치(버전 1.2)](how-to-provision-single-device-linux-symmetric.md) 또는 [IoT Edge를 버전 1.2로 업데이트](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)를 참조하세요.

   이 ARM 템플릿을 사용하여 가상 머신을 성공적으로 만들면 가상 머신의 `SSH` 핸들과 정규화된 도메인 이름(`FQDN`)이 출력됩니다. 이후 단계에서 구성 시 각 가상 머신의 SSH 핸들과 FQDN 또는 IP 주소를 사용하므로 이 정보를 기록해 두세요. 샘플 출력은 아래 그림과 같습니다.

   >[!TIP]
   >Azure Portal에서 IP 주소 및 FQDN을 확인할 수도 있습니다. IP 주소를 보려면 가상 머신 목록으로 이동하고 **공용 IP 주소 필드** 를 확인합니다. FQDN을 보려면 각 가상 머신의 개요 페이지로 이동하고 **DNS 이름** 필드를 찾습니다.

   ![가상 머신이 생성될 때 출력하는 JSON에 SSH 핸들이 포함되어 있습니다.](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* 최저 레이어 디바이스(443, 5671, 8883)를 제외한 모든 디바이스에서 다음 포트가 인바운드로 개방되어 있는지 확인합니다.
  * 443: REST API 호출 및 docker 컨테이너 이미지를 끌어오기 위한 부모 및 자식 에지 허브 간에 사용됩니다.
  * 5671, 8883: AMQP 및 MQTT에 사용됩니다.

  자세한 내용은 [Azure Portal을 사용하여 가상 머신에 대한 포트를 여는 방법](../virtual-machines/windows/nsg-quickstart-portal.md)을 참조하세요.

## <a name="configure-your-iot-edge-device-hierarchy"></a>IoT Edge 디바이스 계층 구조 구성

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>IoT Edge 디바이스의 계층 구조 만들기

IoT Edge 디바이스는 계층 구조의 계층을 구성합니다. 이 자습서에서는 두 개의 IoT Edge 디바이스(**최상위 계층 디바이스** 및 그 자식 디바이스인 **하위 계층 디바이스**)로 구성된 계층 구조를 만듭니다. 필요에 따라 추가 자식 디바이스를 만들 수 있습니다.

IoT Edge 디바이스의 계층 구조를 만들고 구성하려면 `iotedge-config` 도구를 사용합니다. 이 도구는 여러 단계를 자동화하고 두 단계로 압축하여 계층 구조 구성을 간소화합니다.

1. 다음과 같이 클라우드 구성을 설정하고 각 디바이스 구성 준비

   * IoT Hub의 디바이스 만들기
   * 부모-자식 관계를 설정하여 디바이스 간 통신 권한 부여
   * 디바이스 간에 보안 통신을 설정하기 위해 각 디바이스에 대한 인증서 체인 생성
   * 각 디바이스의 구성 파일 생성

1. 다음을 포함하는 각 디바이스 구성 설치

   * 각 디바이스에 인증서 설치
   * 각 디바이스의 구성 파일 적용

또한 `iotedge-config` 도구는 IoT Edge 디바이스에 모듈을 자동으로 배포합니다.

`iotedge-config` 도구를 사용하여 계층 구조를 만들고 구성하려면 Azure CLI에서 다음 단계를 수행합니다.

1. [Azure Cloud Shell](https://shell.azure.com/)에서 자습서의 리소스에 대한 디렉터리를 만듭니다.

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. 구성 도구 및 구성 템플릿의 릴리스를 다운로드합니다.

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   그러면 자습서 디렉터리에 `iotedge_config_cli_release` 폴더가 생성됩니다.

   디바이스 계층 구조를 만드는 데 사용되는 템플릿 파일은 `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial`에 있는 `iotedge_config.yaml` 파일입니다. 동일한 디렉터리에서 `deploymentLowerLayer.json`은 **하위 레이어 디바이스** 에 배포하는 모듈에 대한 지침을 포함하는 JSON 배포 파일입니다. 각 디바이스에 배포된 모듈이 동일하지는 않으므로 `deploymentTopLayer.json` 파일은 **최상위 레이어 디바이스** 를 제외하고는 동일합니다. `device_config.toml` 파일은 IoT Edge 디바이스 구성 템플릿으로, 계층 구조의 디바이스에 대한 구성 번들을 자동으로 생성하는 데 사용됩니다.

   `iotedge-config` 도구의 소스 코드 및 스크립트를 보려면 [GitHub의 Azure-Samples 리포지토리](https://github.com/Azure-Samples/iotedge_config_cli)를 확인하세요.

1. 자습서 구성 템플릿을 열고 사용자 정보로 편집합니다.

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   **iothub** 섹션에서 `iothub_hostname` 및 `iothub_name` 필드를 사용자 정보로 채웁니다. 이 정보는 Azure Portal의 IoT Hub 개요 페이지에서 찾을 수 있습니다.

   선택적 **인증서** 섹션에서 인증서 및 키의 절대 경로로 필드를 채울 수 있습니다. 이러한 필드를 비워 두면 스크립트에서 사용자가 사용할 자체 서명 테스트 인증서를 자동으로 생성합니다. 게이트웨이 시나리오에서 인증서를 사용하는 방법을 잘 모를 경우 [방법 가이드의 인증서 섹션](how-to-connect-downstream-iot-edge-device.md#prepare-certificates)을 확인하세요.

   **구성** 섹션에서 `template_config_path`는 디바이스 구성을 만드는 데 사용되는 `device_config.toml` 템플릿의 경로입니다. `default_edge_agent` 필드는 하위 레이어 디바이스에서 어떤 Edge Agent 이미지를 어디서 끌어올지 결정합니다.

   프로덕션 시나리오의 경우 **edgedevices** 섹션에서 원하는 구조를 반영하도록 계층 트리를 편집할 수 있습니다. 이 자습서에서는 기본 트리를 유지합니다. 각 디바이스에는 디바이스 이름을 지정할 수 있는 `device_id` 필드가 있습니다. 또한 해당 디바이스의 배포 JSON 경로를 지정하는 `deployment` 필드도 있습니다.

   Azure Portal, Azure Cloud Shell 또는 Visual Studio Code를 통해 IoT Hub에 IoT Edge 디바이스를 수동으로 등록할 수도 있습니다. 방법을 알아보려면 [Linux IoT Edge 디바이스를 수동으로 프로비저닝하는 방법에 대한 엔드투엔드 가이드의 시작](how-to-provision-single-device-linux-symmetric.md#register-your-device)을 참조하세요.

   부모-자식 관계도 수동으로 정의할 수 있습니다. 자세히 알아보려면 방법 가이드의 [게이트웨이 계층 구조 만들기](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) 섹션을 참조하세요.

   ![구성 파일의 edgedevices 섹션을 사용하여 계층 구조를 정의할 수 있습니다.](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. 파일을 저장하고 닫습니다.

   `CTRL + S`, `CTRL + Q`

1. 자습서 리소스 디렉터리에서 구성 번들의 출력 디렉터리를 만듭니다.

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. `iotedge_config_cli_release` 디렉터리로 이동하고 도구를 실행하여 IoT Edge 디바이스의 계층 구조를 만듭니다.

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   `--output` 플래그를 사용하면 이 도구가 선택된 디렉터리에 디바이스 인증서, 인증서 번들 및 로그 파일을 만듭니다. `-f` 플래그를 설정하면 이 도구가 IoT Hub에서 기존 IoT Edge 디바이스를 자동으로 찾아서 제거하여 오류를 방지하고 허브를 깔끔하게 유지합니다.

   구성 도구는 IoT Edge 디바이스를 만들고 둘 간에 부모-자식 관계를 설정합니다. 필요에 따라 디바이스에서 사용할 인증서를 만듭니다. 배포 JSON의 경로를 지정하면 이 도구가 디바이스에 이러한 배포를 자동으로 만들지만 필수는 아닙니다. 마지막으로, 이 도구는 디바이스의 구성 번들을 생성하고 출력 디렉터리에 저장합니다. 구성 도구에서 수행하는 단계를 자세히 확인하려면 출력 디렉터리의 로그 파일을 참조하세요.

   ![스크립트가 실행될 때 계층 구조의 토폴로지를 표시합니다.](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

스크립트의 토폴로지 출력이 올바른지 다시 한 번 확인합니다. 계층 구조가 올바르게 구성되었으면 다음 단계를 진행할 수 있습니다.

### <a name="configure-the-iot-edge-runtime"></a>IoT Edge 런타임 구성

구성 단계는 디바이스를 프로비전하는 이외에 앞서 만든 인증서를 사용하여 계층 구조의 디바이스 간에 신뢰할 수 있는 통신을 설정합니다. 또한 계층 구조의 네트워크 구조를 설정하기 시작합니다. 최상위 계층 디바이스는 인터넷 연결을 유지하여 클라우드에서 런타임의 이미지를 끌어오고, 하위 계층 디바이스는 최상위 계층 디바이스를 통해 라우팅하여 이러한 이미지에 액세스합니다.

IoT Edge 런타임을 구성하려면 설치 스크립트에서 만든 구성 번들을 디바이스에 적용해야 합니다. **최상위 레이어 디바이스** 와 **하위 레이어 디바이스** 는 구성이 약간 다르므로 각 디바이스에 어느 디바이스의 구성 파일을 적용하는지 잘 확인해야 합니다.

1. 각 디바이스에는 해당하는 구성 번들이 필요합니다. USB 드라이브 또는 [보안 파일 복사](https://www.ssh.com/ssh/scp/)를 사용하여 구성 번들을 각 디바이스로 이동할 수 있습니다.

   각 디바이스에 올바른 구성 번들을 전송해야 합니다.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   `:~`는 가상 머신의 홈 디렉터리에 구성 폴더가 배치됨을 의미합니다.

1. 가상 머신에 로그온하여 디바이스에 구성 번들을 적용합니다.

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. 각 디바이스에서 구성 번들의 압축을 풉니다. 먼저 zip을 설치해야 합니다.

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. 각 디바이스에서 디바이스에 구성 번들을 적용합니다.

   ```bash
   sudo ./install.sh
   ```

   **최상위 레이어 디바이스** 에서 호스트 이름을 입력하라는 메시지가 표시됩니다. **하위 레이어 디바이스** 에서 호스트 이름 및 부모의 호스트 이름을 묻는 메시지가 표시됩니다. 각 프롬프트에 적절한 IP 또는 FQDN을 입력합니다. 둘 중 하나를 사용할 수 있지만, 여러 디바이스에서 선택한 것만 일관적으로 사용하세요. 설치 스크립트의 출력은 아래 그림에 나와 있습니다.

   디바이스의 구성 파일에 대한 수정 내용을 자세히 확인하려면 [방법 가이드의 디바이스에 IoT Edge 구성 섹션](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)을 참조하세요.

  ![구성 번들을 설치하면 디바이스의 config.toml 파일이 업데이트되고 모든 IoT Edge 서비스가 자동으로 다시 시작됩니다.](./media/tutorial-nested-iot-edge/configuration-install-output.png)

위의 단계를 올바르게 완료한 경우 디바이스가 올바르게 구성되었는지 확인할 수 있습니다.

디바이스에서 구성 및 연결 확인을 실행합니다. **최상위 레이어 디바이스**:

   ```bash
   sudo iotedge check
   ```

**하위 레이어 디바이스** 의 경우 명령에서 진단 이미지를 수동으로 전달해야 합니다.

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:443/azureiotedge-diagnostics:1.2
   ```

**최상위 레이어 디바이스** 에서 몇 가지 전달 평가가 포함된 출력이 표시될 것입니다. 로그 정책 및 DNS 정책(네트워크에 따라 다름)에 대한 경고가 표시될 수 있습니다.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

각 디바이스의 구성이 올바르면 다음 단계를 진행할 수 있습니다.

## <a name="deploy-modules-to-your-devices"></a>디바이스에 모듈 배포

디바이스를 만들면 디바이스에 대한 모듈 배포가 자동으로 생성됩니다. **최상위 및 하위 레이어 디바이스** 가 생성되면 `iotedge-config-cli` 도구가 관련 배포 JSON을 제공합니다. 사용자가 각 디바이스에 IoT Edge 런타임을 구성하는 동안 모듈 배포는 보류됩니다. 런타임을 구성하면 **최상위 레이어 디바이스** 에 대한 배포가 시작됩니다. 이러한 배포가 완료되면 **하위 레이어 디바이스** 가 **IoT Edge API Proxy** 모듈을 사용하여 필요한 이미지를 끌어올 수 있습니다.

[Azure Cloud Shell](https://shell.azure.com/)에서 **최상위 레이어 디바이스의** 배포 JSON을 확인하여 디바이스에 배포된 모듈을 이해할 수 있습니다.

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

**최상위 레이어 디바이스** 는 런타임 모듈 **IoT Edge Agent** 및 **IoT Edge Hub** 외에 **Docker 레지스트리** 모듈 및 **IoT Edge API Proxy** 모듈도 받습니다.

**Docker 레지스트리** 모듈은 기존 Azure Container Registry를 가리킵니다. 이 경우 `REGISTRY_PROXY_REMOTEURL`은 Microsoft Container Registry를 가리킵니다. 기본적으로 **Docke 레지스트리** 는 포트 5000에서 수신 대기합니다.

**IoT Edge API Proxy** 모듈은 HTTP 요청을 다른 모듈로 라우팅하므로 하위 레이어 디바이스가 컨테이너 이미지를 끌어오거나 Blob을 스토리지로 밀어 넣을 수 있습니다. 이 자습서에서는 포트 443에서 통신하고, Docker 컨테이너 이미지 끌어오기 요청을 포트 5000의 **Docker 레지스트리** 모듈로 라우팅하도록 구성됩니다. 또한 모든 Blob Storage 업로드 요청은 포트 11002에서 AzureBlobStorageonIoTEdge 모듈로 라우팅됩니다. **IoT Edge API Proxy** 모듈 및 이 모듈을 구성하는 방법에 대한 자세한 내용은 모듈의 [방법 가이드](how-to-configure-api-proxy-module.md)를 참조하세요.

Azure Portal 또는 Azure Cloud Shell을 통해 이와 같은 배포를 만드는 방법을 알아보려면 [방법 가이드의 최상위 레이어 디바이스 섹션](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices)을 참조하세요.

[Azure Cloud Shell](https://shell.azure.com/)에서 **하위 레이어 디바이스의** 배포 JSON을 확인하여 디바이스에 배포된 모듈을 이해할 수 있습니다.

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

`systemModules` 아래에서 **하위 레이어 디바이스의** 런타임 모듈이 **최상위 레이어 디바이스** 처럼 `mcr.microsoft.com`에서 끌어오는 것이 아니라, `$upstream:443`에서 끌어오도록 설정된 것을 볼 수 있습니다. **하위 레이어 디바이스** 는 클라우드에서 이미지를 직접 끌어올 수 없으므로, 포트 443의 **IoT Edge API Proxy** 모듈로 Docker 이미지 요청을 보냅니다. **하위 레이어 디바이스** 에 배포된 다른 모듈(**Simulated Temperature Sensor** 모듈)도 이미지 요청을 `$upstream:443`으로 보냅니다.

Azure Portal 또는 Azure Cloud Shell을 통해 이와 같은 배포를 만드는 방법을 알아보려면 [방법 가이드의 하위 레이어 디바이스 섹션](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices)을 참조하세요.

명령을 사용하여 모듈의 상태를 볼 수 있습니다.

   ```azurecli
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   이 명령은 모든 edgeAgent reported 속성을 출력합니다. 디바이스 상태를 모니터링하는 데 도움이 되는 속성으로는 *runtime status*, *runtime start time*, *runtime last exit time*, *runtime restart count* 가 있습니다.

[Azure Portal](https://ms.portal.azure.com/)에서 모듈의 상태를 확인할 수도 있습니다. IoT Hub의 **IoT Edge** 섹션으로 이동하여 디바이스 및 모듈을 확인합니다.

모듈 배포에 만족하면 계속 진행할 수 있습니다.

## <a name="view-generated-data"></a>생성된 데이터 보기

사용자가 푸시한 **시뮬레이션된 온도 센서** 모듈은 샘플 환경 데이터를 생성합니다. 주변 온도 및 습도, 머신 온도 및 압력, 타임스탬프가 포함된 메시지를 보냅니다.

[Azure Cloud Shell](https://shell.azure.com/)을 통해 이러한 메시지를 볼 수도 있습니다.

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>문제 해결

`iotedge check` 명령을 실행하여 구성을 확인하고 오류를 해결합니다.

자식 머신이 직접 인터넷에 액세스할 수 없는 경우에도 중첩된 계층 구조에서 `iotedge check`를 실행할 수 있습니다.

하위 레이어에서 `iotedge check`를 실행하면 프로그램이 포트 443을 통해 부모에서 이미지 끌어오기를 시도합니다.

```bash
sudo iotedge check --diagnostics-image-name $upstream:443/azureiotedge-diagnostics:1.2
```

`azureiotedge-diagnostics` 값은 레지스트리 모듈과 연결된 컨테이너 레지스트리에서 가져옵니다. 이 자습서는 기본적으로 https://mcr.microsoft.com: 으로 설정되어 있습니다.

| 이름 | 값 |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

프라이빗 컨테이너 레지스트리를 사용하는 경우 모든 이미지(IoTEdgeAPIProxy, edgeAgent, edgeHub, Simulated Temperature Sensor 및 진단)가 컨테이너 레지스트리에 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

요금이 부과되지 않도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다.

리소스를 삭제하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **리소스 그룹** 을 선택합니다.

2. IoT Hub 테스트 리소스가 포함된 리소스 그룹 이름을 선택합니다. 

3. 리소스 그룹에 포함된 리소스의 목록을 검토합니다. 모든 항목을 삭제하려는 경우 **리소스 그룹 삭제** 를 선택할 수 있습니다. 일부만 삭제하려는 경우 각 리소스를 클릭하여 개별적으로 삭제할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 두 개의 IoT Edge 디바이스를 게이트웨이로 구성하고 다른 디바이스를 부모 디바이스로 설정했습니다. 그런 다음, IoT Edge API Proxy 모듈을 사용하여 게이트웨이를 통해 자식 디바이스로 컨테이너 이미지를 끌어오는 방법을 시연했습니다. 자세히 알아보려면 [프록시 모듈 사용에 대한 방법 가이드](how-to-configure-api-proxy-module.md)를 참조하세요.

게이트웨이를 사용하여 IoT Edge 디바이스의 계층 레이어를 만드는 방법에 대해 자세히 알아보려면 [다운스트림 IoT Edge 디바이스 연결에 대한 방법 가이드](how-to-connect-downstream-iot-edge-device.md)를 참조하세요.

Azure IoT Edge에서 추가 업무용 솔루션을 만드는 방법을 확인하려는 경우 다른 자습서를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Machine Learning 모델을 모듈로 배포](tutorial-deploy-machine-learning.md)
