---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/08/2021
ms.author: alkohli
ms.openlocfilehash: 4740ce4bd59598747cdd9c2147fbbd460b6e648e
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285203"
---
디바이스는 Azure에서 데이터의 대상으로 사용되는 스토리지 계정과 연결됩니다. 스토리지 계정에 대한 액세스는 해당 스토리지 계정과 연결된 두 개의 512비트 스토리지 액세스 키 및 구독을 통해 제어됩니다.

키 중 하나는 Azure Stack Edge 디바이스가 스토리지 계정에 액세스할 때 인증에 사용됩니다. 다른 키는 예약에 보관되므로 정기적으로 키를 회전할 수 있습니다.

보안상의 이유로 많은 데이터 센터 키 회전이 필요합니다. 키 회전에 대해 이 모범 사례를 따르는 것이 좋습니다.

- 스토리지 계정 키는 스토리지 계정의 루트 암호와 비슷합니다. 계정 키를 신중하게 보호합니다. 다른 사용자에게 암호를 배포하거나 하드 코딩하거나 다른 사용자가 액세스할 수 있는 일반 텍스트로 저장하지 마세요.
- 손상될 수 있다고 생각되는 경우 Azure Portal을 통해 [계정 키를 다시 생성](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys)합니다.
- Azure 관리자는 Azure Portal의 [스토리지] 섹션을 사용해 스토리지 계정에 직접 액세스하여 기본 또는 보조 키를 정기적으로 변경하거나 다시 생성해야 합니다.
- 자체 암호화 키를 사용하여 Azure Storage 계정의 데이터를 보호할 수도 있습니다. 고객 관리형 키를 지정하는 경우 해당 키는 데이터를 암호화하는 키에 대한 액세스를 보호하고 제어하는 데 사용됩니다. 데이터를 보호하는 방법에 대한 자세한 내용은 [Azure Storage 계정에 대해 고객 관리형 키 사용](../articles/storage/common/customer-managed-keys-overview.md#enable-customer-managed-keys-for-a-storage-account)을 참조하세요.
