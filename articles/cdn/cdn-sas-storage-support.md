---
title: SAS로 Azure CDN 사용 | Microsoft Docs
description: Azure CDN은 프라이빗 스토리지 컨테이너에 제한된 액세스 권한을 부여하기 위한 SAS(공유 액세스 서명)의 사용을 지원합니다.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: dee2d50d247ffc2afdedd28bfe2467ee3854f6a1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071480"
---
# <a name="using-azure-cdn-with-sas"></a>SAS로 Azure CDN 사용

캐시 콘텐츠를 사용하도록 Azure CDN(Content Delivery Network)에 대한 스토리지 계정을 설정하는 경우 기본적으로 스토리지 컨테이너에 대한 URL을 알고 있는 사람은 업로드한 파일에 액세스할 수 있습니다. 스토리지 계정의 파일을 보호하기 위해 공용에서 프라이빗으로 스토리지 컨테이너의 액세스를 설정할 수 있습니다. 그러나 그렇게 하는 경우 아무도 파일에 액세스할 수 없습니다. 

프라이빗 스토리지 컨테이너에 제한된 액세스를 부여하려는 경우 Azure 스토리지 계정의 SAS(공유 액세스 서명) 기능을 사용할 수 있습니다. SAS는 계정 키를 노출하지 않고 Azure Storage 리소스에 대한 제한된 액세스 권한을 부여하는 URI입니다. 스토리지 계정 키로 신뢰하지 않지만 특정 스토리지 계정 리소스에 대한 액세스를 위임하려는 클라이언트에 SAS를 제공할 수 있습니다. 이러한 클라이언트에 공유 액세스 서명 URI를 배포하여 지정된 기간 동안 리소스에 액세스하도록 할 수 있습니다.
 
SAS를 사용하여 Blob의 액세스 권한에 대해 시작 및 만료 시간, 사용 권한(읽기/쓰기), IP 범위 등과 같은 다양한 매개 변수를 정의할 수 있습니다. 이 문서에서는 Azure CDN과 함께 SAS를 사용하는 방법에 대해 설명합니다. SAS를 만드는 방법과 해당 매개 변수 옵션 등 SAS에 대한 자세한 내용은 [SAS(공유 액세스 서명) 사용](../storage/common/storage-sas-overview.md)을 참조하세요.

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>스토리지 SAS를 사용하도록 Azure CDN 설정
Azure CDN에서 SAS를 사용하는 경우 다음 세 가지 옵션을 사용하는 것이 좋습니다. 모든 옵션에서는 작동하는 SAS를 이미 만들었다고 가정합니다(필수 조건 참조). 
 
