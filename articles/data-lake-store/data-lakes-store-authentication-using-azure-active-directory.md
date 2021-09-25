---
title: 인증 - Data Lake Storage Gen1 및 Azure AD
description: Azure Active Directory를 사용하여 Azure Data Lake Storage Gen1에 인증하는 방법을 알아봅니다.
author: normesta
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: 2a83393f00234b3a8d1a2de066e41d0d6070bac5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571126"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Azure Data Lake Storage Gen1에 인증

Azure Data Lake Storage Gen1은 인증을 위해 Azure Active Directory를 사용합니다. Azure Data Lake Storage Gen1과 함께 작동하는 애플리케이션을 제작하기 전에 Azure AD(Azure Active Directory)를 사용하여 애플리케이션을 인증하는 방법을 결정해야 합니다.

## <a name="authentication-options"></a>인증 옵션

* **최종 사용자 인증** - 최종 사용자의 Azure 자격 증명은 Data Lake Storage Gen1에 인증하는 데 사용됩니다. Data Lake Storage Gen1과 함께 작동하도록 만드는 애플리케이션은 이러한 사용자 자격 증명을 요구합니다. 결과적으로 이 인증 메커니즘은 *대화형* 이고 애플리케이션은 로그인된 사용자의 컨텍스트에서 실행됩니다. 자세한 내용 및 지침은 [Data Lake Storage Gen1에 대한 최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)을 참조하세요.

* **서비스 간 인증** - 애플리케이션이 직접 Data Lake Storage Gen1에 인증하게 하려면 이 옵션을 사용합니다. 이 경우 Azure AD(Active Directory) 애플리케이션 만들고 Azure AD 애플리케이션의 키를 사용하여 Data Lake Storage Gen1에 인증합니다. 결과적으로 이 인증 메커니즘은 *비 대화형* 입니다. 자세한 내용 및 지침은 [Data Lake Storage Gen1에 대한 서비스 간 인증](data-lake-store-service-to-service-authenticate-using-active-directory.md)을 참조하세요.

다음 표에는 Data Lake Storage Gen1에서 최종 사용자 및 서비스 간 인증 메커니즘을 지원하는 방식이 정리되어 있습니다. 표를 읽는 방법은 다음과 같습니다.

* ✔ * 기호는 인증 옵션이 지원된다는 의미이며 인증 옵션 사용 방법을 보여주는 문서와 연결됩니다. 
* ✔ 기호는 인증 옵션이 지원된다는 의미입니다. 
* 빈 셀은 인증 옵션이 지원되지 않는다는 의미입니다.


|다음에서 이 인증 옵션 사용...                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|최종 사용자(MFA 없이**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)** (사용되지 않음)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|최종 사용자(MFA 사용)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|서비스 간(클라이언트 키 사용)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|서비스 간(클라이언트 인증서 사용) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* <b>✔\*</b> 기호를 클릭합니다. 링크입니다.</i><br>
<i>** MFA는 Multi-Factor Authentication을 의미</i>

인증에 Azure Active Directory를 사용하는 방법에 대한 자세한 내용은 [Azure Active Directory의 인증 시나리오](../active-directory/develop/authentication-vs-authorization.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)
* [서비스 간 인증](data-lake-store-service-to-service-authenticate-using-active-directory.md)