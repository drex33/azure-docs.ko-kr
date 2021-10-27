---
title: 관리형 HSM에 영향을 주는 Azure 서비스 중단이 발생하는 경우 수행할 작업 - Azure Key Vault | Microsoft Docs
description: 관리형 HSM에 영향을 주는 Azure 서비스 중단이 발생하는 경우 수행할 작업을 알아봅니다.
services: key-vault
author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 66c20cc564d52d62f71d28dd35fd616a228d088f
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130130957"
---
# <a name="managed-hsm-disaster-recovery"></a>관리형 HSM 재해 복구

다음 이유 중 하나로 인해 원본이 손실되거나 사용할 수 없는 경우 HSM의 정확한 복제본을 만들 수 있습니다.

- 삭제된 후 제거되었습니다.
- 지역에서 치명적인 오류가 발생하여 모든 멤버 파티션이 제거되었습니다.

다음 항목이 있는 경우 동일하거나 다른 지역에서 HSM 인스턴스를 다시 만들 수 있습니다.
- 원본 HSM의 [보안 도메인](security-domain.md)
- 보안 도메인을 암호화하는 프라이빗 키(최소 쿼럼 수)
- 원본 HSM의 최신 전체 HSM [백업](backup-restore.md)

재해 복구 절차의 단계는 다음과 같습니다.

1. 새 HSM 인스턴스를 만듭니다.
1. "보안 도메인 복구"를 활성화합니다. 보안 도메인 전송을 위해 새 RSA 키 쌍(보안 도메인 교환 키)이 생성되고, 응답으로 보내지고, SecurityDomainExchangeKey(공개 키)로 다운로드됩니다.
1. "보안 도메인 전송 파일"을 만들고 업로드합니다. 보안 도메인을 암호화하는 프라이빗 키가 필요합니다. 프라이빗 키는 로컬로 사용되며, 이 프로세스의 어느 곳에서도 전송되지 않습니다.
1. 새 HSM에 대한 백업을 수행합니다. HSM이 비어 있는 경우에도 복원하기 전에 백업이 필요합니다. 백업을 통해 쉽게 롤백할 수 있습니다.
1. 원본 HSM에서 최근 HSM 백업을 복원합니다.

이러한 단계를 통해 HSM의 콘텐츠를 다른 지역에 수동으로 복제할 수 있습니다. HSM 이름(및 서비스 엔드포인트 URI)이 서로 다르므로 애플리케이션 구성을 변경하여 다른 위치에서 이러한 키를 사용해야 할 수도 있습니다.

## <a name="create-a-new-managed-hsm"></a>새 관리형 HSM 만들기

`az keyvault create` 명령을 사용하여 관리형 HSM을 만듭니다. 이 스크립트에는 리소스 그룹 이름, HSM 이름 및 지리적 위치의 세 가지 필수 매개 변수가 있습니다.

관리형 HSM 리소스를 만들려면 다음 입력을 제공해야 합니다.

- HSM 이름
- 구독에 배치할 리소스 그룹
- Azure 위치
- 초기 관리자 목록

