---
title: Azure Key Vault 관리되는 HSM에 대해 HSM 보호 키를 생성하고 전송하는 방법 - Azure Key Vault | Microsoft Docs
description: 이 문서의 설명에 따라 관리되는 HSM에서 사용할 고유의 HSM 보호 키를 계획하고 생성하고 전송할 수 있습니다. 이 방법을 BYOK(Bring Your Own Key)라고 합니다.
services: key-vault
author: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: mbaldwin
ms.openlocfilehash: 3afe9135c620b808ad6853705c5886aa3628ab7a
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370312"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>HSM 보호 키를 Managed HSM으로 가져오기(BYOK)

 Azure Key Vault 관리되는 HSM은 온-프레미스 HSM(하드웨어 보안 모듈)에서 생성된 키 가져오기를 지원합니다. 키는 HSM 보호 경계를 벗어날 수 없습니다. 이 시나리오를 흔히 *BYOK(Bring Your Own Key)* 라고 합니다. 관리되는 HSM은 Marvell LiquidSecurity HSM 어댑터(FIPS 140-2 수준 3 유효성 검사됨)를 사용하여 키를 보호합니다.

이 문서의 정보를 참조하여 관리되는 HSM에서 사용할 고유의 HSM 보호 키를 계획하고 생성하고 전송할 수 있습니다.

