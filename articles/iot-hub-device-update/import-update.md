---
title: 새 업데이트를 추가하는 방법 | Microsoft Docs
description: Device Update for IoT Hub에 새 업데이트를 추가하기 위한 방법 가이드입니다.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 4/19/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: caa58c1affc9e4a9cc9d3ec5b63e0b396e289144
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370425"
---
# <a name="add-an-update-to-device-update-for-iot-hub"></a>Device Update for IoT Hub에 업데이트 추가
Device Update for IoT Hub에 새 업데이트를 추가하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* [IoT Hub용 디바이스 업데이트를 사용으로 설정하여 IoT Hub에 액세스합니다](create-device-update-account.md). 
* IoT Hub 내에서 [Device Update를 위해 프로비저닝된](device-update-agent-provisioning.md) IoT 디바이스(또는 시뮬레이터).
* [PowerShell 5](/powershell/scripting/install/installing-powershell) 이상(Linux, macOS 및 Windows 설치 포함)
* 지원되는 브라우저:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> 이 서비스에 제출한 일부 데이터는 이 인스턴스가 생성된 지역 외부의 지역에서 처리할 수 있습니다.

## <a name="obtain-an-update-for-your-devices"></a>디바이스에 대한 업데이트 얻기

Device Update를 설정하고 디바이스를 프로비저닝했으므로 이제 해당 디바이스에 배포할 업데이트 파일이 필요합니다.

OEM 또는 솔루션 통합자에서 디바이스를 구매한 경우 해당 조직에서는 사용자가 업데이트를 만들 필요가 없도록 업데이트 파일을 제공할 가능성이 높습니다. 업데이트를 제공하는 방법을 알아보려면 OEM 또는 솔루션 통합자에 문의하세요.

