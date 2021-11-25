---
title: 빠른 시작 - Azure Kinect 본문 추적 설정
description: 이 빠른 시작에서는 Azure Kinect에 대한 본문 추적 SDK를 설정합니다.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, sensor, access, depth, sdk, body, tracking, joint, setup, cuda, nvidia
ms.custom: mode-other
ms.openlocfilehash: 675f0d349af1c4b1192d9638d68a507b377b3bc7
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133074725"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>빠른 시작: Azure Kinect 본문 추적 설정

이 빠른 시작에서는 Azure Kinect DK에서 실행되는 본문 추적을 가져오는 과정을 안내합니다.

## <a name="system-requirements"></a>시스템 요구 사항

본문 추적 SDK를 사용하려면 NVIDIA GPU가 호스트 PC에 설치되어 있어야 합니다. 권장되는 본문 추적 호스트 PC 요구 사항은 [시스템 요구 사항](system-requirements.md) 페이지에 설명되어 있습니다.

## <a name="install-software"></a>소프트웨어 설치

### <a name="install-the-latest-nvidia-driver"></a>[최신 NVIDIA 드라이버 설치](https://www.nvidia.com/Download/index.aspx?lang=en-us)

그래픽 카드용 최신 NVIDIA 드라이버를 다운로드하여 설치합니다. 이전 드라이버는 본문 추적 SDK를 사용하여 재배포된 CUDA 이진 파일과 호환되지 않을 수 있습니다.

### <a name="visual-c-redistributable-for-visual-studio-2015"></a>[Visual Studio 2015용 Visual C++ 재배포 가능 패키지](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

Visual Studio 2015용 Visual C++ 재배포 가능 패키지를 다운로드하여 설치합니다. 

## <a name="set-up-hardware"></a>하드웨어 설정

### <a name="set-up-azure-kinect-dk"></a>[Azure Kinect DK 설정](set-up-azure-kinect-dk.md)

[Azure Kinect 뷰어](azure-kinect-viewer.md)를 시작하여 Azure Kinect DK가 올바르게 설정되었는지 확인합니다.

## <a name="download-the-body-tracking-sdk"></a>본문 추적 SDK 다운로드
 
1. [본문 추적 SDK 다운로드](body-sdk-download.md)에 대한 링크를 선택합니다.
2. PC에 본문 추적 SDK를 설치합니다.

## <a name="verify-body-tracking"></a>본문 추적 확인

**Azure Kinect 본문 추적 뷰어** 를 시작하여 본문 추적 SDK가 올바르게 설정되었는지 확인합니다. 뷰어는 SDK msi 설치 관리자를 사용하여 설치됩니다. 시작 메뉴 또는 `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe`에서 찾을 수 있습니다.

충분한 GPU가 없지만 계속 결과를 테스트하려는 경우 `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU` 명령을 사용하여 명령줄에서 **Azure Kinect 본문 추적 뷰어** 를 시작할 수 있습니다.

모든 항목이 올바르게 설정되면 3D 지점 클라우드와 추적된 본문이 있는 창이 표시됩니다.


![본문 추적 3D 뷰어](./media/quickstarts/samples-simple3dviewer.png)

## <a name="examples"></a>예제

[여기](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples)에서 본문 추적 SDK를 사용하는 방법에 대한 예제를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[첫 번째 본문 추적 애플리케이션 빌드](build-first-body-app.md)