아래 예제에서는 **현재 로그인한 사용자** 를 유일한 관리자로 사용하여 **ContosoMHSM** 이라는 HSM을 **미국 중부** 위치에 있는 **ContosoResourceGroup** 리소스 그룹에 만듭니다.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM2" --resource-group "ContosoResourceGroup" --location "centralus" --administrators $oid
```

> [!NOTE]
> create 명령은 몇 분 정도 걸릴 수 있습니다. 성공적으로 반환되면 HSM을 활성화할 준비가 된 것입니다.

이 명령의 출력에는 만든 관리형 HSM의 속성이 표시됩니다. 가장 중요한 두 개의 속성은 다음과 같습니다.

* **name**: 이 예제에서 이름은 ContosoMHSM입니다. 이 이름은 다른 Key Vault 명령에 사용됩니다.
* **hsmUri**: 이 예제에서 URI는 'https://contosomhsm2.managedhsm.azure.net '입니다. REST API를 통해 HSM을 사용하는 애플리케이션은 이 URI를 사용해야 합니다.

Azure 계정에는 이제 이 관리형 HSM에서 모든 작업을 수행할 수 있는 권한이 부여됩니다. 아직까지는 권한이 부여된 사용자가 없습니다.

## <a name="activate-the-security-domain-recovery-mode"></a>보안 도메인 복구 모드 활성화

일반적인 만들기 프로세스에서는 이 시점에서 새 보안 도메인을 초기화하고 다운로드합니다. 그러나 재해 복구 절차를 실행하므로 보안 도메인 복구 모드로 전환하고 보안 도메인 교환 키를 다운로드하도록 HSM에 요청합니다. 보안 도메인 교환 키는 보안 도메인을 HSM에 업로드하기 전에 암호화하는 데 사용되는 RSA 공개 키입니다. 해당 프라이빗 키는 HSM 내에서 보호되어 전송 중에 보안 도메인 콘텐츠를 안전하게 유지합니다.

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>대상 HSM에 보안 도메인 업로드

이 단계에서 필요한 항목은 다음과 같습니다.
- 이전 단계에서 다운로드한 보안 도메인 교환 키
- 원본 HSM의 보안 도메인
- 보안 도메인을 암호화하는 데 사용된 프라이빗 키의 최소 쿼럼 수

`az keyvault security-domain upload` 명령은 다음 작업을 수행합니다.

- 제공한 프라이빗 키를 사용하여 원본 HSM의 보안 도메인 암호를 해독합니다. 
- 이전 단계에서 다운로드한 보안 도메인 교환 키를 사용하여 암호화된 보안 도메인 업로드 Blob을 만든 다음,
- 보안 도메인 업로드 Blob을 HSM에 업로드하여 보안 도메인 복구를 완료합니다.

아래 예제에서는 해당 프라이빗 키가 2인 **ContosoMHSM** 에서 보안 도메인을 사용하여 보안 도메인을 받기 위해 기다리고 있는 **ContosoMHSM2** 에 업로드합니다. 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

이제 원본 HSM(ContosoMHSM)과 대상 HSM(ContosoMHSM2)에는 모두 동일한 보안 도메인이 있습니다. 이제 전체 백업을 원본 HSM에서 대상 HSM으로 복원할 수 있습니다.

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>새 HSM의 백업(복원 지점으로) 만들기

복원에 문제가 발생할 경우에 대비하여 복원 지점을 확보할 수 있도록 항상 전체 HSM 복원을 실행하기 전에 전체 백업을 수행하는 것이 좋습니다.

HSM 백업을 만들려면 다음 항목이 필요합니다.
- 백업이 저장되는 스토리지 계정
- 백업 프로세스에서 암호화된 백업을 저장할 새 폴더를 만드는 이 스토리지 계정의 Blob 스토리지 컨테이너

아래 예제에서는 스토리지 계정 **ContosoBackup** 에 있는 스토리지 컨테이너 **mhsmbackupcontainer** 의 HSM 백업에 `az keyvault backup` 명령을 사용합니다. 30분 후에 만료되는 SAS 토큰을 만들고, 이를 관리형 HSM에 제공하여 백업을 작성합니다.

```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
az storage container create --account-name  mhsmdemobackup --name mhsmbackupcontainer  --account-key $skey
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>원본 HSM에서 백업 복원

이 단계에서 필요한 항목은 다음과 같습니다.

- 원본 HSM의 백업이 저장되는 스토리지 계정 및 Blob 컨테이너
- 백업을 복원하려는 폴더 이름. 정기 백업을 만드는 경우 이 컨테이너 내에는 많은 폴더가 있습니다.


```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

이제 전체 재해 복구 프로세스가 완료되었습니다. 백업이 수행되면 모든 키, 버전, 특성, 태그 및 역할 할당을 포함하여 원본 HSM의 콘텐츠가 대상 HSM에 복사됩니다.

## <a name="next-steps"></a>다음 단계

- 보안 도메인에 대한 자세한 내용은 [관리형 HSM 보안 도메인 정보](security-domain.md)를 참조하세요.
- [관리형 HSM 모범 사례](best-practices.md)를 따릅니다.