사용자가 사용하는 디바이스용 소프트웨어를 조직에서 이미 만든 경우에는 동일한 그룹이 해당 소프트웨어의 업데이트를 만듭니다. Device Update for IoT Hub를 사용하여 배포할 업데이트를 만드는 경우 시나리오에 따라 [이미지 기반 또는 패키지 기반 접근 방식](understand-device-update.md#support-for-a-wide-range-of-update-artifacts)으로 시작합니다. 참고: 자체 업데이트를 만들려고 하지만 이제 막 시작하는 경우 GitHub는 개발을 관리하는 훌륭한 옵션입니다. [GitHub Actions](https://docs.github.com/en/actions/guides/about-continuous-integration)를 사용하여 원본 코드를 저장 및 관리하고 CI(연속 통합)와 CD(지속적인 배포)를 수행할 수 있습니다.

## <a name="create-a-device-update-import-manifest"></a>Device Update 가져오기 매니페스트 만들기

아직 수행하지 않은 경우 기본 [가져오기 개념](import-concepts.md)를 숙지하고 [먼저 이미지 기반](device-update-raspberry-pi.md) 또는 패키지 [기반](device-update-ubuntu-agent.md) 자습서를 사용해 보세요.

1. 업데이트 파일이 PowerShell에서 액세스할 수 있는 디렉터리에 있는지 확인합니다.

2. 업데이트 이미지 파일 또는 APT 매니페스트 파일이 있는 디렉터리에 **AduUpdate.psm1** 이라는 텍스트 파일을 만듭니다. 그런 다음 [AduUpdate.psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) PowerShell cmdlet을 열고 콘텐츠를 텍스트 파일에 복사한 다음, 텍스트 파일을 저장합니다.

3. PowerShell에서는 2단계에서 PowerShell cmdlet을 만든 디렉터리로 이동합니다. 아래 복사 옵션을 사용한 다음 PowerShell에 붙여 넣어 명령을 실행합니다.

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. 샘플 매개 변수 값을 바꿔 업데이트를 설명하는 JSON 파일인 가져오기 매니페스트를 생성하여 다음 명령을 실행합니다.
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    다음 표는 위의 매개 변수를 채우는 방법에 대한 빠른 참조입니다. 자세한 정보가 필요한 경우 전체 [가져오기 매니페스트 스키마](import-schema.md)도 확인할 수 있습니다.

    | 매개 변수 | Description |
    | --------- | ----------- |
    | deviceManufacturer | 업데이트가 호환되는(예: Contoso) 디바이스의 제조업체입니다. _제조업체_ [디바이스 속성](./device-update-plug-and-play.md#device-properties)과 일치해야 합니다.
    | deviceModel | 업데이트가 호환되는 디바이스(예: Toaster)의 모델입니다. _모델_[디바이스 속성](./device-update-plug-and-play.md#device-properties)과 일치해야 합니다.
    | updateProvider | 업데이트를 만들거나 직접 담당하는 엔터티입니다. 회사 이름인 경우가 많습니다.
    | updateName | 업데이트 클래스의 식별자입니다. 클래스는 사용자가 임의로 선택할 수 있습니다. 디바이스 또는 모델 이름인 경우가 많습니다.
    | updateVersion | 이 업데이트를 공급자와 이름이 같은 다른 업데이트와 구별하는 버전 번호입니다. 디바이스에 있는 개별 소프트웨어 구성 요소의 버전과 일치하지 않습니다(하지만 원하는 경우 가능).
    | updateType | <ul><li>이미지 업데이트의 경우 `microsoft/swupdate:1` 지정</li><li>패키지 업데이트의 경우 `microsoft/apt:1` 지정</li></ul>
    | installedCriteria | 배포 중에 디바이스에 이미 있는 버전을 업데이트 버전과 비교하는 데 사용됩니다. installedCriteria 값이 디바이스에 있는 버전과 일치하지 않으면 디바이스에 업데이트를 배포하면 "실패" 결과가 반환됩니다.<ul><li>`microsoft/swupdate:1` 업데이트 형식의 경우 SWVersion 값을 지정합니다. </li><li>`microsoft/apt:1` 업데이트 형식의 경우 **name-version** 을 지정합니다. 여기서, _name_ 은 APT 매니페스트의 이름이고 _version_ 은 APT 매니페스트의 버전입니다. 예: contoso-iot-edge-1.0.0.0.
    | updateFilePath(s) | 컴퓨터의 업데이트 파일 경로


## <a name="review-the-generated-import-manifest"></a>생성된 가져오기 매니페스트 검토

예제 매니페스트 출력은 다음과 같습니다. 이 예제에는 이 업데이트를 구성하는 두 개의 파일인 .json 파일과 .zip 파일이 있습니다. 항목에 대한 질문이 있는 경우 전체 [가져오기 매니페스트 스키마](import-schema.md)를 확인합니다. 
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-an-update"></a>업데이트 가져오기

> [!NOTE]
> 아래 지침에서는 Azure Portal UI를 통해 업데이트를 가져오는 방법을 보여 줍니다. [Device Update for IoT Hub API](#if-youre-importing-via-apis-instead)를 사용하여 업데이트를 대신 가져올 수도 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 디바이스 업데이트를 사용하여 IoT Hub로 이동합니다.

2. 페이지 왼쪽의 “자동 디바이스 관리”에서 “디바이스 업데이트”를 선택합니다.

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="업데이트 가져오기" lightbox="media/import-update/import-updates-3.png":::

3. 화면 위쪽에 여러 탭이 표시됩니다. 업데이트 탭을 선택합니다.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="업데이트" lightbox="media/import-update/updates-tab.png":::

4. “배포 준비 완료” 헤더에 있는 “+ 새 업데이트 가져오기”를 선택합니다.

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="새 업데이트 가져오기" lightbox="media/import-update/import-new-update-2.png":::

5. "매니페스트 파일 가져오기 선택"에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 파일 선택기 대화 상자가 표시됩니다. PowerShell cmdlet을 사용하여 이전에 만든 가져오기 매니페스트를 선택합니다. 다음으로 "하나 이상의 업데이트 파일 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 파일 선택기 대화 상자가 표시됩니다. 가져오기 매니페스트를 만들 때 포함했던 것과 동일한 업데이트 파일을 선택합니다.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="업데이트 파일 선택" lightbox="media/import-update/select-update-files.png":::

6. "스토리지 컨테이너 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 그런 다음 적절한 스토리지 계정을 선택합니다. 스토리지 컨테이너는 업데이트 파일을 임시로 스테이징하는 데 사용됩니다.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Storage 계정" lightbox="media/import-update/storage-account.png":::

7. 이미 컨테이너를 만든 경우 다시 사용할 수 있습니다. (그렇지 않으면 "+ 컨테이너"를 선택하여 업데이트를 위한 새 스토리지 컨테이너를 만듭니다.)  사용할 컨테이너를 선택하고 "선택"을 클릭합니다.

   :::image type="content" source="media/import-update/container.png" alt-text="컨테이너 선택" lightbox="media/import-update/container.png":::

8. "제출"을 선택하여 가져오기 프로세스를 시작합니다.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="업데이트 게시" lightbox="media/import-update/publish-update.png":::

9. 가져오기 프로세스가 시작되고 화면이 “가져오기 기록” 섹션으로 전환됩니다. 가져오기 프로세스가 완료될 때까지 진행률을 보려면 “새로 고침”을 선택합니다(업데이트 크기에 따라 몇 분 안에 완료될 수 있지만, 더 오래 걸릴 수 있음).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="가져오기 시퀀싱 업데이트" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. 상태 열에 가져오기가 성공했음을 나타내는 경우 "배포할 준비가 됨" 헤더를 선택합니다. 이제 목록에 가져온 업데이트가 표시됩니다.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="작업 상태" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>다음 단계

[그룹 만들기](create-update-group.md)

[가져오기 개념에 관해 알아보기](import-concepts.md)

## <a name="if-youre-importing-via-apis-instead"></a>대신 API를 통해 가져오는 경우

Azure Portal을 통해 가져오는 대신 [Device Update for IoT Hub 업데이트 API](/rest/api/deviceupdate/updates)를 사용하여 업데이트를 가져오려면 다음을 참고하세요.
  - 업데이트 API를 호출하기 전에 Azure Blob Storage 위치에 업데이트 파일을 업로드해야 합니다.
  - 위에서 만든 가져오기 매니페스트를 사용하는 이 [샘플 API 호출](import-schema.md#example-import-request-body)을 참조할 수 있습니다.
  - 테스트하는 동안 동일한 SAS URL을 다시 사용하는 경우 토큰이 만료되면 오류가 발생할 수 있습니다. 이는 가져오기 매니페스트 뿐만 아니라 업데이트 콘텐츠 자체를 제출하는 경우입니다.