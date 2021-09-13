---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a8a503f5357ec69a1bab007f4ae2849582fed3b
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835598"
---
디스크에 대한 고객 관리형 키를 설정하려면 처음으로 수행하는 경우 특정 순서로 리소스를 만들어야 합니다. 먼저 Azure Key Vault를 만들고 설정해야 합니다.

## <a name="set-up-your-azure-key-vault"></a>Azure Key Vault 설정

1. [Azure Portal](https://aka.ms/diskencryptionupdates)에 로그인합니다.
1. **키 자격 증명 모음** 을 검색하고 선택합니다.

    [![검색 대화 상자가 확장된 Azure Portal의 스크린샷.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > 배포가 성공하려면 Azure Key Vault, 디스크 암호화 집합, VM, 디스크 및 스냅샷이 모두 동일한 지역과 구독에 있어야 합니다.

1. **+만들기** 를 선택하여 새 Key Vault를 만듭니다.
1. 새 리소스 그룹 만들기
1. 키 자격 증명 모음 이름을 입력하고, 지역을 선택하고, 가격 책정 계층을 선택합니다.

    > [!NOTE]
    > Key Vault 인스턴스를 만드는 경우 일시 삭제 및 제거 보호를 사용하도록 설정해야 합니다. 일시 삭제를 사용하면 지정된 보존 기간(기본적으로 90일) 동안 Key Vault에 삭제된 키를 보관하게 됩니다. 제거 보호를 사용하면 보존 기간이 지날 때까지 삭제된 키를 영구 삭제할 수 없습니다. 이러한 설정은 실수로 삭제하여 데이터가 손실되지 않도록 보호합니다. 이러한 설정은 관리 디스크를 암호화하기 위해 Key Vault를 사용하는 경우 필수입니다.

1. **검토 + 만들기** 를 선택하고, 선택 사항을 확인한 다음, **만들기** 를 선택합니다.

    ![Azure Key Vault 만들기 환경의 스크린샷. 만드는 특정 값 표시](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. 키 자격 증명 모음이 배포를 마치면 선택합니다.
1. **설정** 아래에서 **키** 를 선택합니다.
1. **생성/가져오기** 를 선택합니다.

    ![Key Vault 리소스 설정 창의 스크린샷. 설정 내에 생성/가져오기 단추를 표시합니다.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. **키 유형** 을 **RSA** 로 설정된 상태로 두고 **RSA 키 크기** 를 **2048** 로 설정된 상태로 둡니다.
1. 원하는 대로 나머지 선택 항목을 입력하고 **만들기** 를 선택합니다.

    ![생성/가져오기 단추를 선택하면 나타나는 키 만들기 창의 스크린샷](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

### <a name="add-an-azure-rbac-role"></a>Azure RBAC 역할 추가

이제 Azure Key Vault와 키를 만들었으므로 디스크 암호화 집합에 Azure Key Vault를 사용할 수 있도록 Azure RBAC 역할을 추가해야 합니다.

1. **액세스 제어(IAM)** 를 선택하고 역할을 추가합니다.
1. **Key Vault 관리자**, **소유자** 또는 **기여자** 역할을 추가합니다.

## <a name="set-up-your-disk-encryption-set"></a>디스크 암호화 집합 설정

1. **디스크 암호화 집합** 을 검색하고 선택합니다.
1. **디스크 암호화 집합** 창에서 **+만들기** 를 선택합니다.
1. 리소스 그룹을 선택하고, 암호화 집합의 이름을 지정하고, 키 자격 증명 모음으로 동일한 지역을 선택합니다.
1. **SSE 암호화 유형** 에서 **고객 관리형 키로 미사용 데이터 암호화** 를 선택합니다.

    > [!NOTE]
    > 특정 암호화 유형을 사용하여 디스크 암호화 집합을 만든 후에는 변경할 수 없습니다. 다른 암호화 유형을 사용하려면 새 디스크 암호화 집합을 만들어야 합니다.

1. **키를 선택하려면 클릭** 을 선택합니다.
1. 이전에 만든 키 자격 증명 모음 및 키와 버전을 선택합니다.
1. **선택** 을 누릅니다.
1. [고객 관리형 키의 자동 회전](../articles/virtual-machines/disk-encryption.md#automatic-key-rotation-of-customer-managed-keys)을 사용하려면 **자동 키 회전** 을 선택합니다.
1. **검토 + 만들기**, **만들기** 를 차례로 선택합니다.

    :::image type="content" source="media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png" alt-text="구독, 리소스 그룹, 디스크 암호화 집합 이름, 지역, 키 자격 증명 모음 + 키 선택기를 보여 주는 디스크 암호화 만들기 창의 스크린샷" lightbox="media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png":::
