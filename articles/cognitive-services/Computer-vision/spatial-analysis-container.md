---
title: 공간 분석 컨테이너를 설치하고 실행하는 방법 - Computer Vision
titleSuffix: Azure Cognitive Services
description: 공간 분석 컨테이너를 사용하면 사람 및 거리를 감지할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: pafarley
ms.custom: ignite-fall-2021
ms.openlocfilehash: f866db833381b8dc6c75538265eefdc057445b3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057897"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>공간 분석 컨테이너 설치 및 실행(미리 보기)

공간 분석 컨테이너를 사용하면 실시간 스트리밍 비디오를 분석하여 사람 사이의 공간적 관계, 이들의 움직임 및 물리적 환경에서 물체와의 상호 작용을 이해할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [!INCLUDE [contributor-requirement](../includes/quickstarts/contributor-requirement.md)]
* Azure 구독을 만든 후에는 Azure Portal에서 표준 S1 계층에 대한 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스</a>를 만들어서 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 공간 분석 컨테이너를 실행하려면 직접 만든 리소스의 키와 엔드포인트가 필요합니다. 키와 엔드포인트는 나중에 사용합니다.

### <a name="spatial-analysis-container-requirements"></a>공간 분석 컨테이너 요구 사항

공간 분석 컨테이너를 실행하려면 [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/)가 있는 컴퓨팅 디바이스가 필요합니다. GPU 가속과 함께 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)를 사용하는 것이 좋지만 컨테이너는 최소 요구 사항을 충족하는 다른 데스크톱 컴퓨터에서 실행됩니다. 이 디바이스를 호스트 컴퓨터라고 합니다.

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 디바이스](#tab/azure-stack-edge)

Azure Stack Edge는 HaaS(Hardware-as-a-Service) 솔루션이며 네트워크 데이터 전송 기능이 있는 AI 지원 에지 컴퓨팅 디바이스입니다. 자세한 준비 및 설정 지침은 [Azure Stack Edge 설명서](../../databox-online/azure-stack-edge-deploy-prep.md)를 참조하세요.

#### <a name="desktop-machine"></a>[데스크톱 컴퓨터](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>최소 하드웨어 요구 사항

* 4GB 시스템 RAM
* 4GB GPU RAM
* 8코어 CPU
* NVIDIA Tesla T4 GPU 1개
* 20GB의 HDD 공간

#### <a name="recommended-hardware"></a>권장 하드웨어

* 32GB 시스템 RAM
* 16GB GPU RAM
* 8코어 CPU
* NVIDIA Tesla T4 GPU 2개
* 50GB의 SSD 공간

이 문서에서는 다음 소프트웨어 패키지를 다운로드하고 설치합니다. 호스트 컴퓨터에서 다음을 실행할 수 있어야 합니다(지침은 아래 참조).

* [NVIDIA 그래프 드라이버](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) 및 [NVIDIA CUDA Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)(다중 프로세스 서비스)에 대한 구성
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) 및 [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IoT Edge](../../iot-edge/how-to-provision-single-device-linux-symmetric.md) 런타임

