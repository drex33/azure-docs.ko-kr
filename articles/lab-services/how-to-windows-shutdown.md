---
title: Azure Lab Services에서 Windows 종료 동작을 제어하는 가이드 | Microsoft Docs
description: 유휴 Windows 가상 머신을 자동으로 종료하고 Windows 종료 명령을 제거하는 단계입니다.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: b76855e80d01091686911ab3d55b7b05eaf92bb5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179909"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Windows 종료 동작 제어 가이드

Azure Lab Services는 Windows VM(가상 머신)이 예기치 않게 실행되지 않도록 다음과 같은 몇 가지 비용 제어를 제공합니다.
 - [일정 설정](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
 - [사용자에 대한 할당량 설정](./how-to-configure-student-usage.md#set-quotas-for-users)
 - [연결 해제 시 자동 종료 사용](./how-to-enable-shutdown-disconnect.md)

이러한 비용 제어를 사용하는 경우에도 Windows VM이 예기치 않게 계속 실행되는 경우가 있으며, 이로 인해 학생의 할당량에서 공제될 수 있습니다.

- **RDP 창이 열려 있음**
  
    학생이 RDP를 사용하여 VM에 연결하는 경우 실수로 RDP 창을 열어둘 수 있습니다.  **연결 해제 시 자동 종료** 설정은 RDP 세션의 연결이 끊긴 후에만 트리거되므로 RDP 창이 열려 있는 동안에는 적용되지 않습니다.

- **Windows shutdown 명령을 사용하여 VM을 끔**
  
    학생은 [Azure Lab Services의 중지 단추](./how-to-use-classroom-lab.md#start-or-stop-the-vm)를 사용하는 대신 Windows 종료 명령 또는 Windows 내에서 제공되는 기타 종료 메커니즘을 사용하여 VM을 끌 수 있습니다.  이 경우 Azure Lab Services 관점에서 VM은 계속 사용 중입니다.
    
이러한 상황을 방지하기 위해 이 가이드에서는 유휴 Windows VM을 자동으로 종료하고 **시작** 메뉴에서 Windows 종료 명령을 제거하는 단계를 안내합니다.  

> [!NOTE]
> 학생이 VM을 시작할 때 VM이 예기치 않게 할당량에서 공제될 수도 있지만 실제로 RDP를 사용하여 VM에 연결할 수는 없습니다.  현재 이 가이드에서는 이 시나리오를 다루지 *않습니다*.  대신 학생들은 RDP를 시작한 후 즉시 해당 RDP를 사용하여 VM에 연결하도록 상기해야 하며 그렇지 않을 경우 VM을 중지해야 합니다.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>시작 메뉴에서 Windows 종료 명령 제거

Windows **로컬 그룹 정책** 설정을 사용하여 **시작** 메뉴에서 종료 명령을 제거할 수도 있습니다.

종료 명령을 제거하려면 템플릿 VM에 연결하고 아래 PowerShell 스크립트를 실행합니다.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

또는 템플릿 VM을 사용하여 다음 수동 단계를 수행하도록 선택할 수 있습니다.

1. Windows 키를 누르고 **gpedit** 를 입력한 다음 **그룹 정책 편집(제어판)** 을 선택합니다.

1. **컴퓨터 구성 > 관리 템플릿 > 시작 메뉴 및 작업 표시줄** 로 이동합니다.  

    ![로컬 그룹 정책 편집기](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. 마우스 오른쪽 단추로 **제거를 클릭하고 종료, 다시 시작, 절전 모드 및 최대 절전 모드 명령에 액세스할 수 없도록 차단** 하고 **편집** 을 클릭합니다.

1. **사용** 설정을 선택하고 **확인** 을 클릭합니다.
 
   ![종료 설정](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Windows **시작** 메뉴에 종료 명령이 더 이상 표시되지 않고. **연결 끊기** 명령만 표시됩니다.

    ![종료 명령](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>다음 단계
[Azure Lab Services에서 Windows 템플릿 컴퓨터를 설정하는 가이드](how-to-prepare-windows-template.md) 문서에서 Windows 템플릿 VM을 준비하는 방법 참조