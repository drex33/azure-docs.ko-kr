---
title: 가상 네트워크를 사용하여 Azure Functions를 구성하는 방법
description: Azure Functions에 대해 특정 가상 네트워킹 작업을 수행하는 방법을 보여 주는 문서입니다.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: 1e04a43a7a9b2eb8b3d29be6ced0d92d8bcc8308
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202925"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>가상 네트워크를 사용하여 Azure Functions를 구성하는 방법

이 문서에서는 함수 앱이 가상 네트워크에 연결되어 실행되도록 함수 앱 구성 관련 작업을 수행하는 방법을 보여줍니다. 저장소 계정을 보호하는 방법에 대한 자세한 자습서는 [Virtual Network에 연결 자습서](functions-create-vnet.md)를 참조하세요. Azure Functions 및 네트워킹에 대해 자세히 알아보려면 [Azure Functions 네트워킹 옵션](functions-networking-options.md)을 참조하세요.

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>가상 네트워크에 대한 스토리지 계정 제한 

함수 앱을 만들 때 Blob, 큐 및 Table Storage을 지원하는 범용 Azure Storage 계정을 만들거나 연결해야 합니다. 이 스토리지 계정은 서비스 엔드포인트 또는 프라이빗 엔드포인트로 보호되는 계정으로 바꿀 수 있습니다. 프라이빗 엔드포인트로 저장소 계정을 구성하면 함수 앱에 대한 공용 액세스가 자동으로 사용하지 않도록 설정되며, 가상 네트워크를 통해서만 함수 앱에 액세스할 수 있습니다. 

> [!NOTE]  
> 현재 이 기능은 전용(App Service) 플랜으로 모든 Windows 가상 네트워크 지원 SKU에서 작동하며 Windows 탄력적 프리미엄 플랜에서 작동합니다. Linux 가상 네트워크 지원 SKU에 대한 프라이빗 DNS에서도 지원됩니다. Linux 플랜에 대한 사용량과 사용자 지정 DNS는 지원되지 않습니다. 

프라이빗 네트워크로 제한된 스토리지 계정을 사용하여 함수를 설정하려면 다음을 수행합니다.

1. 서비스 엔드포인트를 사용하도록 설정되지 않은 스토리지 계정으로 함수를 만듭니다.

1. 가상 네트워크에 연결되도록 함수를 구성합니다.

1. 다른 스토리지 계정을 만들거나 구성합니다.  이 계정은 서비스 엔드포인트로 보호되고 함수에 연결되는 스토리지 계정입니다.

1. 보안 스토리지 계정에 [파일 공유를 만듭니다](../storage/files/storage-how-to-create-file-share.md#create-a-file-share).

1. 스토리지 계정에 서비스 엔드포인트 또는 프라이빗 엔드포인트를 사용하도록 설정합니다.  
    * 프라이빗 엔드포인트 연결을 사용 중인 경우 스토리지 계정에 `file` 및 `blob` 하위 리소스에 대한 프라이빗 엔드포인트가 필요합니다.  Durable Functions와 같은 특정 기능을 사용 중인 경우 프라이빗 엔드포인트 연결을 통해 액세스할 수 있는 `queue` 및 `table`도 필요합니다.
    * 서비스 엔드포인트를 사용 중인 경우 스토리지 계정에 함수 앱 전용 서브넷을 사용하도록 설정합니다.

1. 파일 및 Blob 콘텐츠를 함수 앱 스토리지 계정에서 보안 스토리지 계정 및 파일 공유로 복사합니다.

1. 이 스토리지 계정에 대한 연결 문자열을 복사합니다.

1. 함수 앱에 대한 **구성** 아래에 있는 **애플리케이션 설정** 을 다음으로 업데이트합니다.

    | 설정 이름 | 값 | 의견 |
    |----|----|----|
    | `AzureWebJobsStorage`| 스토리지 연결 문자열 | 보안 스토리지 계정에 대한 연결 문자열입니다. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  스토리지 연결 문자열 | 보안 스토리지 계정에 대한 연결 문자열입니다. |
    | `WEBSITE_CONTENTSHARE` | 파일 공유 | 보안 스토리지 계정에 생성된 파일 공유(여기에 프로젝트 배포 파일이 상주)의 이름입니다. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | 새 설정 |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | 모든 아웃바운드 트래픽이 가상 네트워크를 통과하도록 강제합니다. 스토리지 계정에서 프라이빗 엔드포인트 연결을 사용 중인 경우에 필요합니다. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | 앱에서 사용하는 DNS 서버입니다. 스토리지 계정에서 프라이빗 엔드포인트 연결을 사용 중인 경우에 필요합니다. |

1. **저장** 을 선택하여 애플리케이션 설정을 저장합니다. 앱 설정을 변경하면 앱이 다시 시작됩니다.  

함수 앱이 다시 시작되면 이제 보안 스토리지 계정에 연결됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 네트워킹 옵션](functions-networking-options.md)

