---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 06/18/2021
ms.openlocfilehash: 95604a5b7d0bc62568e0e941e5cf035c956f141b
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968453"
---
1. VM 내에서 명령 프롬프트를 엽니다.

1. 다음 명령을 실행하여 VHD를 일반화합니다. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    
    자세한 내용은 [Sysprep(시스템 준비) 개요](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)를 참조하세요.

1.  명령이 완료되면 VM이 종료됩니다. **VM을 다시 시작하지 마세요**.
