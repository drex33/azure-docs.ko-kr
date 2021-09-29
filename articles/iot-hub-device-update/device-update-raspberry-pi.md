---
title: Raspberry Pi 3 B+ 참조 Yocto 이미지를 사용하는 Azure IoT Hub에 대한 디바이스 업데이트 자습서 | Microsoft Docs
description: Raspberry Pi 3 B+ 참조 Yocto 이미지를 사용하는 Azure IoT Hub에 대한 디바이스 업데이트를 시작합니다.
author: ValOlson
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: f1b5196d422d2bc4a961c2b45ed814ee5555f2c4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643843"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-raspberry-pi-3-b-reference-image"></a>Raspberry Pi 3 B+ 참조 Yocto 이미지를 사용하는 Azure IoT Hub에 대한 디바이스 업데이트 자습서

IoT Hub에 대한 디바이스 업데이트는 이미지 기반 및 패키지 기반의 두 가지 업데이트 형식을 지원합니다.

이미지 업데이트는 디바이스의 최종 상태에서 더 높은 수준의 신뢰도를 제공합니다. 일반적으로 사전 프로덕션 환경과 프로덕션 환경 간에 이미지 업데이트 결과를 복제하는 것이 더 쉽습니다. 패키지 및 해당 종속성과 동일한 문제를 일으키지 않기 때문입니다. 원자성 특성 때문에 A/B 장애 조치(failover) 모델을 쉽게 채택할 수 있습니다.

이 자습서에서는 Raspberry Pi 3 B+ 보드에서 Device Update for IoT Hub를 사용하여 엔드투엔드 이미지 기반 업데이트를 완료하는 단계를 설명합니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 이미지 다운로드
> * IoT 디바이스에 태그 추가
> * 업데이트 가져오기
> * 디바이스 그룹 만들기
> * 이미지 업데이트 배포
> * 업데이트 배포 모니터링

## <a name="prerequisites"></a>필수 조건
* 아직 수행하지 않은 경우 IoT Hub 구성을 포함하여 [디바이스 업데이트 계정 및 인스턴스](create-device-update-account.md)를 만듭니다.

## <a name="download-image"></a>이미지 다운로드

