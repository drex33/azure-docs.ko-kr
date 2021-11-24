---
title: 빠른 시작 - Azure Kinect DK 설정
description: 이 빠른 시작에서는 Azure Kinect DK 하드웨어를 설정하는 방법에 대한 지침을 제공합니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: Azure, Kinect, 개발 키트, Azure DK, 설정, 하드웨어, 빠른 시작, USB, 전원, 뷰어, 센서, 스트리밍, 설정, SDK, 펌웨어
ms.custom: CI 114092, CSSTroubleshooting, mode-other
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: 7f6ec3cfdf3edf678f4b8f04a21e2f4c00f16c8b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133052676"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>빠른 시작: Azure Kinect DK 설정

이 빠른 시작에서는 Azure Kinect DK를 설정하는 방법에 대한 지침을 제공합니다. 센서 스트림 시각화를 테스트하고 [Azure Kinect 뷰어](azure-kinect-viewer.md)를 사용하는 방법을 보여 줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="system-requirements"></a>시스템 요구 사항

[시스템 요구 사항](system-requirements.md)을 확인하여 호스트 PC 구성이 모든 Azure Kinect DK 최소 요구 사항을 충족하는지 확인합니다.

## <a name="set-up-hardware"></a>하드웨어 설정

> [!NOTE]
> 디바이스를 사용하기 전에 먼저 카메라 보호 필름을 제거하세요.

1. 전원 커넥터를 디바이스 후면의 전원 잭에 꽂습니다. USB 전원 어댑터를 케이블의 다른 끝에 연결한 다음, 어댑터를 전원 콘센트에 꽂습니다.
2. USB 데이터 케이블을 디바이스에 연결한 다음, PC의 USB 3.0 포트에 연결합니다.
   >[!NOTE]
   >최상의 결과를 얻으려면 케이블을 디바이스와 PC에 직접 연결합니다. 확장 또는 추가 어댑터를 연결에 사용하지 마세요.

3. 전원 표시기 LED(USB 케이블 옆)가 흰색으로 고정되어 있는지 확인합니다.
  
   디바이스 전원을 켜는 데 몇 초 정도 걸립니다. 전면 LED 스트리밍 표시기가 꺼지면 디바이스를 사용할 수 있습니다.  

   전원 표시기 LED에 대한 자세한 내용은 [표시등이란?](hardware-specification.md#what-does-the-light-mean)을 참조하세요.

    ![전체 디바이스 기능](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>SDK 다운로드

1. [SDK 다운로드](sensor-sdk-download.md)에 대한 링크를 선택합니다.
2. SDK를 PC에 설치합니다.

## <a name="update-firmware"></a>펌웨어 업데이트

제대로 작동하려면 SDK에 최신 버전의 디바이스 펌웨어가 필요합니다. 펌웨어 버전을 확인하고 업데이트하려면 [Azure Kinect DK 펌웨어 업데이트](update-device-firmware.md)의 단계를 따릅니다.

## <a name="verify-that-the-device-streams-data"></a>디바이스에서 데이터를 스트림하는지 확인

1. [Azure Kinect 뷰어](azure-kinect-viewer.md)를 시작합니다. 이 도구는 다음 방법 중 하나를 사용하여 시작할 수 있습니다.
   - 뷰어를 명령줄에서 시작하거나 실행 파일을 두 번 클릭하여 시작할 수 있습니다. `k4aviewer.exe` 파일은 SDK 도구 디렉터리에 있습니다(예: `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, 여기서 `X.Y.Z`는 설치된 SDK 버전임).
   - 디바이스 **시작** 메뉴에서 Azure Kinect 뷰어를 시작할 수 있습니다.
2. Azure Kinect 뷰어에서 **디바이스 열기** > **시작** 을 차례로 선택합니다.

    ![Azure Kinect 뷰어](./media/quickstarts/viewer.png)

3. 도구에서 각 센서 스트림을 시각화하는지 확인합니다.
   - 깊이 카메라
   - 컬러 카메라
   - 적외선 카메라
   - IMU
   - 마이크

    ![시각화 도구](./media/quickstarts/visualization-tool.png)

Azure Kinect DK 설정이 완료되었습니다. 이제 애플리케이션 개발 또는 서비스 통합을 시작할 수 있습니다.

문제가 있는 경우 [문제 해결](troubleshooting.md)을 확인하세요.

## <a name="see-also"></a>참고 항목

- [Azure Kinect DK 하드웨어 정보](hardware-specification.md)
- [디바이스 펌웨어 업데이트](update-device-firmware.md)
- [Azure Kinect 뷰어](azure-kinect-viewer.md)에 대한 자세한 정보

## <a name="next-steps"></a>다음 단계

Azure Kinect DK가 준비되어 작동하면 다음을 수행하는 방법을 알아볼 수도 있습니다.
> [!div class="nextstepaction"]
> [센서 스트림을 파일에 기록](record-sensor-streams-file.md)
