---
title: 빠른 시작- Azure Kinect 센서 스트림을 파일에 기록
description: 이 빠른 시작에서는 센서 SDK에서 파일로 데이터 스트림을 기록하는 방법에 대해 알아봅니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: azure, kinect, record, play back, reader, matroska, mkv, streams, depth, rgb, camera, color, imu, audio, sensor
ms.custom: mode-other
ms.openlocfilehash: 14461671199fbc08864da8d90864f73c2c5ee6fe
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133052602"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>빠른 시작: Azure Kinect 센서 스트림을 파일에 기록

이 빠른 시작에서는 [Azure Kinect 레코더](azure-kinect-recorder.md) 도구를 사용하여 센서 SDK에서 파일로 데이터 스트림을 기록하는 방법에 대한 정보를 제공합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작에서는 다음을 가정합니다.

- Azure Kinect DK가 호스트 PC에 연결되어 있고 제대로 작동하고 있습니다.
- 하드웨어 [설정](set-up-azure-kinect-dk.md)을 완료했습니다.

## <a name="create-recording"></a>기록 만들기

1. 명령 프롬프트를 열고 설치된 도구 디렉터리에 있는 [Azure Kinect 레코더](azure-kinect-recorder.md)의 경로를 `k4arecorder.exe`로 제공합니다. 예: `C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe`
2. 5초 간 기록합니다.

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. 기본적으로 레코더는 NFOV Unbinned 깊이 모드를 사용하고 IMU 데이터를 포함한 30fps로 1080p RGB를 출력합니다. 기록 옵션 및 팁에 대한 전체 개요는 [Azure Kinect 레코더](azure-kinect-recorder.md)를 참조하세요.

## <a name="play-back-recording"></a>녹음/녹화 재생

[Azure Kinect Viewer](azure-kinect-viewer.md)를 사용하여 녹음/녹화를 재생할 수 있습니다.

1. 시작 [`k4aviewer.exe`](azure-kinect-viewer.md)
2. **기록 열기** 탭을 펼치고 기록을 엽니다.

## <a name="next-steps"></a>다음 단계

이제 파일에 센서 스트림을 기록하는 방법에 대해 알아보았습니다.

> [!div class="nextstepaction"]
> [첫 번째 Azure Kinect 애플리케이션 빌드](build-first-app.md)
