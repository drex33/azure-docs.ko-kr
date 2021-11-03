---
title: Azure Files와 함께 Storage Explorer 사용 | Microsoft Docs
description: Storage Explorer를 사용하여 파일 공유 및 파일을 작업하는 방법을 알아봅니다.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2017
ms.author: cawa
ms.openlocfilehash: 634310ea429f950daa6b25c4203dea199adf4103
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036640"
---
# <a name="using-storage-explorer-with-azure-files"></a>Azure Files와 함께 Storage Explorer 사용

Azure Files는 표준 SMB(서버 메시지 블록) 프로토콜을 사용하여 클라우드에서 파일 공유를 제공하는 서비스입니다. SMB 2.1과 SMB 3.0 모두를 지원합니다. Azure Files를 사용하여 파일 공유에 의존하는 레거시 애플리케이션을 비경제적인 다시 쓰기 작업 없이 빠르게 Azure로 마이그레이션할 수 있습니다. File Storage를 사용하여 세상에 공개적으로 표시하거나 애플리케이션 데이터를 비공개적으로 저장할 수 있습니다. 이 문서에서는 Storage Explorer를 사용하여 파일 공유 및 파일을 작업하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 요건

이 문서의 단계를 완료하려면 다음과 같이 하는 것이 필요합니다.

