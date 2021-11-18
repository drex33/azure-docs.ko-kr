---
title: 승인된 베이스에서 Azure VM(가상 머신 제품) 만들기
description: 승인된 베이스(Azure Marketplace)에서 VM(가상 머신) 제품을 만드는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: e34a662d88e5798804010c41584f31768ca919bf
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730486"
---
# <a name="create-a-virtual-machine-using-an-approved-base"></a>승인된 베이스를 사용하여 가상 머신 만들기

이 문서에서는 Azure를 사용하여 사전 구성된 인증 운영 체제를 포함하는 VM(가상 머신)을 만드는 방법을 설명합니다. 솔루션과 호환되지 않는 경우 승인된 운영 체제를 사용하여 [온-프레미스 VM을 만들고 구성](azure-vm-use-own-image.md)할 수 있습니다.

> [!NOTE]
> 이 절차를 시작하기 전에 VHD(가상 하드 디스크) 요구 사항을 포함하여 Azure VM 제품에 대한 [기술 요구 사항](marketplace-virtual-machines.md#technical-requirements)을 검토하세요.

## <a name="select-an-approved-base-image"></a>승인된 베이스 이미지 선택

다음 Windows 또는 Linux 이미지 중 하나를 베이스로 선택합니다.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure에서 승인된 Linux 배포판을 다양하게 제공합니다. 현재 목록은 [Azure 보증 배포판의 Linux](../virtual-machines/linux/endorsed-distros.md)를 참조하세요.

## <a name="create-vm-on-the-azure-portal"></a>Azure Portal에서 VM 만들기

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.
2. **가상 머신** 을 선택합니다.
3. **+ 추가** 를 선택하여 **가상 머신 만들기** 화면을 엽니다.
4. 드롭다운 목록에서 이미지를 선택하거나 **모든 퍼블릭 및 프라이빗 이미지 찾아보기** 를 선택하여 사용 가능한 모든 가상 머신 이미지를 검색하거나 찾아봅니다.
5. **Gen 2** VM을 만들려면 **고급** 탭으로 이동하여 **Gen 2** 옵션을 선택합니다.

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="1세대 또는 2세대를 선택":::합니다.

6. 배포할 VM의 크기를 선택합니다.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="선택한 이미지에 권장되는 VM 크기를 선택":::합니다.

7. VM을 만드는 데 필요한 다른 세부 정보를 입력합니다.
8. **검토 + 만들기** 를 선택하여 선택 사항을 검토합니다. **유효성 검사 통과** 메시지가 나타나면 **만들기** 를 선택합니다.

Azure에서 지정한 가상 머신의 프로비저닝을 시작합니다. 왼쪽 메뉴의 **가상 머신** 탭을 선택하여 진행 상황을 추적합니다. 생성이 완료되면 가상 머신의 상태가 **실행 중** 으로 변경됩니다.

## <a name="configure-the-vm"></a>VM 구성

이 섹션에서는 Azure VM의 크기를 조정하고, 업데이트하고, 일반화하는 방법을 설명합니다. 이러한 단계는 Azure Marketplace에서 배포할 VM을 준비하는 데 필요합니다.

### <a name="connect-to-your-vm"></a>VM에 연결

[Windows](../virtual-machines/windows/connect-logon.md) 또는 [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM에 연결하려면 다음 문서를 참조하세요.

### <a name="install-the-most-current-updates"></a>최신 업데이트 설치

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>추가 보안 검사 수행

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>사용자 지정 구성 및 예약된 작업 수행

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>다음 단계

- 권장되는 다음 단계: [VM 이미지 테스트](azure-vm-image-test.md)를 통해 Azure Marketplace 게시 요구 사항을 충족하는지 확인합니다. 선택 사항입니다.
- VM 이미지를 테스트하지 않으려면 [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2165935)에 로그인하여 이미지를 게시합니다.
- 새 Azure 기반 VHD를 만드는 데 어려움이 있는 경우에는 [Azure Marketplace용 VM FAQ](azure-vm-faq.yml)를 참조하세요.
