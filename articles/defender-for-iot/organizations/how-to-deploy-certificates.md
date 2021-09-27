---
title: 인증서 배포
description: IoT 용 Defender에 대 한 인증서를 설정 및 배포 하는 방법을 알아봅니다.
ms.date: 08/29/2021
ms.topic: how-to
ms.openlocfilehash: d6a67a84dab6df165346ff2739d0ac56e2b8eff2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602824"
---
# <a name="about-certificates"></a>인증서 정보

이 문서에서는 IoT 용 Azure Defender에 대 한 인증서를 만들고 배포 하는 데 필요한 정보를 제공 합니다. 보안, PKI 또는 기타 정규화 된 인증서 리더가 인증서 만들기 및 배포를 처리 해야 합니다.

IoT 용 Defender는 SSL/TLS 인증서를 사용 하 여 다음 시스템 구성 요소 간의 통신을 보호 합니다. 

- 사용자와 어플라이언스의 웹 콘솔 간에. 
- 센서와 온-프레미스 관리 콘솔 사이. 
- 관리 콘솔과 고가용성 관리 콘솔 사이
- 센서 및 온-프레미스 관리 콘솔의 REST API 합니다. 

Defender for IoT Admin 사용자는 SSL/TLS 인증서 대화 상자에서 센서 콘솔과 해당 온-프레미스 관리 콘솔에 인증서를 업로드할 수 있습니다.

:::image type="content" source="media/how-to-deploy-certificates/certificate-upload.png" alt-text="인증서-업로드 대화 상자":::

## <a name="about-certificate-generation-methods"></a>인증서 생성 방법 정보

모든 인증서 생성 방법은 다음을 사용 하 여 지원 됩니다.  

- 개인 및 Enterprise 키 인프라 (사설 PKI) 
- 공개 키 인프라 (공용 PKI) 
- 기기에서 로컬로 생성 된 인증서 (로컬에서 자체 서명 됨) 

> [!Important]
> 로컬에서 자체 서명 된 인증서를 사용 하지 않는 것이 좋습니다. 이 유형의 연결은 안전하지 않으며 테스트 환경에만 사용해야 합니다. 인증서 소유자의 유효성을 검사할 수 없고 시스템의 보안을 유지할 수 없으므로 프로덕션 네트워크에는 자체 서명 된 인증서를 사용 하면 안 됩니다.

## <a name="about-certificate-validation"></a>인증서 유효성 검사 정보

시스템 구성 요소 간의 통신을 보호 하는 것 외에도 사용자는 인증서 유효성 검사를 수행할 수 있습니다.  

유효성 검사는 다음에 대해 평가 됩니다.

- CRL (인증서 해지 목록)
- 인증서 만료 날짜  
- 인증서 신뢰 체인

유효성 검사는 두 번 수행 됩니다.

