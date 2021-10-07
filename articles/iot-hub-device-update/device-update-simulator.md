---
title: Ubuntu(18.04 x64) 시뮬레이터 참조 에이전트를 사용하는 Azure IoT Hub용 디바이스 업데이트 자습서 | Microsoft Docs
description: Ubuntu(18.04 x64) 시뮬레이터 참조 에이전트를 사용하여 Azure IoT Hub용 디바이스 업데이트를 시작합니다.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: d5a860455a66dc7db137b8d0eb7aad942271ef90
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128566315"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Ubuntu(18.04 x64) 시뮬레이터 참조 에이전트를 사용하는 Azure IoT Hub용 디바이스 업데이트 자습서

IoT Hub용 디바이스 업데이트는 이미지 기반 및 패키지 기반의 두 가지 업데이트 형태를 지원합니다.

이미지 업데이트는 디바이스의 최종 상태에서 더 높은 수준의 신뢰도를 제공합니다. 일반적으로 사전 프로덕션 환경과 프로덕션 환경 간에 이미지 업데이트 결과를 복제하는 것이 더 쉽습니다. 패키지 및 해당 종속성과 동일한 문제를 일으키지 않기 때문입니다. 원자성 특성 때문에 A/B 장애 조치(failover) 모델을 쉽게 채택할 수 있습니다.

이 자습서에서는 IoT Hub에 대한 디바이스 업데이트를 사용하여 엔드투엔드 이미지 기반 업데이트를 완료하는 단계를 안내합니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 이미지 다운로드 및 설치
> * IoT 디바이스에 태그 추가
> * 업데이트 가져오기
> * 디바이스 그룹 만들기
> * 이미지 업데이트 배포
> * 업데이트 배포 모니터링

## <a name="prerequisites"></a>필수 조건
* 아직 수행하지 않은 경우 IoT Hub 구성을 포함하여 [디바이스 업데이트 계정 및 인스턴스](create-device-update-account.md)를 만듭니다.

### <a name="download-and-install"></a>다운로드 및 설치

* PowerShell용 Az(Azure CLI) cmdlet:
  * PowerShell을 열고 Azure CLI를 설치합니다("신뢰할 수 없는" 원본에서 설치라는 메시지에 "Y"를 선택).

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>Windows 디바이스(Linux용 Windows 하위 시스템)에서 WSL을 사용하도록 설정

1. 컴퓨터에서 관리자 권한으로 PowerShell을 열고 다음 명령을 실행합니다. (각 단계 후 다시 시작하라는 메시지가 표시될 수 있습니다. 메시지가 표시되면 다시 시작하세요.)

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(이 단계를 완료한 후 다시 시작하라는 메시지가 표시될 수 있습니다.)

