---
title: 데이터 암호화 - Azure Portal - Azure Database for MySQL
description: Azure Portal을 사용하여 Azure Database for MySQL에 대한 데이터 암호화를 설정하고 관리하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5522f07ce9543af330b3526c4021a5916a3c80df
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674455"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Azure Portal를 사용하는 Azure Database for MySQL 데이터 암호화

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Portal을 사용하여 Azure Database for MySQL에 대한 데이터 암호화를 설정하고 관리하는 방법을 알아봅니다.

## <a name="prerequisites-for-azure-cli"></a>Azure CLI에 대한 사전 요구 사항

* Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
* Azure Key Vault에서 고객 관리형 키에 사용할 키 자격 증명 모음 및 키를 만듭니다.
* 고객 관리형 키로 사용하려면 키 자격 증명 모음에는 다음 속성이 있어야 합니다.
  * [일시 삭제](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [제거 보호](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * 보존 일수를 90일로 설정
  
    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* 고객 관리형 키로 사용하려면 키에 다음 특성이 있어야 합니다.
  * 만료 날짜 없음
  * 사용 안 함 없음
  * **가져오기**, **래핑**, **래핑 해제** 작업 수행
  * recoverylevel 특성이 **복구 가능** 으로 설정됨(보존 기간이 90일로 설정된 일시 삭제를 사용해야 함)
  * 제거 보호 사용

다음 명령을 사용하여 키의 특성을 확인할 수 있습니다.

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```
* Azure Database for MySQL - 단일 서버는 범용 또는 메모리 최적화 가격 책정 계층 및 범용 스토리지 v2에 있어야 합니다. 계속 진행하기 전에 [고객 관리형 키를 사용한 데이터 암호화](concepts-data-encryption-mysql.md#limitations) 제한 사항을 참조하세요.
## <a name="set-the-right-permissions-for-key-operations"></a>키 작업에 대한 올바른 권한 설정

1. Key Vault에서 **액세스 정책** > **액세스 정책 추가** 를 선택합니다.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="액세스 정책 및 액세스 정책 추가가 강조 표시된 Key Vault의 스크린샷":::

2. **키 사용 권한** 을 선택하고 **가져오기**, **래핑**, **래핑 해제**, **보안 주체**(MySQL 서버의 이름)를 차례로 선택합니다. 기존 보안 주체 목록에서 서버 보안 주체를 찾을 수 없는 경우 서버 보안 주체를 등록해야 합니다. 처음으로 데이터 암호화를 설정하려고 하지만 실패할 때 서버 보안 주체를 등록하라는 메시지가 표시됩니다.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="액세스 정책 개요":::

3. **저장** 을 선택합니다.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL에 대한 데이터 암호화 설정

1. Azure Database for MySQL에서 **데이터 암호화** 를 선택하여 고객 관리형 키를 설정합니다.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="데이터 암호화가 강조 표시된 Azure Database for MySQL의 스크린샷":::

2. 키 자격 증명 모음 및 키 쌍을 선택하거나 키 식별자를 입력할 수 있습니다.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="데이터 암호화 옵션이 강조 표시된 Azure Database for MySQL의 스크린샷":::

3. **저장** 을 선택합니다.

4. 임시 파일을 포함하여 모든 파일이 완전히 암호화되었는지 확인하려면 서버를 다시 시작합니다.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>복원 또는 복제본 서버에 데이터 암호화 사용

Key Vault에 저장된 고객 관리형 키를 사용하여 Azure Database for MySQL을 암호화한 후에는 새로 만든 서버 복사본도 암호화됩니다. 로컬 또는 지역 복원 작업을 통해 또는 복제본(로컬/영역 간) 작업을 통해 새 복사본을 만들 수 있습니다. 따라서 암호화된 MySQL 서버의 경우 다음 단계를 사용하여 암호화된 복원된 서버를 만들 수 있습니다.

1. 서버에서 **개요** > **복원** 을 선택합니다.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="개요 및 복원이 강조 표시된 Azure Database for MySQL의 스크린샷":::

   또는 복제를 사용하는 서버의 경우 **설정** 제목 아래에서 **복제** 를 선택합니다.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysql-replica.png" alt-text="복제가 강조 표시된 Azure Database for MySQL의 스크린샷":::

2. 복원 작업이 완료되면 새로 만든 서버가 주 서버 키로 암호화됩니다. 하지만 서버에서 기능 및 옵션을 사용할 수 없으며 서버에 액세스할 수 없습니다. 그러면 새 서버의 ID에 키 자격 증명 모음에 대한 액세스 권한이 아직 제공되지 않았기 때문에 데이터 조작을 방지할 수 있습니다.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="액세스할 수 없음 상태가 강조 표시된 Azure Database for MySQL의 스크린샷":::

3. 서버에 액세스할 수 있도록 하려면 복원된 서버에서 키의 유효성을 다시 검사합니다. **데이터 암호화** > **키 유효성 재검사** 를 선택합니다.

   > [!NOTE]
   > 새 서버의 서비스 사용자에게 키 자격 증명 모음에 대한 액세스 권한이 부여되어야 하기 때문에 첫 번째 유효성 재검사 시도는 실패합니다. 서비스 사용자를 생성하려면 **키 유효성 재검사** 를 선택합니다. 그러면 오류가 표시되지만 서비스 사용자는 생성됩니다. 이 다음은 이 문서 앞부분의 [이 단계](#set-the-right-permissions-for-key-operations)를 참조하세요.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="유효성 재검사 단계가 강조 표시된 Azure Database for MySQL의 스크린샷":::

   키 자격 증명 모음에 새 서버에 대한 액세스 권한을 부여해야 합니다.

4. 서비스 사용자를 등록한 후 키 유효성 재검사를 다시 실시합니다. 그러면 서버가 정상 기능을 다시 시작합니다.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="복원된 기능을 보여 주는 Azure Database for MySQL의 스크린샷":::

## <a name="next-steps"></a>다음 단계

* [Azure Database for MySQL에 대한 데이터 암호화 유효성 검사](howto-data-encryption-validation.md)
* [Azure Database for MySQL에서 데이터 암호화 문제 해결](howto-data-encryption-troubleshoot.md)
* [고객 관리형 키 개념을 사용한 데이터 암호화](concepts-data-encryption-mysql.md).
