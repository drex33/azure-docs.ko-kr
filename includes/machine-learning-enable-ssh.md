---
author: sdgilley
ms.service: machine-learning
ms.topic: include
ms.date: 07/30/2021
ms.author: sgilley
ms.openlocfilehash: bd6760d152edeb3f8e50fa50a5b72476fc46150f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536123"
---
**다음: 고급 설정** 을 선택한 후:

1. **SSH 액세스 사용** 을 설정합니다.
1. **SSH 공개 키 원본** 의 드롭다운에서 옵션 중 하나를 선택합니다.
    * **새 키 쌍을 생성** 하는 경우:
        1. **키 쌍 이름** 에 키의 이름을 입력합니다.
        1. **만들기** 를 선택합니다.
        1. **프라이빗 키 다운로드 및 컴퓨팅 만들기** 를 선택합니다.  키는 일반적으로 **다운로드** 폴더에 다운로드합니다.  
    * **Azure에 저장된 기존 공개 키 사용** 을 선택한 경우 **저장된 키** 에서 키를 검색하여 선택합니다.
    * **기존 공개 키 사용** 을 선택하는 경우 단일 줄 형식("ssh-rsa"로 시작) 또는 다중 회선 PEM 형식으로 RSA 공개 키를 제공합니다. Linux 및 OS X에서 ssh-keygen을 사용하거나 Windows에서 PuTTYGen을 사용하여 SSH 키를 생성할 수 있습니다.