- [Storage Explorer 다운로드 및 설치](https://www.storageexplorer.com/)

- [Azure Storage 계정 또는 서비스에 연결](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-file-share"></a>파일 공유 만들기

모든 파일은 단지 파일의 논리적 그룹화인 파일 공유에 있어야 합니다. 한 계정에 포함될 수 있는 파일 공유 수에 제한이 없으며, 각 공유에 저장될 수 있는 파일 수에도 제한이 없습니다.

다음 단계에서는 Storage Explorer 내에서 파일 공유를 만드는 방법을 보여줍니다.

1. Storage Explorer를 엽니다.

1. 왼쪽 창에서 파일 공유를 만들고자 하는 곳의 스토리지 계정을 확장합니다.

1. **파일 공유** 를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **파일 공유 만들기** 를 선택합니다.

    ![파일 공유 만들기](media/vs-azure-tools-storage-explorer-files/image1.png)

1. 텍스트 상자가 **파일 공유** 폴더 아래에 표시됩니다. 파일 공유에 대한 이름을 입력합니다. 파일 공유 이름 명명 규칙 및 제한 사항 목록은 [공유 이름 명명 규칙](./storage/blobs/storage-quickstart-blobs-dotnet.md) 섹션을 참조하세요.

    ![공유 이름 지정](media/vs-azure-tools-storage-explorer-files/image2.png)

1. 파일 공유 만들기가 끝나면 **Enter** 키를 누르거나 **Esc** 키를 눌러 취소합니다. 파일 공유가 성공적으로 만들어졌다면 선택한 스토리지 계정에 대해 **파일 공유** 폴더 아래에 표시됩니다.

    ![새 공유](media/vs-azure-tools-storage-explorer-files/image3.png)

## <a name="view-a-file-shares-contents"></a>파일 공유의 내용 보기

파일 공유는 파일 및 폴더(파일을 포함할 수도 있음)를 포함합니다.

다음 단계에서는 Storage Explorer 내에서 파일 공유 내용 보기 방법을 보여줍니다.

1. Storage Explorer를 엽니다.

1. 왼쪽 창에서 보려는 파일 공유가 들어 있는 스토리지 계정을 확장합니다.

1. 스토리지 계정의 **파일 공유** 를 확장합니다.

1. 마우스 오른쪽 단추로 보려는 파일 공유를 클릭하고, 상황에 맞는 메뉴에서 **열기** 를 선택합니다. 또한 보려는 파일 공유를 두 번 클릭할 수도 있습니다.

    ![공유 열기](media/vs-azure-tools-storage-explorer-files/image4.png)

1. 기본 창에 파일 공유 내용이 표시됩니다.

    ![공유 콘텐츠를 보여주는 Storage Explorer의 파일 공유에 대한 기본 창의 스크린샷](media/vs-azure-tools-storage-explorer-files/image5.png)

## <a name="delete-a-file-share"></a>파일 공유 삭제

필요에 따라 파일 공유를 쉽게 만들고 삭제할 수 있습니다. (개별 파일 삭제 방법에 대한 자세한 내용은 [파일 공유의 파일 관리](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) 섹션을 참조하세요.)

다음 단계에서는 Storage Explorer 내에서 파일 공유를 삭제하는 방법을 보여줍니다.

1. Storage Explorer를 엽니다.

1. 왼쪽 창에서 보려는 파일 공유가 들어 있는 스토리지 계정을 확장합니다.

1. 스토리지 계정의 **파일 공유** 를 확장합니다.

1. 마우스 오른쪽 단추로 삭제하려는 파일 공유를 클릭하고, 상황에 맞는 메뉴에서 **삭제** 를 선택합니다. 또한 **삭제** 를 눌러 현재 선택된 파일 공유를 삭제할 수도 있습니다.

    ![DELETE](media/vs-azure-tools-storage-explorer-files/image6.png)

1. 확인 대화 상자에서 **확인** 을 선택합니다.

    ![확인 대화 상자](media/vs-azure-tools-storage-explorer-files/image7.png)

## <a name="copy-a-file-share"></a>파일 공유 복사

Storage Explorer를 사용하여 파일 공유를 클립보드에 복사한 다음, 다른 Storage 계정에 붙여넣을 수 있습니다. (개별 파일 복사 방법에 대한 자세한 내용은 [파일 공유의 파일 관리](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) 섹션을 참조하세요.)

다음 단계에서는 한 스토리지 계정에서 다른 계정으로 파일 공유를 복사하는 방법을 보여 줍니다.

1. Storage Explorer를 엽니다.

1. 왼쪽 창에서 복사하려는 파일 공유가 들어 있는 스토리지 계정을 확장합니다.

1. 스토리지 계정의 **파일 공유** 를 확장합니다.

1. 마우스 오른쪽 단추로 복사하려는 파일 공유를 클릭하고, 상황에 맞는 메뉴에서 **파일 공유 복사** 를 선택합니다.

    ![파일 공유 복사](media/vs-azure-tools-storage-explorer-files/image8.png)

1. 마우스 오른쪽 단추로 파일 공유를 붙여 넣을 원하는 &quot;대상&quot; 스토리지 계정을 클릭하고, 상황에 맞는 메뉴에서 **파일 공유 붙여넣기** 를 선택합니다.

    ![파일 공유 붙여넣기](media/vs-azure-tools-storage-explorer-files/image9.png)

## <a name="get-the-sas-for-a-file-share"></a>파일 공유에 대한 SAS 가져오기

[SAS(공유 액세스 서명)](./storage/common/storage-sas-overview.md)는 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 즉, 계정 액세스 키를 공유할 필요 없이 지정된 권한 집합을 사용하여 지정된 기간 동안 클라이언트에게 스토리지 계정의 개체에 대한 제한된 권한을 부여할 수 있습니다.

다음 단계에서는 파일 공유에 대한 SAS를 만드는 방법을 보여 줍니다.

1. Storage Explorer를 엽니다.

1. 왼쪽 창에서 SAS 가져오기를 하려는 파일 공유가 들어 있는 스토리지 계정을 확장합니다.

1. 스토리지 계정의 **파일 공유** 를 확장합니다.

1. 마우스 오른쪽 단추로 원하는 파일 공유를 클릭하고, 상황에 맞는 메뉴에서 **공유 액세스 서명 가져오기** 를 선택합니다.

    ![공유 액세스 서명 가져오기](media/vs-azure-tools-storage-explorer-files/image10.png)

1. **공유 액세스 서명** 대화 상자에서 정책, 시작 및 만료 날짜, 표준 시간대, 리소스에 적용할 액세스 수준을 지정합니다.

    ![SAS 대화 상자](media/vs-azure-tools-storage-explorer-files/image11.png)

1. SAS 옵션 지정하기를 마치면 **만들기** 를 선택합니다.

1. 파일 공유와 함께 스토리지 리소스에 액세스하는 데 사용할 수 있는 URL 및 쿼리 문자열이 나열되는 두 번째 **공유 액세스 서명** 대화 상자가 표시됩니다. 클립보드에 복사할 URL 옆에 있는 **복사** 를 선택합니다.

    ![두 번째 SAS 대화 상자](media/vs-azure-tools-storage-explorer-files/image12.png)

1. 완료되면 **닫기** 를 선택합니다.

## <a name="manage-access-policies-for-a-file-share"></a>파일 공유에 대한 액세스 정책 관리

다음 단계에서는 파일 공유에 대한 액세스 정책을 관리(추가 및 제거)하는 방법을 보여 줍니다. 액세스 정책은 사람들이 정의된 기간 동안 Azure Files 리소스에 액세스하는 데 사용할 수 있는 SAS URL을 만드는 데 사용됩니다.

1. Storage Explorer를 엽니다.

1. 왼쪽 창에서 관리하려는 액세스 정책의 파일 공유가 들어 있는 스토리지 계정을 확장합니다.

1. 스토리지 계정의 **파일 공유** 를 확장합니다.

1. 원하는 파일 공유를 선택하고, 상황에 맞는 메뉴에서 **액세스 정책 관리** 를 선택합니다.

    ![액세스 정책 상황에 맞는 메뉴 관리](media/vs-azure-tools-storage-explorer-files/image13.png)

1. **액세스 정책** 대화 상자는 선택된 파일 공유에 대해 이미 만들어진 모든 액세스 정책을 나열합니다.

    ![액세스 정책](media/vs-azure-tools-storage-explorer-files/image14.png)

1. 액세스 정책 관리 작업에 따라 다음 단계를 수행하세요.

    - **새 액세스 정책 추가** - **추가** 를 선택합니다. 생성되었다면 **액세스 정책** 대화 상자는 (기본 설정을 사용하여) 새로 추가된 액세스 정책을 표시합니다.

    - **액세스 정책 편집** - 원하는 편집을 모두 마치고, **저장** 을 선택합니다.

    - **액세스 정책 제거** - 제거하려는 액세스 정책 옆에 있는 **제거** 를 선택합니다.

1. 앞에서 만든 액세스 정책을 사용하여 새 SAS URL을 만듭니다.

    ![SAS 가져오기](media/vs-azure-tools-storage-explorer-files/image15.png)

    ![SAS 이름 및 속성](media/vs-azure-tools-storage-explorer-files/image16.png)

## <a name="managing-files-in-a-file-share"></a>파일 공유에서 파일 관리

파일 공유를 만들었다면 해당 파일 공유에 파일 업로드, 로컬 컴퓨터에 파일 다운로드, 로컬 컴퓨터에서 파일 열기 등 많은 작업을 할 수 있습니다.

다음 단계에서는 파일 공유 내에서 파일 (및 폴더)을 관리하는 방법을 보여 줍니다.

1.  Storage Explorer를 엽니다.

1.  왼쪽 창에서 관리하려는 파일 공유가 들어 있는 스토리지 계정을 확장합니다.

1.  스토리지 계정의 **파일 공유** 를 확장합니다.

1.  보려는 파일 공유를 두 번 클릭합니다.

1.  기본 창에 파일 공유 내용이 표시됩니다.

    ![첫 번째 폴더가 선택된 공유 콘텐츠를 보여주는 Storage Explorer의 파일 공유 myazurefileshare에 대한 기본 창의 스크린샷](media/vs-azure-tools-storage-explorer-files/image17.png)

1.  기본 창에 파일 공유 내용이 표시됩니다.

1.  수행하려는 작업에 따라서 다음 단계를 수행하세요.

    - **파일 공유에 파일 업로드**

        a.  기본 창 도구 모음에서 **업로드** 를 선택하고, 드롭 다운 메뉴에서 **파일 업로드** 를 합니다.

        ![파일 업로드](media/vs-azure-tools-storage-explorer-files/image18.png)

        b. **파일 업로드** 대화 상자에서 **파일** 텍스트 상자 오른쪽에 있는 줄임표(**…**) 단추를 선택하여 업로드할 파일을 선택합니다.

        ![파일 추가](media/vs-azure-tools-storage-explorer-files/image19.png)

        다. **업로드** 를 선택합니다.

    - **파일 공유에 폴더 업로드**

        a. 기본 창 도구 모음에서 **업로드** 를 선택하고, 드롭 다운 메뉴에서 **폴더 업로드** 를 선택합니다.

        ![폴더 메뉴 업로드](media/vs-azure-tools-storage-explorer-files/image20.png)

        b. **폴더 업로드** 대화 상자에서 **폴더** 텍스트 상자 오른쪽에 있는 줄임표(**…**) 단추를 선택하여 내용을 업로드할 폴더를 선택합니다.

        다. 필요에 따라 선택한 폴더 내용을 업로드할 대상 폴더를 지정합니다. 대상 폴더가 없다면 폴더가 만들어집니다.

        d. **업로드** 를 선택합니다.

    - **로컬 컴퓨터에 파일 다운로드**

        a. 다운로드하려는 파일을 선택합니다.

        b. 기본 창 도구 모음에서 **다운로드** 를 선택합니다.

        다. **다운로드한 파일을 저장할 위치 지정** 대화 상자에서 파일을 다운로드할 위치와 명명하려는 이름을 지정합니다.

        d. **저장** 을 선택합니다.

    - **로컬 컴퓨터에서 파일 열기**

        a.  열려는 파일을 선택합니다.

        b.  기본 창 도구 모음에서 **열기** 를 선택합니다.

        다.  파일의 기본 파일 형식과 연결된 애플리케이션을 사용하여 파일을 다운로드하고 엽니다.

    - **파일을 클립보드에 복사**

        a. 복사하려는 파일을 선택합니다.

        b. 기본 창 도구 모음에서 **복사** 를 선택합니다.

        다. 왼쪽 창에서 또 다른 파일 공유로 이동하고 기본 창에서 보려면 두 번 클릭합니다.

        d. 기본 창 도구 모음에서 파일의 복사본을 만들려면 **붙여넣기** 를 선택합니다.

    - **파일 삭제**

        a. 삭제하려는 파일을 선택합니다.

        b. 기본 창 도구 모음에서 **삭제** 를 선택합니다.

        다. 확인 대화 상자에서 **확인** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [최신 Storage Explorer 릴리스 정보 및 비디오](https://www.storageexplorer.com/)를 보세요.

- [Azure blob, 테이블, 큐 및 파일을 사용하여 애플리케이션을 작성](https://azure.microsoft.com/documentation/services/storage/)하는 방법 알아보기.
