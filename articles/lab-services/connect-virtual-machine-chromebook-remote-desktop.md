---
title: Chromebook에서 Azure Lab Services VM에 연결하는 방법 | Microsoft Docs
description: Azure Lab Services에서 Chromebook을 가상 머신으로 연결하는 방법에 대해 알아봅니다.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: how-to
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 7fb33b580aa0984366b47f0358df7f915a77730b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176152"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Chromebook에서 원격 데스크톱 프로토콜을 사용하여 VM에 연결

이 섹션에서는 학생이 RDP를 사용하여 Chromebook에서 교실 랩 VM에 연결할 수 있는 방법을 보여 줍니다.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Chromebook에서 Microsoft 원격 데스크톱 설치

1. Chromebook에서 App Store를 열고 **Microsoft 원격 데스크톱** 을 검색합니다.

    ![Microsoft 원격 데스크톱](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
    
1. 최신 버전의 Microsoft 원격 데스크톱를 설치합니다. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>RDP를 사용하여 Chromebook에서 VM에 액세스

1. **Microsoft 원격 데스크톱** 이 설치된 컴퓨터에서 다운로드한 **RDP** 파일을 엽니다. VM에 대한 연결이 시작되어야 합니다. 

    ![VM에 연결](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. 메시지가 표시되면 암호를 입력합니다.

    ![사용자 이름과 암호를 입력하는 로그온 화면을 표시하는 스크린샷](./media/how-to-use-classroom-lab/password-chromebook.png)

1. 다음 경고가 표시되면 **계속** 을 선택합니다. 

    ![인증서 경고](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. 연결하려는 VM의 데스크톱이 표시되어야 합니다.

## <a name="next-steps"></a>다음 단계

Linux VM에 연결하는 방법에 대한 자세한 내용은 [Linux 가상 머신에 연결](how-to-use-remote-desktop-linux-student.md)을 참조하세요.

