---
title: Azure Key Vault에서 인증서 서명 요청 만들기 및 병합
description: Azure Key Vault에서 CSR을 만들고 병합하는 방법에 대해 알아봅니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: 54b387892e3ad7d813fb97fb8e9ab399ccdb2a1c
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515110"
---
# <a name="create-and-merge-a-certificate-signing-request-in-key-vault"></a>Key Vault에서 인증서 서명 요청 만들기 및 병합

Azure Key Vault는 모든 CA(인증 기관)에서 발급한 디지털 인증서를 키 자격 증명 모음에 저장하도록 지원합니다. 프라이빗/공개 키 쌍을 사용하여 CSR(인증서 서명 요청) 만들기를 지원합니다. CSR은 모든 CA(내부 엔터프라이즈 CA 또는 외부 공용 CA)에서 서명할 수 있습니다. CSR(인증서 서명 요청)은 디지털 인증서를 요청하기 위해 CA에 보내는 메시지입니다.

인증서에 대한 일반적인 내용은 [Azure Key Vault 인증서](./about-certificates.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>파트너 CA에서 발급한 Key Vault에 인증서 추가

Key Vault는 다음 인증 기관과 협력하여 인증서 만들기를 간소화합니다.

|공급자|인증서 종류|구성 설정  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault가 DigiCert를 통해 OV 또는 EV SSL 인증서 제공| [통합 가이드](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault가 GlobalSign을 통해 OV 또는 EV SSL 인증서 제공| [통합 가이드](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>비파트너 CA에서 발급한 Key Vault에 인증서 추가

다음 단계에 따라 Key Vault와 파트너 관계가 없는 CA의 인증서를 추가합니다. (예를 들어 GoDaddy는 신뢰할 수 있는 Key Vault CA가 아닙니다.)

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 인증서를 추가하려는 주요 자격 증명 모음으로 이동합니다.
1. 속성 페이지에서 **인증서** 아이콘을 선택합니다.
1. **생성/가져오기** 탭을 선택합니다.
1. **인증서 만들기** 화면에서 다음 값을 선택합니다.
    - **인증서를 만드는 방법**: Generate.
    - **인증서 이름**: ContosoManualCSRCertificate.
    - **CA(인증 기관) 유형**: 통합되지 않은 CA에서 발급한 인증서.
    - **제목**: `"CN=www.contosoHRApp.com"`.
     > [!NOTE]
     > 값에 쉼표(,)가 포함된 RDN(상대 고유 이름)을 사용하는 경우 특수 문자가 포함된 값을 큰따옴표로 묶습니다. 
     >
     > **Subject** 에 대한 예제 항목: `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > 이 예제에서 RDN `OU`에는 이름에 쉼표가 있는 값이 포함되어 있습니다. `OU`의 결과 출력은 **Docs, Contoso** 입니다.
1. 원하는 다른 값을 선택한 다음, **만들기** 를 선택하여 인증서를 **인증서** 목록에 추가합니다.

    ![인증서 속성의 스크린샷](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. **인증서** 목록에서 새 인증서를 선택합니다. 아직 CA가 인증서를 발급하지 않았기 때문에 인증서의 현재 상태는 **사용 안 함** 입니다.
1. **인증서 작업** 탭에서 **CSR 다운로드** 를 선택합니다.

   ![CSR 다운로드 단추를 강조 표시하는 스크린샷.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. CA가 CSR(.csr)에 서명하도록 합니다.
1. 요청이 서명된 후, **인증서 작업** 탭에서 **서명된 요청 병합** 을 선택하여 서명된 인증서를 Key Vault에 추가합니다.

이제 인증서 요청이 성공적으로 병합되었습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 인증서 정책을 만듭니다. 이 시나리오에서 선택한 CA는 파트너 관계가 없기 때문에 **IssuerName** 이 **알 수 없음** 으로 설정되고 Key Vault가 인증서를 등록하거나 갱신하지 않습니다.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > 값에 쉼표(,)가 포함된 RDN(상대 고유 이름)을 사용하는 경우 전체 값 또는 값 집합에 작은따옴표를 사용하고 특수 문자가 포함된 값을 큰따옴표로 묶습니다. 
     >
     >**SubjectName** 에 대한 예제 항목: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. 이 예제에서 `OU` 값은 **Docs, Contoso** 로 읽습니다. 이 형식은 쉼표가 포함된 모든 값에 대해 작동합니다.
     > 
     > 이 예제에서 RDN `OU`에는 이름에 쉼표가 있는 값이 포함되어 있습니다. `OU`의 결과 출력은 **Docs, Contoso** 입니다.

1. CSR을 만듭니다.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. CA가 CSR에 서명하도록 합니다. `$csr.CertificateSigningRequest`는 인증서에 대한 기본 인코딩된 CSR입니다. 이 BLOB을 발급자의 인증서 요청 웹 사이트에 덤프할 수 있습니다. 이 단계는 CA마다 다릅니다. 이 단계를 실행하는 방법은 CA의 지침을 찾아보세요. certreq 또는 openssl 같은 도구를 사용하여 서명된 CSR을 가져와서 인증서 생성 프로세스를 완료할 수도 있습니다.

1. Key Vault에서 서명된 요청을 병합합니다. 인증서 요청이 서명된 후 Azure Key Vault에서 만든 초기 프라이빗/공개 키 쌍을 사용하여 병합할 수 있습니다.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

이제 인증서 요청이 성공적으로 병합되었습니다.

---

## <a name="add-more-information-to-the-csr"></a>CSR에 추가 정보 추가

CSR을 만들 때 추가 정보를 추가하려는 경우 **SubjectName** 에서 정의합니다. 다음과 같은 정보를 추가하는 것이 좋습니다.
- 국가
- 구/군/시
- 시/도
- 조직
- 조직 구성 단위

예제

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> 추가 정보가 포함된 DV(도메인 유효성 검사) 인증서를 요청하는 경우 CA는 요청의 모든 정보에 대해 유효성을 검사할 수 없으면 요청을 거부할 수 있습니다. OV(조직 유효성 검사) 인증서를 요청하는 경우 추가 정보가 더 적절할 수 있습니다.

## <a name="faqs"></a>FAQ

- 내 CSR을 어떻게 모니터링하거나 관리합니까?

     [인증서 만들기 모니터링 및 관리](./create-certificate-scenarios.md)를 참조하세요.

- **오류 유형 '지정된 X.509 인증서 콘텐츠에 있는 최종 엔터티 인증서의 공개 키와 지정된 프라이빗 키의 공개 부분이 일치하지 않습니다. 인증서가 올바른지 확인하세요.'** 가 표시되면 어떻게 됩니까?

     동일한 CSR 요청이 시작된 상태에서 서명된 CSR을 병합하지 않을 경우 이 오류가 발생합니다. 만들어진 각각의 새 CSR에는 서명된 요청을 병합할 때 일치해야 하는 프라이빗 키가 있습니다.

- CSR이 병합되면 전체 체인이 병합되나요?

     예, 사용자가 병합하기 위해 .p7b 파일을 다시 가져오면 전체 체인이 병합됩니다.

- 발급된 인증서가 Azure Portal에서 사용할 수 없는 상태이면 어떻게 되나요?

     **인증서 작업** 탭을 보고 해당 인증서에 대한 오류 메시지를 검토합니다.

- **오류 유형 '주체 이름이 유효한 X500 이름이 아닙니다'** 가 표시되면 어떻게 됩니까?

     **SubjectName** 에 특수 문자가 포함되어 있는 경우 이 오류가 발생할 수 있습니다. Azure Portal 및 PowerShell 지침의 메모를 참조하세요.

- 오류 유형 **인증서를 가져오는 데 사용된 CSR이 이미 사용되었습니다. 새 CSR로 새 인증서를 생성해 보세요.**
     인증서의 '고급 정책' 섹션으로 이동하여 '갱신 시 키 다시 사용' 옵션이 해제되어 있는지 확인합니다.
---

## <a name="next-steps"></a>다음 단계

- [인증, 요청 및 응답](../general/authentication-requests-and-responses.md)
- [Key Vault 개발자 가이드](../general/developers-guide.md)
- [Azure Key Vault REST API 참조](/rest/api/keyvault)
- [자격 증명 모음 - 만들기 또는 업데이트](/rest/api/keyvault/vaults/createorupdate)
- [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)