2. 웹에서 Microsoft Store로 이동하여 [Ubuntu 18.04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`)를 설치합니다.

3. "Ubuntu 18.04 LTS"를 시작하여 설치합니다.

4. 설치가 끝나면 루트 이름(사용자 이름) 및 암호를 설정하라는 메시지가 표시됩니다. 기억하기 쉬운 루트 이름 및 암호를 사용해야 합니다.

5. PowerShell에서 다음 명령을 실행하여 Ubuntu를 기본 Linux 배포로 설정합니다.

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. 모든 Linux 배포를 나열하여 Ubuntu가 기본값으로 설정되어 있는지 확인합니다.

```powershell
PS> wsl --list
```

7. 다음과 같이 표시되어야 합니다. **Ubuntu-18.04(기본값)**

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>디바이스 업데이트 Ubuntu(18.04 x64) 시뮬레이터 참조 에이전트 다운로드

Ubuntu 참조 에이전트는업데이트는 [여기](https://github.com/Azure/iot-hub-device-update/releases)에 있는 릴리스 정보의 *자산* 섹션에서 다운로드할 수 있습니다.

에이전트에는 두 가지 버전이 있습니다. 이 자습서에서는 이미지 기반 시나리오를 연습하고 있으므로 AducIotAgentSim-microsoft-swupdate를 사용합니다. 패키지 기반 시나리오를 대신 연습하려는 경우에는 AducIotAgentSim-microsoft-apt를 사용합니다.

## <a name="install-device-update-agent-simulator"></a>디바이스 업데이트 에이전트 시뮬레이터 설치

1. Ubuntu WSL을 시작하고 다음 명령을 입력합니다(끝에 추가 공백 및 점이 있음에 유의).

  ```shell
  explorer.exe .
  ```

2. 로컬 폴더의 /mnt 아래에 다운로드한 AducIotAgentSim-microsoft-swupdate(또는 AducIotAgentSim-microsoft-apt)를 WSL의 홈 폴더로 복사합니다.

3. 다음 명령을 실행하여 이진 파일 실행 파일을 만듭니다.

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  또는

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
Azure IoT Hub용 디바이스 업데이트 소프트웨어에는 다음 사용 조건이 적용됩니다.
   * [IoT Hub에 대한 디바이스 업데이트 라이선스](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [배달 최적화 클라이언트 라이선스](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
에이전트를 사용하기 전에 사용 조건을 읽어보세요. 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우 IoT Hub 에이전트에 대한 디바이스 업데이트를 사용하지 마세요.

## <a name="add-device-to-azure-iot-hub"></a>Azure IoT Hub에 디바이스 추가

디바이스 업데이트 에이전트가 IoT 디바이스에서 실행되면 디바이스를 Azure IoT Hub에 추가해야 합니다.  Azure IoT Hub 내에서 특정 디바이스에 대한 연결 문자열이 생성됩니다.

1. Azure Portal에서 디바이스 업데이트 IoT Hub를 시작합니다.
2. 새 디바이스를 만듭니다.
3. 페이지 왼쪽에서 'IoT 디바이스'로 이동 > "새로 만들기"를 선택합니다.
4. '디바이스 ID' 아래에 디바이스 이름을 입력합니다. - "키 자동 생성" 확인란이 선택되어 있는지 확인하세요.
5. '저장'을 선택합니다.
6. 이제 '디바이스' 페이지로 돌아가면 만든 디바이스가 목록에 표시됩니다. 해당 디바이스를 선택합니다.
7. 디바이스 보기에서 '기본 연결 문자열' 옆의 '복사' 아이콘을 선택합니다.
8. 아래 단계에서 나중에 사용할 복사한 문자를 임의 위치에 붙여넣습니다. **이 복사된 문자열은 디바이스 연결 문자열입니다**.

## <a name="add-connection-string-to-simulator"></a>시뮬레이터에 연결 문자열 추가

새 소프트웨어 디바이스에서 디바이스 업데이트 에이전트를 시작합니다.

1. Ubuntu를 시작합니다.
2. 디바이스 업데이트 에이전트를 실행하고 이전 섹션에서 아포스트로피를 사용하여 래핑된 디바이스 연결 문자열을 지정합니다.

`<device connection string>`을 연결 문자열로 바꿉니다.
```shell
sudo ./AducIotAgentSim-microsoft-swupdate "<device connection string>"
```

또는

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. 위로 스크롤하여 디바이스가 "유휴" 상태임을 나타내는 문자열을 찾습니다. "유휴" 상태는 디바이스가 서비스 명령을 수신할 준비가 됐음을 나타냅니다.

```markdown
Agent running. [main]
```

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

1. [샘플 가져오기 매니페스트](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0/TutorialImportManifest_Sim.json) 및 [샘플 이미지 업데이트](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0-rc1/adu-update-image-raspberrypi3-0.6.5073.1.swu)를 다운로드합니다. _참고_: 이 자습서의 업데이트가 시뮬레이션 되므로 특정 파일 콘텐츠가 중요하지 않기 때문에 이러한 업데이트 파일은 Raspberry Pi 자습서에서 다시 사용됩니다. 
2. [Azure Portal](https://portal.azure.com/)에 로그인하고 디바이스 업데이트를 사용하여 IoT Hub로 이동합니다. 그런 다음, 왼쪽 탐색 모음에서 자동 디바이스 관리 아래에 있는 디바이스 업데이트 옵션을 선택합니다.

3. 업데이트 탭을 선택합니다.

4. "+ 새 업데이트 가져오기"를 선택합니다.

5. "매니페스트 파일 가져오기 선택"에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 파일 선택기 대화 상자가 표시됩니다. 위의 1단계에서 다운로드한 _샘플 가져오기 매니페스트_ 를 선택합니다.  다음으로 "하나 이상의 업데이트 파일 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 파일 선택기 대화 상자가 표시됩니다. 위의 1단계에서 다운로드한 _샘플 이미지 업데이트_ 를 선택합니다. 

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="파일 선택을 보여 주는 스크린샷" lightbox="media/import-update/select-update-files.png":::

6. "스토리지 컨테이너 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 그런 다음 적절한 스토리지 계정을 선택합니다.

7. 이미 컨테이너를 만든 경우 다시 사용할 수 있습니다. (그렇지 않으면 "+ 컨테이너"를 선택하여 업데이트를 위한 새 스토리지 컨테이너를 만듭니다.)  사용할 컨테이너를 선택하고 "선택"을 클릭합니다.
  
  :::image type="content" source="media/import-update/container.png" alt-text="스크린샷에 컨테이너 선택을 보여 주는 스크린샷" lightbox="media/import-update/container.png":::

8. "제출"을 선택하여 가져오기 프로세스를 시작합니다.

9. 가져오기 프로세스가 시작되고 화면이 "가져오기 기록" 섹션으로 변경됩니다. 가져오기 프로세스가 완료될 때까지 진행률을 보려면 "새로 고침"을 선택합니다. 업데이트 크기에 따라 몇 분 내에 완료될 수 있지만 시간이 더 오래 걸릴 수 있습니다.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="업데이트 가져오기 시퀀스를 보여 주는 스크린샷" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. 상태 열에 가져오기가 성공했음을 나타내는 경우 "배포할 준비가 됨" 헤더를 선택합니다. 이제 목록에 가져온 업데이트가 표시됩니다.

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

이제 Ubuntu(18.04 x64) 시뮬레이터 참조 에이전트를 사용하는 IoT Hub용 디바이스 업데이트를 사용하여 엔드투엔드 이미지 업데이트를 성공적으로 완료했습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 디바이스 업데이트 계정, 인스턴스, IoT Hub 및 IoT 디바이스를 정리합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [문제 해결](troubleshoot-device-update.md)

