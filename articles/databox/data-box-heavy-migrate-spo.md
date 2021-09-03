---
title: Azure Data Box Heavy를 사용하여 파일 공유 콘텐츠를 SharePoint Online으로 이동
description: 이 자습서에서는 Azure Data Box Heavy를 사용하여 파일 공유 콘텐츠를 SharePoint Online으로 마이그레이션하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: how-to
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: 7d0270c08b592742f159ebc7e951dc88731e1773
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122530299"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Azure Data Box Heavy를 사용하여 파일 공유 콘텐츠를 SharePoint Online으로 마이그레이션

Azure Data Box Heavy와 SPMT(SharePoint 마이그레이션 도구)를 사용하여 파일 공유 콘텐츠를 SharePoint Online과 OneDrive로 간편하게 마이그레이션할 수 있습니다. Data Box Heavy를 사용하면 WAN(광대역 네트워크) 링크에 대한 종속성을 제거하고 데이터를 전송할 수 있습니다.

Microsoft Azure Data Box는 Microsoft Azure Portal에서 디바이스를 주문할 수 있는 서비스입니다. 그런 다음, 테라바이트의 데이터를 서버에서 디바이스로 복사할 수 있습니다. 이것을 Microsoft로 다시 배송하면 귀하의 데이터가 Azure로 복사됩니다. 전송하려는 데이터의 크기에 따라 다음 중에서 선택할 수 있습니다.

- [Data Box Disk](./data-box-disk-overview.md): 주문당 사용 가능한 용량이 35TB이며 중소형 데이터 세트에 적합합니다.
- [Data Box](./data-box-overview.md): 디바이스당 사용 가능한 용량이 80TB이며 중대형 데이터 세트에 적합합니다.
- [Data Box Heavy](./data-box-heavy-overview.md): 디바이스당 사용 가능한 용량이 770TB이며 대형 데이터 세트에 적합합니다.

이 문서에서는 Data Box Heavy를 사용하여 파일 공유 콘텐츠를 SharePoint Online으로 마이그레이션하는 방법을 구체적으로 설명합니다.

## <a name="requirements-and-costs"></a>요구 사항 및 비용

### <a name="for-data-box-heavy"></a>Data Box Heavy의 경우

