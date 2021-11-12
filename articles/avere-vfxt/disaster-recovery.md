---
title: Avere vFXT for Azure에 대한 재해 복구 지침
description: Avere vFXT for Azure에서 데이터를 실수로 삭제하거나 중단하는 것을 방지하는 방법
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 5ae2ac35cc01ed8dfbc34ab2ff90117c46298980
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325565"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Avere vFXT for Azure에 대한 재해 복구 지침

이 문서에서는 Avere vFXT for Azure 워크플로를 보호하는 전략을 간략하게 설명하고 사고 또는 중단으로부터 복구할 수 있도록 데이터 백업을 위한 지침을 제공합니다.

Avere vFXT for Azure는 데이터를 일시적으로 캐시에 저장합니다. 데이터는 백 엔드 스토리지 시스템(온-프레미스 하드웨어 시스템, Azure Blob Storage 컨테이너 또는 둘 다)에 장기간 저장됩니다.

중단 및 가능한 데이터 손실을 방지하려면 다음 네 가지 영역을 고려하세요.

* Avere vFXT for Azure 시스템을 사용할 수 없게 되는 경우 가동 중지 방지
* 클러스터 캐시의 데이터 보호
* 백 엔드 NAS 하드웨어 스토리지에서 데이터 보호
* 백 엔드 Azure Blob Cloud Storage에서 데이터 보호

