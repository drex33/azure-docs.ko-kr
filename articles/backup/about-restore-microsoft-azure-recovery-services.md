---
title: MARS(Microsoft Azure Recovery Services) 에이전트를 사용한 복원 옵션
description: MARS(Microsoft Azure Recovery Services) 에이전트에서 사용할 수 있는 복원 옵션에 관해 알아봅니다.
ms.reviewer: mepand
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: d2d6868c787080ddd78b574d1b20434fa27d8574
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250334"
---
# <a name="about-restore-using-the-microsoft-azure-recovery-services-mars-agent"></a>MARS(Microsoft Azure Recovery Services) 에이전트를 사용한 복원 정보 

이 문서에서는 MARS(Microsoft Azure Recovery Services) 에이전트에서 사용할 수 있는 복원 옵션을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

- 최신 버전의 [MARS 에이전트](https://aka.ms/azurebackup_agent)가 설치되어 있는지 확인합니다.
- [네트워크 대역폭 제한](backup-windows-with-mars-agent.md#enable-network-throttling)이 사용되지 않는지 확인합니다.
- [에이전트 캐시 폴더](./backup-azure-file-folder-backup-faq.yml)에 충분한 공간이 있는 고속 스토리지를 사용할 수 있는지 확인합니다.
- 메모리와 CPU 리소스를 모니터링하고 데이터 압축 해제 및 암호 해독에 충분한 리소스를 사용할 수 있는지 확인합니다.
- **즉시 복원** 기능을 사용하여 복구 지점을 디스크로 탑재하는 동안 다중 스레드 복사 옵션(/MT 스위치)과 함께 **robocopy** 를 사용하여 탑재된 복구 지점에서 파일을 효율적으로 복사합니다.

## <a name="restore-options"></a>복원 옵션

MARS 에이전트는 여러 복원 옵션을 제공합니다. 각 옵션은 특정 시나리오에 적합하게 만드는 고유한 이점을 제공합니다.

MARS 에이전트를 사용하여 다음을 수행할 수 있습니다.

- **[Windows Server 시스템 상태 백업 복원](backup-azure-restore-system-state.md):** 이 옵션은 Azure Backup의 복구 지점에서 시스템 상태를 파일로 복원하고 Windows Server 백업 유틸리티를 사용하여 Windows Server에 적용하는 데 도움이 됩니다.  
- **[볼륨의 백업된 모든 파일 복원](restore-all-files-volume-mars.md):** 이 옵션은 Azure Backup의 복구 지점에서 지정된 볼륨의 백업된 모든 데이터를 복구합니다. 전송 속도를 높일 수 있습니다(최대 40MBPS).<br>대용량 데이터 또는 전체 볼륨을 복구하는 데 이 옵션을 사용하는 것이 좋습니다.
- **[PowerShell을 사용하여 볼륨에서 백업된 파일 및 폴더의 특정 세트 복원](backup-client-automation.md#restore-data-from-azure-backup):** 볼륨 루트를 기준으로 한 파일과 폴더의 경로를 알고 있는 경우 이 옵션을 사용하면 전체 볼륨 복원의 더 빠른 전송 속도를 사용하여 복구 지점에서 지정된 파일과 폴더의 지정된 세트를 복원할 수 있습니다. 그러나 이 옵션은 즉시 복원 옵션을 사용하여 복구 지점에서 파일과 폴더를 검색하는 편의를 제공하지 않습니다.
- **[즉시 복원을 사용하여 개별 파일 및 폴더 복원](backup-azure-restore-windows-server.md):** 이 옵션을 사용하면 복구 지점에 볼륨을 드라이브로 탑재하여 백업 데이터에 빠르게 액세스할 수 있습니다. 그런 다음, 파일과 폴더를 찾아보고 복사할 수 있습니다. 이 옵션은 최대 6MBPS의 복사 속도를 제공하며 해당 속도는 전체 크기가 80GB 미만인 개별 파일과 폴더를 복구하는 데 적합합니다. 필요한 파일이 복사되면 복구 지점을 분리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 기타 자주 묻는 질문은 [MARS 에이전트 FAQ](backup-azure-file-folder-backup-faq.yml)를 참조하세요.
- 지원되는 시나리오 및 제한 사항에 관한 자세한 내용은 [MARS 에이전트를 사용한 백업에 대한 지원 매트릭스](backup-support-matrix-mars-agent.md)를 참조하세요.
