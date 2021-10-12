---
title: Azure Kinect DK 다시 설정
description: Azure Kinect DK 디바이스를 공장 이미지로 다시 설정하는 방법을 설명합니다.
author: martinekuan
ms.author: martinek
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: Kinect, 다시 설정
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: 71579ba69852dd3f5359526a70f8b99d266344e4
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808880"
---
# <a name="reset-azure-kinect-dk"></a>Azure Kinect DK 다시 설정

펌웨어 업데이트가 제대로 설치되지 않은 경우와 같이 Azure Kinect DK를 공장 이미지로 다시 설정해야 하는 상황이 발생할 수 있습니다.

1. Azure Kinect DK 전원을 끕니다. 이렇게 하려면 USB 케이블과 전원 케이블을 분리합니다.
  ![다시 설정 단추를 보호하는 나사의 위치를 보여 주는 다이어그램](media/reset-azure-kinect-dk-diagram.png)
1. 다시 설정 단추를 찾으려면 삼각대 잠금 장치에 있는 나사를 제거합니다.
1. 전원 케이블을 다시 연결합니다.
1. 곧게 펼친 종이 클립의 끝을 삼각대 잠금 장치의 빈 나사 구멍에 삽입합니다.
1. 종이 클립을 사용하여 다시 설정 단추를 살짝 길게 누릅니다.
1. 다시 설정 단추를 누른 상태에서 USB 케이블을 다시 연결합니다.
1. 약 3초 후 전원 표시등이 황색으로 변경됩니다. 표시등이 변경되면 다시 설정 단추를 해제합니다.  
   
   다시 설정 단추를 해제하면 디바이스가 다시 설정되는 동안 전원 표시등이 흰색과 황색으로 깜박입니다. 
1. 전원 표시등이 흰색으로 고정될 때까지 기다립니다.
1. 삼각대 잠금 장치의 나사를 다시 설정 단추 위에 놓습니다.
1. Azure Kinect 뷰어를 사용하여 펌웨어가 다시 설정되었는지 확인합니다. 이렇게 하려면 [Azure Kinect 뷰어](azure-kinect-viewer.md)를 시작한 다음, **디바이스 펌웨어 버전 정보** 를 선택하여 Azure Kinect DK에 설치된 펌웨어 버전을 확인합니다.

항상 최신 펌웨어가 디바이스에 설치되어 있는지 확인합니다. 최신 펌웨어 버전을 가져오려면 Azure Kinect 펌웨어 도구를 사용합니다. 펌웨어 상태를 확인하는 방법에 대한 자세한 내용은 [디바이스 펌웨어 버전 확인](azure-kinect-firmware-tool.md#check-device-firmware-version)을 참조하세요.

## <a name="related-topics"></a>관련 항목

- [Azure Kinect DK 정보](about-azure-kinect-dk.md)
- [Azure Kinect DK 설정](set-up-azure-kinect-dk.md)
- [Azure Kinect DK 하드웨어 사양: 운영 환경](hardware-specification.md#operating-environment)
- [Azure Kinect 펌웨어 도구](azure-kinect-firmware-tool.md)
- [Azure Kinect 뷰어](azure-kinect-viewer.md)
- [여러 Azure Kinect DK 디바이스 간의 동기화](multi-camera-sync.md)
