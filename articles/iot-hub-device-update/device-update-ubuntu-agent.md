---
title: Ubuntu Server 18.04 x64 패키지 에이전트를 사용하는 Azure IoT Hub에 대한 디바이스 업데이트 자습서 | Microsoft Docs
description: Ubuntu Server 18.04 x64 패키지 에이전트를 사용하는 Azure IoT Hub에 대한 디바이스 업데이트를 시작합니다.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 1cd4ea0d01299b8e361e65a1f8152c4222df3bf0
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372173"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Ubuntu Server 18.04 x64에서 패키지 에이전트를 사용하는 Azure IoT Hub에 대한 디바이스 업데이트 자습서

IoT Hub에 대한 디바이스 업데이트는 이미지 기반 및 패키지 기반의 두 가지 업데이트 형식을 지원합니다.

패키지 기반 업데이트는 디바이스의 특정 구성 요소 또는 애플리케이션만 변경 대상으로 하는 업데이트입니다. 대역폭 사용량을 줄이고, 업데이트를 다운로드하고 설치하는 시간을 줄이는 데 도움이 됩니다. 또한 패키지 기반 업데이트는 일반적으로 업데이트를 적용할 때 디바이스의 가동 중지 시간을 줄이고 이미지를 만드는 오버헤드를 방지할 수 있습니다. 지정된 리포지토리에서 APT 매니페스트 파일에 지정된 패키지(및 해당 종속성)를 다운로드하고 설치하는 데 필요한 정보를 디바이스 업데이트 에이전트에 제공하는 [APT 매니페스트](device-update-apt-manifest.md)를 사용합니다.

이 엔드투엔드 간 자습서에서는 디바이스 업데이트 패키지 에이전트를 사용하여 Ubuntu Server 18.04 x64에서 Azure IoT Edge를 업데이트하는 과정을 안내합니다. 자습서에서 IoT Edge를 업데이트하는 방법을 시연하지만, 비슷한 단계를 사용하여 IoT Edge가 사용하는 컨테이너 엔진과 같은 다른 패키지를 업데이트할 수 있습니다.

이 자습서의 도구와 개념은 다른 OS 플랫폼 구성을 사용하려는 경우에도 계속 적용됩니다. 엔드투엔드 업데이트 프로세스에 대한 이 소개를 완료하고 원하는 업데이트 형식 및 OS 플랫폼을 선택하여 세부 정보를 살펴봅니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 디바이스 업데이트 에이전트 및 해당 종속성 다운로드 및 설치
> * 디바이스에 태그 추가
> * 업데이트 가져오기
> * 디바이스 그룹 만들기
> * 패키지 업데이트 배포
> * 업데이트 배포 모니터링

## <a name="prerequisites"></a>필수 조건

* 아직 수행하지 않은 경우 IoT Hub 구성을 포함하여 [디바이스 업데이트 계정 및 인스턴스](create-device-update-account.md)를 만듭니다.
* [IoT Edge 디바이스에 대한 연결 문자열](../iot-edge/how-to-provision-single-device-linux-symmetric.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-provisioning-information)

## <a name="prepare-a-device"></a>디바이스 준비
### <a name="using-the-automated-deploy-to-azure-button"></a>자동화된 Azure에 배포 단추 사용

편의를 위해 이 자습서에서는 [cloud-init](../virtual-machines/linux/using-cloud-init.md) 기반 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)을 사용하여 Ubuntu 18.04 LTS 가상 머신을 빠르게 설정할 수 있습니다. Azure IoT Edge 런타임과 디바이스 업데이트 패키지 에이전트를 모두 설치한 다음, 사용자가 제공하는 IoT Edge 디바이스(필수 구성 요소)에 대한 디바이스 연결 문자열을 사용하여 프로비저닝 정보를 사용하여 디바이스를 자동으로 구성합니다. 또한 Azure Resource Manager 템플릿에서는 SSH 세션을 시작하여 설치를 완료하지 않아도 됩니다.

