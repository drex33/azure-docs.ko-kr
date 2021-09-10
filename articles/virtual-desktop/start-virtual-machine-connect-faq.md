---
title: Azure Virtual Desktop 시작 VM 연결 FAQ - Azure
description: 연결 시 VM 시작 기능을 사용하기 위한 질문과 대답 및 모범 사례입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0ba2fcbc404a17b4b31d48b4e7d2e540a875f4f1
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122568150"
---
# <a name="start-vm-on-connect-faq"></a>연결 시 VM 시작 FAQ

이 문서에서는 Azure Virtual Desktop 호스트 풀의 연결 시 VM(가상 머신) 시작 기능에 대해 자주 묻는 질문을 다룹니다.

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>사용자가 VM 사용을 중단하면 자동으로 할당이 취소되나요?

아니요. 세션에서 사용자를 로그아웃하고 Azure 자동화 스크립트를 실행하여 VM 할당을 취소하는 추가 정책을 구성해야 합니다.

위치 정책을 구성하려면 다음을 수행합니다.

1. 정책을 설정하려는 VM에 원격으로 연결합니다.

2. **그룹 정책 편집기** 를 연 다음, **로컬 컴퓨터 정책** > **컴퓨터 구성** > **관리 템플릿** > **Windows 구성 요소** > **원격 데스크톱 서비스** > **원격 데스크톱 세션 호스트** > **세션 시간 제한** 으로 이동합니다.

3. **연결이 끊긴 세션에 대한 시간 제한 설정** 을 나타내는 정책을 찾은 다음, 해당 값을 **사용** 으로 변경합니다.

4. 정책을 사용하도록 설정한 후 **연결이 끊기 세션 종료** 를 선택합니다.

>[!NOTE]
>"연결이 끊긴 세션 종료" 정책에 대한 시간 제한을 5분보다 큰 값으로 설정해야 합니다. 시간 제한이 낮으면 네트워크의 연결이 너무 오래 끊어져 사용자의 세션이 종료되어 작업이 손실될 수 있습니다.

사용자를 로그아웃해도 해당 VM의 할당은 최소되지 않습니다. VM 할당을 취소하는 방법을 알아보려면 개인 호스트 풀의 경우 [작업 시간 외 VM 시작 또는 중지](../automation/automation-solution-vm-management.md)를 참조하고 풀링된 호스트 풀의 경우 [Scale session hosts using Azure Automation](set-up-scaling-script.md)(Azure Automation을 사용하여 세션 호스트 스케일링)을 참조하세요.

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>사용자가 클라이언트에서 VM을 끌 수 있나요?

예. 사용자는 물리적 머신에서와 마찬가지로 세션 내에서 시작 메뉴를 사용하여 VM을 종료할 수 있습니다. 그러나 VM을 종료해도 VM 할당이 취소되지 않습니다. VM 할당을 취소하는 방법을 알아보려면 개인 호스트 풀의 경우 [작업 시간 외 VM 시작 또는 중지](../automation/automation-solution-vm-management.md)를 참조하고 풀링된 호스트 풀의 경우 [Scale session hosts using Azure Automation](set-up-scaling-script.md)(Azure Automation을 사용하여 세션 호스트 스케일링)을 참조하세요.

## <a name="next-steps"></a>다음 단계

연결 시 VM 시작을 구성하는 방법을 알아보려면 [Start virtual machine on connect](start-virtual-machine-connect.md)(연결 시 가상 머신 시작)를 참조하세요.

Azure Virtual Desktop에 대한 일반적인 질문이 있는 경우 일반적인 [FAQ](faq.yml)를 확인하세요.
