---
title: azcopy login | Microsoft Docs
description: 이 문서에서는 azcopy login 명령에 관한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f5c0532f194aad27f99752c9c5312f30dfe0a21d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600212"
---
# <a name="azcopy-login"></a>azcopy login

Azure Active Directory에 로그인하여 Azure Storage 리소스에 액세스합니다.

## <a name="synopsis"></a>개요

Azure Active Directory에 로그인하여 Azure Storage 리소스에 액세스합니다.

Azure Storage 계정에 대한 권한을 부여하려면 스토리지 계정, 부모 리소스 그룹 또는 부모 구독의 컨텍스트에서 사용자 계정에 **스토리지 Blob 데이터 참가자** 역할을 할당해야 합니다.

이 명령은 OS 기본 제공 메커니즘을 사용하여 현재 사용자에 대한 암호화된 로그인 정보를 캐시합니다.

> [!IMPORTANT]
> 명령줄을 사용하여 환경 변수를 설정하는 경우 명령줄 기록에서 해당 변수를 읽을 수 있습니다. 명령줄 기록에서 자격 증명을 포함하는 변수를 지우는 것이 좋습니다. 기록에 변수가 나타나지 않게 하려면 스크립트를 사용하여 사용자에게 자격 증명을 요청하는 메시지를 표시하고 환경 변수를 설정하면 됩니다.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [자습서: AzCopy를 사용하여 클라우드 스토리지로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy 및 Blob Storage를 사용하여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)

## <a name="examples"></a>예

일반으로 설정된 기본 AAD 테넌트 ID로 대화형으로 로그인합니다.

```azcopy
azcopy login
```

지정된 테넌트 ID로 대화형으로 로그인합니다.

```azcopy
azcopy login --tenant-id "[TenantID]"
```

VM(가상 머신)의 시스템 할당 ID를 사용하여 로그인합니다.

```azcopy
azcopy login --identity
```

VM의 사용자 할당 ID 및 서비스 ID의 클라이언트 ID를 사용하여 로그인합니다.

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

VM의 사용자 할당 ID 및 서비스 ID의 개체 ID를 사용하여 로그인합니다.

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

VM의 사용자 할당 ID 및 서비스 ID의 리소스 ID를 사용하여 로그인합니다.

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

클라이언트 암호를 사용하여 서비스 주체로 로그인: 환경 변수 AZCOPY_SPA_CLIENT_SECRET을 AZCOPY_SPA_CLIENT_SECRET을 비밀 기반 서비스 주체 인증에 대한 클라이언트 암호로 설정합니다.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

인증서 및 암호를 사용하여 서비스 주체로 로그인합니다.

환경 변수 AZCOPY_SPA_CERT_PASSWORD를 인증서 기반 서비스 주체 인증에 대한 인증서의 암호로 설정합니다.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

`/path/to/my/cert`를 PEM 또는 PKCS12 파일의 경로로 처리합니다. AzCopy는 인증서를 가져오기 위해 시스템 인증서 저장소에 도달하지 않습니다.

`--certificate-path`는 인증서 기반 서비스 주체 인증을 수행할 때 반드시 필요합니다.

## <a name="options"></a>옵션

**--aad-endpoint** 문자열    사용할 Azure Active Directory 엔드포인트입니다. 기본값(https://login.microsoftonline.com) )은 글로벌 Azure 클라우드에 대해 올바릅니다. 국가별 클라우드에서 인증할 때 이 매개 변수를 설정합니다. 관리되는 서비스 ID에는 필요하지 않습니다.

**--application-id** 문자열  사용자 할당 ID의 애플리케이션 ID입니다. 서비스 주체 인증에 필요합니다.

**--certificate-path** 문자열  SPN 인증을 위한 인증서 경로입니다. 인증서 기반 서비스 주체 인증에 필요합니다.

**--help**   `azcopy login` 명령에 대한 도움말입니다.

**--identity**   가상 머신의 ID를 사용하는 로그인으로 MSI(관리 서비스 ID)라고도 합니다.

**--identity-client-id** 문자열  사용자 할당 ID의 클라이언트 ID입니다.

**--identity-object-id** 문자열  사용자 할당 ID의 개체 ID입니다.

**--identity-resource-id** 문자열  사용자 할당 ID의 리소스 ID입니다.

**--service-principal**   인증서 또는 암호를 사용하는 SPN(서비스 주체 이름)을 통한 로그인입니다. 클라이언트 암호 또는 인증서 암호는 해당 환경 변수에 배치되어야 합니다. 환경 변수의 이름 및 설명을 보려면 AzCopy env를 입력합니다.

**--tenant-id** 문자열   OAuth 디바이스 대화형 로그인에 사용하는 Azure Active Directory 테넌트 ID입니다.

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속된 옵션

|옵션|Description|
|---|---|
|--cap-mbps float|전송 속도(초당 메가비트)의 한도를 지정합니다. 시간 경과별 처리량은 한도와 약간 다를 수 있습니다. 이 옵션이 0으로 설정되거나 생략되는 경우 처리량이 제한되지 않습니다.|
|--output-type string|명령 출력의 형식을 지정합니다. text, json 등을 선택할 수 있습니다. 기본값은 “text”입니다.|
|--trusted-microsoft-suffixes string   |Azure Active Directory 로그인 토큰이 전송될 수 있는 추가 도메인 접미사를 지정합니다.  기본값은 ‘ *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net’입니다. 여기에 나열된 항목은 모두 기본값에 추가됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치해야 합니다. 여러 항목은 세미콜론으로 구분합니다.|

## <a name="see-also"></a>참조

- [azcopy](storage-ref-azcopy.md)
