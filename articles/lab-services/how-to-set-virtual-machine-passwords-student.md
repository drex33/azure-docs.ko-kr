---
title: Azure Lab Services의 클래스룸 랩 VM용 암호 재설정 | Microsoft Docs
description: Azure Lab Services의 랩에서 VM(가상 머신)의 암호를 재설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: ca311c08419ff54fdfe72b20a2a2bffbc436d1dd
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180137"
---
# <a name="set-or-reset-password-for-virtual-machines-in-labs-students"></a>랩(학생)에서 가상 머신의 암호 설정 또는 재설정
이 문서에서는 학생이 VM에 대한 암호를 설정/재설정하는 방법을 보여 줍니다. 

## <a name="enable-resetting-of-passwords"></a>암호 재설정 사용
랩을 만들 때 랩 소유자는 **모든 가상 머신에 대해 동일한 암호 사용** 을 하거나 사용하지 않도록 설정할 수 있습니다. 이 옵션을 사용하도록 설정하면 학생이 암호를 재설정할 수 없습니다. 랩의 모든 VM은 강사가 설정한 것과 동일한 암호를 갖습니다. 

이 설정을 사용하지 않도록 설정하면 학생들이 처음으로 VM에 연결을 시도할 때 암호를 설정하라는 메시지가 표시됩니다. 이 문서의 마지막 섹션에 표시된 대로 나중에 언제든지 암호를 재설정할 수 있습니다. 

## <a name="reset-password-for-the-first-time"></a>처음으로 암호 재설정
**모든 가상 머신에 대해 동일한 암호 사용** 옵션을 사용하지 않도록 설정한 경우, 사용자(학생)가 **내 가상 머신** 페이지의 랩 타일에서 **연결** 단추를 선택하면 VM에 대한 암호를 설정하는 다음 대화 상자를 볼 수 있습니다. 

![학생 암호 재설정](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>나중에 암호 재설정
또한 학생은 랩 타일에서 오버플로 메뉴(**세로 점 세 개**)를 클릭하고 **암호 재설정** 을 선택하여 암호를 설정할 수 있습니다. 

![나중에 암호 재설정](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>다음 단계
랩 소유자가 구성할 수 있는 다른 학생 사용량 옵션에 대해 알아보려면 다음 [학생 사용량 구성](how-to-configure-student-usage.md) 문서를 참조하세요.