1. 시작하려면 아래 단추를 클릭합니다.

   [![iotedge-vm-deploy를 위한 Azure에 배포하기 단추](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fdevice-update-tutorial%2FedgeDeploy.json)

1. 새로 시작된 창에서 사용 가능한 양식 필드를 채웁니다.

    > [!div class="mx-imgBorder"]
    > [![iotedge-vm-deploy 템플릿을 보여 주는 스크린샷](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **구독**: 가상 머신을 배포할 활성 Azure 구독입니다.

    **리소스 그룹**: 가상 머신과 연결된 리소스를 포함하는 기존 리소스 그룹 또는 새로 만든 리소스 그룹입니다.

    **DNS 레이블 접두사**: 가상 머신의 호스트 이름을 접두사로 지정하는 데 사용되는 선택 항목의 필수 값입니다.

    **관리자 사용자 이름**: 배포에 대한 루트 권한이 제공되는 사용자 이름입니다.

    **디바이스 연결 문자열**: 의도한 [IoT Hub](../iot-hub/about-iot-hub.md) 내에 만들어진 디바이스에 대한 [디바이스 연결 문자열](../iot-edge/how-to-provision-single-device-linux-symmetric.md#view-registered-devices-and-retrieve-provisioning-information)입니다.

    **VM 크기**: 배포할 가상 머신의 [크기](../cloud-services/cloud-services-sizes-specs.md)입니다.

    **Ubuntu OS 버전**: 기본 가상 머신에 설치할 Ubuntu OS 버전입니다. 기본값은 이미 Ubuntu 18.04-LTS로 설정되어 있으므로 변경하지 않고 그대로 둡니다.

    **위치**: 가상 머신을 배포할 [지리적 지역](https://azure.microsoft.com/global-infrastructure/locations/)이며, 이 값은 기본적으로 선택한 리소스 그룹의 위치로 설정됩니다.

    **인증 유형**: 기본 설정에 따라 **sshPublicKey** 또는 **password** 를 선택합니다.

    **관리자 암호 또는 키**: 선택한 인증 유형에 따라 SSH 공개 키 또는 암호의 값입니다.

    모든 필드가 채워지면 페이지 아래쪽의 확인란을 선택하여 약관에 동의하고, **구매** 를 선택하여 배포를 시작합니다.

1. 배포가 성공적으로 완료되었는지 확인합니다. 배포가 완료된 후 몇 분 동안 사후 설치 및 구성에서 IoT Edge 및 디바이스 패키지 업데이트 에이전트 설치를 완료할 수 있도록 합니다.

   가상 머신 리소스는 선택한 리소스 그룹에 배포되어야 합니다.  머신 이름을 적어 둡니다. 이는 `vm-0000000000000` 형식이어야 합니다. 또한 연결된 **DNS 이름** 을 기록해 둡니다. 형식은 `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com이어야 합니다.

    **DNS 이름** 은 Azure Portal 내에 새로 배포된 가상 머신의 **개요** 섹션에서 가져올 수 있습니다.

    > [!div class="mx-imgBorder"]
    > [![IoT Edge VM의 DNS 이름을 보여 주는 스크린샷](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > 설정 후에 SSH를 이 VM에 연결하려면 `ssh <adminUsername>@<DNS_Name>` 명령에서 연결된 **DNS 이름** 을 사용합니다.

### <a name="optional-manually-prepare-a-device"></a>(선택 사항) 수동으로 디바이스 준비
[클라우드 초기화 스크립트](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt)에 의해 자동화된 단계와 유사하게 디바이스를 설치하고 구성하는 수동 단계는 다음과 같습니다. 이러한 단계는 물리적 디바이스를 준비하는 데 사용할 수 있습니다.

1. 지침에 따라 [Azure IoT Edge 런타임을 설치](../iot-edge/how-to-provision-single-device-linux-symmetric.md?view=iotedge-2020-11&preserve-view=true)합니다.
   > [!NOTE]
   > 디바이스 업데이트 패키지 에이전트는 IoT Edge에 종속되지 않습니다. 그러나 IoT Edge(1.2.0 이상)와 함께 설치되는 IoT ID 서비스 디먼을 사용하여 ID를 가져오고 IoT Hub에 연결합니다.
   >
   > 이 자습서에서는 설명하지 않지만 [IoT ID 서비스 디먼을 독립 실행형으로 Linux 기반 IoT 디바이스에 설치할 수 있습니다](https://azure.github.io/iot-identity-service/installation.html). 설치 순서가 중요합니다. 디바이스 업데이트 패키지 에이전트는 IoT ID 서비스 _다음_ 에 설치해야 합니다. 그렇지 않으면 패키지 에이전트가 IoT Hub에 대한 연결을 설정할 수 있는 권한 있는 구성 요소로 등록되지 않습니다.

1. 그런 다음, 디바이스 업데이트 에이전트 .deb 패키지를 설치합니다.

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

Azure IoT Hub에 대한 디바이스 업데이트 소프트웨어 패키지에는 다음 사용 조건이 적용됩니다.
  * [IoT Hub에 대한 디바이스 업데이트 라이선스](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [배달 최적화 클라이언트 라이선스](https://github.com/microsoft/do-client/blob/main/LICENSE)

패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치하고 사용하면 이러한 사용 조건에 동의하는 것입니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

## <a name="add-a-tag-to-your-device"></a>디바이스에 태그 추가

1. [Azure Portal](https://portal.azure.com)에 로그인하여 IoT Hub로 이동합니다.

2. 왼쪽 탐색 창의 ‘IoT Edge’에서 IoT Edge 디바이스를 찾고 디바이스 쌍 또는 모듈 쌍으로 이동합니다.

3. 디바이스 업데이트 에이전트 모듈의 모듈 쌍에서 기존 디바이스 업데이트 태그 값을 null로 설정하여 삭제합니다. 디바이스 업데이트 에이전트에서 디바이스 ID를 사용하는 경우 디바이스 쌍에서 이러한 변경을 수행합니다.

4. 아래와 같이 새 디바이스 업데이트 태그 값을 추가합니다.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>업데이트 가져오기

1. GitHub의 [디바이스 업데이트 릴리스](https://github.com/Azure/iot-hub-device-update/releases)로 이동하여 “자산” 드롭다운을 클릭합니다.

3. `Edge.package.update.samples.zip`을 클릭하여 다운로드합니다.

5. 폴더의 콘텐츠를 추출하여 [APT 매니페스트](device-update-apt-manifest.md) 샘플 및 해당 [가져오기 매니페스트](import-concepts.md)를 검색합니다. 

2. Azure Portal의 IoT Hub 왼쪽 탐색 모음에서 자동 디바이스 관리 아래에 있는 디바이스 업데이트 옵션을 선택합니다.

3. 업데이트 탭을 선택합니다.

4. "+ 새 업데이트 가져오기"를 선택합니다.

5. "매니페스트 파일 가져오기 선택"에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 파일 선택기 대화 상자가 표시됩니다. 이전에 다운로드한 폴더에서 `sample-1.0.1-aziot-edge-importManifest.json` 가져오기 매니페스트를 선택합니다. 다음으로 "하나 이상의 업데이트 파일 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 파일 선택기 대화 상자가 표시됩니다. 이전에 다운로드한 폴더에서 `sample-1.0.1-aziot-edge-apt-manifest.json` apt 매니페스트 업데이트 파일을 선택합니다.
이 업데이트는 `aziot-identity-service` 및 `aziot-edge` 패키지를 디바이스의 버전 1.2.0 ~ rc4-1로 업데이트합니다.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="파일 선택을 보여 주는 스크린샷" lightbox="media/import-update/select-update-files.png":::

6. "스토리지 컨테이너 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 그런 다음 적절한 스토리지 계정을 선택합니다.

7. 이미 컨테이너를 만든 경우 다시 사용할 수 있습니다. (그렇지 않으면 "+ 컨테이너"를 선택하여 업데이트를 위한 새 스토리지 컨테이너를 만듭니다.)  사용할 컨테이너를 선택하고 "선택"을 클릭합니다.

   :::image type="content" source="media/import-update/container.png" alt-text="스크린샷에 컨테이너 선택을 보여 주는 스크린샷" lightbox="media/import-update/container.png":::

8. "제출"을 선택하여 가져오기 프로세스를 시작합니다.

9. 가져오기 프로세스가 시작되고 화면이 "가져오기 기록" 섹션으로 변경됩니다. 가져오기 프로세스가 완료될 때까지 진행률을 보려면 "새로 고침"을 선택합니다. 업데이트 크기에 따라 가져오기 프로세는 몇 분 내에 완료될 수 있지만 시간이 오래 걸릴 수 있습니다.

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="업데이트 가져오기 시퀀스를 보여 주는 스크린샷" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. 상태 열에 가져오기가 성공했음을 나타내는 경우 "배포할 준비가 됨" 헤더를 선택합니다. 이제 목록에 가져온 업데이트가 표시됩니다.

업데이트 가져오기에 대해 [자세히 알아보세요](import-update.md).

## <a name="create-update-group"></a>업데이트 그룹 만들기

1. 이전에 디바이스 업데이트 인스턴스에 연결한 IoT Hub로 이동합니다.

1. 왼쪽 탐색 모음에서 자동 디바이스 관리 아래에 있는 디바이스 업데이트 옵션을 선택합니다.

1. 페이지 맨 위에서 그룹 탭을 선택합니다.

1. 추가 단추를 선택하여 새 그룹을 만듭니다.

1. 이전 단계에서 만든 IoT Hub 태그를 목록에서 선택합니다. 업데이트 그룹 만들기를 선택합니다.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="태그 선택을 보여 주는 스크린샷" lightbox="media/create-update-group/select-tag.PNG":::

태그를 추가하고 업데이트 그룹을 만드는 방법에 대해 [자세히 알아보기](create-update-group.md)

## <a name="deploy-update"></a>업데이트 배포

1. 그룹이 만들어지면 _사용 가능한 업데이트_ 열에 업데이트 링크와 함께 디바이스 그룹에 사용할 수 있는 새 업데이트가 표시됩니다. 한 번 새로 고쳐야 할 수 있습니다.

1. 사용 가능한 업데이트에 대한 링크를 클릭합니다.

1. 올바른 그룹이 대상 그룹으로 선택되었는지 확인하고, 배포를 예약합니다.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="업데이트 선택" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > 기본적으로 시작 날짜/시간은 현재 시간으로부터 24시간입니다. 배포를 더 일찍 시작하려면 다른 날짜/시간을 선택해야 합니다.

1. 업데이트 배포를 선택합니다.

1. 규정 준수 차트를 봅니다. 이제 업데이트가 진행 중인 것으로 표시됩니다. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="업데이트 진행 중" lightbox="media/deploy-update/update-in-progress.png":::

1. 디바이스를 성공적으로 업데이트한 후에는 규정 준수 차트와 배포 세부 정보가 업데이트되어 동일한 내용을 반영할 것입니다. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="업데이트 성공" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>업데이트 배포 모니터링

1. 페이지 맨 위에서 배포 탭을 선택합니다.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="배포 탭" lightbox="media/deploy-update/deployments-tab.png":::

1. 배포 세부 정보를 보려면 만든 배포를 선택합니다.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="배포 세부 정보" lightbox="media/deploy-update/deployment-details.png":::

1. 최신 상태 세부 정보를 보려면 새로 고침을 선택합니다. 상태가 성공으로 변경될 때까지 이 프로세스를 계속합니다.

이제 Ubuntu Server 18.04 x64 디바이스에서 IoT Hub에 대한 디바이스 업데이트를 사용하여 엔드투엔드 패키지 업데이트를 성공적으로 완료했습니다. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 디바이스 업데이트 계정, 인스턴스, IoT Hub 및 IoT Edge 디바이스(Azure에 배포 단추를 통해 VM을 만든 경우)를 정리합니다. 각 개별 리소스로 이동하고 "삭제"를 선택하여 이 작업을 수행할 수 있습니다. 디바이스 업데이트 인스턴스를 정리해야 디바이스 업데이트 계정을 정리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Raspberry Pi 3 B+에서 이미지 업데이트 자습서](device-update-raspberry-pi.md)
