---
title: 관리형 HSM에서 키 관리 - Azure Key Vault | Microsoft Docs
description: 이 문서를 사용하여 관리형 HSM에서 키를 관리합니다.
services: key-vault
author: mbaldwin
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 71cc84defc8eb791b8d1f1189162b97ee44db9ec
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129209"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>Azure CLI를 사용하여 관리형 HSM 관리

> [!NOTE]
> Key Vault는 자격 증명 모음과 관리형 HSM의 두 가지 종류의 리소스를 지원합니다. 이 문서에서는 **관리형 HSM** 에 대해 설명합니다. 자격 증명 모음을 관리하는 방법을 알아보려면 [Azure CLI를 사용하여 Key Vault 관리](../general/manage-with-cli2.md)를 참조하세요.

관리형 HSM에 대한 개요는 [관리형 HSM이란?](overview.md)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 항목이 있어야 합니다.

* Microsoft Azure에 대한 구독. 아직 구독하지 않은 경우 [평가판](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
* Azure CLI 버전 2.25.0 이상 `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.
* 구독의 관리형 HSM. [빠른 시작: Azure CLI를 사용하여 관리형 HSM 프로비저닝 및 활성화](quick-create-cli.md)를 참조하여 관리형 HSM을 프로비저닝하고 활성화합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

CLI를 통한 로그인 옵션에 대한 자세한 내용은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

> [!NOTE]
> 아래의 모든 명령은 두 가지 사용 방법을 보여 줍니다. 하나는 **--hsm-name** 및 **--name**(키 이름) 매개 변수를 사용하고, 다른 하나는 해당하는 경우 키 이름을 포함하여 전체 URL을 지정할 수 있는 **--id** 매개 변수를 사용합니다. 후자의 방법은 호출자(사용자 또는 애플리케이션)에게 컨트롤 플레인에 대한 읽기 액세스 권한이 없고 데이터 평면에 대한 제한된 액세스 권한만 있는 경우에 유용합니다.

> [!NOTE]
> 키 자료와의 일부 상호 작용에는 특정 로컬 RBAC 권한이 필요합니다. 기본 제공 로컬 RBAC 역할 및 권한의 전체 목록은 [Managed HSM 로컬 RBAC 기본 제공 역할](./built-in-roles.md)을 참조하세요. 사용자에게 이러한 권한을 할당하려면 [관리형 HSM에 대한 액세스 보안](./secure-your-managed-hsm.md)을 참조하세요.
## <a name="create-an-hsm-key"></a>HSM 키 만들기

> [!NOTE]
> 관리형 HSM으로 생성되거나 가져온 키는 내보낼 수 없습니다. 키 이식성 및 내구성에 대한 권장 모범 사례를 참조하세요.

`az keyvault key create` 명령을 사용하여 키를 만듭니다.

### <a name="create-an-rsa-key"></a>RSA 키 만들기

아래 예제에서는 **wrapKey, unwrapKey** 작업(--ops)에만 사용되는 3,072비트 **RSA** 키를 만드는 방법을 보여줍니다. 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

`get` 작업은 공개 키와 키 특성만 반환합니다. 프라이빗 키(비대칭 키의 경우) 또는 키 자료(대칭 키의 경우)를 반환하지 않습니다.

### <a name="create-an-ec-key"></a>EC 키 만들기

아래 예제에서는 P-256 곡선을 사용하여 **서명 및 확인** 작업(-ops)에만 사용되고 두 개의 태그(**usage** 및 **appname**)가 있는 **EC** 키를 만드는 방법을 보여 줍니다. 태그는 추적 및 관리를 위해 추가 메타데이터를 키에 추가하는 데 도움이 됩니다.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>256비트 대칭 키 만들기

아래 예제에서는 **암호화 및 암호 해독** 작업(--ops)에만 사용되는 256비트 **대칭** 키를 만드는 방법을 보여 줍니다.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops encrypt decrypt  --tags ‘usage=signing] appname=myapp’ --kty oct-HSM --size 256
```

## <a name="view-key-attributes-and-tags"></a>키 특성 및 태그 보기

`az keyvault key show` 명령을 사용하여 키에 대한 특성, 버전 및 태그를 봅니다.

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>키 나열

`az keyvault key list` 명령을 사용하여 관리형 HSM 내의 모든 키를 나열합니다.

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>키 삭제

`az keyvault key delete` 명령을 사용하여 관리형 HSM에서 키를 삭제합니다. 일시 삭제는 항상 켜져 있습니다. 따라서 삭제된 키는 삭제됨 상태로 유지되며, 키가 복구 불가능한 상태로 제거(영구 삭제)되는 경우 보존 일 수가 경과될 때까지 복구할 수 있습니다.

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>삭제된 키 나열

`az keyvault key list-deleted` 명령을 사용하여 관리형 HSM에서 삭제된 상태의 모든 키를 나열합니다.

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>삭제된 키 복구(삭제 취소)

`az keyvault key list-deleted` 명령을 사용하여 관리형 HSM에서 삭제된 상태의 모든 키를 나열합니다. 삭제된 키를 복구하는 동안 --id 매개 변수를 사용하여 키를 복구(삭제 취소)해야 하는 경우 `az keyvault key list-deleted` 명령에서 얻은 삭제된 키의 `recoveryId` 값을 확인해야 합니다.

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>키 제거(영구 삭제)

`az keyvault key purge` 명령을 사용하여 키를 제거(영구 삭제)합니다.

> [!NOTE]
> 관리형 HSM에서 제거 보호를 사용하도록 설정된 경우 제거 작업이 허용되지 않습니다. 보존 기간이 경과하면 키가 자동으로 제거됩니다.

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key purge --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>단일 키 백업 만들기

`az keyvault key backup`을 사용하여 키 백업을 만듭니다. 백업 파일은 암호화된 방식으로 원본 HSM의 보안 도메인에 연결된 암호화된 Blob입니다. 이는 동일한 보안 도메인을 공유하는 HSM에서만 복원할 수 있습니다. [보안 도메인](security-domain.md)에 대해 자세히 알아보세요.

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>백업에서 단일 키 복원

`az keyvault key restore`를 사용하여 단일 키를 복원합니다. 백업이 만들어진 원본 HSM은 키가 복원되는 대상 HSM과 동일한 보안 도메인을 공유해야 합니다.

> [!NOTE]
> 동일한 이름의 키가 활성 또는 삭제됨 상태에 있으면 복원이 성공하지 못합니다.

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key restore --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.backup

```

## <a name="import-a-key-from-a-file"></a>파일에서 키 가져오기

`az keyvault key import` 명령을 사용하여 파일에서 키(RSA 및 EC만)를 가져옵니다. 인증서 파일에는 프라이빗 키가 있어야 하며, PEM 인코딩(RFC [1421](https://tools.ietf.org/html/rfc1421), [1422](https://tools.ietf.org/html/rfc1422), [1423](https://tools.ietf.org/html/rfc1423), [1424](https://tools.ietf.org/html/rfc1424)에 정의됨)을 사용해야 합니다.

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

키를 온-프레미스 HSM에서 관리형 HSM으로 가져오려면 [HSM 보호 키를 관리형 HSM으로 가져오기(BYOK)](hsm-protected-keys-byok.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Key Vault 명령에 대한 전체 Azure CLI 참조에 대해서는 [Key Vault CLI 참조](/cli/azure/keyvault)를 참조하세요.
- 프로그래밍 참조는 [Azure Key Vault 개발자 가이드](../general/developers-guide.md)를 참조하세요.
- [관리형 HSM 역할 관리](role-management.md)에 대해 자세히 알아봅니다.
- [관리형 HSM 모범 사례](best-practices.md)에 대해 자세히 알아봅니다.
