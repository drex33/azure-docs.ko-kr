---
title: '자습서: 데이터 복사 서비스를 사용하여 디바이스에 복사'
titleSuffix: Azure Data Box
description: 이 자습서에서는 데이터 복사 서비스를 통해 Azure Data Box 디바이스로 데이터를 복사하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: alkohli
ms.openlocfilehash: 857b9ece50b4852e4d459915f9fd13477628781a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429283"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>자습서: 데이터 복사 서비스를 사용하여 Azure Data Box로 데이터 복사(미리 보기)

이 자습서에서는 중간 호스트가 필요 없는 데이터 복사 서비스를 사용하여 데이터를 수집하는 방법을 설명합니다. 데이터 복사 서비스는 Microsoft Azure Data Box에서 로컬로 실행되며, SMB를 통해 NAS(Network Attached Storage) 디바이스에 연결되고 데이터를 Data Box에 복사합니다.

데이터 복사 서비스 사용:

- NAS 환경에서 중간 호스트를 사용하지 못할 수도 있습니다.
- 데이터의 수집 및 업로드에 몇 주가 소요되는 작은 파일 사용. 데이터 복사 서비스는 작은 파일에 대한 수집 및 업로드 시간을 대폭 단축시킵니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Data Box에 데이터 복사

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

1. 이 자습서에서 [Azure Data Box 설정](data-box-deploy-set-up.md)을 완료했습니다.
2. Data Box 디바이스를 받았고 포털의 주문 상태가 **배달됨** 입니다.
3. 데이터를 복사하기 위해 연결하는 원본 NAS 디바이스의 자격 증명이 있어야 합니다.
4. 고속 네트워크에 연결되어 있어야 합니다. 10GbE(기가비트 이더넷) 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없으면 1GbE 데이터 링크를 사용해도 되지만, 이 경우 복사 속도가 떨어집니다.

## <a name="copy-data-to-data-box"></a>Data Box에 데이터 복사

NAS 디바이스에 연결된 후, 다음 단계는 데이터를 복사하는 것입니다. 데이터 복사를 시작하기 전에 다음 고려 사항을 검토합니다.

* 데이터를 복사하는 동안 데이터 크기가 [Azure 스토리지 및 Data Box 제한](data-box-limits.md) 문서에 설명된 크기 제한을 준수해야 합니다.

* Data Box에 의해 업로드되는 데이터가 Data Box 외부의 다른 애플리케이션에 의해 동시에 업로드되는 경우 업로드 작업이 실패하고 데이터 손상이 발생할 수 있습니다.

* 데이터 복사 서비스가 데이터를 읽는 동안 데이터가 변동되는 경우 오류 또는 데이터 손상이 표시될 수 있습니다.

> [!IMPORTANT]
> Data Box에서 Azure Storage로 데이터를 전송했음을 확인할 수 있을 때까지 원본 데이터의 복사본을 유지하세요.

데이터 복사 서비스를 사용하여 데이터를 복사하려면 작업을 만들어야 합니다.

