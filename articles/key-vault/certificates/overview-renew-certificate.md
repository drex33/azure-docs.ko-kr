---
title: Azure Key Vault 인증서 갱신 정보
description: 이 문서에서는 Azure Key Vault 인증서를 갱신하는 방법을 설명합니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: b2eeca4bb6f5d8af01aa283446961b56d27918d7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761672"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Azure Key Vault 인증서 갱신

Azure Key Vault를 사용하여 네트워크에 대한 디지털 인증서를 쉽게 프로비저닝, 관리 및 배포하고 애플리케이션에 대한 보안 통신을 사용하도록 설정할 수 있습니다. 인증서에 대한 자세한 내용은 [Azure Key Vault 인증서 정보](./about-certificates.md)를 참조하세요.

단기 인증서를 사용하거나 인증서 순환 주기를 늘리면 권한 없는 사용자가 애플리케이션에 액세스하는 것을 방지할 수 있습니다.

이 문서에서는 Azure Key Vault 인증서를 갱신하는 방법을 설명합니다.

## <a name="get-notified-about-certificate-expiration"></a>인증서 만료에 대한 알림 받기
인증서 수명 이벤트에 대한 알림을 받으려면 인증서 연락처를 추가해야 합니다. 인증서 연락처에는 인증서 수명 이벤트에서 트리거된 알림을 보내도록 연락처 정보가 포함됩니다. 연락처 정보는 키 자격 증명 모음의 모든 인증서에서 공유합니다. 알림은 키 자격 증명 모음의 모든 인증서에 대한 이벤트에 대해 지정한 모든 연락처로 보내집니다.

### <a name="steps-to-set-certificate-notifications"></a>인증서 알림을 설정하는 단계는 다음과 같습니다.
먼저 인증서 연락처를 키 자격 증명 모음에 추가합니다. Azure Portal 또는 PowerShell cmdlet [Add-AzKeyVaultCertificateContact](/powershell/module/az.keyvault/add-azkeyvaultcertificatecontact)를 사용하여 추가할 수 있습니다.

둘째, 인증서 만료에 대한 알림을 받을 조건을 구성합니다. 인증서의 수명 주기 특성을 구성하려면 [Key Vault에서 인증서 자동 순환 구성](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate)을 참조하세요.

인증서를 자동으로 갱신하도록 정책이 설정되는 경우 알림을 보내는 이벤트는 다음과 같습니다.

- 인증서 갱신 전
- 인증서 갱신 후 - 인증서가 성공적으로 갱신된 경우에 시작되거나, 오류가 있는 경우 인증서를 수동으로 갱신해야 합니다.  

  인증서를 수동으로(이메일을 통해서만) 갱신하는 정책을 설정하는 경우 인증서를 갱신할 시간이 되면 알림이 전송됩니다.  

Key Vault에는 다음과 같은 세 가지 범주의 인증서가 있습니다.
-    DigiCert 또는 GlobalSign과 같은 통합 CA(인증 기관)를 통해 생성된 인증서
-    비-통합 CA를 통해 생성된 인증서
-    자체 서명된 인증서

## <a name="renew-an-integrated-ca-certificate"></a>통합 CA 인증서 갱신 
Azure Key Vault는 신뢰할 수 있는 Microsoft 인증 기관 DigiCert 및 GlobalSign에서 발급한 인증서의 엔드투엔드 유지 관리를 처리합니다. [신뢰할 수 있는 CA를 Key Vault와 통합하는 방법](./how-to-integrate-certificate-authority.md)을 알아보세요.

## <a name="renew-a-nonintegrated-ca-certificate"></a>비-통합 CA 인증서 갱신 
Azure Key Vault를 사용하면 CA에서 인증서를 가져올 수 있으며, 여러 Azure 리소스를 통합하여 쉽게 배포할 수 있는 장점이 있습니다. 인증서 만료 날짜를 추적하지 못하게 되거나 인증서가 이미 만료된 후 그 사실을 알게 되는 사태를 방지할 수 있도록 키 자격 증명 모음을 통해 최신 정보를 계속 받을 수 있습니다. 비-통합 CA 인증서의 경우 키 자격 증명 모음을 통해 만료 예정 이메일 알림을 설정할 수 있습니다. 이러한 알림을 여러 사용자에 대해서도 설정할 수 있습니다.

> [!IMPORTANT]
> 인증서는 버전이 지정된 개체입니다. 현재 버전이 만료될 경우 새 버전을 만들어야 합니다. 개념적으로 각각의 새 버전은 키와 해당 키를 ID에 연결하는 BLOB으로 구성된 새 인증서입니다. 비-파트너 CA를 사용하는 경우 키 자격 증명 모음은 키/값 쌍을 생성하고 CSR(인증서 서명 요청)을 반환합니다.

비-통합 CA 인증서를 갱신하려면 다음을 수행합니다.

1. Azure Portal에 로그인한 다음, 갱신할 인증서를 엽니다.
1. 인증서 창에서 **새 버전** 을 선택합니다.
1. **인증서 작업** 을 선택합니다.
1. **CSR 다운로드** 를 선택하여 CSR 파일을 로컬 드라이브에 다운로드합니다.
1. 선택한 CA로 CSR을 보내서 요청에 서명합니다.
1. 서명한 요청을 다시 가져온 다음, 동일한 인증서 작업 창에서 **CSR 병합** 을 선택합니다.

> [!NOTE]
> 서명된 CSR을 앞에서 만든 동일한 CSR 요청과 병합해야 합니다. 그렇지 않으면 키가 일치하지 않습니다.

새 CSR을 만드는 방법에 대한 자세한 내용은 [Key Vault에서 CSR 만들기 및 병합](create-certificate-signing-request.md)을 참조하세요.

## <a name="renew-a-self-signed-certificate"></a>자체 서명된 인증서 갱신

Azure Key Vault는 자체 서명된 인증서의 자동 갱신도 처리합니다. 발급 정책 변경 및 인증서의 수명 주기 특성 업데이트에 대한 자세한 내용은 [Key Vault에서 인증서 자동 순환 구성](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate)을 참조하세요.

## <a name="troubleshoot"></a>문제 해결
* 발급된 인증서가 Azure Portal에서 *사용 안 함* 상태이면 **인증서 작업** 으로 이동하여 인증서의 오류 메시지를 확인합니다.
* 오류 유형 "인증서를 가져오는 데 사용된 CSR이 이미 사용되었습니다. 새 CSR을 사용하여 새 인증서를 생성해 보세요."
  인증서의 '고급 정책' 섹션으로 이동하여 **'갱신 시 키 다시 사용'** 옵션이 해제되어 있는지 확인합니다.


## <a name="frequently-asked-questions"></a>자주 묻는 질문

**인증서의 자동 순환 기능을 테스트하려면 어떻게 해야 하나요?**

유효 기간이 **1개월** 인 자체 서명된 인증서를 만든 다음, 순환의 수명 동작을 **1%** 로 설정합니다. 앞으로 며칠 동안 생성되는 인증서 버전 기록을 볼 수 있습니다.
  
**인증서 자동 갱신 후 태그가 복제되나요?**

예, 자동 갱신 후 태그가 복제됩니다.

## <a name="next-steps"></a>다음 단계
*    [DigiCert 인증 기관과 Key Vault 통합](how-to-integrate-certificate-authority.md)
*    [자습서: Key Vault에서 인증서 자동 순환 구성](tutorial-rotate-certificates.md)
