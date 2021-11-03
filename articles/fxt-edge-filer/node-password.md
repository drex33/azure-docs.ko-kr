---
title: '자습서: 하드웨어 초기화 - Azure FXT Edge Filer'
description: 하드웨어 노드에 연결하고 Azure FXT Edge 필터 노드에서 초기 암호를 설정하는 방법을 알아봅니다.
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: f78c7e60210d71aa94a20b4ce198a8d9d154a06f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033022"
---
# <a name="tutorial-set-hardware-passwords"></a>자습서: 하드웨어 암호 설정

처음으로 Azure FXT Edge Filer 노드의 전원을 켤 때 루트 암호를 설정해야 합니다. 하드웨어 노드는 기본 암호가 지정된 상태로 제공되지 않습니다.

암호가 설정되고 루트 사용자가 로그인할 때까지 네트워크 포트를 사용할 수 없습니다.

이 단계는 노드를 설치하고 전원 케이블을 연결한 후 클러스터 만들기를 시도하기 전에 수행하세요.

이 자습서에서는 하드웨어 노드에 연결하고 암호를 설정하는 방법을 설명합니다. 또한 노드를 보호하는 데 도움이 되도록 BIOS 설정 암호를 추가하는 방법도 설명합니다.

이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
>
> * 노드에 키보드 및 모니터를 연결하고 전원 켜기
> * BIOS 설정 암호 지정
> * 이 노드에서 iDRAC 포트 및 루트 사용자의 암호 설정
> * 루트로 로그인

클러스터에서 사용할 각 노드에 대해 이러한 단계를 반복합니다.

이 자습서를 완료하는 데 약 15분이 소요됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에 다음 단계를 완료하세요.

* 장비 랙에 각 Azure FXT Edge Filer 노드를 [설치](install.md)하고 [이전의 자습서](network-power.md)에 설명된 대로 전원 케이블 및 네트워크 액세스를 연결합니다.
* 하드웨어 노드에 연결할 수 있는 USB 연결 키보드와 VGA 연결 모니터를 찾습니다. (노드의 직렬 포트는 암호가 설정되기 전까지 비활성 상태입니다.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>노드에 키보드 및 모니터 연결

모니터 및 키보드를 Azure FXT Edge Filer 노드에 물리적으로 연결합니다.

* 모니터를 VGA 포트에 연결합니다.
* 키보드를 USB 포트 중 하나에 연결합니다.

이 참조 다이어그램을 사용하여 섀시의 뒷면에 있는 포트를 찾습니다.

> [!NOTE]
> 직렬 포트는 암호가 설정되기 전까지 비활성 상태입니다.

![직렬 포트, VGA 및 USB 포트 레이블이 표시된 Azure FXT Edge Filer의 후면도](media/fxt-back-serial-vga-usb.png)

둘 이상의 노드를 동일한 주변 장치에 연결하려는 경우 KVM 스위치를 사용하면 됩니다.

앞면에 있는 전원 단추를 눌러 노드를 켭니다.

![Azure FXT Edge Filer의 전면도 - 오른쪽 상단 근처에 둥근 전원 단추의 레이블이 표시됨](media/fxt-front-annotated.png)

## <a name="create-a-bios-setup-password"></a>BIOS 설정 암호 만들기

BIOS 설정 암호는 우발적이거나 무단 변경으로부터 노드의 BIOS 설정을 보호합니다. 이 암호는 클러스터를 만드는 데 필요하지 않지만 클러스터 보안 전략의 일부로 강력히 추천됩니다.

BIOS 설정 암호를 만들려면 다음을 수행합니다.

1. 노드를 설정하거나 다시 시작한 다음, 즉시 F2 키를 눌러 시스템 설정 유틸리티를 엽니다.

1. **시스템 설정 주 메뉴** 화면에서 **시스템 BIOS** > **시스템 보안** 을 차례로 선택합니다.

1. **암호 상태** 설정이 **잠금 해제** 인지 확인합니다.

1. **암호 설정** 필드를 사용하여 암호를 설정합니다. (하나의 암호만 사용하려는 경우 이 화면에서 시스템 BIOS 암호를 설정할 수도 있습니다.)

1. Esc 키를 눌러 **시스템 BIOS** 화면으로 돌아간 다음, Esc 키를 다시 누릅니다. 변경 내용을 저장하라는 메시지가 표시됩니다. 시스템이 자동으로 다시 시작되지 않으면 시스템을 다시 시작하여 일반 시작 화면으로 이동합니다.<!-- how to exit this mode/do you need to reboot to get to the initial setup screen? -->

## <a name="set-initial-passwords"></a>초기 암호 설정

Azure FXT Edge Filer 노드는 부팅 중에 모니터에 다양한 메시지를 출력합니다. 잠시 후 다음과 같은 초기 설정 화면이 표시됩니다.

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set the iDRAC and temporary root password.
Minimum password length is 8.

NOTE: This does not set a BIOS setup password. For security,
Microsoft recommends using a BIOS setup password, restricting
physical access to the node, and other measures. Learn more at
https://aka.ms/fxt-security.

Enter new password:

```

입력한 암호는 두 가지 용도로 사용됩니다.

* 이는 이 Azure FXT Edge Filer 노드의 임시 루트 암호입니다.

  이 노드를 사용하여 클러스터를 만들거나 클러스터에 이 노드를 추가하면 이 암호가 변경됩니다. ``admin`` 사용자와 연결된 클러스터 관리 암호는 클러스터의 모든 노드에 대한 루트 암호이기도 합니다.

* 이는 iDRAC/IPMI 하드웨어 관리 포트의 장기 암호입니다.

  나중에 하드웨어 문제 해결을 위해 IPMI로 로그인해야 하는 경우를 대비해 암호를 기억해 두어야 합니다.

암호를 입력하고 확인합니다.

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

암호를 입력하면 시스템이 부팅을 계속합니다. 완료되면 ``login:`` 프롬프트가 표시됩니다.

## <a name="sign-in-as-root"></a>루트로 로그인

방금 설정한 암호를 사용하여 ``root``로 로그인합니다.

```
login: root
Password:**********
```

루트로 로그인하면 네트워크 포트가 활성화되고 DHCP 서버에 연결하여 IP 주소를 확인합니다.

## <a name="next-steps"></a>다음 단계

이 노드는 클러스터의 일부가 될 준비가 되었습니다. 이를 Azure FXT Edge Filer 클러스터를 만드는 데 사용하거나 [기존 클러스터에 추가](add-nodes.md)할 수 있습니다.

> [!div class="nextstepaction"]
> [클러스터 만들기](cluster-create.md)
