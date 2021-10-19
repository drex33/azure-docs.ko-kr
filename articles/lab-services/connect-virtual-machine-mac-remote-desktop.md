---
title: Mac에서 Azure Lab Services VM에 연결하는 방법 | Microsoft Docs
description: Azure Lab Services에서 Mac을 가상 머신에 연결하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: cf5c3e38a1f2f850a4dbeb9c989dffb395992118
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178598"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Mac에서 원격 데스크톱 프로토콜을 사용하여 VM에 연결
이 섹션에서는 학생이 RDP를 사용하여 Mac에서 교실 랩 VM에 연결할 수 있는 방법을 보여 줍니다.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Mac에서 Microsoft 원격 데스크톱 설치
1. Mac에서 App Store를 열고 **Microsoft 원격 데스크톱** 을 검색합니다.

    ![Microsoft 원격 데스크톱](./media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. 최신 버전의 Microsoft 원격 데스크톱를 설치합니다. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>RDP를 사용하여 Mac에서 VM 액세스
1. **Microsoft 원격 데스크톱** 이 설치된 컴퓨터에서 다운로드한 **RDP** 파일을 엽니다. VM에 대한 연결이 시작되어야 합니다. 

    ![VM에 연결](./media/how-to-use-classroom-lab/connect-linux-vm.png)
1. 다음 경고가 표시되면 **계속** 을 선택합니다. 

    ![인증서 경고](./media/how-to-use-classroom-lab/certificate-error.png)
1. VM이 표시됩니다. 

    > [!NOTE]
    > 다음 예제는 CentOS Linux VM에 대한 것입니다. 

    ![VM](./media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>다음 단계
RDP를 사용하여 Linux VM에 연결하는 방법을 알아보려면 [Linux 가상 머신에 원격 데스크톱 사용](how-to-use-remote-desktop-linux-student.md)을 참조하세요.


