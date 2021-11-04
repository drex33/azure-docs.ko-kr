---
title: 실행 명령 사용하여 Azure의 Windows 또는 Linux VM에서 스크립트 실행
description: 이 항목에서는 실행 명령 기능을 사용하여 Azure 가상 머신 내에서 스크립트를 실행하는 개요를 제공합니다.
ms.service: virtual-machines
author: cynthn
ms.author: cynthn
ms.date: 10/27/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0442c97e07e89238bed6b670c1529a862ddb1ab0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482965"
---
# <a name="run-scripts-in-your-vm-by-using-run-command"></a>실행 명령 사용하여 VM에서 스크립트 실행

실행 명령 VM(가상 머신) 에이전트를 사용하여 Azure Windows 또는 Linux VM 내에서 스크립트를 실행합니다. 이러한 스크립트는 일반 머신 또는 애플리케이션 관리에 사용할 수 있습니다. 이를 통해 VM 액세스 및 네트워크 문제를 신속하게 진단 및 수정하고 VM을 정상 상태로 되돌릴 수 있습니다. 스크립트는 속성에 포함되거나 미리 게시된 갤러리 스크립트에 참조될 수 있습니다. 


원래 명령 집합은 작업 지향입니다. 현재 공개 미리 보기로 업데이트된 명령 집합은 관리 지향적이며 여러 스크립트를 실행할 수 있으며 제한 사항은 적습니다. 이 문서에서는 두 실행 명령 집합 간의 차이점을 설명하고 시나리오에서 사용하기에 적합한 집합을 결정하는 데 도움이 됩니다.  

> [!IMPORTANT]
> **관리되는 실행 명령** 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="when-to-use-action-or-managed-commands"></a>작업 또는 관리되는 명령을 사용하는 경우

원래 명령 집합은 작업 지향입니다. 다음 명령을 실행해야 하는 경우 이 명령 집합을 사용하는 것이 좋습니다.
- VM에서 콘텐츠를 얻는 작은 스크립트 
- VM을 구성하는 스크립트(레지스트리 키 설정, 구성 변경) 
- 진단을 위한 일회성 스크립트

사용 가능한 작업 명령 및 적용 방법에 [대한 지침은 linux용](./linux/run-command.md) [작업 실행](./windows/run-command.md) 명령 및 Windows 대한 작업 실행 명령을 참조하세요. 

현재 공개 미리 보기로 업데이트된 명령 집합은 관리 지향입니다. 다음 예제와 일치해야 하는 경우 관리형 실행 명령을 사용하는 것이 좋습니다.
- 스크립트를 VM 배포의 일부로 실행해야 합니다. 
- 되풀이 스크립트 실행이 필요합니다. 
- 여러 스크립트를 순차적으로 실행해야 합니다. 
- 설치 스크립트를 실행하여 VM 부트스트랩 
- 공유 및 재사용할 사용자 지정 스크립트 게시 

사용 방법에 대한 자세한 내용은 [Linux용 관리 실행 명령](./linux/run-command-managed.md) 및 [관리되는 실행 명령 Windows](./windows/run-command-managed.md) 참조하세요.


## <a name="compare-feature-support"></a>기능 지원 비교

| 기능 지원  | Action RunCommand  | 관리되는 RunCommand  |
|---|---|---|
| ARM 템플릿  | 아니요, POST 작업입니다.  | 예, 리소스 종류입니다.  |
| 장기 실행  | 90분 제한  | 고객이 지정한 시간 제한  |
| 실행 계정  | 시스템 계정/루트  | 고객이 지정한 사용자  |
| 여러 실행 명령  | 활성 상태 하나만  | 병렬 또는 시퀀스된 다중  |
| 큰 출력  | 4k로 제한(상태 Blob)  | 고객 추가 Blob에 업로드  |
| 진행률 추적  | 최종 상태만 보고  | 실행 중 진행률 및 마지막 4k 출력 보고  |
| 비동기 실행  | 스크립트가 완료될 때까지 목표 상태/프로비저닝 대기  | 프로비전이 스크립트를 기다리는 경우 고객이 지정한 비동기 플래그  |
| 가상 머신 확장 집합 지원  | VM 인스턴스에서만  | 가상 머신 확장 집합 모델 지원 및 규모 확장  |
| SAS 생성  | Blob 지원 없음  | 자동화된 CRP는 고객 Blob에 대한 SAS를 생성하고 관리합니다.  |
| 갤러리(사용자 지정 명령)  | 기본 제공 commandId만  | 고객이 스크립트를 게시하고 공유할 수 있습니다.  |


## <a name="next-steps"></a>다음 단계

[Linux용 관리 실행 명령](./linux/run-command-managed.md) 또는 Windows [용 관리되는 실행 명령](./windows/run-command-managed.md)시작합니다. 