> [!NOTE]
> 이 기능은 Azure 중국 21Vianet에 사용할 수 없습니다. 이 가져오기 방법은 [지원되는 HSM](#supported-hsms)에만 사용할 수 있습니다. 

자세한 내용 및 관리되는 HSM 사용 시작에 대한 자습서는 [관리되는 HSM이란?](overview.md)을 참조하세요.

## <a name="overview"></a>개요

다음은 프로세스의 개요입니다. 구체적인 단계는 이 문서의 뒷부분에서 설명합니다.

* 관리되는 HSM에서 키(KEK(‘키 교환 키’)라고 함)를 생성합니다. KEK는 `import` 키 작업만 포함하는 RSA-HSM 키여야 합니다. 
* KEK 공개 키를 .pem 파일로 다운로드합니다.
* KEK 공개 키를 온-프레미스 HSM에 연결된 오프라인 컴퓨터로 전송합니다.
* 오프라인 컴퓨터에서, HSM 공급업체에서 제공한 BYOK 도구를 사용하여 BYOK 파일을 만듭니다. 
* 대상 키는 KEK로 암호화되며, 관리되는 HSM으로 전송될 때까지 암호화된 상태로 유지됩니다. 암호화된 버전의 키만 온-프레미스 HSM을 벗어납니다.
* 관리되는 HSM 내에 생성되는 KEK는 내보낼 수 없습니다. HSM은 관리되는 HSM 외부에 KEK의 일반 버전이 없다는 규칙을 적용합니다.
* KEK는 대상 키를 가져올 관리되는 HSM과 동일한 관리되는 HSM에 있어야 합니다.
* BYOK 파일이 관리되는 HSM에 업로드되면 관리되는 HSM은 KEK 프라이빗 키를 사용하여 대상 키 자료를 암호 해독하고 HSM 키로 가져옵니다. 이 작업은 완전히 HSM 내에서 수행됩니다. 대상 키는 항상 HSM 보호 경계에 남아 있습니다.


## <a name="prerequisites"></a>사전 요구 사항

이 문서에서 Azure CLI 명령을 사용하려면 다음 항목이 있어야 합니다.

* Microsoft Azure에 대한 구독. 아직 구독하지 않은 경우 [평가판](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
* Azure CLI 버전 2.12.0 이상. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.
* 구독의 [지원되는 HSM 목록](#supported-hsms)에 있는 관리되는 HSM. [빠른 시작: Azure CLI를 사용하여 관리형 HSM 프로비저닝 및 활성화](quick-create-cli.md)를 참조하여 관리형 HSM을 프로비저닝하고 활성화합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

CLI를 통한 로그인 옵션에 대한 자세한 내용은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)을 살펴보세요.

## <a name="supported-hsms"></a>지원되는 HSM

|공급업체 이름|공급업체 유형|지원되는 HSM 모델|자세한 정보|
|---|---|---|---|
|Cryptomathic|ISV(엔터프라이즈 키 관리 시스템)|다음을 포함한 여러 HSM 브랜드 및 모델<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>자세한 내용은 [Cryptomathic 사이트](https://www.cryptomathic.com/azurebyok) 참조|[Cryptomathic BYOK 도구 및 설명서](https://www.cryptomathic.com/azurebyok)|
|Entrust|제조업체,<br/>서비스로 제공되는 HSM|<ul><li>HSM의 nShield 제품군</li><li>서비스로 제공되는 nShield</ul>|[nCipher 새 BYOK 도구 및 설명서](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Fortanix|제조업체,<br/>서비스로 제공되는 HSM|<ul><li>SDKMS(자체 방어 키 관리 서비스)</li><li>Equinix SmartKey</li></ul>|[BYOK용 클라우드 공급자에게 SDKMS 키 내보내기 - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|IBM|제조업체|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Marvell|제조업체|다음을 사용하는 모든 LiquidSecurity HSM<ul><li>펌웨어 버전 2.0.4 이상</li><li>펌웨어 버전 3.2 이상</li></ul>|[Marvell BYOK 도구 및 설명서](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Securosys SA|제조업체, 서비스로 제공되는 HSM|Primus HSM 제품군, Securosys 클라우드 HSM|[Primus BYOK 도구 및 설명서](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV(엔터프라이즈 키 관리 시스템)|다음을 포함한 여러 HSM 브랜드 및 모델<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>[자세한 내용은 StorMagic 사이트](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)를 참조하세요.|[SvKMS 및 Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|Thales|제조업체|<ul><li>펌웨어 버전 7.3 이상을 사용하는 Luna HSM 7 제품군</li></ul>| [Luna BYOK 도구 및 설명서](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Utimaco|제조업체,<br/>서비스로 제공되는 HSM|u.trust Anchor, CryptoServer|[Utimaco BYOK 도구 및 통합 가이드](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>지원되는 키 유형

|키 이름|키 유형|키 크기/곡선|원본|Description|
|---|---|---|---|---|
|KEK(키 교환 키)|RSA-HSM| 2048비트<br />3072비트<br />4096비트|관리형 HSM|관리되는 HSM에서 생성된 HSM 지원 RSA 키 쌍|
|대상 키|
||RSA-HSM|2048비트<br />3072비트<br />4096비트|공급업체 HSM|관리되는 HSM으로 전송할 키|
||EC-HSM|P-256<br />P-384<br />P-521|공급업체 HSM|관리되는 HSM으로 전송할 키|
||대칭 키(oct-hsm)|128비트<br />192비트<br />256비트|공급업체 HSM|관리되는 HSM으로 전송할 키|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>키를 생성하여 관리되는 HSM으로 전송

키를 생성하여 관리되는 HSM으로 전송하려면 다음을 수행합니다.

  - [1단계: KEK 생성](#step-1-generate-a-kek)
  - [2단계: KEK 공개 키 다운로드](#step-2-download-the-kek-public-key)
  - [3단계: 키를 생성하고 전송 준비](#step-3-generate-and-prepare-your-key-for-transfer)
  - [4단계: 관리되는 HSM으로 키 전송](#step-4-transfer-your-key-to-managed-hsm) 
   
### <a name="step-1-generate-a-kek"></a>1단계: KEK 생성

KEK는 관리되는 HSM에서 생성되는 RSA 키입니다. KEK는 가져오려는 키(*대상* 키)를 암호화하는 데 사용됩니다.

KEK는 다음과 같아야 합니다.
- RSA HSM 키(2048비트, 3072비트 또는 4096비트)이어야 함
- 대상 키를 가져오려는 관리되는 HSM과 동일한 관리되는 HSM에서 생성되어야 함
- 허용되는 키 작업이 `import`로 설정된 상태에서 만들어야 함

> [!NOTE]
> KEK의 유일하게 허용되는 키 작업은 'import'여야 합니다. 'import'는 다른 모든 키 작업과 상호 배타적 관계입니다.

[az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create) 명령을 사용하여 키 작업이 `import`로 설정된 KEK를 만듭니다. 다음 명령에서 반환된 키 식별자(`kid`)를 기록해 둡니다. ([3단계](#step-3-generate-and-prepare-your-key-for-transfer)에서 `kid` 값이 사용됩니다.)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>2단계: KEK 공개 키 다운로드

[az keyvault key download](/cli/azure/keyvault/key#az_keyvault_key_download) 명령을 사용하여 KEK 공개 키를 .pem 파일로 다운로드합니다. 가져오는 대상 키는 KEK 공개 키를 사용하여 암호화됩니다.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

KEKforBYOK.publickey.pem 파일을 오프라인 컴퓨터로 전송합니다. 다음 단계에서 이 파일이 필요합니다.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3단계: 키를 생성하고 전송 준비

HSM 공급업체의 설명서를 참조하여 BYOK 도구를 다운로드하고 설치합니다. HSM 공급업체의 지침에 따라 대상 키를 생성한 다음, 키 전송 패키지(BYOK 파일)를 만듭니다. BYOK 도구는 [1단계](#step-1-generate-a-kek)의 `kid` 및 [2단계](#step-2-download-the-kek-public-key)에서 다운로드한 KEKforBYOK.publickey.pem 파일을 사용하여 BYOK 파일에 암호화된 대상 키를 생성합니다.

BYOK 파일을 연결된 컴퓨터로 전송합니다.

> [!NOTE] 
> RSA 1024비트 키를 가져오는 것은 지원되지 않습니다. 현재 타원 곡선(Elliptic Curve) 키 가져오기는 지원되지 않습니다.
>
> **알려진 문제**: Luna HSM에서 RSA 4K 대상 키 가져오기는 펌웨어 7.4.0 이상에서만 지원됩니다.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>4단계: 관리되는 HSM으로 키 전송

키 가져오기를 완료하려면 연결이 끊어진 컴퓨터의 키 전송 패키지(BYOK 파일)를 인터넷에 연결된 컴퓨터로 전송합니다. [az keyvault key import](/cli/azure/keyvault/key#az_keyvault_key_import) 명령을 사용하여 BYOK 파일을 관리되는 HSM에 업로드합니다.

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

업로드가 성공하면 가져온 키의 속성이 Azure CLI에 표시됩니다.

## <a name="next-steps"></a>다음 단계

이제 관리되는 HSM에서 이 HSM 보호 키를 사용할 수 있습니다. 자세한 내용은 [가격 및 기능 비교](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.