### <a name="prerequisites"></a>필수 구성 요소
시작하려면 스토리지 계정을 만든 다음, 자산에 대한 SAS를 생성합니다. 서비스 SAS, 계정 SAS 등 두 가지 유형의 저장된 액세스 서명을 생성할 수 있습니다. 자세한 내용은 [공유 액세스 서명 유형](../storage/common/storage-sas-overview.md#types-of-shared-access-signatures)을 참조하세요.

SAS 토큰을 생성한 후 URL에 `?sv=<SAS token>`을 추가하여 Blob Storage 파일에 액세스할 수 있습니다. 이 URL의 형식은 다음과 같습니다. 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
예를 들면 다음과 같습니다.

```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

매개 변수 설정에 대한 자세한 내용은 [SAS 매개 변수 고려 사항](#sas-parameter-considerations) 및 [공유 액세스 서명 매개 변수](../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works)를 참조하세요.

![CDN SAS 설정](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>옵션 1: Azure CDN에서 Blob Storage로 통과된 SAS 사용

이 옵션은 Azure CDN에서 원본 서버로 전달된 단일 SAS 토큰만 사용하는 가장 단순한 방법입니다.
 
1. 엔드포인트를 선택하고 **캐싱 규칙** 을 선택한 다음, **쿼리 문자열 캐싱** 목록에서 **각 고유한 URL 캐시** 를 선택합니다.

    ![CDN 캐싱 규칙](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. 스토리지 계정에서 SAS를 설정한 후 CDN 엔드포인트 및 원본 서버 URL과 함께 SAS 토큰을 사용하여 파일에 액세스해야 합니다. 
   
   결과 CDN 엔드포인트 URL의 형식은 다음과 같습니다. `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   예를 들면 다음과 같습니다.   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. 캐싱 규칙을 사용하거나 원본 서버에서 `Cache-Control` 헤더를 추가하여 캐시 지속 기간을 미세 조정합니다. Azure CDN은 SAS 토큰을 일반 쿼리 문자열로 취급하므로 SAS 만료 시간 또는 이전에 만료되는 캐싱 기간을 설정하는 것이 좋습니다. 그러지 않으면 SAS가 활성 상태인 기간보다 오랫동안 파일이 캐시되는 경우 SAS 만료 시간이 지난 후에도 Azure CDN 원본 서버에서 파일에 액세스할 수 있습니다. 이러한 상황이 발생하는 경우 캐시된 파일에 액세스할 수 없도록 하려면 파일에서 제거 작업을 수행하여 캐시에서 파일을 지워야 합니다. Azure CDN에서 캐시 기간을 설정하는 방법은 [캐싱 규칙으로 Azure CDN 캐싱 동작 제어](cdn-caching-rules.md)를 참조하세요.

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>옵션 2: 다시 쓰기 규칙을 사용하여 숨긴 CDN SAS 토큰
 
이 옵션은 **Verizon의 Azure CDN Premium** 프로필에서만 사용할 수 있습니다. 이 옵션을 사용하여 원본 서버에서 Blob Storage를 보호할 수 있습니다. 파일에 대한 특정 액세스 제한이 필요하지 않지만 사용자가 스토리지 원본에 직접 액세스하지 못하게 하여 Azure CDN 오프로드 시간을 개선하려는 경우 이 옵션을 사용할 수 있습니다. 원본 서버의 지정된 컨테이너에서 파일에 액세스하는 모든 사용자에게 알려지지 않은 SAS 토큰이 필요합니다. 그러나 URL 다시 쓰기 규칙으로 인해 SAS 토큰은 CDN 엔드포인트에 필요하지 않습니다.
 
1. [규칙 엔진](./cdn-verizon-premium-rules-engine.md)을 사용하여 URL 다시 쓰기 규칙을 만듭니다. 새 규칙을 전파하는 데 최대 4시간이 소요됩니다.

   ![CDN 관리 단추](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN 규칙 엔진 단추](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   다음 샘플 URL 다시 쓰기 규칙은 캡처링 그룹 및 *sasstoragedemo* 라는 엔드포인트와 함께 정규식 패턴을 사용합니다.
   
   원본:   
   `(container1/.*)`


   대상:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL 재작성 규칙 - 왼쪽](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN URL 재작성 규칙 - 오른쪽](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. 새 규칙이 활성화되면 URL에서 SAS 토큰을 사용하는지 여부에 관계 없이 누구나 CDN 엔드포인트에 지정된 컨테이너에서 파일에 액세스할 수 있습니다. 형식은 다음과 같습니다. `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   예를 들면 다음과 같습니다.   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. 캐싱 규칙을 사용하거나 원본 서버에서 `Cache-Control` 헤더를 추가하여 캐시 지속 기간을 미세 조정합니다. Azure CDN은 SAS 토큰을 일반 쿼리 문자열로 취급하므로 SAS 만료 시간 또는 이전에 만료되는 캐싱 기간을 설정하는 것이 좋습니다. 그러지 않으면 SAS가 활성 상태인 기간보다 오랫동안 파일이 캐시되는 경우 SAS 만료 시간이 지난 후에도 Azure CDN 엔드포인트에서 파일에 액세스할 수 있습니다. 이러한 상황이 발생하는 경우 캐시된 파일에 액세스할 수 없도록 하려면 파일에서 제거 작업을 수행하여 캐시에서 파일을 지워야 합니다. Azure CDN에서 캐시 기간을 설정하는 방법은 [캐싱 규칙으로 Azure CDN 캐싱 동작 제어](cdn-caching-rules.md)를 참조하세요.

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>옵션 3: 다시 쓰기 규칙과 함께 CDN 보안 토큰 인증 사용

Azure CDN 보안 토큰 인증을 사용하려면 **Verizon의 Azure CDN Premium** 프로필이 있어야 합니다. 이 옵션은 가장 안전하며 사용자 지정이 가능합니다. 클라이언트 액세스는 보안 토큰에 설정된 보안 매개 변수를 기반으로 합니다. 보안 토큰을 생성하고 설정하면 모든 CDN 엔드포인트 URL에서 필요합니다. 그러나 URL 다시 쓰기 규칙으로 인해 SAS 토큰은 CDN 엔드포인트에 필요하지 않습니다. SAS 토큰이 나중에 잘못되는 경우 Azure CDN은 원본 서버에서 콘텐츠의 유효성을 더 이상 다시 검사할 수 없습니다.

1. [Azure CDN 보안 토큰을 만들고](./cdn-token-auth.md#setting-up-token-authentication) CDN 엔드포인트에 대한 규칙 엔진과 사용자가 파일에 액세스할 수 있는 경로를 사용하여 이 토큰을 활성화합니다.

   보안 토큰 엔드포인트 URL의 형식은 다음과 같습니다.   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   예를 들면 다음과 같습니다.   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   보안 토큰 인증에 대한 매개 변수 옵션은 SAS 토큰에 대한 매개 변수 옵션과 다릅니다. 보안 토큰을 만들 때 만료 시간을 사용하는 경우 SAS 토큰의 만료 시간과 같은 값으로 설정해야 합니다. 이렇게 하면 만료 시간을 예측할 수 있게 됩니다. 
 
2. [규칙 엔진](./cdn-verizon-premium-rules-engine.md)으로 URL 다시 쓰기 규칙을 만들어 컨테이너의 모든 Blob에 대한 SAS 토큰 액세스를 사용하도록 설정합니다. 새 규칙을 전파하는 데 최대 4시간이 소요됩니다.

   다음 샘플 URL 다시 쓰기 규칙은 캡처링 그룹 및 *sasstoragedemo* 라는 엔드포인트와 함께 정규식 패턴을 사용합니다.
   
   원본:   
   `(container1/.*)`
   
   대상:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL 재작성 규칙 - 왼쪽](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN URL 재작성 규칙 - 오른쪽](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. SAS를 갱신하는 경우 새 SAS 토큰을 사용하여 URL 다시 쓰기 규칙을 업데이트해야 합니다. 

## <a name="sas-parameter-considerations"></a>SAS 매개 변수 고려 사항

SAS 매개 변수는 Azure CDN에 표시되지 않으므로 Azure CDN은 이 매개 변수를 기반으로 배달 동작을 변경할 수 없습니다. 정의된 매개 변수 제한은 클라이언트가 Azure CDN에 하는 요청이 아니라 Azure CDN이 원본 서버에 하는 요청에만 적용됩니다. SAS 매개 변수를 설정할 때 이 구분을 고려해야 합니다. 이러한 고급 기능이 필요하고 [옵션 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)을 사용하는 경우 Azure CDN 보안 토큰에 대해 적절한 제한 사항을 설정합니다.

| SAS 매개 변수 이름 | Description |
| --- | --- |
| 시작 | Azure CDN이 Blob 파일에 액세스하기 시작할 수 있는 시간입니다. 클록 스큐(clock skew)(신호가 구성 요소에 따라 다른 시간에 도착하는 경우)로 인해 자산을 즉시 사용할 수 있게 하려면 15분 이른 시간을 선택합니다. |
| 끝 | Azure CDN이 Blob 파일에 더 이상 액세스할 수 없는 시간입니다. Azure CDN에서 이전에 캐시된 파일은 계속 액세스할 수 있습니다. 파일 만료 시간을 제어하려면 Azure CDN 보안 토큰에 적절한 만료 시간을 설정하거나 자산을 제거합니다. |
| 허용된 IP 주소 | (선택 사항) **Verizon의 Azure CDN** 을 사용하는 경우 이 매개 변수를 you can set this parameter to the ranges defined in [Azure CDN from Verizon Edge Server IP Ranges](./cdn-pop-list-api.md)(Verizon의 Azure CDN 에지 서버 IP 범위)에 정의된 범위로 설정할 수 있습니다. **Akamai의 Azure CDN** 을 사용하는 경우에는 IP 주소가 고정 주소가 아니므로 IP 범위 매개 변수를 설정할 수 없습니다.|
| 허용되는 프로토콜 | 계정 SAS를 사용하여 요청하는 경우 허용되는 프로토콜입니다. HTTPS 설정을 사용하는 것이 좋습니다.|

## <a name="next-steps"></a>다음 단계

SAS에 대한 자세한 내용은 다음 문서를 참조하세요.
- [SAS(공유 액세스 서명) 사용](../storage/common/storage-sas-overview.md)
- [공유 액세스 서명 2부: Blob Storage를 통해 SAS 만들기 및 사용](../storage/common/storage-sas-overview.md)

토큰 인증을 설정하는 방법에 대한 자세한 내용은 [토큰 인증을 사용하여 Azure Content Delivery Network 자산 보안 유지](./cdn-token-auth.md)를 참조하세요.