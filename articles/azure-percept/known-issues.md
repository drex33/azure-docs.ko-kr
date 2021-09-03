---
title: Azure Percept의 알려진 문제
description: Azure Percept의 알려진 문제 및 해결 방법에 대한 자세한 정보
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: bf1e6f1297e73811e4ce7044319b13dc4c5b149f
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295871"
---
# <a name="azure-percept-known-issues"></a>Azure Percept의 알려진 문제

다음은 제품 팀이 알고 있는 Azure Percept DK, Azure Percept Audio 또는 Azure Percept Studio의 문제입니다. 가능한 경우 해결 방법 및 문제 해결 단계가 제공됩니다. 이러한 문제로 인해 차단된 경우 [Microsoft Q&A](/answers/topics/azure-percept.html)에 질문으로 게시하거나 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에서 고객 지원 요청을 제출할 수 있습니다. 

|영역|증상|문제 설명|해결 방법|
|-------|---------|---------|---------|
| Azure Percept DK | Azure Percept Studio에서 샘플 및 데모 모델을 배포할 수 없음 | 때때로 azureeyemodule 또는 azureearspeechmodule 모듈이 실행을 중지합니다. edgeAgent 로그에 "기호화된 링크 수준이 너무 많음" 오류가 표시됩니다. | [USB를 통해 업데이트](./how-to-update-via-usb.md)하여 디바이스 재설정 |
| 지역화 | 영어를 사용하지 않는 사용자는 Azure Percept DK 설정 환경의 일부가 영어 텍스트를 표시하는 것을 볼 수 있습니다. | Azure Percept DK 설정 환경은 완전히 지역화되지 않았습니다. | 수정은 2021년 7월로 예정되어 있습니다.  |
| Azure Percept DK | Mac에서 설정 환경으로 진행할 때 Wi-Fi에 연결한 후 설정 환경이 갑자기 닫힙니다. | Mac에서 설치 환경을 진행하는 경우 처음에는 웹 브라우저가 아닌 창에서 열립니다. 연결이 디바이스의 액세스 지점에서 Wi-Fi로 전환되면 창이 유지되지 않습니다. | 웹 브라우저를 열고 https://10.1.1.1 로 이동하면 설치 환경을 완료할 수 있습니다. |
| Azure Percept DK | 개발 키트는 사용자 지정 모델을 실행하고 있으며 개발 키트를 다시 부팅한 후 기본 샘플 모델을 실행합니다. | 사용자 지정 모델의 모듈 쌍 컨테이너가 디바이스를 다시 부팅할 때 지속되지 않습니다. 다시 부팅 후 사용자 지정 모듈에 대한 모듈 쌍을 다시 빌드해야 하며 5분 이상 걸릴 수 있습니다. 개발 키트는 해당 프로세스가 완료될 때까지 기본 모델을 실행합니다. | 다시 부팅 후 사용자 지정 모듈 쌍이 다시 만들어질 때까지 기다려야 합니다. |