1. Data Box 디바이스의 로컬 웹 UI에서 **관리** > **데이터 복사** 로 이동합니다.
2. **데이터 복사** 페이지에서 **만들기** 를 선택합니다.

    ![“데이터 복사” 페이지에서 만들기를 선택합니다.](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. **작업 구성 및 시작** 대화 상자에서 다음 필드를 채웁니다.
    
    |필드                          |값    |
    |-------------------------------|---------|
    |**작업 이름**                       |작업에 사용할 230자 미만의 고유한 이름입니다. \<, \>, \|, \?, \*, \\, \:, \/ 및 \\\. 문자는 작업 이름에 사용할 수 없습니다.         |
    |**원본 위치**                |`\\<ServerIPAddress>\<ShareName>` 또는 `\\<ServerName>\<ShareName>` 형식으로 데이터 소스에 대한 SMB 경로를 를 제공합니다.        |
    |**사용자 이름**                       |데이터 원본에 액세스하기 위한 `\\<DomainName><UserName>` 형식의 사용자 이름입니다. 로컬 관리자가 연결하는 경우 명시적 보안 권한이 필요합니다. 폴더를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택한 다음, **보안** 을 선택합니다. 이렇게 하면 **보안** 탭에 로컬 관리자가 추가됩니다.       |
    |**암호**                       |데이터 원본에 액세스하기 위한 암호입니다.           |
    |**대상 스토리지 계정**    |목록에서 데이터를 업로드하려면 대상 스토리지 계정을 선택합니다.         |
    |**대상 유형**       |다음 목록에서 대상 스토리지 유형을 선택합니다. **블록 Blob**, **페이지 Blob** 또는 **Azure Files**.        |
    |**대상 컨테이너/공유**    |대상 스토리지 계정에 데이터를 업로드할 컨테이너 또는 공유의 이름을 입력합니다. 공유 이름 또는 컨테이너 이름일 수 있습니다. 예를 들면 `myshare` 또는 `mycontainer`를 사용합니다. 또한 `sharename\directory_name` 또는 `containername\virtual_directory_name` 형식으로 이름을 입력할 수 있습니다.        |
    |**패턴과 일치하는 파일 복사**    | 다음 두 가지 방법으로 패턴과 일치하는 파일 이름을 입력할 수 있습니다.<ul><li>**와일드카드 식을 사용합니다.** 와일드카드 식에서는 `*` 및 `?`만 지원됩니다. 예를 들어 `*.vhd` 식은 `.vhd` 확장명을 가진 모든 파일과 일치합니다. 마찬가지로 `*.dl?`는 확장명이 `.dl`이거나 `.dl`(예: `.dll`)로 시작하는 모든 파일과 일치합니다. 또한 `*foo`는 이름이 `foo`로 끝나는 모든 파일과 일치합니다.<br>필드에 와일드카드 식을 직접 입력할 수 있습니다. 기본적으로 필드에 입력된 값은 와일드카드 식으로 처리됩니다.</li><li>**정규식을 사용합니다.** POSIX 기반 정규식이 지원됩니다. 예를 들어, 정규식 `.*\.vhd`는 확장명이 `.vhd`인 모든 파일과 일치합니다. 정규식의 경우 `<pattern>`을 `regex(<pattern>)`로 직접 입력합니다. 정규식에 대한 자세한 내용은 [정규식 언어 - 빠른 참조](/dotnet/standard/base-types/regular-expression-language-quick-reference)를 참조하세요.</li><ul>|
    |**파일 최적화**              |이 기능을 사용하는 경우 1MB 미만의 파일만 수집하는 동안 압축됩니다. 이 압축으로 작은 파일에 대한 데이터 복사 속도가 빨라집니다. 또한 파일 수가 디렉터리 수보다 훨씬 많은 경우 상당한 시간이 절약됩니다.</br>파일 최적화를 사용하는 경우:<ul><li>배송 준비를 실행한 후 원래 파일 이름을 나열하는 [BOM 파일을 다운로드](data-box-logs.md#inspect-bom-during-prepare-to-ship)하여 올바른 모든 파일이 복사되도록 할 수 있습니다.</li><li>GUID로 파일 이름으로 식별되는 압축된 파일은 삭제하지 마세요. 압축된 파일을 삭제하면 나중에 데이터를 복사하는 동안 원래 파일이 업로드되지 않습니다.</li><li>SMB, NFS 또는 REST API와 같은 다른 프로토콜을 통해 복사 서비스와 함께 복사하는 것과 동일한 파일을 복사하지 마세요. 다른 프로토콜을 사용하면 데이터 업로드 중에 충돌 및 오류가 발생할 수 있습니다.</li></ul>    |
 
4. **시작** 을 선택합니다. 입력의 유효성이 확인되고 유효성 검사에 성공하면 작업이 시작됩니다. 작업이 시작될 때까지 몇 분 정도 걸립니다.

    ![“작업 구성 및 시작” 대화 상자에서 작업 시작](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. 지정된 설정으로 작업이 생성됩니다. 작업을 일시 중지, 다시 시작, 취소 및 다시 시작할 수 있습니다. 작업 이름 옆에 있는 확인란을 선택한 다음, 적절한 단추를 선택합니다.

    ![“데이터 복사” 페이지에서 작업 관리](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - 사용량이 많은 시간 동안 NAS 디바이스의 리소스에 영향을 미치는 경우 작업을 일시 중지할 수 있습니다.

        ![“데이터 복사” 페이지에서 작업 일시 중지](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        사용량이 적은 시간 동안 나중에 작업을 다시 시작할 수 있습니다.

        ![“데이터 복사” 페이지에서 작업 다시 시작](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - 언제든지 작업을 취소할 수 있습니다.

        ![“데이터 복사” 페이지에서 작업 취소](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        작업을 취소하는 경우 확인이 필요합니다.

        ![작업 취소 확인](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        작업을 취소하려는 경우 이미 복사된 데이터는 삭제되지 않습니다. Data Box 디바이스에서 복사한 데이터를 삭제하려면 디바이스를 재설정합니다.

        ![디바이스 재설정](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > 작업을 취소하거나 일시 중지하는 경우 대용량 파일은 일부만 복사될 수 있습니다. 이러한 일부 복사된 파일은 동일한 상태로 Azure에 업로드됩니다. 작업을 취소하거나 일시 중지하는 경우 파일이 제대로 복사되어 있는지 확인합니다. 파일의 유효성을 검사하려면 SMB 공유를 살펴보거나 BOM 파일을 다운로드합니다.

    - 네트워크 결함과 같은 일시적인 오류로 인해 실패했을 경우 작업을 다시 시작할 수 있습니다. **성공함** 또는 **오류와 함께 완료됨** 과 같은 터미널 상태에 도달한 경우에는 작업을 다시 시작할 수 없습니다. 작업 실패는 파일 명명 또는 파일 크기 문제가 원인일 수 있습니다. 이러한 오류는 로깅되지만 작업은 완료되고 나면 다시 시작할 수 없습니다.

        ![실패한 작업 다시 시작](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        오류가 발생하고 작업을 다시 시작할 수 없는 경우 오류 로그를 다운로드하고 로그 파일에서 오류를 살펴봅니다. 문제를 수정한 후 새 작업을 만들어 파일을 복사할 수 있습니다. 또한 [SMB를 통해 파일 복사](data-box-deploy-copy-data.md)할 수도 있습니다.
    
    - 이 릴리스에서는 작업을 삭제할 수 없습니다.
    
    - 무제한 작업을 만들 수 있지만 주어진 시간에 최대 10개의 작업을 병렬로 실행할 수 있습니다.
    - **파일 최적화** 를 사용하는 경우 복사 성능을 개선하기 위해 작은 파일은 수집 시 압축됩니다. 이러한 경우 압축된 파일이 표시됩니다(해당 파일 이름으로 GUID를 갖습니다). 이 파일은 삭제하지 마십시오. 업로드하는 동안 압축이 해제됩니다.

6. 작업을 사용하는 동안 **데이터 복사** 페이지에서

    - **상태** 열에서 복사 작업의 상태를 볼 수 있습니다. 상태는 다음과 같을 수 있습니다.
        - **실행 중**
        - **실패**
        - **성공함**
        - **일시 중지하는 중**
        - **일시 중지됨**
        - **취소하는 중**
        - **Canceled**
        - **오류와 함께 완료됨**
    - **파일** 열에서 복사 중인 파일의 수 및 전체 크기를 볼 수 있습니다.
    - **처리됨** 열에서 처리된 파일의 수 및 전체 크기를 볼 수 있습니다.
    - **작업 세부 정보** 열에서 **보기** 를 선택하면 작업 세부 정보가 표시됩니다.
    - **오류 수** 열에 표시된 대로 복사 프로세스 동안 오류가 있는 경우 **오류 로그** 열로 이동하여 문제 해결을 위해 오류 로그를 다운로드합니다.

복사 작업이 완료될 때까지 기다립니다. 일부 오류만 **연결 및 복사** 페이지에 로그되므로 다음 단계를 진행하기 전에 복사 작업이 오류 없이 완료되었는지 확인해야 합니다.

![“연결 및 복사” 페이지에서 오류 없음](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

데이터 무결성을 보장하기 위해 데이터가 복사될 때 체크섬이 인라인으로 계산됩니다. 복사가 완료된 후 **대시보드 보기** 를 선택하여 디바이스에서 사용 중인 공간과 여유 공간을 확인합니다.
    
![대시보드에서 여유 공간 및 사용 중인 공간 확인](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

복사 작업이 완료된 후 **배송 준비** 를 선택할 수 있습니다.

>[!NOTE]
> 복사 작업이 진행 중이면 **배송 준비** 를 실행할 수 없습니다.

## <a name="next-steps"></a>다음 단계

Data Box 디바이스를 Microsoft로 다시 배송하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Microsoft로 Azure Data Box 디바이스 배송](./data-box-deploy-picked-up.md)