[디바이스 업데이트 GitHub 릴리스 페이지](https://github.com/Azure/iot-hub-device-update/releases)의 "자산"에 샘플 이미지가 있습니다. .gz 파일은 Raspberry Pi B3+ 보드에서 플래시할 수 있는 기본 이미지이며, swUpdate 파일은 Device Update for IoT Hub를 통해 가져올 업데이트입니다. 

## <a name="flash-sd-card-with-image"></a>이미지를 사용하여 SD 카드 플래시

선호하는 OS 플래시 도구를 사용하여 Raspberry Pi 3 B+ 디바이스에서 사용될 SD 카드에 디바이스 업데이트 기본 이미지(adu-base-image)를 설치합니다.

### <a name="using-bmaptool-to-flash-sd-card"></a>bmaptool을 사용하여 SD 카드 플래시

1. `bmaptool` 유틸리티를 설치합니다(아직 설치하지 않은 경우).

   ```shell
   sudo apt-get install bmap-tools
   ```

2. `/dev`에서 SD 카드의 경로를 찾습니다. 경로는 `/dev/sd*` 또는 `/dev/mmcblk*`와 같습니다. `dmesg` 유틸리티를 사용하여 올바른 경로를 쉽게 찾을 수 있습니다.

3. 탑재된 모든 파티션은 플래시하기 전에 분리해야 합니다.

   ```shell
   sudo umount /dev/<device>
   ```

4. 디바이스에 대한 쓰기 권한이 있는지 확인합니다.

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. 선택 사항입니다. 더 빨리 플래시하려면 이미지 파일과 함께 bimap 파일을 다운로드하여 동일한 디렉터리에 저장합니다.

6. SD 카드를 플래시합니다.

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
Azure IoT Hub에 대한 디바이스 업데이트 소프트웨어에는 다음 사용 조건이 적용됩니다.
   * [IoT Hub에 대한 디바이스 업데이트 라이선스](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [배달 최적화 클라이언트 라이선스](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
에이전트를 사용하기 전에 사용 조건을 읽어보세요. 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우 IoT Hub 에이전트에 대한 디바이스 업데이트를 사용하지 마세요.

## <a name="create-device-or-module-in-iot-hub-and-get-connection-string"></a>IoT Hub에서 디바이스나 모듈 만들기 및 연결 문자열 가져오기

이제 디바이스를 Azure IoT Hub에 추가해야 합니다.  Azure IoT Hub 내에서 디바이스에 대한 연결 문자열이 생성됩니다.

1. Azure Portal에서 Azure IoT Hub를 시작합니다.
2. 새 디바이스를 만듭니다.
3. 페이지 왼쪽에서 'IoT 디바이스'로 이동 > "새로 만들기"를 선택합니다.
4. '디바이스 ID' 아래에 디바이스 이름을 입력합니다. - "키 자동 생성" 확인란이 선택되어 있는지 확인하세요.
5. '저장'을 선택합니다.
6. 이제 '디바이스' 페이지로 돌아가면 만든 디바이스가 목록에 표시됩니다. 
7. 디바이스 연결 문자열을 가져옵니다.
    - 옵션 1 모듈 ID로 디바이스 업데이트 에이전트 사용: 동일한 '디바이스' 페이지 맨 위에 있는 '+ 모듈 ID 추가'를 클릭합니다. 이름이 'IoTHubDeviceUpdate'인 새 디바이스 업데이트 모듈을 만들고 사용 사례에 적용되는 다른 옵션을 선택한 다음, '저장'을 클릭합니다. 새로 만든 '모듈'을 클릭하고 모듈 보기에서 '기본 연결 문자열' 옆에 있는 '복사' 아이콘을 선택합니다.
    - 옵션 2 디바이스 ID로 디바이스 업데이트 에이전트 사용: 디바이스 보기에서 '기본 연결 문자열' 옆에 있는 '복사' 아이콘을 선택합니다.
8. 아래 단계에서 나중에 사용할 복사한 문자를 임의 위치에 붙여넣습니다.
   **복사된 이 문자열은 디바이스 연결 문자열입니다**.

## <a name="provision-connection-string-on-sd-card"></a>SD 카드에 연결 문자열 프로비저닝

1. Raspberry Pi3이 네트워크에 연결되어 있는지 확인합니다.
2. PowerShell에서 아래 명령을 사용하여 디바이스에 ssh를 수행합니다.
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. 'root'로 로그인을 입력하고 암호는 비어 있는 상태로 유지해야 합니다.
5. 디바이스에 성공적으로 ssh를 수행한 후에 아래 명령을 실행합니다.
 
`<device connection string>`을 연결 문자열로 바꿉니다.
 ```markdown
    echo "connection_string=<device connection string>" > /adu/adu-conf.txt  
    echo "aduc_manufacturer=ADUTeam" >> /adu/adu-conf.txt
    echo "aduc_model=RefDevice" >> /adu/adu-conf.txt
   ```

## <a name="connect-the-device-in-device-update-iot-hub"></a>디바이스 업데이트 IoT Hub에서 디바이스 연결

1. 페이지 왼쪽에서 'IoT 디바이스'를 선택합니다.
2. 디바이스 이름을 사용하여 링크를 선택합니다.
3. IoT 디바이스 ID를 사용하여 디바이스 업데이트에 직접 연결할 경우 페이지 맨 위에 있는 '디바이스 쌍'을 선택합니다. 그렇지 않으면 위에서 만든 모듈을 선택하고 '모듈 쌍'을 클릭합니다.
4. 디바이스 쌍 속성의 '보고됨' 섹션에서 Linux 커널 버전을 찾습니다.
디바이스 업데이트에서 업데이트를 받지 않은 새 디바이스의 경우 [DeviceManagement:DeviceInformation:1.swVersion](device-update-plug-and-play.md) 값은 디바이스에서 실행되는 펌웨어 버전을 나타냅니다.  업데이트가 디바이스에 적용되면 디바이스 업데이트는 [AzureDeviceUpdateCore:ClientMetadata:4.installedUpdateId](device-update-plug-and-play.md) 속성 값을 사용하여 디바이스에서 실행되는 펌웨어 버전을 나타냅니다.
5. 기본 이미지와 업데이트 이미지 파일의 이름에는 버전 번호가 있습니다.

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

아래의 업데이트 가져오기 단계에서 해당 버전 번호를 사용합니다.

## <a name="add-a-tag-to-your-device"></a>디바이스에 태그 추가

1. [Azure Portal](https://portal.azure.com)에 로그인하여 IoT Hub로 이동합니다.

2. 왼쪽 탐색 창의 'IoT 디바이스' 또는 'IoT Edge'에서 IoT 디바이스를 찾아 디바이스 쌍 또는 모듈 쌍으로 이동합니다.

3. 디바이스 업데이트 에이전트 모듈의 모듈 쌍에서 기존 디바이스 업데이트 태그 값을 null로 설정하여 삭제합니다. 디바이스 업데이트 에이전트에서 디바이스 ID를 사용하는 경우 디바이스 쌍에서 이러한 변경을 수행합니다.

4. 아래와 같이 새 디바이스 업데이트 태그 값을 추가합니다.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>업데이트 가져오기

1. [샘플 가져오기 매니페스트](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0/TutorialImportManifest_Pi.json) 및 [샘플 이미지 업데이트](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0-rc1/adu-update-image-raspberrypi3-0.6.5073.1.swu)를 다운로드합니다.
2. [Azure Portal](https://portal.azure.com/)에 로그인하고 디바이스 업데이트를 사용하여 IoT Hub로 이동합니다. 그런 다음, 왼쪽 탐색 모음에서 자동 디바이스 관리 아래에 있는 디바이스 업데이트 옵션을 선택합니다.
3. 업데이트 탭을 선택합니다.
4. "+ 새 업데이트 가져오기"를 선택합니다.
5. "매니페스트 파일 가져오기 선택"에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 파일 선택기 대화 상자가 표시됩니다. 위의 1단계에서 다운로드한 _샘플 가져오기 매니페스트_ 를 선택합니다.  다음으로 "하나 이상의 업데이트 파일 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 파일 선택기 대화 상자가 표시됩니다. 위의 1단계에서 다운로드한 _샘플 업데이트 파일_ 을 선택합니다.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="파일 선택을 보여 주는 스크린샷" lightbox="media/import-update/select-update-files.png":::

5. "스토리지 컨테이너 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 그런 다음 적절한 스토리지 계정을 선택합니다.

6. 이미 컨테이너를 만든 경우 다시 사용할 수 있습니다. (그렇지 않으면 "+ 컨테이너"를 선택하여 업데이트를 위한 새 스토리지 컨테이너를 만듭니다.)  사용할 컨테이너를 선택하고 "선택"을 클릭합니다.
  
  :::image type="content" source="media/import-update/container.png" alt-text="스크린샷에 컨테이너 선택을 보여 주는 스크린샷" lightbox="media/import-update/container.png":::

7. "제출"을 선택하여 가져오기 프로세스를 시작합니다.

8. 가져오기 프로세스가 시작되고 화면이 "가져오기 기록" 섹션으로 변경됩니다. 가져오기 프로세스가 완료될 때까지 진행률을 보려면 "새로 고침"을 선택합니다. 업데이트 크기에 따라 몇 분 내에 완료될 수 있지만 시간이 더 오래 걸릴 수 있습니다.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="업데이트 가져오기 시퀀스를 보여 주는 스크린샷" lightbox="media/import-update/update-publishing-sequence-2.png":::

9. 상태 열에 가져오기가 성공했음을 나타내는 경우 "배포할 준비가 됨" 헤더를 선택합니다. 이제 목록에 가져온 업데이트가 표시됩니다.

업데이트 가져오기에 대해 [자세히 알아보세요](import-update.md).

## <a name="create-update-group"></a>업데이트 그룹 만들기

1. 이전에 디바이스 업데이트 인스턴스에 연결한 IoT Hub로 이동합니다.

2. 왼쪽 탐색 모음에서 자동 디바이스 관리 아래에 있는 디바이스 업데이트 옵션을 선택합니다.

3. 페이지 맨 위에서 그룹 탭을 선택합니다. 

4. 추가 단추를 선택하여 새 그룹을 만듭니다.

5. 이전 단계에서 만든 IoT Hub 태그를 목록에서 선택합니다. 업데이트 그룹 만들기를 선택합니다.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="태그 선택을 보여 주는 스크린샷" lightbox="media/create-update-group/select-tag.PNG":::

태그를 추가하고 업데이트 그룹을 만드는 방법에 대해 [자세히 알아보기](create-update-group.md)


## <a name="deploy-update"></a>업데이트 배포

1. 그룹이 만들어지면 업데이트 보류 중 업데이트에 대한 링크를 사용하여 디바이스 그룹에 사용할 수 있는 새 업데이트가 표시됩니다. 한 번 새로 고쳐야 할 수 있습니다. 

2. 사용 가능한 업데이트를 클릭합니다.

3. 올바른 그룹이 대상 그룹으로 선택되었는지 확인합니다. 배포를 예약한 후 업데이트 배포를 선택합니다.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="업데이트 선택" lightbox="media/deploy-update/select-update.png":::

4. 규정 준수 차트를 봅니다. 이제 업데이트가 진행 중인 것으로 표시됩니다. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="업데이트 진행 중" lightbox="media/deploy-update/update-in-progress.png":::

5. 디바이스를 성공적으로 업데이트한 후에는 규정 준수 차트와 배포 세부 정보가 업데이트되어 동일한 내용을 반영할 것입니다. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="업데이트 성공" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>업데이트 배포 모니터링

1. 페이지 맨 위에서 배포 탭을 선택합니다.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="배포 탭" lightbox="media/deploy-update/deployments-tab.png":::

2. 배포 세부 정보를 보려면 만든 배포를 선택합니다.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="배포 세부 정보" lightbox="media/deploy-update/deployment-details.png":::

3. 최신 상태 세부 정보를 보려면 새로 고침을 선택합니다. 상태가 성공으로 변경될 때까지 이 프로세스를 계속합니다.

이제 Raspberry Pi 3 B+ 디바이스에서 IoT Hub에 대한 디바이스 업데이트를 사용하여 엔드투엔드 이미지 업데이트를 성공적으로 완료했습니다. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 디바이스 업데이트 계정, 인스턴스, IoT Hub 및 IoT 디바이스를 정리합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [시뮬레이터 참조 에이전트](device-update-simulator.md)
