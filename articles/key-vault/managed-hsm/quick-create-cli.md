---
title: 빠른 시작 - Azure 관리형 HSM 프로비저닝 및 활성화
description: Azure CLI를 사용하여 관리형 HSM을 프로비저닝 및 활성화하는 방법을 보여 주는 빠른 시작
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 06/21/2021
ms.author: mbaldwin
ms.custom: mode-api
ms.openlocfilehash: 21fc33dc4916abc4117d79a6c1ab06a6ef9448dc
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133057970"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 관리형 HSM 프로비저닝 및 활성화

Azure Key Vault 관리형 HSM은 **FIPS 140-2 수준 3** 유효성이 검사된 HSM을 사용하여 클라우드 애플리케이션용 암호화 키를 보호할 수 있는 완전 관리형 고가용 단일 테넌트 표준 규격 클라우드 서비스입니다. 관리형 HSM에 대한 자세한 내용을 보려면 [개요](overview.md)를 살펴보세요. 

이 빠른 시작에서는 Azure CLI를 사용하여 관리형 HSM을 만들고 활성화합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하려면 다음 항목이 있어야 합니다.

* Microsoft Azure에 대한 구독. 아직 구독하지 않은 경우 [평가판](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
* Azure CLI 버전 2.25.0 이상 `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *centralus* 위치에 *ContosoResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location centralus
```

## <a name="create-a-managed-hsm"></a>관리형 HSM 만들기

관리형 HSM 만들기는 2단계 프로세스입니다.
1. 관리형 HSM 리소스를 프로비저닝합니다.
1. 보안 도메인을 다운로드하여 관리형 HSM을 활성화합니다.

### <a name="provision-a-managed-hsm"></a>관리형 HSM 프로비저닝

`az keyvault create` 명령을 사용하여 관리형 HSM을 만듭니다. 이 스크립트에는 리소스 그룹 이름, HSM 이름 및 지리적 위치의 세 가지 필수 매개 변수가 있습니다.

관리형 HSM 리소스를 만들려면 다음 입력을 제공해야 합니다.
- 구독에 배치할 리소스 그룹
- Azure 위치입니다.
- 초기 관리자 목록

아래 예제에서는 **현재 로그인한 사용자** 를 유일한 관리자로 사용하고 일시 삭제에 대해 **28일 보존 기간** 을 사용하여 **ContosoMHSM** 이라는 HSM을 **미국 중부** 위치에 있는 **ContosoResourceGroup** 리소스 그룹에 만듭니다. [관리되는 HSM 일시 삭제](soft-delete-overview.md)에 관해 자세히 알아보기

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "centralus" --administrators $oid --retention-days 28
```

> [!NOTE]
> create 명령은 몇 분 정도 걸릴 수 있습니다. 성공적으로 반환되면 HSM을 활성화할 준비가 된 것입니다.

이 명령의 출력에는 만든 관리형 HSM의 속성이 표시됩니다. 가장 중요한 두 개의 속성은 다음과 같습니다.

* **name**: 이 예제에서 이름은 ContosoMHSM입니다. 이 이름은 다른 Key Vault 명령에 사용됩니다.
* **hsmUri**: 이 예제에서 URI는 'https://contosohsm.managedhsm.azure.net '입니다. REST API를 통해 HSM을 사용하는 애플리케이션은 이 URI를 사용해야 합니다.

Azure 계정에는 이제 이 관리형 HSM에서 모든 작업을 수행할 수 있는 권한이 부여됩니다. 아직까지는 권한이 부여된 사용자가 없습니다.

### <a name="activate-your-managed-hsm"></a>관리형 HSM 활성화

HSM이 활성화되기 전까지는 모든 데이터 평면 명령이 비활성화됩니다. 키를 만들거나 역할을 할당할 수 없습니다. create 명령을 실행하는 동안 할당된 지정된 관리자만 HSM을 활성화할 수 있습니다. HSM을 활성화하려면 [보안 도메인](security-domain.md)을 다운로드해야 합니다.

HSM을 활성화하려면 다음이 필요합니다.
- 최소 3개의 RSA 키 쌍(최대 10개)
- 보안 도메인(쿼럼)의 암호를 해독하는 데 필요한 최소 키 수를 지정합니다.

HSM을 활성화하려면 적어도 3개(최대 10개)의 RSA 공개 키를 HSM에 전송합니다. HSM은 이러한 키를 사용하여 보안 도메인을 암호화하고 다시 보냅니다. 이 보안 도메인 다운로드가 성공적으로 완료되면 HSM을 사용할 준비가 된 것입니다. 또한 보안 도메인의 암호를 해독하는 데 필요한 최소 프라이빗 키 수인 쿼럼을 지정해야 합니다.

아래 예제에서는 `openssl`을 사용하여 자체 서명된 인증서 3개를 생성하는 방법을 보여 줍니다.

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> 이 단계에서 생성된 RSA 키 쌍 및 보안 도메인 파일을 안전하게 만들어 저장합니다.

`az keyvault security-domain download` 명령을 사용하여 보안 도메인을 다운로드하고 관리형 HSM을 활성화합니다. 아래 예제에서는 3개의 RSA 키 쌍을 사용하고(이 명령에는 공개 키만 필요함) 쿼럼을 2로 설정합니다.

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

보안 도메인 파일과 RSA 키 쌍을 안전하게 저장하세요. 재해 복구를 위해 또는 동일한 보안 도메인을 공유하는 다른 관리형 HSM을 만들기 위해 키를 공유할 수 있도록 해야 합니다.

보안 도메인을 성공적으로 다운로드한 후 HSM은 활성 상태가 되고 사용할 준비가 됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 빠른 시작과 자습서는 이 빠른 시작을 기반으로 하여 작성됩니다. 이후의 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 리소스 그룹 및 모든 관련된 리소스를 제거할 수 있습니다. 다음과 같이 리소스를 삭제할 수 있습니다.

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Key Vault를 만들어 비밀을 저장했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [관리형 HSM 개요](overview.md) 읽기
- [관리형 HSM에서 키 관리](key-management.md)에 대해 알아보기
- [관리되는 HSM의 역할 관리](role-management.md)에 대해 알아보기
- [관리형 HSM 모범 사례](best-practices.md) 검토
