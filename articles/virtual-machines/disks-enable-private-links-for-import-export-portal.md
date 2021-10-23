---
title: Azure Portal - 관리 디스크에 대한 가져오기/내보내기 액세스 제한
description: Azure Portal을 사용하여 관리 디스크에 대한 Private Link를 사용합니다. 가상 네트워크 내에서 디스크를 안전하게 내보내고 가져올 수 있습니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/03/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6ca686a9fcf2b2ba290e14687f090f833714502c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225180"
---
# <a name="restrict-importexport-access-for-managed-disks-using-azure-private-link"></a>Azure Private Link를 사용하여 관리 디스크에 대한 가져오기/내보내기 액세스 제한

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

[프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 관리 디스크의 내보내기 및 가져오기를 제한하고 Azure 가상 네트워크의 클라이언트에서 [프라이빗 링크](../private-link/private-link-overview.md)를 통해 더 안전하게 데이터에 액세스할 수 있습니다. 프라이빗 엔드포인트는 관리 디스크에 대해 가상 네트워크 주소 공간의 IP 주소를 사용합니다. 가상 네트워크의 클라이언트와 관리 디스크 간의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 프라이빗 링크만 통과하여 공용 인터넷으로부터의 노출을 제거합니다.

Private Link를 사용하여 관리 디스크를 내보내거나 가져오려면 먼저 디스크 액세스 리소스를 만들고 프라이빗 엔드포인트를 만들어 동일한 구독의 가상 네트워크에 연결합니다. 그런 다음, 디스크 또는 스냅샷을 디스크 액세스 인스턴스와 연결합니다.

## <a name="limitations"></a>제한 사항

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="create-a-disk-access-resource"></a>디스크 액세스 리소스 만들기

1. Azure Portal에 로그인하고 [이 링크](https://aka.ms/disksprivatelinks)를 사용하여 **디스크 액세스** 로 이동합니다.

    > [!IMPORTANT]
    > [제공된 링크](https://aka.ms/disksprivatelinks)를 사용하여 디스크 액세스 창으로 이동해야 합니다. 링크를 사용하지 않으면 현재 퍼블릭 포털에 표시되지 않습니다.

1. **+ 만들기** 를 선택하여 새 디스크 액세스 리소스를 만듭니다.
1. **디스크 액세스 만들기** 창에서 구독 및 리소스 그룹을 선택합니다. **인스턴스 세부 정보** 에서 이름을 입력하고 지역을 선택합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="디스크 액세스 만들기 창의 스크린샷. 원하는 이름을 입력하고, 지역을 선택하고, 리소스 그룹을 선택하고, 계속 진행합니다.":::

1. **검토 + 만들기** 를 선택합니다.
1. 리소스가 만들어지면 해당 리소스로 직접 이동합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="포털의 리소스로 이동 단추에 대한 스크린샷":::

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

다음으로 프라이빗 엔드포인트를 만들어 디스크 액세스에 맞도록 구성해야 합니다.

1. 디스크 액세스 리소스의 **설정** 에서 **프라이빗 엔드포인트 연결** 을 선택합니다.
1. **+ 프라이빗 엔드포인트** 를 선택합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="디스크 액세스 리소스에 대한 개요 창의 스크린샷. 프라이빗 엔드포인트 연결이 강조 표시됩니다.":::

1. **프라이빗 엔드포인트 만들기** 창에서 리소스 그룹을 선택합니다.
1. 이름을 제공하고 디스크 액세스 리소스를 만든 지역과 동일한 지역을 선택합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="프라이빗 엔드포인트 만들기 워크플로의 첫 번째 창 스크린샷. 적절한 지역을 선택하지 않으면 나중에 문제가 발생할 수 있습니다.":::

1. 완료되면 **다음: 리소스** 를 선택합니다.
1. **리소스** 창에서 **내 디렉터리의 Azure 리소스에 연결** 을 선택합니다.
1. **리소스 종류** 에 대해 **Microsoft.Compute/diskAccesses** 를 선택합니다.
1. **리소스** 에 대해 이전에 만든 디스크 액세스 리소스를 선택합니다.
1. **대상 하위 리소스** 를 **디스크** 로 유지합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="프라이빗 엔드포인트 만들기 워크플로의 두 번째 창 스크린샷. 모든 값(리소스 종류, 리소스, 대상 하위 리소스)이 강조 표시되어 있습니다.":::

1. **다음: 구성** 을 선택합니다.
1. 디스크 가져오기 및 내보내기를 제한할 가상 네트워크를 선택합니다. 이렇게 하면 디스크를 다른 가상 네트워크로 가져오고 내보낼 수 없습니다.

    > [!NOTE]
    > 선택한 서브넷을 사용하는 네트워크 보안 그룹이 있는 경우 이 서브넷의 프라이빗 엔드포인트에만 NSG를 사용하지 않습니다. 이 서브넷의 다른 리소스는 네트워크 보안 그룹 적용을 유지합니다.

1. 적절한 서브넷을 선택합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="프라이빗 엔드포인트 만들기 워크플로의 세 번째 창 스크린샷. 가상 네트워크와 서브넷이 강조 표시되어 있습니다.":::

1. **검토 + 만들기** 를 선택합니다.

## <a name="enable-private-endpoint-on-your-disk"></a>디스크에서 프라이빗 엔드포인트 사용

1. 구성하려는 디스크로 이동합니다.
1. **설정** 에서 **네트워킹** 을 선택합니다.
1. **프라이빗 엔드포인트(디스크 액세스를 통해)** 를 선택하고, 이전에 만든 디스크 액세스를 선택합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="관리 디스크 네트워킹 창 스크린샷. 프라이빗 엔드포인트 선택 항목과 선택한 디스크 액세스가 강조 표시되어 있습니다. 저장하면 이 액세스를 위해 디스크가 구성됩니다.":::

1. **저장** 을 선택합니다.

이제 관리 디스크를 가져오고 내보내는 데 사용할 수 있는 프라이빗 링크를 구성했습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: Managed Disks를 내보내고 가져오는 데 개인 링크를 사용 하는 경우의 혜택은 무엇 인가요?**

**A:** 개인 링크를 사용 하 여 내보내기 및 가져오기 프로세스를 Azure 가상 네트워크 에서만 Managed Disks로 제한할 수 있습니다.

**Q: 개인 링크를 통해서만 디스크를 내보내거나 가져올 수 있는지 확인 하려면 어떻게 해야 하나요?**

**A:** **DiskAccessId** 속성을 디스크 액세스 개체의 인스턴스로 설정 해야 합니다. 또한 **Networkaccesspolicy** 속성을 **allowprivate** 으로 설정할 수 있습니다.

**Q: 여러 가상 네트워크를 동일한 디스크 액세스 개체에 연결할 수 있나요?**

**A:** 아니요. 현재는 디스크 액세스 개체를 하나의 가상 네트워크에만 연결할 수 있습니다.

**Q: 다른 구독의 디스크 액세스 개체에 가상 네트워크를 연결할 수 있나요?**

**A:** 아니요. 현재 동일한 구독의 가상 네트워크에만 디스크 액세스 개체를 연결할 수 있습니다.

**Q: 같은 디스크 액세스 개체를 사용 하는 여러 내보내기 또는 가져오기를 동시에 수행할 수 있나요?**

**A:** 5 개의 동시 내보내기 또는 가져오기를 수행할 수 있습니다.

**Q: 디스크 또는 스냅숏의 SAS URI를 사용 하 여 디스크와 연결 된 개인 끝점의 서브넷과 동일한 서브넷에 있지 않은 VM의 기본 VHD를 다운로드할 수 있나요?**

**A:** 아니요. 디스크와 연결 된 개인 끝점의 서브넷과 동일한 서브넷에 있는 VM에 대해서만이 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure에 VHD를 업로드하거나 관리 디스크를 다른 지역에 복사 - [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) 또는 [Azure PowerShell 모듈](windows/disks-upload-vhd-to-managed-disk-powershell.md)
- VHD 다운로드 - [Windows](windows/download-vhd.md) 또는 [Linux](linux/download-vhd.md)
- [프라이빗 링크 및 관리 디스크에 대한 FAQ](./faq-for-disks.yml)
- [PowerShell을 사용하여 관리 스냅샷을 VHD로 다른 지역의 스토리지 계정에 내보내기/복사](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)
