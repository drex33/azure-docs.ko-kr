---
title: Azure Percept Vision AI 모듈
description: Azure Percept DK에서 AI 비전 워크로드를 실행하는 역할을 하는 모듈인 azureeyemodule의 개요입니다.
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: overview
ms.date: 08/09/2021
ms.custom: template-overview
ms.openlocfilehash: b09c5eb0dc2fa977c544d7da8178408dadcd08a6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222371"
---
# <a name="azure-percept-vision-ai-module"></a>Azure Percept Vision AI 모듈

azureeyemodule은 Azure Percept DK에서 AI 비전 워크로드를 실행하는 역할을 하는 에지 모듈의 이름입니다. 이는 Azure IoT 제품군 에지 모듈의 일부이며 [설치 환경](./quickstart-percept-dk-set-up.md)에서 Azure Percept DK에 배포됩니다. 이 문서는 모듈 및 해당 아키텍처의 개요를 제공합니다.

## <a name="architecture"></a>아키텍처

:::image type="content" source="media/azureeyemodule-overview/azureeyemodule-architecture.png" alt-text="azureeyemodule의 아키텍처를 보여주는 다이어그램":::

Azure Percept DK의 Azure Percept 워크로드는 azureeyemodule docker 컨테이너 내에서 실행되는 C++ 애플리케이션입니다. 이미지 처리 및 모델 실행을 위해 OpenCV GAPI를 사용합니다. azureeyemodule은 Azure Percept DK에서 실행되는 Azure IoT 제품군 모듈의 일부로 Mariner 운영 체제에서 실행됩니다.

Azure Percept 워크로드는 이미지 및 출력 이미지와 메시지를 사용합니다. 출력 이미지는 경계 상자, 조각화 마스크, 조인트, 레이블 등의 그리기로 표시될 수 있습니다. 출력 메시지는 다운스트림 작업에서 수집 및 사용할 수 있는 유추 결과의 JSON 스트림입니다.
결과는 디바이스의 포트 8554에서 사용할 수 있는 RTSP 스트림으로 처리됩니다. 결과는 포트 3000에서 실행되는 HTTP 서버에 래핑된 RTSP 스트림의 서비스를 제공하는 디바이스에서 실행되는 다른 모듈에도 제공됩니다. 어느 방법이든 로컬 네트워크에서만 볼 수 있습니다.

> [!CAUTION]
> RTSP 피드와 관련된 암호화 또는 인증이 *없습니다*. 로컬 네트워크의 모든 사용자는 올바른 주소를 웹 브라우저 또는 RTSP 미디어 플레이어에 입력하여 Azure Percept Vision에 표시되는 것을 정확하게 볼 수 있습니다.

Azure Percept 워크로드를 사용하면 최종 사용자가 사용할 수 있는 여러 기능을 사용할 수 있습니다.
- 일반적인 컴퓨터 비전 사용 사례(예: 개체 분류 및 공통 개체 검색)에 대한 비 코드 솔루션
- 개발자가 자체(잠재적으로 종속된) 학습된 모델을 디바이스에 가져와서 실행하여 디바이스에서 실행되는 자체 생성의 다른 IoT 모듈로 결과를 전달할 수 있는 고급 솔루션
- 디바이스에서 정기적으로 이미지를 가져오고, 클라우드에서 모델을 다시 학습한 다음, 새로 학습된 모델을 디바이스에 다시 푸시하는 재학습 루프 디바이스의 기능을 사용하여 즉시 모델을 업데이트하고 교환합니다.

## <a name="ai-workload-details"></a>AI 워크로드 세부 정보
워크로드 애플리케이션은 Azure Percept Advanced Development [github 리포지토리](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app)에서 오픈 소스로 사용되며, 많은 소규모 C++ 모듈로 구성되며, 그 중 일부는 다음과 같은 중요한 요소로 구성됩니다.
- [main.cpp](https://github.com/microsoft/azure-percept-advanced-development/blob/main/azureeyemodule/app/main.cpp): 모든 항목을 설정한 다음, 주 루프를 실행합니다.
- [iot](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/iot): 이 폴더에는 Azure IoT Edge Hub 및 쌍 업데이트 메서드에서 들어오고 나가는 메시지를 처리하는 모듈이 포함되어 있습니다.
- [model](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/model): 이 폴더에는 컴퓨터 비전 모델의 클래스 계층 구조에 대한 모듈이 포함되어 있습니다.
- [kernels](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/kernels): 이 폴더에는 G-API 커널, ops 및 C++ 래퍼 함수에 대한 모듈이 포함되어 있습니다.

개발자는 이 워크로드 애플리케이션을 사용하여 사용자 지정 모듈을 빌드하거나 현재 azureeyemodule를 사용자 지정할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- 이제 azureeyemodule 및 Azure Percept 워크로드에 대한 자세한 내용을 배웠으므로 [다음 자습서](https://github.com/microsoft/azure-percept-advanced-development/blob/main/tutorials/README.md) 중 하나를 수행하여 고유한 모델 또는 파이프라인을 사용해 보세요.
- 또는 바로 사용 가능한 **기계 학습 Notebook** 중 하나를 사용하여 [전이 학습](https://github.com/microsoft/azure-percept-advanced-development/tree/main/machine-learning-notebooks)을 시도해 보세요.

