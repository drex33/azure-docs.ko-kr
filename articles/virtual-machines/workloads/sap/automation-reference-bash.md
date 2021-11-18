---
title: SAP deployment automation framework Bash 참조 | Microsoft Docs
description: Azure Bash 참조의 SAP 배포 자동화 프레임 워크
services: virtual-machines-windows
author: kimforss
manager: kimforss
keywords: Azure, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.workload: infrastructure
ms.date: 11/17/2021
ms.author: kimforss
ms.openlocfilehash: efb02895ef6d25bd94a1d0a8f8dcf11d2e0d5181
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730440"
---
# <a name="using-sap-deployment-automation-framework-shell-scripts"></a>SAP deployment automation framework shell 스크립트 사용

셸 스크립트를 사용 하 여 Azure 구성 요소 [에 모든 SAP deployment automation 프레임 워크](automation-deployment-framework.md) 를 배포할 수 있습니다.

## <a name="control-plane-operations"></a>제어 평면 작업

[Prepare_region](bash/automation-prepare-region.md) shell 스크립트를 사용 하 여 제어 평면을 배포 하거나 업데이트할 수 있습니다.

[Remove_region](bash/automation-remove-region.md) shell 스크립트를 사용 하 여 제어 평면을 제거 합니다.

[Install_deployer](bash/automation-install_deployer.md) Shell 스크립트를 사용 하 여 컨트롤 평면에서 배포자을 부트스트랩 할 수 있습니다.

[Install_library](bash/automation-install_library.md) Shell 스크립트를 사용 하 여 제어 평면에서 SAP 라이브러리를 부트스트랩 할 수 있습니다.

## <a name="workload-zone-operations"></a>작업 영역 작업

셸 스크립트를 사용 하 여 작업 영역을 배포 하거나 업데이트 합니다 [`install_workloadzone`](bash/automation-install_workloadzone.md) .

셸 스크립트를 사용 하 여 작업 영역을 제거 합니다 [`remover`](bash/automation-remover.md) .


## <a name="sap-system-operations"></a>SAP 시스템 작업

셸 스크립트를 사용 하 여 SAP 시스템을 배포 또는 업데이트 [`installer`](bash/automation-installer.md) 합니다.

셸 스크립트를 사용 하 여 SAP 시스템을 제거 [`remover`](bash/automation-remover.md)  합니다.


## <a name="other-operations"></a>기타 작업

셸 스크립트를 사용 하 여 배포 자격 증명을 설정 합니다 [`Set SPN secrets`](bash/automation-set-secrets.md) .

셸 스크립트를 사용 하 여 Terraform 상태 파일을 업데이트 합니다 [`Update Terraform state`](bash/automation-advanced_state_management.md) .

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bash를 사용 하 여 컨트롤 평면 배포](bash/automation-prepare-region.md)
