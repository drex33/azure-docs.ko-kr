---
title: Azure Percept DK 컨테이너 릴리스 정보
description: Azure Percept DK 컨테이너 릴리스에 대한 변경 내용 및 수정 사항 정보입니다.
author: amiyouss
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: 5b4e67197e5d43e929ca01c8c81e297e134edf47
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271615"
---
# <a name="azure-percept-dk-container-release-notes"></a>Azure Percept DK 컨테이너 릴리스 정보

이 페이지에서는 Azure Percept DK 컨테이너 릴리스에 대한 변경 내용 및 수정 사항에 대한 정보를 제공합니다.

컨테이너 업데이트를 다운로드하려면 [Azure Percept Studio](https://ms.portal.azure.com/#blade/AzureEdgeDevices/main/overview)이동하고, 왼쪽 탐색 창에서 디바이스를 선택하고, 특정 디바이스를 선택한 다음, 비전 및 음성 탭을 선택하여 컨테이너 다운로드를 시작합니다. 

## <a name="august-2108-release"></a>8월(2108) 릴리스

- Azureyemodule(mcr.microsoft.com/azureedgedevices/azureeyemodule:2108-1)
    - 고사용성X 카메라 펌웨어 업데이트에 대한 Intel 최신(5월) 드롭으로 업데이트되었습니다. 
    - UVC(USB 비디오 클래스) 카메라를 입력 원본으로 사용하도록 설정했습니다. UVC 카메라를 입력 원본으로 사용하는 방법은 [고급 개발 github를](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule#using-uvcusb-video-class-camera-as-input-source) 참조합니다. 
    - H.264 원시 RTSP 스트림을 사용할 때 모듈 충돌이 수정되었습니다.

## <a name="june-2106-release"></a>6월(2016년) 릴리스

- Azureyemodule
    - 이 릴리스에서는 느린 신경망의 유추를 비디오 스트림과 시간 맞춤에 대한 지원을 추가합니다. 비디오 스트림에 대기 시간이 신경망의 대기 시간 정도와 같게 추가되지만 적절한 위치의 비디오를 통해 유추(예: 경계 상자)가 그려집니다. 
    - 이 기능을 사용하도록 설정하려면 `TimeAlignRTSP: true` IoT Hub Azure Portal 모듈 쌍에 를 추가합니다.
- Azureearspeechclientmodule
    - [Speech 모듈에 Speech 1.16 SDK를](../cognitive-services/speech-service/devices-sdk-release-notes.md) 통합했습니다. 이 모듈에는 음성 토큰 지원에 대한 수정이 포함되며, 낮은 수준의 lib에서 EAR SOM을 기본 지원 디바이스로 통합합니다.
    - 보안 MCU가 제거되었지만 코덱이 제거되지 않은 동안 PnP 검색 문제가 해결되었습니다.
    - PTT/PTS 단추 함수를 통해 Speech Service 시간 제한 문제를 해결했습니다.
    - 보안 수정
        - 범위를 벗어났습니다. 음성 모듈에서 TLS 데이터 수신을 읽습니다.
        - MCU 및 코덱 USB 포트는 DFU를 수행하는 동안 다시 노출됩니다.
        - JSON을 구문 분석할 때 예외 처리
        - 강화된 모든 컴파일러 보안 플래그를 사용하도록 설정
        - 범위를 벗어났습니다. 음성 모듈에서 TLS 데이터 수신을 읽습니다.
        - SSL 및 libcurl 의존성 버전 및 취약한 버전을 방지합니다.
        - HTTPS 적용 및 curl 연결에 TLS CA 고정.

## <a name="april-2104-release"></a>4월(2014년) 릴리스

- Azureyemodule
    - IoT Hub 메시지 형식이 UTF-8로 인코딩된 JSON(이전에는 64비트 인코딩 형식)으로 수정되었습니다.
    - Custom Vision 분류자(이전에는 Custom Vision 분류자 모델이 제대로 작동하지 않아서 출력 텐서의 잘못된 차원을 클래스 신뢰도로 해석하여 신뢰도에 관계없이 항상 단일 클래스를 예측하게 됨)를 수정했습니다.
    - Azure 비디오 분석기 통합을 위해 UDP 대신 TCP를 사용하도록 H.264가 업데이트되었습니다.

## <a name="next-steps"></a>다음 단계

- [업데이트 전략을 확인하는 방법](./how-to-determine-your-update-strategy.md)
