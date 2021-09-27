---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: 10de63c842eeb60f5e7e725457c88961581370f0
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129063807"
---
>[!NOTE]
>고정되지 않은 리소스의 경우 지원 티켓을 열어 할당량 증가를 요청하세요. 더 높은 한도를 얻기 위해 추가 Azure Media Services 계정을 만들지 마세요.

| 리소스 | 제한 | 
| --- | --- | 
| 단일 구독의 Azure Media Services 계정 | 25(고정) | 
| Media Services 계정 작업당 작업 | 50,000<sup>2</sup> |
| 작업당 연결된 태스크 | 30(고정) |
| Media Services 계정당 자산 | 1,000,000|
| 태스크당 자산 | 50 |
| 작업당 자산 | 100 |
| 한번에 자산과 연결된 고유 로케이터 | 5<sup>4</sup> |
| Media Services 계정당 라이브 채널 |5<sup>5</sup>|
| 채널당 중지 상태인 프로그램 |50|
| 채널당 실행 상태인 프로그램 |3|
| Media Services 계정당 중지되거나 실행되는 스트리밍 엔드포인트|2|
| 스트리밍 엔드포인트당 스트리밍 단위 |10 |
| Storage 계정 | 100<sup>6(고정)</sup> |
| 정책 | 1,000,000<sup>7</sup> |
| 파일 크기| 일부 시나리오에서는 Media Services 처리에 지원되는 최대 파일 크기에 제한이 있습니다. <sup>8</sup> |

<sup>1</sup>예를 들어 S2에서 S1으로 형식을 변경하면 최대 예약 단위 제한이 다시 설정됩니다.

<sup>2</sup>이 번호에는 대기 중, 완료, 활성 및 취소된 작업이 포함됩니다. 삭제된 작업은 포함되지 않습니다. **IJob.Delete** 또는 **DELETE** HTTP 요청을 사용하여 이전 작업을 삭제할 수 있습니다.

2017년 4월 1일부터 사용자 계정에 있는 90일이 지난 작업 레코드는 연결된 태스크 레코드와 함께 자동으로 삭제됩니다. 레코드의 총 수가 최고 할당량 미만인 경우에도 자동 삭제됩니다. 작업 및 태스크 정보를 보관하려면 [Media Services .NET SDK를 사용하여 자산 관리](../articles/media-services/previous/media-services-dotnet-manage-entities.md)에 설명된 코드를 사용합니다.

<sup>3</sup>작업 엔터티 목록을 요청할 때 요청당 최대 1,000개 작업이 반환됩니다. 제출된 모든 작업을 추적하려면 [OData 시스템 쿼리 옵션](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))에 설명된 대로 top 또는 skip 쿼리를 사용합니다.

<sup>4</sup>로케이터는 사용자별 액세스 제어를 관리하도록 설계되지 않았습니다. 개별 사용자에게 서로 다른 액세스 권한을 부여하려면 DRM(디지털 권한 관리) 솔루션을 사용합니다. 자세한 내용은 [Azure Media Services를 사용하여 콘텐츠 보호](../articles/media-services/latest/drm-content-protection-concept.md)를 참조하세요.

<sup>5</sup> 선택한 스트리밍 사용 사례 및 지역 데이터 센터에 따라 AMS는 Media Services 계정당 5개 이상의 라이브 채널을 수용할 수 있습니다. 계정 할당량을 늘리려면 지원 요청을 제출하세요.

<sup>6</sup> 스토리지 계정은 동일한 Azure 구독에 있어야 합니다.

<sup>7</sup> 다양한 Media Services 정책에 대해 1,000,000개의 정책으로 제한됩니다. 로케이터 정책 또는 ContentKeyAuthorizationPolicy에 대한 예가 있습니다. 

>[!NOTE]
> 항상 동일한 요일과 액세스 권한을 사용하는 경우 동일한 정책 ID를 사용합니다. 자세한 내용과 예제는 [Media Services .NET SDK를 사용하여 자산 관리](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)를 참조하세요.

<sup>8</sup> 단일 Blob에 지원되는 최대 크기는 현재 Azure Blob Storage 최대 5 TB입니다. 서비스에서 사용되는 VM 크기에 따라 Media Services에 추가 제한이 적용됩니다. 크기 제한은 업로드하는 파일과 Media Services 처리(인코딩 또는 분석)의 결과로 생성되는 파일에도 적용됩니다. 원본 파일이 260GB보다 크면 작업이 실패할 수 있습니다. 

