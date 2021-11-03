---
title: Azure HPC Cache에서 확장 그룹 사용
description: Azure HPC Cache에서 클라이언트가 스토리지 대상에 액세스할 수 있도록 디렉터리 서비스를 구성하는 방법
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: femila
ms.openlocfilehash: 21bf14ad46ec1625e4c8eb5efed1002da7ed159e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019916"
---
# <a name="configure-directory-services"></a>디렉터리 서비스 구성

**디렉터리 서비스** 설정을 사용하면 Azure HPC Cache가 외부 소스를 사용하여 백 엔드 스토리지에 액세스하는 사용자를 인증할 수 있습니다.

NFS 스토리지 대상 및 16개가 넘는 그룹의 구성원인 클라이언트가 워크플로에 포함된 경우 **확장 그룹** 을 사용해야 할 수 있습니다.

확장 그룹을 사용하도록 설정하는 단추를 클릭한 후 Azure HPC Cache가 사용자 및 그룹 자격 증명을 가져오는 데 사용할 소스를 선택해야 합니다.

* [Active Directory](#configure-active-directory) - 외부 Active Directory 서버에서 자격 증명을 가져옵니다. 해당 작업에는 Azure Active Directory를 사용할 수 없습니다.
* [플랫 파일](#configure-file-download) - 네트워크 위치에서 `/etc/group` 파일과 `/etc/passwd` 파일을 다운로드합니다.
* [LDAP](#configure-ldap) - LDAP(경량 디렉터리 액세스 프로토콜) 호환 소스에서 자격 증명을 가져옵니다.

> [!NOTE]
> 캐시가 보안 서브넷 내에서 그룹 정보 원본에 액세스할 수 있는지 확인합니다.<!-- + details/examples -->

**다운로드된 사용자 이름** 필드에는 가장 최근에 다운로드한 그룹 정보의 상태가 표시됩니다.

![확장 그룹에 예 옵션이 선택되어 있고, 다운로드 원본 열기라는 레이블이 붙은 드롭다운 메뉴가 나와 있는 포털의 디렉터리 서비스 페이지의 설정 페이지 스크린샷.](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Active Directory 구성

이 섹션에서는 캐시를 설정하여 외부 AD(Active Directory) 서버에서 사용자 및 그룹 자격 증명을 가져오는 방법을 설명합니다.

**Active 디렉터리 세부 정보** 아래에 다음 값을 입력합니다.

* **기본 DNS** - 캐시가 AD 도메인 이름을 확인하는 데 사용할 수 있는 도메인 이름 서버의 IP 주소를 지정합니다.

* **보조 DNS**(선택 사항) - 주 서버를 사용할 수 없는 경우 사용할 이름 서버의 주소를 입력합니다.

* **AD DNS 도메인 이름** - 캐시가 자격 증명을 얻기 위해 가입할 AD 서버의 정규화된 도메인 이름을 제공합니다.

* **캐시 서버 이름(컴퓨터 계정)** - HPC 캐시가 AD 도메인에 조인할 때 할당받게 될 이름을 설정합니다. 해당 캐시로 인식하기 쉬운 이름을 지정하세요. 이름은 최대 15자까지 지정할 수 있으며 대문자 또는 소문자, 숫자, 하이픈(-)을 포함할 수 있습니다.

**자격 증명** 섹션에서 Azure HPC Cache가 AD 서버에 액세스하는 데 사용할 수 있는 AD 관리자 사용자 이름과 암호를 제공합니다. 이 정보는 저장될 때 암호화되며 쿼리될 수 없습니다.

페이지 위쪽 단추를 클릭하여 설정을 저장합니다.

![Active Directory 값이 채워진 다운로드 세부 정보 섹션의 스크린샷](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>구성 파일 다운로드

해당 값은 사용자 및 그룹 정보를 사용하여 파일을 다운로드하려는 경우에 필요합니다. 파일은 표준 Linux/UNIX `/etc/group` 및 `/etc/passwrd` 형식이어야 합니다.

* **사용자 파일 URI** - `/etc/passwrd` 파일의 전체 URI를 입력합니다.
* **그룹 파일 URI** - `/etc/group` 파일의 전체 URI를 입력합니다.

![플랫 파일 다운로드를 위한 다운로드 세부 정보 섹션의 스크린샷](media/group-download-details-file.png)

## <a name="configure-ldap"></a>LDAP 구성

비AD LDAP 원본을 사용하여 사용자 및 그룹 자격 증명을 가져오려면 해당 값을 입력합니다. 해당 값에 대해 도움이 필요한 경우 LDAP 관리자에게 문의하세요.

* **LDAP 서버** - 사용할 LDAP 서버의 IP 주소 또는 정규화된 도메인 이름을 입력합니다. <!-- only one, not up to 3 -->

* **LDAP 기본 DN** - LDAP 도메인의 기본 고유 이름을 DN 형식으로 지정합니다. 기본 DN을 모르는 경우 LDAP 관리자에게 문의하세요.

LDAP를 작동시키려면 서버 및 기본 DN만 설정되어 있으면 되지만, 추가 옵션을 사용하면 더 안전하게 연결할 수 있습니다.

![디렉터리 서비스 페이지 내 설정 페이지의 LDAP 구성 영역 스크린샷](media/group-download-details-ldap.png)

**보안 액세스** 섹션에서 LDAP 연결에 대해 암호화 및 인증서 유효성 검사를 사용하도록 설정할 수 있습니다. **예** 를 클릭하여 암호화를 사용하면 다음과 같은 옵션을 사용할 수 있습니다.

* **인증서 유효성 검사** - 이 옵션을 설정하면 아래 URI 필드의 인증 기관에 LDAP 서버의 인증서가 확인됩니다.

* **CA 인증서 URI** - 신뢰할 수 있는 인증서의 경로를 지정합니다. 이는 CA에서 유효성을 검사한 인증서 또는 자체 서명된 인증서일 수 있습니다. 이 필드는 외부에서 유효성을 검사한 인증서 설정을 사용하는 데 필요합니다.

* **인증서 자동 다운로드** - 해당 설정을 제출하는 즉시 인증서를 다운로드하려는 경우 **예** 를 선택합니다.

LDAP 보안에 고정적인 자격 증명을 사용하려는 경우 **자격 증명** 섹션을 채웁니다. 이 정보는 저장될 때 암호화되며 쿼리될 수 없습니다.

* **바인딩 DN** - LDAP 서버에 인증하는 데 사용할 바인딩 고유 이름을 입력합니다. (DN 형식을 사용합니다.)
* **바인딩 암호** - 바인딩 DN에 대한 암호를 제공합니다.

## <a name="next-steps"></a>다음 단계

* [Azure HPC Cache 탑재](hpc-cache-mount.md)에서 클라이언트 액세스에 대해 자세히 알아보세요.
* 자격 증명이 올바르게 다운로드되지 않으면 관리자에게 자격 증명 원본을 문의하세요. 필요한 경우 [지원 티켓](hpc-cache-support-ticket.md)을 엽니다.