#### <a name="azure-vm-with-gpu"></a>[GPU가 있는 Azure VM](#tab/virtual-machine)
이 예에서는 K80 GPU가 하나 있는 [NC 시리즈 VM](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 활용합니다.

---

| 요구 사항 | Description |
|--|--|
| 카메라 | 공간 분석 컨테이너는 특정 카메라 브랜드와 관련이 없습니다. 카메라 디바이스는 RTSP(Real-Time Streaming Protocol) 및 H.264 인코딩을 지원하고, 호스트 컴퓨터에 액세스할 수 있어야 하며 15FPS 및 1080p 해상도로 스트리밍할 수 있어야 합니다. |
| Linux OS | [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/)가 호스트 컴퓨터에 설치되어 있어야 합니다.  |

## <a name="set-up-the-host-computer"></a>호스트 컴퓨터 설정

호스트 컴퓨터에 Azure Stack Edge 디바이스를 사용하는 것이 좋습니다. 다른 디바이스를 구성하는 경우 **데스크톱 컴퓨터** 를 클릭하고, VM을 활용하는 경우 **가상 머신** 을 클릭합니다.

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 디바이스](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Azure Stack Edge 포털에서 컴퓨팅 구성 
 
공간 분석은 Azure Stack Edge의 컴퓨팅 기능을 사용하여 AI 솔루션을 실행합니다. 컴퓨팅 기능을 사용하도록 설정하려면 다음을 확인해야 합니다. 

* Azure Stack Edge 디바이스를 [연결하고 활성화](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md)했습니다. 
* 디바이스 액세스용으로 PowerShell 5.0 이상을 실행하는 Windows 클라이언트 시스템이 있습니다.  
* Kubernetes 클러스터를 배포하려면 [Azure Portal](https://portal.azure.com/)에서 **로컬 UI** 를 통해 Azure Stack Edge 디바이스를 구성해야 합니다. 
  1. Azure Stack Edge 디바이스에서 컴퓨팅 기능을 사용하도록 설정합니다. 컴퓨팅을 사용하도록 설정하려면 디바이스의 웹 인터페이스에서 **컴퓨팅** 페이지로 이동합니다. 
  2. 컴퓨팅에 사용할 네트워크 인터페이스를 선택한 다음, **사용** 을 클릭합니다. 그러면 네트워크 인터페이스에서 디바이스에 가상 스위치가 생성됩니다.
  3. Kubernetes 테스트 노드 IP 주소와 Kubernetes 외부 서비스 IP 주소는 비워둡니다.
  4. **적용** 을 클릭합니다. 이 작업은 약 2분 정도 걸릴 수 있습니다. 

![컴퓨팅 구성](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Edge 컴퓨팅 역할 설정 및 IoT Hub 리소스 만들기

[Azure Portal](https://portal.azure.com/)에서 Azure Stack Edge 리소스로 이동합니다. **개요** 페이지 또는 탐색 목록에서 Edge 컴퓨팅 **시작** 단추를 클릭합니다.  **Edge 컴퓨팅 구성**  타일에서 **구성** 을 클릭합니다. 

![링크](media/spatial-analysis/configure-edge-compute-tile.png)

 **Edge 컴퓨팅 구성**  페이지에서 기존 IoT Hub를 선택하거나 새 항목을 만들도록 선택합니다. 기본적으로 IoT Hub 리소스를 만드는 데는 표준(S1) 가격 책정 계층이 사용됩니다. 무료 계층 IoT Hub 리소스를 사용하려면 리소스를 만든 다음 선택합니다. IoT Hub 리소스는 Azure Stack Edge 리소스에서 사용하는 것과 동일한 구독 및 리소스 그룹을 사용합니다. 

**만들기** 를 클릭합니다. IoT Hub 리소스 만들기는 몇 분 정도 걸릴 수 있습니다. IoT Hub 리소스가 만들어지면  **Edge 컴퓨팅 구성** 타일이 새 구성을 표시하도록 업데이트됩니다. Edge 컴퓨팅 역할이 구성되었는지 확인하려면  **컴퓨팅 구성**  타일의  **구성 보기** 를 선택합니다.

Edge 컴퓨팅 역할이 Edge 디바이스에 설정되면 두 가지 디바이스, 즉 IoT 디바이스와 IoT Edge 디바이스가 만들어집니다. 이 두 디바이스는 모두 IoT Hub 리소스에서 볼 수 있습니다. Azure IoT Edge 런타임은 이미 IoT Edge 디바이스에서 실행되고 있습니다.

> [!NOTE]
> * 현재 IoT Edge 디바이스에는 Linux 플랫폼만 지원됩니다. Azure Stack Edge 디바이스 문제 해결에 도움이 필요하면 [로깅 및 문제 해결](spatial-analysis-logging.md) 문서를 참조하세요.
> * 프록시 서버를 통해 통신하도록 IoT Edge 디바이스를 구성하는 방법에 대해 자세히 알아 보려면 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](../../iot-edge/how-to-configure-proxy-support.md#azure-portal)을 참조하세요.

###  <a name="enable-mps-on-azure-stack-edge"></a>Azure Stack Edge에서 MPS 사용 

Windows 클라이언트에서 원격으로 연결하려면 다음 단계를 수행합니다.

1. 관리자 권한으로 Windows PowerShell 세션을 실행합니다.
2. Windows 원격 관리 서비스가 클라이언트에서 실행되고 있는지 확인합니다. 명령 프롬프트에 다음을 입력합니다.

    ```powershell
    winrm quickconfig
    ```

    자세한 내용은 [Windows 원격 관리를 위한 설치 및 구성](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration)을 참조하세요.

3. `hosts` 파일에 사용되는 연결 문자열에 변수를 할당합니다.

    ```powershell
    $Name = "<Node serial number>.<DNS domain of the device>"
    ``` 

    `<Node serial number>` 및 `<DNS domain of the device>`를 디바이스의 노드 일련 번호 및 DNS 도메인으로 바꿉니다. 디바이스의 로컬 웹 UI에 있는 **디바이스** 페이지에서 **인증서** 페이지 및 DNS 도메인의 노드 일련 번호에 대한 값을 가져올 수 있습니다.

4. 디바이스의 연결 문자열을 클라이언트의 신뢰할 수 있는 호스트 목록에 추가하려면 다음 명령을 입력합니다.

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    ```

5. 디바이스에서 Windows PowerShell 세션을 시작합니다.

    ```powershell
    Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL
    ```

    신뢰 관계와 관련된 오류가 표시되는 경우 디바이스에 업로드된 노드 인증서의 서명 체인이 디바이스에 액세스하는 클라이언트에도 설치되어 있는지 확인합니다.

6. 메시지가 표시되면 암호를 입력합니다. 로컬 웹 UI에 로그인하는 데 사용되는 것과 동일한 암호를 사용합니다. 기본 로컬 웹 UI 암호는 *Password1* 입니다. 원격 PowerShell을 사용하여 디바이스에 성공적으로 연결하면 다음 샘플 출력이 표시됩니다.  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $Name = "1HXQG13.wdshcsso.com"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [1HXQG13.wdshcsso.com]: PS>
    ```

#### <a name="desktop-machine"></a>[데스크톱 컴퓨터](#tab/desktop-machine)

호스트 컴퓨터가 Azure Stack Edge 디바이스가 아닌 경우 다음 지침을 따르세요.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>호스트 컴퓨터에 NVIDIA CUDA Toolkit 및 Nvidia 그래픽 드라이버 설치

다음 bash 스크립트를 사용하여 필요한 Nvidia 그래픽 드라이버 및 CUDA Toolkit을 설치합니다.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

컴퓨터를 재부팅하고 다음 명령을 실행합니다.

```bash
nvidia-smi
```

다음 출력이 표시되어야 합니다.

![NVIDIA 드라이버 출력](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>호스트 컴퓨터에 Docker CE 및 nvidia-docker2 설치

호스트 컴퓨터에 Docker CE를 설치합니다.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

*nvidia-docker-2* 소프트웨어 패키지를 설치합니다.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>호스트 컴퓨터에서 NVIDIA MPS를 사용하도록 설정

> [!TIP]
> * GPU 컴퓨팅 기능이 7.x 미만(Volta 이전)인 경우 MPS를 설치하지 마십시오. 자세한 내용은 [CUDA 호환성](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title)을 참조하세요. 
> * 호스트 컴퓨터의 터미널 창에서 MPS 명령을 실행합니다. Docker 컨테이너 인스턴스 내부가 아닙니다.

최상의 성능 및 사용률을 위해 [NVIDIA MPS(Multiprocess Service)](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)에 대해 호스트 컴퓨터의 GPU를 구성합니다. 호스트 컴퓨터의 터미널 창에서 MPS 명령을 실행합니다.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>호스트 컴퓨터에서 Azure IoT Edge 구성

호스트 컴퓨터에 공간 분석 컨테이너를 배포하려면 표준(S1) 또는 무료(F0) 가격 책정 계층을 사용하여 [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) 서비스 인스턴스를 만듭니다. 

Azure CLI를 사용하여 Azure IoT Hub 인스턴스를 만듭니다. 해당하는 경우 매개 변수를 바꿉니다. 또는 [Azure Portal](https://portal.azure.com/)에서 Azure IoT Hub를 만들 수 있습니다.

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
사용 가능한 지역은 [지역 지원](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)을 참조하세요.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

[Azure IoT Edge](../../iot-edge/how-to-provision-single-device-linux-symmetric.md) 버전 1.0.9를 설치해야 합니다. 올바른 버전을 다운로드하려면 다음 단계를 수행합니다.

Ubuntu Server 18.04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

생성된 목록을 복사합니다.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Microsoft GPG 공개 키를 설치합니다.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

디바이스에서 패키지 목록을 업데이트합니다.

```bash
sudo apt-get update
```

1\.0.9 릴리스를 설치합니다.

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

그런 다음, [연결 문자열](../../iot-edge/how-to-provision-single-device-linux-symmetric.md#register-your-device)을 사용하여 호스트 컴퓨터를 IoT Hub 인스턴스에서 IoT Edge 디바이스로 등록합니다.

IoT Edge 디바이스를 Azure IoT Hub에 연결해야 합니다. 이전에 만든 IoT Edge 디바이스에서 연결 문자열을 복사해야 합니다. 또는 Azure CLI에서 아래 명령을 실행할 수 있습니다.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

호스트 컴퓨터에서 `/etc/iotedge/config.yaml`을 편집용으로 엽니다. `ADD DEVICE CONNECTION STRING HERE`를 연결 문자열로 바꿉니다. 파일을 저장하고 닫습니다. 이 명령을 실행하여 호스트 컴퓨터에서 IoT Edge 서비스를 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

[Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) 또는 [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows)에서 공간 분석 컨테이너를 호스트 컴퓨터의 IoT 모듈로 배포합니다. 포털을 사용하는 경우 이미지 URI를 Azure Container Registry의 위치로 설정합니다. 

아래 단계에 따라 Azure CLI를 사용하여 컨테이너를 배포합니다.

#### <a name="azure-vm-with-gpu"></a>[GPU가 있는 Azure VM](#tab/virtual-machine)

GPU가 있는 Azure Virtual Machine을 사용하여 공간 분석을 실행할 수도 있습니다. 아래 예에서는 K80 GPU가 하나 있는 [NC 시리즈](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM을 사용합니다.

#### <a name="create-the-vm"></a>VM 만들기

Azure Portal에서 [가상 머신 만들기](https://ms.portal.azure.com/#create/Microsoft.VirtualMachine) 마법사를 엽니다.

VM에 이름을 지정하고 지역을 (미국) 미국 서부 2로 선택합니다. 

> [!IMPORTANT]
> `Availability Options`을 “인프라 중복이 필요하지 않습니다”로 설정해야 합니다. 전체 구성은 아래 그림을 참조하고 올바른 VM 크기를 찾는 데 도움이 필요하면 다음 단계를 참조하세요. 

:::image type="content" source="media/spatial-analysis/virtual-machine-instance-details.jpg" alt-text="가상 머신 구성 세부 정보" lightbox="media/spatial-analysis/virtual-machine-instance-details.jpg":::

VM 크기를 찾으려면 "모든 크기 보기"를 선택한 다음, 아래에 표시된 "비 Premium Storage VM 크기" 목록을 확인합니다.

:::image type="content" source="media/spatial-analysis/virtual-machine-sizes.png" alt-text="가상 머신 크기" lightbox="media/spatial-analysis/virtual-machine-sizes.png":::

그런 다음, **NC6** 또는 **NC6_Promo** 중 하나를 선택합니다.

:::image type="content" source="media/spatial-analysis/promotional-selection.png" alt-text="프로모션 선택" lightbox="media/spatial-analysis/promotional-selection.png":::

그런 다음, VM을 만듭니다. 만든 후에는 Azure Portal에서 VM 리소스로 이동하고 왼쪽 창에서 `Extensions`를 선택합니다. "추가"를 클릭하여 사용 가능한 모든 확장이 있는 확장 창을 표시합니다. 를 검색하여 선택하고 `NVIDIA GPU Driver Extension` 만들기를 클릭한 다음 마법사를 완료합니다.

확장이 성공적으로 적용되면 Azure Portal의 VM 기본 페이지로 이동하고 `Connect`를 클릭합니다. VM은 SSH 또는 RDP를 통해 액세스할 수 있습니다. RDP가 유용합니다. 시각화 도우미 창을 볼 수 있기 때문입니다(뒷부분에 설명되어 있음). [이 단계](../../virtual-machines/linux/use-remote-desktop.md)를 수행하고 VM에 대한 원격 데스크톱 연결을 열어서 RDP 액세스를 구성합니다.

### <a name="verify-graphics-drivers-are-installed"></a>그래픽 드라이버가 설치되어 있는지 확인

다음 명령을 실행하여 그래픽 드라이버가 성공적으로 설치되었는지 확인합니다. 

```bash
nvidia-smi
```

다음 출력이 표시되어야 합니다.

![NVIDIA 드라이버 출력](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-vm"></a>VM에 Docker CE 및 nvidia-docker2 설치

VM에 Docker CE 및 nvidia-docker2를 설치하려면 다음 명령을 한 번에 하나씩 실행합니다.

호스트 컴퓨터에 Docker CE를 설치합니다.

```bash
sudo apt-get update
```
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```


*nvidia-docker-2* 소프트웨어 패키지를 설치합니다.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce nvidia-docker2
```
```bash
sudo systemctl restart docker
```

VM을 설정하고 구성했으면 다음 단계에 따라 Azure IoT Edge를 구성합니다. 

## <a name="configure-azure-iot-edge-on-the-vm"></a>VM에서 Azure IoT Edge 구성

VM에 공간 분석 컨테이너를 배포하려면 표준(S1) 또는 무료(F0) 가격 책정 계층을 사용하여 [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) 서비스 인스턴스를 만듭니다.

Azure CLI를 사용하여 Azure IoT Hub 인스턴스를 만듭니다. 해당하는 경우 매개 변수를 바꿉니다. 또는 [Azure Portal](https://portal.azure.com/)에서 Azure IoT Hub를 만들 수 있습니다.

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
사용 가능한 지역은 [지역 지원](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)을 참조하세요.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

[Azure IoT Edge](../../iot-edge/how-to-provision-single-device-linux-symmetric.md) 버전 1.0.9를 설치해야 합니다. 올바른 버전을 다운로드하려면 다음 단계를 수행합니다.

Ubuntu Server 18.04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

생성된 목록을 복사합니다.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Microsoft GPG 공개 키를 설치합니다.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

디바이스에서 패키지 목록을 업데이트합니다.

```bash
sudo apt-get update
```

1\.0.9 릴리스를 설치합니다.

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

그런 다음, [연결 문자열](../../iot-edge/how-to-provision-single-device-linux-symmetric.md#register-your-device)을 사용하여 VM을 IoT Hub 인스턴스에서 IoT Edge 디바이스로 등록합니다.

IoT Edge 디바이스를 Azure IoT Hub에 연결해야 합니다. 이전에 만든 IoT Edge 디바이스에서 연결 문자열을 복사해야 합니다. 또는 Azure CLI에서 아래 명령을 실행할 수 있습니다.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

VM에서 `/etc/iotedge/config.yaml`을 편집용으로 엽니다. `ADD DEVICE CONNECTION STRING HERE`를 연결 문자열로 바꿉니다. 파일을 저장하고 닫습니다. 이 명령을 실행하여 VM에서 IoT Edge 서비스를 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

[Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) 또는 [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows)에서 공간 분석 컨테이너를 VM의 IoT 모듈로 배포합니다. 포털을 사용하는 경우 이미지 URI를 Azure Container Registry의 위치로 설정합니다. 

아래 단계에 따라 Azure CLI를 사용하여 컨테이너를 배포합니다.

---

### <a name="iot-deployment-manifest"></a>IoT 배포 매니페스트

여러 호스트 컴퓨터에서 컨테이너 배포를 간소화하기 위해 배포 매니페스트 파일을 만들어서 컨테이너 생성 옵션 및 환경 변수를 지정할 수 있습니다. GitHub에서 [Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [기타 데스크톱 머신](https://go.microsoft.com/fwlink/?linkid=2152270) 및 [GPU가 있는 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189)에 대한 배포 매니페스트의 예를 찾을 수 있습니다.

다음 표에서는 IoT Edge 모듈에 사용되는 다양한 환경 변수를 보여줍니다. 위에 연결된 배포 매니페스트에서 `env`의 `spatialanalysis`특성을 사용하여 설정할 수도 있습니다.

| 설정 이름 | 값 | 설명|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Info; Verbose | 로깅 수준, 두 값 중 하나 선택|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | 수정 금지|
| ARCHON_PERF_MARKER| false| 성능 로깅을 위해 true로 설정합니다. 그렇지 않으면 false여야 합니다.| 
| ARCHON_NODES_LOG_LEVEL | Info; Verbose | 로깅 수준, 두 값 중 하나 선택|
| OMP_WAIT_POLICY | PASSIVE | 수정 금지|
| QT_X11_NO_MITSHM | 1 | 수정 금지|
| APIKEY | API 키| 이 값은 Azure Portal의 Computer Vision 리소스에서 수집합니다. 리소스의 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. |
| 청구 | 엔드포인트 URI| 이 값은 Azure Portal의 Computer Vision 리소스에서 수집합니다. 리소스의 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다.|
| EULA | accept | 컨테이너를 실행하려면 이 값을 *accept* 로 설정해야 합니다. |
| DISPLAY | :1 | 이 값은 호스트 컴퓨터의 `echo $DISPLAY` 출력과 동일해야 합니다. Azure Stack Edge 디바이스에는 디스플레이가 없습니다. 이 설정은 적용할 수 없습니다.|
| ARCHON_GRAPH_READY_TIMEOUT | 600 | GPU가 T4 또는 NVIDIA 2080 Ti가 **아닌** 경우 이 환경 변수를 추가합니다.|
| ORT_TENSORRT_ENGINE_CACHE_ENABLE | 0 | GPU가 T4 또는 NVIDIA 2080 Ti가 **아닌** 경우 이 환경 변수를 추가합니다.|
| KEY_ENV | ASE 암호화 키 | Video_URL이 난독 처리된 문자열인 경우 이 환경 변수를 추가합니다. |
| IV_ENV | 초기화 벡터 | Video_URL이 난독 처리된 문자열인 경우 이 환경 변수를 추가합니다.|

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

자체 설정 및 작업 선택으로 [Azure Stack Edge 디바이스](https://go.microsoft.com/fwlink/?linkid=2142179), [데스크톱 컴퓨터](https://go.microsoft.com/fwlink/?linkid=2152270) 또는 [GPU가 있는 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189)에 대한 배포 매니페스트를 업데이트한 후에는 아래 [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows) 명령을 사용하여 컨테이너를 호스트 컴퓨터에 IoT Edge 모듈로 배포할 수 있습니다.

```azurecli
sudo az login
sudo az extension add --name azure-iot
sudo az iot edge set-modules --hub-name "<iothub-name>" --device-id "<device-name>" --content DeploymentManifest.json --subscription "<name or ID of Azure Subscription>"
```

|매개 변수  |설명  |
|---------|---------|
| `--hub-name` | Azure IoT Hub 이름입니다. |
| `--content` | 배포 파일의 이름입니다. |
| `--target-condition` | 호스트 컴퓨터의 IoT Edge 디바이스 이름입니다. |
| `-–subscription` | 구독 ID 또는 이름입니다. |

이 명령을 실행하면 배포가 시작됩니다. Azure Portal에서 Azure IoT Hub 인스턴스 페이지로 이동하여 배포 상태를 확인합니다. 디바이스가 컨테이너 이미지 다운로드를 완료하고 실행을 시작할 때까지 상태가 *417* -- 디바이스의 배포 구성이 설정되지 않았습니다라고 표시될 수 있습니다.

## <a name="validate-that-the-deployment-is-successful"></a>배포가 성공했는지 확인

컨테이너가 실행되고 있는지 확인하는 방법은 여러 가지가 있습니다. Azure Portal의 Azure IoT Hub 인스턴스에 있는 공간 분석 모듈에 대한 **IoT Edge 모듈 설정** 에서 런타임 상태를 찾습니다. 런타임 상태에 대한 **원하는 값** 및 **보고된 값** 이 실행 중인지 확인합니다. 

![배포 확인 예](./media/spatial-analysis/deployment-verification.png)

배포가 완료되고 컨테이너가 실행되면 **호스트 컴퓨터** 가 Azure IoT Hub로 이벤트를 보내기 시작합니다. `.debug` 버전의 작업을 사용하는 경우 배포 매니페스트에서 구성한 각 카메라에 대한 시각화 도우미 창이 표시됩니다. 이제 배포 매니페스트에서 모니터링할 선과 영역을 정의하고 지침에 따라 다시 배포할 수 있습니다. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>공간 분석에서 수행되는 작업 구성

연결된 카메라를 사용하도록 컨테이너를 구성하고 작업을 구성하는 등의 작업을 수행하려면 [공간 분석 작업](spatial-analysis-operations.md)을 사용해야 합니다. 구성하는 각 카메라 디바이스에 대해 공간 분석 작업은 Azure IoT Hub 인스턴스로 전송되는 JSON 메시지의 출력 스트림을 생성합니다.

## <a name="use-the-output-generated-by-the-container"></a>컨테이너에서 생성된 출력 사용

컨테이너에서 생성된 출력을 사용하기 시작하려면 다음 문서를 참조하세요.

*    선택한 프로그래밍 언어용 Azure Event Hub SDK를 사용하여 Azure IoT Hub 엔드포인트에 연결하고 이벤트를 수신합니다. 자세한 내용은 [기본 제공 엔드포인트에서 디바이스-클라우드 메시지 읽기](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)를 참조하세요. 
*    Azure IoT Hub에서 메시지 라우팅을 설정하여 이벤트를 다른 엔드포인트로 보내거나 Azure Blob Storage 등에 이벤트를 저장합니다. 자세한 내용은 [IoT Hub 메시지 라우팅](../../iot-hub/iot-hub-devguide-messages-d2c.md)을 참조하세요. 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>녹화된 비디오 파일로 공간 분석 실행

공간 분석은 녹화된 비디오 또는 라이브 비디오 모두에 사용할 수 있습니다. 녹화된 비디오에 공간 분석을 사용하려면 비디오 파일을 녹화하고 mp4 파일로 저장해 보세요. Azure에서 Blob 스토리지 계정을 만들거나 기존 계정을 사용합니다. 그런 다음, Azure Portal에서 다음 Blob 스토리지 설정을 업데이트합니다.
    1. **보안 전송 필요** 를 **사용 안 함** 으로 변경합니다.
    2. **Blob 공용 액세스 허용** 을 **사용** 으로 설정합니다.

**컨테이너** 섹션으로 이동하고 새 컨테이너를 만들거나 기존 컨테이너를 사용합니다. 그런 다음, 비디오 파일을 컨테이너에 업로드합니다. 업로드된 파일에 대한 파일 설정을 확장하고 **SAS 생성** 을 선택합니다. 테스트 기간을 포함할 만큼 **만료 날짜** 를 충분히 길게 설정해야 합니다. **허용되는 프로토콜** 을 *HTTP*(*HTTPS* 는 지원되지 않음)로 설정합니다.

**SAS 토큰 및 URL 생성** 을 클릭하고 Blob SAS URL을 복사합니다. 시작 `https`를 `http`로 바꾸고 비디오 재생을 지원하는 브라우저에서 URL을 테스트합니다.

모든 그래프에 대해 [Azure Stack Edge 디바이스](https://go.microsoft.com/fwlink/?linkid=2142179), [데스크톱 컴퓨터](https://go.microsoft.com/fwlink/?linkid=2152270) 또는 [GPU가 있는 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189)에 대한 배포 매니페스트의 `VIDEO_URL`을 직접 만든 URL로 바꿉니다. `VIDEO_IS_LIVE`를 `false`로 설정하고 업데이트된 매니페스트로 공간 분석 컨테이너를 다시 배포합니다. 아래 예제를 참조하세요.

공간 분석 모듈이 비디오 파일을 사용하기 시작하고 계속해서 자동으로 재생됩니다.


```json
"zonecrossing": {
    "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "Replace http url here",
        "VIDEO_SOURCE_ID": "personcountgraph",
        "VIDEO_IS_LIVE": false,
      "VIDEO_DECODE_GPU_INDEX": 0,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"events\": [{\"type\": \"zonecrossing\", \"config\": {\"threshold\": 16.0, \"focus\": \"footprint\"}}]}]}"
    }
   },

```

## <a name="troubleshooting"></a>문제 해결

컨테이너를 시작하거나 실행할 때 문제가 발생하는 경우 [원격 분석 및 문제 해결](spatial-analysis-logging.md)에서 일반적인 문제에 대한 단계를 참조하세요. 이 문서에는 로그 생성 및 수집과 시스템 상태 수집에 대한 정보도 포함되어 있습니다.

[!INCLUDE [Diagnostic container](../containers/includes/diagnostics-container.md)]

## <a name="billing"></a>결제

공간 분석 컨테이너는 청구 정보를 Azure로 보내며, Azure 계정의 Computer Vision 리소스를 사용합니다. 공개 미리 보기에서 공간 분석을 사용하는 것은 현재 무료입니다. 

Azure Cognitive Services 컨테이너는 측정/청구 엔드포인트에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 사용자는 컨테이너가 항상 청구 엔드포인트와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 비디오 또는 이미지)를 Microsoft에 보내지 않습니다.


## <a name="summary"></a>요약

이 문서에서는 공간 분석 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* 공간 분석은 Docker용 Linux 컨테이너입니다.
* 컨테이너 이미지는 Microsoft Container Registry에서 다운로드됩니다.
* 컨테이너 이미지는 Azure IoT Edge에서 IoT 모듈로 실행됩니다.
* 컨테이너를 구성하고 호스트 머신에 배포하는 방법

## <a name="next-steps"></a>다음 단계

* [인원 수를 세는 웹 애플리케이션 배포](spatial-analysis-web-app.md)
* [공간 분석 작업 구성](spatial-analysis-operations.md)
* [로깅 및 문제 해결](spatial-analysis-logging.md)
* [카메라 배치 가이드](spatial-analysis-camera-placement.md)
* [영역 및 선 배치 가이드](spatial-analysis-zone-line-placement.md)
