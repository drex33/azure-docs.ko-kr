---
title: '호스트 크기 조정을 위한 배율 단위 추가: PowerShell'
titleSuffix: Azure Bastion
description: PowerShell을 사용하여 Azure Bastion 추가 인스턴스(배율 단위)를 추가하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 11/29/2021
ms.author: cherylmc
ms.openlocfilehash: c84dd593d9208a28bd39c1038bd031b2b732a823
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386850"
---
# <a name="configure-host-scaling-using-azure-powershell"></a>Azure PowerShell 사용하여 호스트 크기 조정 구성

이 문서에서는 PowerShell을 사용하여 추가 동시 클라이언트 연결을 수용하기 위해 Azure Bastion 추가 배율 단위(인스턴스)를 추가하는 데 도움이 됩니다. 호스트 스케일링에 대한 자세한 내용은 [구성 설정](configuration-settings.md#instance)을 참조하세요.

## <a name="configuration-steps"></a>구성 단계

1. 대상 Bastion 리소스를 얻습니다. 아래 예제를 사용하여 필요에 따라 값을 수정합니다.

   ```azurepowershell-interactive
   $bastion = Get-AzBastion -Name bastion -ResourceGroupName bastion-rg
   ```

1. "인스턴스 수"라고도 하는 대상 배율 단위를 설정합니다. 다음 예제에서는 배율 단위를 5로 설정합니다.

   ```azurepowershell-interactive
   $bastion.ScaleUnit = 5
   Set-AzBastion -InputObject $bastion
   ```

1. 리소스를 덮어쓰려면 "Y"를 확인합니다. 리소스를 덮어쓰면 지정된 값이 "배율 단위"에 대한 출력에 표시됩니다.

   >[!NOTE]
   > 호스트 배율 단위를 변경하면 활성 배스션 연결이 중단됩니다.
   >

## <a name="next-steps"></a>다음 단계

구성 설정에 대한 자세한 내용은 [Azure Bastion 구성 설정을 참조하세요.](configuration-settings.md)

