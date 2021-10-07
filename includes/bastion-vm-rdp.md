---
title: 파일 포함
description: 포함 파일
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 08/27/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4561159b143f7cbaa9908aabb48e8a0dd08578be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627162"
---
1. [Azure Portal](https://portal.azure.com)에서 연결하려는 가상 머신으로 이동합니다. **개요** 페이지에서 **연결** 을 선택한 다음, 드롭다운에서 **Bastion** 을 선택합니다.

   :::image type="content" source="./media/bastion-vm-rdp/connect.png" alt-text="연결 스크린샷." lightbox="./media/bastion-vm-rdp/connect.png":::

1. 드롭다운에서 Bastion을 선택하면 다음과 같은 세 개의 탭이 있는 사이드바가 나타납니다. RDP, SSH 및 Bastion Bastion이 가상 네트워크용으로 프로비저닝되었기 때문에 Bastion 탭은 기본적으로 활성화되어 있습니다. **Bastion 사용** 을 선택합니다.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Bastion 사용 선택의 스크린샷.":::

1. **Azure Bastion을 사용하여 연결** 페이지에서 가상 머신의 사용자 이름과 암호를 입력한 다음, **연결** 을 선택합니다.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="연결 단추의 스크린샷.":::

1. 이 가상 머신에 대한 RDP 연결은 포트 443 및 Bastion 서비스를 사용하여 Azure Portal에서 직접 열립니다(HTML5를 통해). 

   * 연결할 때 VM의 바탕 화면이 예제 스크린샷과 다르게 보일 수 있습니다. 
   * VM에 연결된 상태에서 바로 가기 키를 사용하면 로컬 컴퓨터의 바로 가기 키와 동일한 동작이 수행되지 않을 수 있습니다. 예를 들어 Windows 클라이언트에서 Windows VM에 연결된 경우 CTRL+ALT+END는 로컬 컴퓨터에서 CTRL+ALT+Delete의 바로 가기 키입니다. Windows VM에 연결된 동안 Mac에서 이 작업을 수행하려면 바로 가기 키는 Fn+CTRL+ALT+Backspace입니다.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="포트 443을 사용하는 연결 스크린샷.":::