1. 센서 및 온-프레미스 관리 콘솔에 인증서를 업로드 하는 경우 유효성 검사에 실패 하면 인증서를 업로드할 수 없습니다.
1. 암호화 된 통신을 시작할 때:

    - IoT 시스템 구성 요소 (예: 센서 및 온-프레미스 관리 콘솔) 용 Defender

    - IoT 용 Defender 및 전달 규칙에 정의 된 특정 타사 서버.  자세한 내용은 [전달 된 경고 정보](how-to-forward-alert-information-to-partners.md#about-forwarded-alert-information) 정보를 참조 하세요.  

유효성 검사에 실패 하면 관련 구성 요소 간의 통신이 중단 되 고 유효성 검사 오류가 콘솔에 표시 됩니다.

## <a name="about-certificate-upload-to-defender-for-iot"></a>IoT 용 Defender에 인증서 업로드 정보

센서 및 온-프레미스 관리 콘솔 설치 다음에 로컬 자체 서명 된 인증서가 생성 되어 센서 및 온-프레미스 관리 콘솔 웹 응용 프로그램에 액세스 하는 데 사용 됩니다.

처음으로 센서 및 온-프레미스 관리 콘솔에 로그인 하는 경우에는 SSL/TLS 인증서를 업로드 하 라는 메시지가 관리자에 게 표시 됩니다. SSL/TLS 인증서를 사용 하는 것이 좋습니다.

인증서 리더가 인증서를 제대로 만들지 않았거나 연결에 문제가 있는 경우 인증서를 업로드할 수 없으며, 사용자가 로컬로 서명 된 인증서를 사용 해야 합니다.  

업로드 된 인증서 및 타사 인증서의 유효성을 검사 하는 옵션은 자동으로 사용 하도록 설정 되지만 사용 하지 않도록 설정할 수 있습니다. 사용 하지 않도록 설정 하면 인증서가 유효 하지 않은 경우에도 구성 요소 간 암호화 된 통신이 계속 됩니다.

## <a name="certificate-deployment-tasks"></a>인증서 배포 작업

이 섹션에서는 인증서 배포를 원활 하 게 실행 하기 위해 수행 해야 하는 단계에 대해 설명 합니다.

**인증서를 배포 하려면 다음을 확인 합니다.**

- 보안, PKI 또는 인증서 전문가는 인증서 만들기 또는 감독를 만듭니다. 
- 각 센서, 관리 콘솔 및 HA 컴퓨터에 대해 고유한 인증서를 만듭니다.
- 인증서 만들기 요구 사항을 충족 합니다.  [인증서 만들기 요구 사항](#certificate-creation-requirements)을 참조 하세요.
- IoT 센서에 대해 각 Defender에 로그인 하는 관리자 사용자와 온-프레미스 관리 콘솔 및 HA 컴퓨터는 인증서에 액세스할 수 있습니다.

## <a name="certificate-creation-requirements"></a>인증서 만들기 요구 사항

이 섹션에서는 다음을 비롯 한 인증서 만들기 요구 사항에 대해 설명 합니다.

- [인증서 유효성 검사를 위한 포트 액세스 요구 사항](#port-access-requirements-for-certificate-validation)

- [파일 형식 요구 사항](#file-type-requirements)

- [키 파일 요구 사항](#key-file-requirements)

- [인증서 체인 파일 요구 사항 (pem이 사용 되는 경우)](#certificate-chain-file-requirements-if-pem-is-used)

### <a name="port-access-requirements-for-certificate-validation"></a>인증서 유효성 검사를 위한 포트 액세스 요구 사항

인증서 유효성 검사를 사용 하는 경우 포트 80에 대 한 액세스를 사용할 수 있는지 확인 합니다.

인증서 유효성 검사는 인증서 해지 목록 및 인증서 만료 날짜에 대해 평가 됩니다. 즉, 어플라이언스는 인증서로 정의 된 CRL 서버에 대 한 연결을 설정할 수 있어야 합니다. 기본적으로 인증서는 HTTP 포트 80에서 CRL URL을 참조합니다. 

일부 조직의 보안 정책은 이 포트에 대한 액세스를 차단할 수 있습니다. 조직에 포트 80에 대한 액세스 권한이 없는 경우 다음을 수행할 수 있습니다. 

1. 인증서에 다른 URL과 특정 포트를 정의합니다.

    - URL은 https://가 아닌 http://로 정의 되어야 합니다.

    - 대상 CRL 서버가 정의한 포트에서 수신 대기할 수 있는지 확인합니다. 

1. 포트 80에서 CRL에 액세스할 프록시 서버를 사용합니다.

### <a name="file-type-requirements"></a>파일 형식 요구 사항

IoT 용 Defender에는 각 CA 서명 인증서에 키 파일과 .crt 파일이 포함 되어 있어야 합니다. 이러한 파일은 로그인 한 후 센서 및 온-프레미스 관리 콘솔에 업로드 됩니다. 일부 조직에는 pem 파일이 필요할 수 있습니다. IoT 용 Defender에는이 파일 형식이 필요 하지 않습니다.

**.crt – 인증서 컨테이너 파일**  
다른 확장명을 가진 pem 또는 형식이 지정 된 파일입니다. 해당 파일은 Windows 탐색기에서 인증서로 인식됩니다. Windows 탐색기에서 pem 파일을 인식할 수 없습니다.

**. key – 개인 키 파일**  
키 파일은 PEM 파일과 동일한 형식이지만 확장명이 다릅니다.

**. pem – 인증서 컨테이너 파일 (옵션)** PEM은 인증서 텍스트의 Base64 인코딩을 포함 하는 텍스트 파일입니다. 일반 텍스트 헤더는 인증서의 시작과 끝을 표시 하는 바닥글 & 합니다.

기존 파일 형식을 지원 되는 형식으로 변환 해야 할 수도 있습니다. 자세한 내용은 [기존 파일을 지원 되는 파일로 변환을](#convert-existing-files-to-supported-files) 참조 하세요.

### <a name="certificate-file-parameter-requirements"></a>인증서 파일 매개 변수 요구 사항

인증서를 만들기 전에 다음 매개 변수 요구 사항을 충족 하는지 확인 합니다.

- [CRT 파일 요구 사항](#crt-file-requirements)
- [키 파일 요구 사항](#key-file-requirements)
- [인증서 체인 파일 요구 사항 (pem이 사용 되는 경우)](#certificate-chain-file-requirements-if-pem-is-used)

### <a name="crt-file-requirements"></a>CRT 파일 요구 사항

이 섹션에서는 crt 필드 요구 사항에 대해 설명 합니다.

- 서명 알고리즘 = SHA256RSA 
- 서명 해시 알고리즘 = SHA256 
- 유효 개시 시점 = 유효한 지난 날짜 
- 유효 종료 시점 = 유효한 미래 날짜 
- 공개 키 = RSA 2,048비트(최소) 또는 4,096비트 
- CRL 배포 지점 = .crl 파일의 URL 
- 주체 CN (일반 이름) = 어플라이언스의 도메인 이름 예: Sensor.contoso.com 또는 *. contoso.com.  
- 주체 (C)ountry = 정의됨(예: US) 
- 주체 (OU) Org Unit = 정의됨(예: Contoso Labs) 
- 주체 (O)rganization = 정의됨(예: Contoso Inc) 

다른 매개 변수를 사용하는 인증서도 작동할 수 있지만 Microsoft에서 지원하지 않습니다.  

### <a name="key-file-requirements"></a>키 파일 요구 사항

RSA 2048 비트 또는 4096 비트를 사용 합니다.

4096 비트의 키 길이를 사용 하는 경우 각 연결을 시작할 때 SSL 핸드셰이크 속도가 더 느려집니다. 또한 핸드셰이크 중 CPU 사용량이 증가 합니다.

### <a name="certificate-chain-file-requirements-if-pem-is-used"></a>인증서 체인 파일 요구 사항 (pem이 사용 되는 경우)

인증서를 초래한 신뢰 체인에 있는 모든 인증 기관의 인증서를 포함 하는 pem 파일입니다.  

모음 특성은 인증서 체인 파일에서 지원 됩니다.

## <a name="create-certificates"></a>인증서 만들기

인증서 관리 플랫폼을 사용 하 여 자동화 된 PKI 관리 플랫폼과 같은 인증서를 만듭니다. 인증서가 인증서 파일 요구 사항을 충족 하는지 확인 합니다. 만든 파일 테스트에 대 한 자세한 내용은 인증서 테스트를 참조 하십시오.

인증서 유효성 검사를 수행 하지 않는 경우 인증서에서 CRL URL 참조를 제거 합니다. 이 매개 변수에 대 한 자세한 내용은 [CRT 파일 요구 사항](#crt-file-requirements) 을 참조 하세요.

인증서를 자동으로 만들 수 있는 응용 프로그램이 없는 경우 보안, PKI 또는 기타 자격 증명 잠재 고객을 참조 하세요.

[만든 인증서를 테스트할](#test-certificates-you-create)수 있습니다.  

새 인증서 파일을 만들지 않으려는 경우 기존 인증서 파일을 변환할 수도 있습니다. 자세한 내용은 [기존 파일을 지원 되는 파일로 변환을](#convert-existing-files-to-supported-files) 참조 하세요.

### <a name="sample-certificate"></a>샘플 인증서

인증서를 아래 샘플 인증서와 비교할 수 있습니다. 동일한 필드가 종료 되 고 필드의 순서가 동일한 지 확인 합니다.

:::image type="content" source="media/how-to-deploy-certificates/sample-certificate.png" alt-text="샘플-인증서.":::

## <a name="test-certificates-you-create"></a>만든 인증서 테스트

센서 및 온-프레미스 관리 콘솔에 인증서를 배포 하기 전에 인증서를 테스트할 수 있습니다. 인증서 csr 파일 또는 개인 키 파일 내에서 정보를 확인 하려는 경우 다음 명령을 사용 합니다.

| **테스트** | **CLI 명령** |
|--|--|
| CSR(인증서 서명 요청) 확인 | openssl 요청-텍스트-noout-확인-csr csr |
| 프라이빗 키 확인  | openssl rsa-in privateKey-check  |
| 인증서 확인  | openssl x509-텍스트-noout |

이러한 테스트에 실패 하면 [인증서 파일 매개 변수 요구 사항을](#certificate-file-parameter-requirements) 검토 하 여 파일 매개 변수가 정확한 지 확인 하거나 인증서 리더를 참조 합니다.

## <a name="convert-existing-files-to-supported-files"></a>기존 파일을 지원 되는 파일로 변환 

이 섹션에서는 기존 인증서 파일을 지원 되는 형식으로 변환 하는 방법을 설명 합니다.

|**설명** | **CLI 명령** |
|--|--|
| .Crt 파일을 pem 파일로 변환   | `openssl x509 -inform PEM -in <full path>/<pem-file-name>.pem -out <fullpath>/<crt-file-name>.crt`  | 
| Pem 파일을 .crt 파일로 변환   | `openssl x509 -inform PEM -in <full path>/<pem-file-name>.pem -out <fullpath>/<crt-file-name>.crt` |  
| 개인 키와 인증서를 포함 하는 PKCS # 12 파일 (.pfx. p12)을 pem으로 변환 합니다.   | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes`. -Nocerts를 추가 하 여 개인 키만 출력 하거나-nocerts를 추가 하 여 인증서만 출력할 수 있습니다.  |  

## <a name="troubleshooting"></a>문제 해결  

이 섹션에서는 인증서 업로드 및 유효성 검사 중에 발생할 수 있는 다양 한 문제 및 문제를 해결 하기 위해 수행 하는 단계에 대해 설명 합니다.

### <a name="troubleshoot-ca-certificate-upload"></a>CA-Certificate 업로드 문제 해결  

처음으로 센서 또는 온-프레미스 관리 콘솔에 로그인을 시도 하는 관리자는 인증서가 제대로 생성 되지 않았거나 잘못 된 경우 CA 서명 인증서를 업로드할 수 없습니다. 인증서 업로드가 실패 하면 오류 메시지 중 하나 이상이 표시 됩니다.

| **인증서 유효성 검사 오류** | **권장** |
|--|--|
| 암호가 키와 일치 하지 않습니다. | 올바른 암호를 입력 했는지 확인 합니다. 문제가 계속 되 면 올바른 암호를 사용 하 여 인증서를 다시 만드십시오. |
| 신뢰 체인의 유효성을 검사할 수 없습니다. 제공 된 인증서와 루트 CA가 일치 하지 않습니다.  | . Pem 파일은 .crt 파일과 관련이 있는지 확인 합니다. 문제가 계속 되 면 올바른 신뢰 체인 (pem 파일에 정의 됨)을 사용 하 여 인증서를 다시 만드십시오. |
| 이 SSL 인증서가 만료 되어 올바른 것으로 간주 되지 않습니다.  | 유효한 날짜로 새 인증서를 만듭니다.|
| 이 SSL 인증서가 만료 되어 올바른 것으로 간주 되지 않습니다.  | 유효한 날짜로 새 인증서를 만듭니다.|
|이 인증서는 CRL에 의해 해지 되었으며 보안 연결을 위해 신뢰할 수 없습니다. | 새 해지 되지 않은 인증서를 만듭니다. |
|CRL (인증서 해지 목록) 위치에 연결할 수 없습니다. 이 어플라이언스에서 URL에 액세스할 수 있는지 확인 합니다. | 네트워크 구성을 통해 기기가 인증서에 정의 된 CRL 서버에 연결할 수 있는지 확인 합니다. 직접 연결을 설정할 때 제한이 있는 경우 프록시 서버를 사용할 수 있습니다.  
|인증서 유효성 검사 실패  | 이는 어플라이언스의 일반 오류를 나타냅니다. [Microsoft 지원](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099)에 문의 하세요.|

### <a name="troubleshoot-file-conversions"></a>파일 변환 문제 해결  

파일 변환에서 유효한 인증서를 만들지 못할 수 있습니다. 예를 들어 파일 구조는 정확 하지 않을 수 있습니다.

변환이 실패 하는 경우:  

- [기존 파일을 지원 되는 파일로 변환](#convert-existing-files-to-supported-files)에서 설명 하는 변환 명령을 사용 합니다.
- 파일 매개 변수가 정확한 지 확인 합니다. 자세한 내용은 [파일 형식 요구 사항](#file-type-requirements) 및  [인증서 파일 매개 변수 요구 사항](#certificate-file-parameter-requirements) 을 참조 하세요.  
- 인증서 리드를 참조 하세요.