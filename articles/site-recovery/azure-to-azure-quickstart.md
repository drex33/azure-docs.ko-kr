---
title: Azure Site Recovery를 사용하여 보조 지역으로 Azure VM 재해 복구 설정
description: Azure Site Recovery 서비스를 사용하여 Azure VM에 대한 다른 Azure 지역으로 재해 복구를 신속하게 설정 합니다.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc, mode-other
ms.openlocfilehash: 5466130ac615532ec5b3c3332a1efc2787af3a9b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133063619"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>빠른 시작: 보조 Azure 지역에 Azure VM의 재해 복구 설정

[Azure Site Recovery](site-recovery-overview.md) 서비스는 계획되거나 계획되지 않은 정전 중에도 비즈니스 애플리케이션을 온라인 상태로 유지하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. Site Recovery는 복제, 장애 조치(failover), 복구를 포함하여 온-프레미스 머신 및 Azure VM(가상 머신)의 재해 복구를 오케스트레이션합니다.

이 빠른 시작에서는 보조 Azure 지역으로 복제하여 Azure VM에 대한 재해 복구를 설정하는 방법을 설명합니다. 일반적으로 기본 설정은 복제를 활성화하는 데 사용됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 Azure 구독과 VM이 필요합니다.

- 활성 구독이 있는 Azure 계정이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.
- 최소 1GB의 RAM이 있는 VM을 사용하는 것이 좋습니다. VM을 만드는 방법에 대해 [자세히 알아봅니다](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-replication-for-the-azure-vm"></a>Azure VM에 대해 복제 사용

다음 단계에서는 보조 위치로 VM 복제를 사용하도록 설정합니다.

1. Azure Portal의 **홈** > **가상 머신** 메뉴에서 복제할 VM을 선택합니다.
1. **작업** 에서 **재해 복구** 를 선택합니다.
1. **기본** > **대상 지역** 에서 대상 지역을 선택합니다.
1. 복제 설정을 보려면 **검토 + 복제 시작** 을 선택합니다. 기본값을 변경해야 하는 경우 **고급 설정** 을 선택합니다.
1. VM 복제를 사용하도록 설정하는 작업을 시작하려면 **복제 시작** 을 선택합니다.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="복제를 사용하도록 설정합니다.":::

## <a name="verify-settings"></a>설정 확인

복제 작업이 완료되면 복제 상태를 확인하고, 복제 설정을 수정하고, 배포를 테스트할 수 있습니다.

1. Azure Portal 메뉴에서 **가상 머신** 을 선택한 다음, 복제할 VM을 선택합니다.
1. **작업** 에서 **재해 복구** 를 선택합니다.
1. **개요** 에서 복제 세부 정보를 보려면 **Essentials** 를 선택합니다. 자세한 내용은 **Health 및 상태**, **장애 조치(failover) 준비** 및 **인프라 보기** 맵에 표시되어 있습니다.

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="복제 상태.":::

## <a name="clean-up-resources"></a>리소스 정리

주 지역에서 VM의 복제를 중지하려면 복제를 비활성화해야 합니다.

- 원본 복제 설정이 자동으로 정리됩니다.
- 복제하는 동안 VM에 설치된 Site Recovery 확장은 제거되지 않습니다.
- VM에 대한 Site Recovery 청구는 중지됩니다.

복제를 비활성화하려면 다음 단계를 수행합니다.

1. Azure Portal 메뉴에서 **가상 머신** 을 선택한 다음, 복제할 VM을 선택합니다.
1. **작업** 에서 **재해 복구** 를 선택합니다.
1. **개요** 에서 **복제 사용 안 함** 을 선택합니다.
1. Site Recovery 확장을 제거하려면 VM의 **설정** > **확장** 으로 이동합니다.

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="복제를 사용하지 않도록 설정합니다.":::

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 단일 VM을 보조 지역에 복제합니다. 그런 다음, 여러 Azure VM을 위한 복제를 설정합니다.

> [!div class="nextstepaction"]
> [Azure VM에 대한 재해 복구 설정](azure-to-azure-tutorial-enable-replication.md)
