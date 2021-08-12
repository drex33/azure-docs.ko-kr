---
title: HSM 보호 키를 생성하고 전송하는 방법 – Azure Key Vault
description: Azure Key Vault에서 사용할 고유의 HSM 보호 키를 생성하고 전송하기 위한 계획을 세우는 방법을 알아봅니다. BYOK, 즉 Bring Your Own Key라고도 합니다.
services: key-vault
author: mbaldwin
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: mbaldwin
ms.openlocfilehash: 554a7669011feb431091399a1a5d1e0a14cfb25f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459859"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Key Vault로 HSM 보호 키 가져오기

보안을 강화하기 위해 Azure Key Vault 사용 시 HSM 경계를 절대로 벗어나지 않고 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이 시나리오를 흔히 BYOK( *Bring Your Own Key*)라고 합니다. Azure Key Vault는 HSM(FIPS 140-2 Level 2 유효성 검사 통과)의 nCipher nShield 제품군을 사용하여 키를 보호합니다.

이 기능은 Azure 중국 21Vianet에 사용할 수 없습니다.

> [!NOTE]
> Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../general/overview.md)  
> HSM 보호 키에 대한 자격 증명 모음을 만드는 내용이 포함된 자습서를 시작하려면 [Azure Key Vault란?](../general/overview.md)을 참조하세요.

## <a name="supported-hsms"></a>지원되는 HSM

사용하는 HSM에 따라 두 가지 방법을 통해 HSM 보호 키를 Key Vault에 전송할 수 있습니다. 아래 표를 참조하여 HSM 생성에 사용할 방법을 결정한 다음, Azure Key Vault에 사용할 고유의 HSM 보호 키를 전송하세요. 

|공급업체 이름|공급업체 유형|지원되는 HSM 모델|지원되는 HSM 키 전송 방법|
|---|---|---|---|
|Cryptomathic|ISV(엔터프라이즈 키 관리 시스템)|다음을 포함한 여러 HSM 브랜드 및 모델<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>자세한 내용은 [Cryptomathic 사이트](https://www.cryptomathic.com/azurebyok) 참조|[새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|
|Entrust|제조업체,<br/>서비스로 제공되는 HSM|<ul><li>HSM의 nShield 제품군</li><li>서비스로 제공되는 nShield</ul>|[새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|
|Fortanix|제조업체,<br/>서비스로 제공되는 HSM|<ul><li>SDKMS(자체 방어 키 관리 서비스)</li><li>Equinix SmartKey</li></ul>|[새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|
|IBM|제조업체|IBM 476x, CryptoExpress|[새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|
|Marvell|제조업체|다음을 사용하는 모든 LiquidSecurity HSM<ul><li>펌웨어 버전 2.0.4 이상</li><li>펌웨어 버전 3.2 이상</li></ul>|[새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|제조업체,<br/>서비스로 제공되는 HSM|<ul><li>HSM의 nShield 제품군</li><li>서비스로 제공되는 nShield</ul>|**방법 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md)(사용되지 않음). 이 방법은 <strong>2021년 6월 30일</strong> 이후 지원되지 않습니다.<br/>**방법 2:** [새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)(권장)<br/>위 Entrust 행을 참조하세요.|
|Securosys SA|제조업체,<br/>서비스로 제공되는 HSM|Primus HSM 제품군, Securosys 클라우드 HSM|[새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|
|StorMagic|ISV(엔터프라이즈 키 관리 시스템)|다음을 포함한 여러 HSM 브랜드 및 모델<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>[자세한 내용은 StorMagic 사이트](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)를 참조하세요.|[새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|
|Thales|제조업체|<ul><li>펌웨어 버전 7.3 이상을 사용하는 Luna HSM 7 제품군</li></ul>| [새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|
|Utimaco|제조업체,<br/>서비스로 제공되는 HSM|u.trust Anchor, CryptoServer|[새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|
|||||

## <a name="next-steps"></a>다음 단계

* [Key Vault 보안 개요](../general/security-features.md)를 검토하여 키의 보안, 내구성 및 모니터링을 확보합니다.
* 새 BYOK 방법에 대한 자세한 내용은 [BYOK 사양](./byok-specification.md)을 참조하세요.