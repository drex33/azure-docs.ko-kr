---
title: Azure Automanage에서 Azure Automanage machine 모범 사례
description: Azure Automanage를 통한 SMB 관리의 개요 컴퓨터 모범 사례
author: daniellee-microsoft
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/1/2021
ms.author: jol
ms.openlocfilehash: 2246b54b6831d0e88581c80aedc4e39388c6f377
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327389"
---
# <a name="smb-over-quic-with-automanage-machine-best-practices"></a>Automanage를 통한 SMB 및 컴퓨터의 Automanage 모범 사례

인터넷과 같은 신뢰할 수 없는 네트워크를 통해 edge 파일 서버에 안전 하 고 신뢰할 수 있는 연결을 제공 하는 기업, 모바일 장치 사용자 및 지점에 대 한 "SMB VPN"을 제공 합니다. 예를 들어, SMB를 통한 smb 및 향상을 통해 smb를 구성 하는 방법에 대 한 자세한 내용은 [ ic를](https://aka.ms/smboverquic)통한 smb를 참조 하세요.

또한 smb를 통한 SMB를 사용 하는 경우 컴퓨터 모범 사례 자동 관리와 통합 되어 SMB를 통한 SMB 관리를 용이 하 게 합니다. 향상 된 인증서를 사용 하 여 암호화 및 조직에서 복잡 한 공개 키 인프라를 유지 관리 하는 경우가 자주 발생 합니다. 자동 관리 컴퓨터 모범 사례는 인증서가 경고 없이 만료 되지 않도록 하 고, SMB를 통한 SMB가 최대 서비스 연속성을 위해 활성화 되어 있는지 확인 합니다.

## <a name="how-to-get-started"></a>시작하는 방법

> [!NOTE]
> 자동 관리 컴퓨터 모범 사례 사용에 대 한 필수 구성 요소 [는 Azure Portal의 vm에서 사용](quick-create-virtual-machines-portal.md)을 참조 하세요.

> [!NOTE]
> 미리 보기 단계에서 [이 링크](https://aka.ms/automanage-ws-portal-preview)를 사용 하 여 Azure Portal를 시작할 수 있습니다.

## <a name="enable-automanage-best-practices-when-creating-a-new-vm"></a>새 VM을 만들 때 자동 관리 모범 사례 사용

VM에서 SMB에 대 한 컴퓨터 모범 사례 자동 관리를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. 위의 미리 보기 링크를 사용 하 여 Azure Portal에 로그인 합니다.

2. _Windows server 2022 Datacenter: azure Edition_ 이미지를 사용 하 여 azure VM을 만들어 azure에서 사용 하는 Windows 서버 기능에 대 한 automanage를 가져옵니다.

3. **관리** 탭의 Azure Automanage Environment 설정에서 **개발/테스트** 또는 **프로덕션** 을 선택 하 여 컴퓨터 모범 사례 자동 관리를 사용 하도록 설정 합니다.

    :::image type="content" source="media\automanage-smb-over-quic\create-vm-automanage-setting.png" alt-text="VM을 만들 때 Automanage를 사용 하도록 설정 합니다.":::

4. 필요에 따라 추가 설정을 구성 하 고 VM을 만듭니다.

## <a name="enable-automanage-best-practices-on-existing-vms"></a>기존 Vm에서 자동 관리 모범 사례 사용

이전에 만든 VM에 대 한 컴퓨터 모범 사례 자동 관리를 사용 하도록 설정할 수도 있습니다. _Windows server 2022 Datacenter: Azure Edition_ 이미지를 사용 하 여 VM을 만든 후에 SMB를 통한 SMB를 비롯 하 여 Windows 서버 기능에 대 한 automanage를 가져와야 합니다.

1. 이전에 만든 VM으로 이동 합니다.
2. Automanage 메뉴를 선택 하 고 **개발/테스트** 또는 **프로덕션** 환경을 선택한 다음 **사용** 을 클릭 합니다.

    :::image type="content" source="media\automanage-smb-over-quic\vm-enable-automanage.png" alt-text="기존 VM에 대해 Automanage를 사용 하도록 설정 합니다.":::

## <a name="viewing-automanage-best-practice-compliance"></a>Automanage 모범 사례 준수 보기

컴퓨터 모범 사례를 구성 하는 데 몇 시간 정도 걸릴 수 있으며, VM에서 모범 사례 정책을 할당 하 고 평가할 수 있습니다. 완료 되 면 아래에 표시 된 것 처럼, 해당 정책 및 해당 상태에 대 한 SMB가 표시 됩니다. 이러한 정책은 사용 중인 SMB가 제대로 구성 되어 있고 사용 된 인증서가 유효 하 고 정상 상태 인지 확인 하기 위해 지속적으로 자동 평가 됩니다.

:::image type="content" source="media\automanage-smb-over-quic\vm-automanage-configured.png" alt-text="VM에 대 한 SMB를 통해 SMB를 확인 합니다.":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [SMB를 통한 SMB에 대 한 자세한 정보](https://aka.ms/smboverquic)