- Data Box Heavy는 EA(기업 계약), CSP(클라우드 솔루션 공급자) 또는 Azure 스폰서쉽 제안에서만 사용할 수 있습니다. 구독이 이 유형에 해당하지 않는다면 Microsoft 지원에 문의하여 구독을 업그레이드하거나 [Azure 구독 가격 책정](https://azure.microsoft.com/pricing/)을 참조하세요.
- Data Box Heavy에는 사용료가 있습니다. [Data Box Heavy 가격 책정](https://azure.microsoft.com/pricing/details/databox/heavy/)을 반드시 검토하세요.


### <a name="for-sharepoint-online"></a>SharePoint Online의 경우

- [SPMT(SharePoint 마이그레이션 도구)의 최소 요구 사항](/sharepointmigration/how-to-use-the-sharepoint-migration-tool)을 검토하세요.

## <a name="workflow-overview"></a>워크플로 개요

이 워크플로를 수행하려면 Azure Data Box Heavy는 물론 SharePoint Online에 대한 단계를 수행해야 합니다.
다음 단계는 Azure Data Box Heavy와 관련이 있습니다.

1. Azure Data Box Heavy를 주문합니다.
2. 디바이스를 받아서 설정합니다.
3. 온-프레미스 파일 공유의 데이터를 디바이스의 Azure Files에 해당하는 폴더로 복사합니다.
4. 복사가 완료되면 지침에 따라 디바이스를 반송합니다.
5. 데이터가 Azure에 완전히 업로드될 때까지 기다립니다.

다음 단계는 SharePoint Online과 관련이 있습니다.

6. Azure Portal에서 VM을 만들어서 Azure 파일 공유에 탑재합니다.
7. Azure VM에 SPMT 도구를 설치합니다.
8. Azure 파일 공유를 원본(*source*)으로 사용하여 SPMT 도구를 실행합니다.
9. 도구의 마지막 단계를 완료합니다.
10. 데이터의 유효성을 검사하고 확인합니다.

## <a name="use-data-box-heavy-to-copy-data"></a>Data Box Heavy를 사용하여 데이터 복사

다음 단계를 수행하여 Data Box Heavy에 데이터를 복사합니다.

1. [Data Box Heavy를 주문](data-box-heavy-deploy-ordered.md)합니다.
2. Data Box Heavy를 받으면, [Data Box Heavy를 설정](data-box-heavy-deploy-set-up.md)합니다. 디바이스에 케이블을 연결하고 두 노드를 모두 구성합니다.
3. [Azure Data Box Heavy에 데이터를 복사](data-box-heavy-deploy-copy-data.md)합니다. 복사하는 동안, 다음 사항을 지켜야 합니다.

    - 데이터 복사에는 Data Box Heavy의 *StorageAccountName_AzFile* 폴더만 사용합니다. 블록 Blob이나 페이지 Blob이 아닌 Azure 파일 공유의 데이터가 복사되어야 하기 때문입니다.
    - *StorageAccountName_AzFile* 폴더 내의 폴더에 파일을 복사합니다. *StorageAccountName_AzFile* 폴더 내의 하위 폴더는 파일 공유를 만듭니다. *StorageAccountName_AzFile* 폴더에 직접 복사된 파일은 오류가 발생하고 블록 Blob으로 업로드됩니다. 이 파일 공유는 다음 단계에서 VM에 탑재됩니다.
    - Data Box Heavy의 두 노드에 데이터를 복사합니다.
3. 디바이스에서 [배송 준비](data-box-heavy-deploy-picked-up.md#prepare-to-ship)를 실행합니다. 배송 준비에 성공해야 파일을 Azure에 성공적으로 업로드할 수 있습니다.
4. [디바이스를 반환](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back)합니다.
5. [Azure에 대한 데이터 업로드를 확인](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure)합니다.

## <a name="use-spmt-to-migrate-data"></a>SPMT를 사용하여 데이터 마이그레이션

데이터 복사가 완료되었다는 Azure 데이터 팀의 확인 메시지를 받으면 데이터를 SharePoint Online으로 마이그레이션합니다.

최고의 성능과 연결을 위해 Azure VM(가상 머신)을 만드는 것이 좋습니다.

1. Azure Portal에 로그인한 다음, [가상 머신을 만듭니다](../virtual-machines/windows/quick-create-portal.md).
2. [Azure 파일 공유를 VM에 탑재](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer)합니다.
3. [SharePoint 마이그레이션 도구를 다운로드](https://spmtreleasescus.blob.core.windows.net/install/default.htm)하여 Azure VM에 설치합니다.
4. SharePoint 마이그레이션 도구를 시작합니다. **로그인** 을 클릭하고 회사 또는 학교 계정의 사용자 이름 및 암호를 입력합니다.
5. **데이터가 어디에 있나요?** 라는 메시지가 표시되면 **파일 공유** 를 선택합니다. 데이터가 있는 Azure 파일 공유의 경로를 입력합니다.
6. 대상 위치를 포함하여 나머지 지시를 평소와 같이 따릅니다. 자세한 내용은 [SharePoint 마이그레이션 도구를 사용하는 방법](/sharepointmigration/how-to-use-the-sharepoint-migration-tool)을 참조하세요.

> [!IMPORTANT]
> - 데이터가 SharePoint Online으로 수집되는 속도는 데이터가 Azure에 이미 있는 경우에도 여러 가지 요소의 영향을 받습니다. 이러한 요소를 이해하면 마이그레이션을 계획하고 효율성을 극대화할 수 있습니다.  자세한 내용은 [SharePoint Online 및 OneDrive 마이그레이션 속도](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed)를 참조하세요.
> - 데이터를 SharePoint Online으로 마이그레이션하는 경우, 파일에 대한 기존 사용 권한이 손실될 위험이 있습니다. 또한 만든 사람(*Created by*) 및 수정된 날짜(*Date modified by*)와 같은 특정 메타데이터도 손실될 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Data Box Heavy 주문](./data-box-heavy-deploy-ordered.md)