모든 Avere vFXT for Azure 고객이 이러한 항목을 포함하는 포괄적인 자체 재해 복구 계획을 세워야 합니다. vFXT 클러스터에서 사용하는 애플리케이션에 복원력을 빌드할 수도 있습니다. 도움말은 [다음 단계](#next-steps)에서 링크를 참조하세요.

## <a name="protect-against-downtime"></a>가동 중지 방지

중복도는 Avere vFXT for Azure 제품에 기본 제공됩니다.

* 클러스터는 가용성이 뛰어나며, 개별 클러스터 노드는 중단을 최소화하면서 장애 조치(failover)할 수 있습니다.
* 캐시에서 변경된 데이터는 장기 저장을 위해 백 엔드 코어 필러(하드웨어 NAS 또는 Azure Blob)에 정기적으로 기록됩니다.

각 Avere vFXT for Azure 클러스터는 단일 가용성 영역에 있어야 하지만 지역에서 중단 발생 시 액세스를 빠르게 제공하기 위해 서로 다른 영역 또는 다른 지역에 있는 중복 클러스터를 사용할 수 있습니다.

데이터에 대한 액세스 중단이 염려되는 경우 스토리지 컨테이너를 여러 지역에 배치할 수도 있습니다. 그러나 지역 간 트랜잭션은 지역 내에 유지되는 트랜잭션보다 대기 시간과 비용이 더 많이 듭니다.

## <a name="protect-data-in-the-cluster-cache"></a>클러스터 캐시의 데이터 보호

캐시된 데이터는 정기적인 종료 전에 항상 코어 파일러에 기록되지만, 예기치 않게 종료되는 경우 캐시의 변경된 데이터가 손실될 수 있습니다.

클러스터를 사용하여 파일 읽기만 최적화하는 경우 손실되는 변경 내용이 없습니다. 클러스터를 사용하여 파일 변경 내용을 캐시하는 경우(쓰기) 코어 파일러의 [캐시 정책](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)을<!-- link to legacy doc --> 장기 스토리지에 데이터를 기록하는 빈도를 사용자 지정하도록 조정할지 여부를 고려합니다.

일반적으로 복구 계획은 더 많은 데이터를 보관하고 일반적으로 오류 발생 후 워크플로를 다시 설정하는 데 더 중요한 백 엔드 스토리지 시스템을 백업하는 데 치중해야 합니다.

## <a name="protect-data-in-nas-core-filers"></a>NAS 코어 파일러의 데이터 보호

허용되는 방법을 사용하여 NAS 공급자가 권장하는 대로 스냅샷 및 전체 백업을 포함해 온-프레미스 NAS 하드웨어 코어 파일러에 저장된 데이터를 보호합니다. 이러한 코어 파일러에 대한 재해 복구는 이 문서의 범위를 벗어나는 내용입니다.

## <a name="protect-data-in-azure-blob-storage"></a>Azure Blob Storage에서 데이터 보호

Avere vFXT for Azure는 Azure Blob 코어 필러에 LRS(로컬 중복 스토리지)를 사용합니다. 즉, Blob 컨테이너의 데이터는 데이터 센터 내의 일시적인 하드웨어 오류에 대한 보호를 위해 자동으로 복사됩니다.

이 섹션에서는 드물게 발생하는 지역 전체 중단 또는 실수로 인한 삭제로부터 Blob Storage의 데이터를 추가로 보호하는 방법에 대한 팁을 제공합니다.

Azure Blob Storage에서 데이터를 보호하기 위한 모범 사례는 다음과 같습니다.

* 다른 지역의 다른 스토리지 계정에 중요한 데이터를 종종 복사합니다(빈도는 재해 복구 계획에 따름).
* 실수로 삭제하거나 손상되지 않도록 모든 대상 시스템에서 데이터에 대한 액세스를 제어합니다. 데이터 스토리지에 [리소스 잠금](../azure-resource-manager/management/lock-resources.md) 사용을 고려해 보세요.
* Blob 코어 파일러에 Avere vFXT for Azure [클라우드 스냅샷](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html) 기능을 사용하도록 설정합니다.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Avere vFXT 코어 파일러 데이터를 백업 계정에 복사

다른 계정에서 데이터 백업을 설정하려면 다음 단계를 수행합니다.

1. 필요한 경우 새 암호화 키를 생성하여 영향을 받는 시스템 외부에 안전하게 저장합니다.

   Avere vFXT for Azure 클러스터에서 데이터를 암호화하는 경우 데이터를 다른 스토리지 계정에 복사하기 전에 새 암호화 키를 생성해야 합니다. 해당 키와 암호는 안전하고 지역에서 발생하는 장애의 영향을 받지 않는 시설에 안전하게 보관합니다.

   클러스터에 컨테이너를 추가할 때(컨테이너를 원래 클러스터에 다시 추가하는 경우에도) 이 키를 제공해야 합니다.

   자세한 내용은 [클라우드 암호화 설정](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html)을<!-- link to legacy doc site --> 참조하세요.

   컨테이너가 Azure의 기본 제공 암호화만 사용하는 경우 이 단계를 건너뛸 수 있습니다.

1. 시스템에서 코어 파일러를 제거합니다. 그러면 클러스터가 변경된 모든 데이터를 백 엔드 스토리지에 기록합니다.

   백업 후 코어 파일러를 다시 추가해야 하지만 코어 파일러를 제거하는 것이 모든 데이터가 백 엔드에 완전히 기록되도록 보장하는 가장 좋은 방법입니다. “일시 중단” 옵션을 사용하면 때때로 캐시에 변경된 데이터가 남을 수 있습니다. <!-- xxx true? or just metadata? -->

   백업 후 컨테이너를 다시 추가할 때 복제할 수 있도록 (제어판의 **네임스페이스** 페이지에 나열된) 코어 파일러의 이름 및 분기 동기화 정보를 기록해 둡니다.

   클러스터 제어판을 사용하여 코어 파일러를 제거합니다. [클러스터 제어판을 열고](avere-vfxt-cluster-gui.md) **코어 파일러**  > **코어 파일러 관리** 를 선택합니다. 백업하려는 스토리지 시스템을 찾은 다음 **제거** 단추를 사용하여 클러스터에서 삭제합니다.

1. 다른 지역의 다른 스토리지 계정에 비어 있는 새 Blob Storage 컨테이너를 만듭니다.

1. 편리한 복사 도구를 사용하여 코어 파일러의 데이터를 새 컨테이너에 복사합니다. 복사는 Avere vFXT for Azure에서 사용하는 독점 클라우드 파일 시스템 형식을 방해하지 않고 데이터를 변경 없이 복제해야 합니다. Azure 기반 도구에는 [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md), [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)가 있습니다.

1. 데이터를 백업 컨테이너에 복사한 후 [스토리지 구성](avere-vfxt-add-storage.md)에서 설명하는 대로 원래 컨테이너를 클러스터에 다시 추가합니다.

   * 클라이언트 워크플로를 변경할 필요가 없도록 동일한 코어 파일러 이름과 분기 동기화 정보를 사용합니다.
   * **버킷 콘텐츠** 값을 기존 데이터 옵션으로 설정합니다.
   * 컨테이너가 클러스터에 의해 암호화되는 경우 해당 콘텐츠에 대한 현재 암호화 키를 입력해야 합니다. 이 키는 1단계에서 업데이트한 키입니다.

첫 번째 백업 이후 백업의 경우 새 스토리지 컨테이너를 만들 필요가 없습니다. 그러나 현재 키가 저장된 장소에 저장되도록 백업을 수행할 때마다 새 암호화 키를 생성하는 것이 좋습니다.

### <a name="access-a-backup-data-source-during-an-outage"></a>중단 중 백업 데이터 원본에 액세스

Avere vFXT for Azure 클러스터에서 백업 컨테이너에 액세스하려면 다음 프로세스를 수행합니다.

1. 필요한 경우 영향을 받지 않는 지역에서 새 Avere vFXT for Azure 클러스터를 만듭니다.

   > [!TIP]
   > Avere vFXT for Azure 클러스터를 만들 때 만들기 템플릿과 매개 변수의 복사본을 저장할 수 있습니다. 기본 클러스터를 만들 때 이 정보를 저장하면 속성이 동일한 교체 클러스터를 만들 때 사용할 수 있습니다. [요약](avere-vfxt-deploy.md#validation-and-purchase) 페이지에서 **템플릿 및 매개 변수 다운로드** 링크를 클릭합니다. 클러스터를 만들기 전에 해당 정보를 파일에 저장합니다.

1. 중복 Blob 컨테이너를 가리키는 새 클라우드 코어 파일러를 추가합니다.

   코어 파일러 만들기 마법사의 **버킷 콘텐츠** 설정에서 대상 컨테이너가 이미 데이터를 포함하도록 지정해야 합니다. 이 설정을 실수로 **비어 있음** 으로 설정된 상태로 두면 시스템에서 경고를 표시해야 합니다.  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. 필요한 경우 원본 대신 새 클러스터 또는 새 코어 파일러를 탑재하도록 클라이언트를 업데이트합니다. (원본 컨테이너와 동일한 이름과 분기 동기화 경로를 사용하여 교체 코어 파일러를 추가하는 경우 새 IP 주소에 새 클러스터를 탑재해야 할 경우를 제외하고는 클라이언트 프로세스를 업데이트할 필요가 없습니다.)

## <a name="next-steps"></a>다음 단계

* Avere vFXT for Azure에 대한 설정을 사용자 지정하는 방법에 대한 자세한 내용은 [클러스터 튜닝](avere-vfxt-tuning.md)을 참조하세요.
* Azure에서 재해 복구와 복원력 있는 애플리케이션 빌드에 대해 자세히 알아보세요.

  * [Azure 복구력 기술 지침](/azure/architecture/reliability/architect)
  * [지역 전체의 서비스 중단으로부터 복구](/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Azure 애플리케이션에 대한 재해 복구 및 고가용성](/azure/architecture/framework/resiliency/backup-and-recovery)
  <!-- can't find these in the source tree to use relative links -->
