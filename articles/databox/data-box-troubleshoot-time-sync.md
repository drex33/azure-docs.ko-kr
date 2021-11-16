---
title: Azure Data Box, Azure Data Box Heavy 장치에 대 한 시간 동기화 문제 해결
description: PowerShell 인터페이스를 통해 Azure Data Box 또는 Azure Data Box Heavy 장치에 대 한 시간 동기화 문제를 해결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 11/15/2021
ms.author: alkohli
ms.openlocfilehash: 8999c2873528dd5dd0fefc0b730f4856ac995649
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556934"
---
# <a name="sync-device-time-for-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box 및 Azure Data Box Heavy에 대 한 장치 시간 동기화

이 문서에서는 Data Box 동기화 되지 않은 것을 진단 하 고 Data Box 장치에서 시간을 변경 하는 방법을 설명 합니다. 이 문서의 정보는 Data Box 및 Data Box Heavy 장치 모두에 대 한 가져오기 및 내보내기 주문에 적용 됩니다.


## <a name="about-device-time-sync"></a>장치 시간 동기화 정보

Data Box는 기본 Windows 시간 서버를 사용 하 여 인터넷에 연결 되어 있는 시간을 자동으로 동기화 `time.windows.com` 합니다. 그러나 Data Box 인터넷에 연결 되지 않은 경우 장치 시간이 동기화 되지 않을 수 있습니다. 이러한 상황이 발생 하면 REST API 또는 시간 제약 조건이 있는 특정 도구를 통해 복사 하는 경우에 특히 Data Box 원본 데이터의 데이터 복사에 영향을 줄 수 있습니다. 

사이트의 Data Box와 다른 로컬 장치 간의 시간 차이가 표시 되는 경우 PowerShell 인터페이스에 액세스 하 여 Data Box 시간을 동기화 할 수 있습니다. 는 `Set-Date API` 장치 시간을 수정 하는 데 사용 됩니다. 자세한 내용은 [설정-날짜 API](/powershell/module/microsoft.powershell.utility/set-date?view=powershell-7.1&preserve-view=true)를 참조 하세요.


## <a name="connect-to-powershell-interface"></a>PowerShell 인터페이스에 연결

시간 동기화 문제를 해결 하려면 먼저 장치의 PowerShell 인터페이스에 연결 해야 합니다.

[!INCLUDE [Connect to Data Box PowerShell interface](../../includes/data-box-connect-powershell-interface.md)]


## <a name="change-device-time"></a>장치 시간 변경

장치 시간을 변경 하려면 다음 단계를 수행 합니다.

1. 장치 시간이 동기화 되지 않은 경우 진단 하려면 먼저 장치 시간을 가져옵니다. Cmdlet을 사용 `Get-Date` 하 여 Data Box 날짜 및 시간을 확인 합니다.

    `Get-Date`

1. 장치 시간이 동기화 되지 않은 경우에는 cmdlet을 사용 `Set-Date` 하 여 Data Box 시간을 변경 합니다.

    - 시간을 2 분 앞으로 설정 합니다.
    
        ```powershell
        Set-Date -Adjust <time change in hours:mins:secs format> -DisplayHint Time
        ```
    - 시간을 2 분으로 다시 설정 합니다.

        ```powershell
        Set-Date -Adjust -<time change in hours:mins:secs format> -DisplayHint Time
        ```    

        출력의 예제는 다음과 같습니다.
        
        ```powershell
        [by506b4b5d0790.microsoftdatabox.com]: PS>Get-date
        Friday, September 3, 2021 2:22:50 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Set-Date -Adjust 00:02:00 -DisplayHint Time
        2:25:18 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Set-Date -Adjust -00:02:00 -DisplayHint Time
        2:23:28 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Get-date
        Friday, September 3, 2021 2:23:42 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>
        ```
        자세한 내용은 [설정-날짜 API](/powershell/module/microsoft.powershell.utility/set-date?view=powershell-7.1&preserve-view=true)를 참조 하세요.
    
## <a name="next-steps"></a>다음 단계

다른 Data Box 문제를 해결 하려면 다음 문서 중 하나를 참조 하세요.

- [Data Box Blob storage 오류 문제를 해결](data-box-troubleshoot-rest.md)합니다.
- [Data Box 데이터 복사 오류 문제를 해결](data-box-troubleshoot.md)합니다.
- [데이터 업로드 오류 Data Box 문제를 해결](data-box-troubleshoot-data-upload.md)합니다.
