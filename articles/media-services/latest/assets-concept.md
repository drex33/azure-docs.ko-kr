---
title: Azure Media Services의 자산
description: Azure Media Services에서 사용하는 자산 및 사용 방법에 관해 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: cb96b9e1fa0fc64dda35fc9e15e1ab60a42e7650
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278541"
---
# <a name="assets-in-azure-media-services-v3"></a>Azure Media Services v3의 자산

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services에서 [자산](/rest/api/media/assets)은 핵심 개념입니다. 미디어 입력(예: 업로드나 라이브 수집을 통해), 미디어 출력(작업 출력에서), 미디어 게시(스트리밍을 위해)를 수행하는 위치입니다. 

자산은 [Azure Storage 계정](storage-account-concept.md)의 Blob 컨테이너에 매핑되고 자산의 파일은 해당 컨테이너에 블록 Blob으로 저장됩니다. 자산에는 Azure Storage에 저장된 디지털 파일(비디오, 오디오, 이미지, 썸네일 컬렉션, 텍스트 트랙, 선택 자막 파일 포함)에 관한 정보가 포함됩니다.

Media Services는 계정이 범용 v2(GPv2) 스토리지를 사용할 때 Blob 계층을 지원합니다. GPv2를 사용하는 경우 파일을 [쿨 또는 보관 스토리지](../../storage/blobs/access-tiers-overview.md)로 이동할 수 있습니다. **보관** 스토리지는 더 이상 필요 없는 원본 파일을 보관하는 데 적합합니다(예: 인코딩된 후).

**보관** 스토리지 계층은 이미 인코딩되었고 인코딩 작업 출력이 출력 Blob 컨테이너에 배치된 대용량 원본 파일에만 사용할 것을 권장합니다. 자산과 연결하고 콘텐츠를 스트리밍하거나 분석하는 데 사용하려는 출력 컨테이너의 Blob은 **핫** 또는 **쿨** 스토리지 계층에 있어야 합니다.

## <a name="naming"></a>이름 지정 

### <a name="assets"></a>자산

자산 이름은 고유해야 합니다. Media Services v3 리소스 이름(예: 자산, 작업, 변환)에는 Azure Resource Manager 명명 제약 조건이 적용됩니다. 자세한 내용은 [명명 규칙](media-services-apis-overview.md#naming-conventions)을 참조하세요.

### <a name="blobs"></a>Blob

자산 내의 파일/BLOB 이름은 [BLOB 이름 요구 사항](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)과 [NTFS 이름 요구 사항](/windows/win32/fileio/naming-a-file)을 따라야 합니다. 이러한 요구 사항을 따라야 하는 이유는 파일이 처리를 위해 BLOB 스토리지에서 로컬 NTFS 디스크로 복사될 수 있기 때문입니다.

## <a name="next-steps"></a>다음 단계

[Media Services 개요](media-services-overview.md)

## <a name="see-also"></a>참고 항목

[Media Services v2와 v3의 차이점](migrate-v-2-v-3-migration-introduction.md)
