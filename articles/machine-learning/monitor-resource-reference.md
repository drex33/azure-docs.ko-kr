---
title: Azure Machine Learning 데이터 참조 모니터링 | Microsoft Docs
description: Azure Machine Learning 모니터링에 대한 참조 설명서입니다. Azure Monitor에서 수집하여 사용할 수 있는 데이터 및 리소스에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 04/07/2021
ms.openlocfilehash: 124b8679ad11eb9a39881cdcf39969360e3275a6
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114227814"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Azure 기계 학습 데이터 참조 모니터링

Azure Machine Learning 작업 영역의 Azure Monitor에서 수집된 데이터 및 리소스에 대해 알아봅니다. 모니터링 데이터의 수집 및 분석 방법 관련 세부 정보는 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.

## <a name="metrics"></a>메트릭

이 섹션에서는 Azure Machine Learning에 대해 수집되는 모든 자동 수집 플랫폼 메트릭을 나열합니다. 이러한 메트릭에 대한 리소스 공급자는 [Microsoft.MachineLearningServices/workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces)입니다.

**모델**

| 메트릭 | 단위 | 설명 |
|--|--|--|
| 모델 레지스터 성공 | 개수 | 이 작업 영역에서 성공한 모델 등록 수 |
| 모델 레지스터 실패 | 개수 | 이 작업 영역에서 실패한 모델 등록 수 |
| 모델 배포 시작 | 개수 | 이 작업 영역에서 시작한 모델 배포 수 |
| 모델 배포 성공 | 개수 | 이 작업 영역에서 성공한 모델 배포 수 |
| 모델 배포 실패 | 개수 | 이 작업 영역에서 실패한 모델 배포 수 |

**할당량**

할당량 정보는 Azure Machine Learning 컴퓨팅 전용입니다.

| 메트릭 | 단위 | 설명 |
|--|--|--|
| 총 노드 수 | 개수 | 총 노드 수. 이 합계에는 활성 노드, 유휴 노드, 사용할 수 없는 노드, 선점된 노드, 나가는 노드 중 일부가 포함됩니다. |
| 활성 노드 | 개수 | 활성 노드의 수. 현재 작업을 실행 중인 노드입니다. |
| 유휴 노드 | 개수 | 유휴 노드 수. 유휴 노드는 현재 작업을 실행하고 있지 않지만 새 작업이 있으면 수락할 수 있는 노드입니다. |
| 사용 불가 노드 | 개수 | 사용할 수 없는 노드 수. 사용할 수 없는 노드는 확인할 수 없는 문제 때문에 작동하지 않는 노드입니다. Azure에서는 이러한 노드를 재활용합니다. |
| 선점된 노드 | 개수 | 선점된 노드 수. 사용 가능한 노드 풀에서 가져온 낮은 우선 순위 노드입니다. |
| 나가는 노드 | 개수 | 나가는 노드 수. 나가는 노드란 방금 작업 처리를 완료했으며 곧 유휴 상태로 전환될 노드입니다. |
| 총 코어 수 | 개수 | 총 코어 수 |
| 활성 코어 | 개수 | 활성 코어 수 |
| 유휴 코어 | 개수 | 유휴 코어 수 |
| 사용 불가 코어 | 개수 | 사용할 수 없는 코어 수 |
| 선점된 코어 | 개수 | 선점된 코어 수 |
| 나가는 코어 | 개수 | 나가는 코어 수 |
| 할당량 사용률 | 개수 | 사용한 할당량 백분율 |

**리소스**

| 메트릭| 단위 | 설명 |
|--|--|--|
| CpuUtilization | 개수 | CPU 노드의 사용률(백분율). 사용률은 1분 간격으로 보고됩니다. |
| GpuUtilization | 개수 | GPU 노드의 사용률(백분율). 사용률은 1분 간격으로 보고됩니다. |
| GpuMemoryUtilization | 개수 | GPU 노드의 메모리 사용률(백분율). 사용률은 1분 간격으로 보고됩니다. |
| GpuEnergyJoules | 개수 | GPU 노드의 간격 에너지(Joules). 에너지는 1분 간격으로 보고됩니다. |

**실행**

작업 영역에 대한 학습 실행 정보입니다.

| 메트릭 | 단위 | 설명 |
|--|--|--|
| 취소된 실행 | 개수 | 이 작업 영역에 대해 취소된 실행 수. 실행이 성공적으로 취소되면 개수가 업데이트됩니다. |
| 취소가 요청된 실행 | 개수 | 이 작업 영역에 대해 취소가 요청된 실행 수. 실행 취소 요청이 수신되면 개수가 업데이트됩니다. |
| 완료된 실행 | 개수 | 이 작업 영역에 대해 성공적으로 완료된 실행 수. 실행이 완료되고 출력이 수집되면 개수가 업데이트됩니다. |
| 실패한 실행 | 개수 | 이 작업 영역에 대해 실패한 실행 수. 실행이 실패하면 개수가 업데이트됩니다. |
| 완료 중인 실행 | 개수 | 이 작업 영역에 대해 완료 중인 상태에 진입한 실행 수. 실행이 완료되었지만 출력 수집이 아직 진행 중일 때 개수가 업데이트됩니다. | 
| 응답 없는 실행 | 개수 | 이 작업 영역에 대해 응답하지 않는 실행 수. 실행이 응답 없음 상태로 전환될 때 개수가 업데이트됩니다. |
| 시작되지 않은 실행 | 개수 | 이 작업 영역에 대해 시작되지 않음 상태의 실행 수. 실행을 만드는 요청이 수신되었지만 실행 정보가 아직 채워지지 않은 경우 개수가 업데이트됩니다. |
| 준비 중인 실행 | 개수 | 이 작업 영역에 대해 준비 중인 실행 수. 실행 환경을 준비하는 동안 실행이 준비 중인 상태에 진입하면 개수가 업데이트됩니다. |
| 프로비저닝 중인 실행 | 개수 | 이 작업 영역에 대해 프로비저닝 중인 실행 수. 실행이 컴퓨팅 대상 생성 또는 프로비저닝을 기다릴 때 개수가 업데이트됩니다. |
| 큐에서 대기 중인 실행 | 개수 | 이 작업 영역에 대해 큐에서 대기 중인 실행 수. 실행이 컴퓨팅 대상의 큐에서 대기할 때 개수가 업데이트됩니다. 필요한 컴퓨팅 노드가 준비될 때까지 대기하는 동안 발생할 수 있습니다. |
| 실행 시작 | 개수 | 이 작업 영역에 대해 실행 중인 실행 수. 실행이 필요한 리소스에서 실행되기 시작할 때 개수가 업데이트됩니다. |
| 시작 중인 실행 | 개수 | 이 작업 영역에 대해 시작된 실행 수. 실행 및 실행 정보(예: 실행 ID)를 만드는 요청이 채워지면 개수가 업데이트됩니다. |
| 오류 | 개수 | 이 작업 영역의 실행 오류 수. 실행에서 오류가 발생할 때마다 개수가 업데이트됩니다. |
| 경고 | 개수 | 이 작업 영역의 실행 경고 수. 실행에서 경고가 발생할 때마다 개수가 업데이트됩니다. |

## <a name="metric-dimensions"></a>메트릭 차원

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)을 참조하세요.

Azure Machine Learning에는 해당 메트릭과 관련된 다음과 같은 차원이 있습니다.

| 차원 | Description |
| ---- | ---- |
| 클러스터 이름 | 컴퓨팅 클러스터 리소스의 이름입니다. 모든 할당량 메트릭에 사용할 수 있습니다. |
| VM 제품군 이름 | 클러스터에서 사용하는 VM 제품군의 이름입니다. 할당량 사용률에 사용할 수 있습니다. |
| VM 우선 순위 | VM의 우선 순위입니다. 할당량 사용률에 사용할 수 있습니다.
| CreatedTime | CpuUtilization 및 GpuUtilization에만 사용할 수 있습니다. |
| DeviceId | 디바이스의 ID입니다(GPU). GpuUtilization에만 사용할 수 있습니다. |
| NodeId | 작업이 실행되는 위치에 생성된 노드의 ID입니다. CpuUtilization 및 GpuUtilization에만 사용할 수 있습니다. |
| RunId | 실행/작업의 ID입니다. CpuUtilization 및 GpuUtilization에만 사용할 수 있습니다. |
| ComputeType | 실행에 사용되는 컴퓨팅 형식입니다. 완료된 실행, 실패한 실행 및 시작된 실행에 대해서만 사용할 수 있습니다. |
| PipelineStepType | 실행에 사용되는 [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep)의 형식입니다. 완료된 실행, 실패한 실행 및 시작된 실행에 대해서만 사용할 수 있습니다. |
| PublishedPipelineId | 실행에 사용되는 게시된 파이프라인의 ID입니다. 완료된 실행, 실패한 실행 및 시작된 실행에 대해서만 사용할 수 있습니다. |
| RunType | 실행의 형식입니다. 완료된 실행, 실패한 실행 및 시작된 실행에 대해서만 사용할 수 있습니다. |

RunType 차원에 대한 유효한 값은 다음과 같습니다.

| 값 | Description |
| ----- | ----- |
| 실험 | 파이프라인이 실행되지 않습니다. |
| PipelineRun | StepRun의 부모인 파이프라인 실행입니다. |
| StepRun | 파이프라인 단계에 대한 실행입니다. |
| ReusedStepRun | 이전 실행을 다시 사용하는 파이프라인 단계에 대한 실행입니다. |

## <a name="activity-log"></a>활동 로그

다음 테이블에는 활동 로그에서 만들어질 수 있는 Azure Machine Learning 관련 작업이 나열되어 있습니다.

| 작업(Operation) | Description |
|:---|:---|
| Machine Learning 작업 영역 만들기 또는 업데이트 | 작업 영역을 만들거나 업데이트함 |
| CheckComputeNameAvailability | 컴퓨팅 이름이 이미 사용 중인지 확인 |
| 컴퓨팅 리소스 만들기 또는 업데이트 | 컴퓨팅 리소스를 만들거나 업데이트함 |
| 컴퓨팅 리소스 삭제 | 컴퓨팅 리소스가 삭제됨 |
| 암호 나열 | 작업 시 Machine Learning 작업 영역에 대한 비밀이 나열됨 |

## <a name="resource-logs"></a>리소스 로그

이 섹션에서는 Azure Machine Learning 작업 영역에 대해 수집할 수 있는 리소스 로그 유형을 나열합니다.

리소스 공급자 및 형식: [Microsoft.MachineLearningServices/workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Category | 표시 이름 |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |
| ModelsChangeEvent | ModelsChangeEvent |
| ModelsReadEvent | ModelsReadEvent |
| ModelsActionEvent | ModelsActionEvent |
| DeploymentReadEvent | DeploymentReadEvent |
| DeploymentEventACI | DeploymentEventACI |
| DeploymentEventAKS | DeploymentEventAKS |
| InferencingOperationAKS | InferencingOperationAKS |
| InferencingOperationACI | InferencingOperationACI |
| EnvironmentChangeEvent | EnvironmentChangeEvent |
| EnvironmentReadEvent | EnvironmentReadEvent |
| DataLabelChangeEvent | DataLabelChangeEvent |
| DataLabelReadEvent | DataLabelReadEvent |
| ComputeInstanceEvent | ComputeInstanceEvent |
| DataStoreChangeEvent | DataStoreChangeEvent |
| DataStoreReadEvent | DataStoreReadEvent |
| DataSetChangeEvent | DataSetChangeEvent |
| DataSetReadEvent | DataSetReadEvent |
| PipelineChangeEvent | PipelineChangeEvent |
| PipelineReadEvent | PipelineReadEvent |
| RunEvent | RunEvent |
| RunReadEvent | RunReadEvent |


## <a name="schemas"></a>스키마

Azure Machine Learning에서 다음 스키마를 사용하고 있음

### <a name="amlcomputejobevent-table"></a>AmlComputeJobEvent 테이블

| 속성 | Description |
|:--- |:---|
| TimeGenerated | 로그 항목이 생성된 시간 |
| OperationName | 로그 이벤트와 연결된 작업의 이름 |
| Category | 로그 이벤트의 이름, AmlComputeClusterNodeEvent |
| JobId | 제출된 작업의 ID |
| ExperimentId | 실험의 ID |
| ExperimentName | 실험의 이름 |
| CustomerSubscriptionId | 실험 및 작업을 제출하는 SubscriptionId |
| WorkspaceName | 기계 학습 작업 영역의 이름 |
| ClusterName | 클러스터의 이름 |
| ProvisioningState | 작업 제출의 상태 |
| ResourceGroupName | 리소스 그룹의 이름 |
| JobName | 작업의 이름 |
| ClusterId | 클러스터의 ID |
| EventType | 작업 이벤트의 유형입니다. 예: JobSubmitted, JobRunning, JobFailed, JobSucceeded |
| ExecutionState | 작업의 상태입니다(Run). 예: Queued, Running, Succeeded, Failed |
| ErrorDetails | 작업 오류에 대한 세부 정보 |
| CreationApiVersion | 작업을 만드는 데 사용되는 API 버전 |
| ClusterResourceGroupName | 클러스터의 리소스 그룹 이름 |
| TFWorkerCount | TF 작업자의 수 |
| TFParameterServerCount | TF 매개 변수 서버의 수 |
| ToolType | 사용된 도구의 유형 |
| RunInContainer | 컨테이너 내에서 작업을 실행해야 하는지 설명하는 플래그 |
| JobErrorMessage | 작업 오류에 대한 자세한 메시지 |
| NodeId | 작업이 실행되는 위치에 생성된 노드의 ID |

### <a name="amlcomputeclusterevent-table"></a>AmlComputeClusterEvent 테이블

| 속성 | Description |
|:--- |:--- |
| TimeGenerated | 로그 항목이 생성된 시간 |
| OperationName | 로그 이벤트와 연결된 작업의 이름 |
| Category | 로그 이벤트의 이름, AmlComputeClusterNodeEvent |
| ProvisioningState | 클러스터의 프로비전 상태 |
| ClusterName | 클러스터의 이름 |
| ClusterType | 클러스터의 유형 |
| CreatedBy | 클러스터를 만든 사용자 |
| CoreCount | 클러스터의 코어 수 |
| VmSize | 클러스터의 VM 크기 |
| VmPriority | 클러스터 내에서 만든 노드의 우선 순위 Dedicated/LowPriority |
| ScalingType | 클러스터 크기 조정의 유형 수동/자동 |
| InitialNodeCount | 클러스터의 초기 노드 수 |
| MinimumNodeCount | 클러스터의 최소 노드 수 |
| MaximumNodeCount | 클러스터의 최대 노드 수 |
| NodeDeallocationOption | 노드의 할당을 취소하는 방법 |
| 게시자 | 클러스터 유형의 게시자 |
| 제안 | 클러스터가 생성되는 제안 |
| SKU | 클러스터 내에서 만든 노드/VM의 SKU |
| 버전 | 노드/VM이 생성되는 동안 사용된 이미지의 버전 |
| SubnetId | 클러스터의 SubnetId |
| AllocationState | 클러스터 할당 상태 |
| CurrentNodeCount | 클러스터의 현재 노드 수 |
| TargetNodeCount | 스케일 업/다운하는 동안 클러스터의 대상 노드 수 |
| EventType | 클러스터를 만드는 동안 발생하는 이벤트의 유형입니다. |
| NodeIdleTimeSecondsBeforeScaleDown | 클러스터가 스케일 다운되기까지 유휴 시간(초) |
| PreemptedNodeCount | 클러스터의 선점된 노드 수 |
| IsResizeGrow | 클러스터가 스케일 업됨을 나타내는 플래그 |
| VmFamilyName | 클러스터 내에서 만들 수 있는 노드의 VM 제품군 이름 |
| LeavingNodeCount | 클러스터의 나가는 노드 수 |
| UnusableNodeCount | 클러스터의 사용 불가 노드 수 |
| IdleNodeCount | 클러스터의 유휴 상태인 노드 수 |
| RunningNodeCount | 클러스터의 실행 중인 노드 수 |
| PreparingNodeCount | 클러스터의 준비 중인 노드 수 |
| QuotaAllocated | 클러스터에 할당된 할당량 |
| QuotaUtilized | 클러스터의 사용된 할당량 |
| AllocationStateTransitionTime | 한 상태에서 다른 상태로 전환 시간 |
| ClusterErrorCodes | 클러스터를 만들거나 크기를 조정하는 동안 받은 오류 코드 |
| CreationApiVersion | 클러스터를 만드는 동안 사용된 API 버전 |

### <a name="amlcomputeclusternodeevent-table"></a>AmlComputeClusterNodeEvent 테이블

| 속성 | Description |
|:--- |:--- |
| TimeGenerated | 로그 항목이 생성된 시간 |
| OperationName | 로그 이벤트와 연결된 작업의 이름 |
| Category | 로그 이벤트의 이름, AmlComputeClusterNodeEvent |
| ClusterName | 클러스터의 이름 |
| NodeId | 만든 클러스터 노드의 ID |
| VmSize | 노드의 VM 크기 |
| VmFamilyName | 노드가 속한 VM 제품군 |
| VmPriority | 만든 노드의 우선 순위 Dedicated/LowPriority |
| 게시자 | VM 이미지의 게시자입니다. 예: microsoft-dsvm |
| 제안 | VM 만들기와 관련된 제안 |
| SKU | 만든 노드/VM의 SKU |
| 버전 | 노드/VM이 생성되는 동안 사용된 이미지의 버전 |
| ClusterCreationTime | 클러스터가 만들어진 시간 |
| ResizeStartTime | 클러스터 스케일 업/다운 시작 시간 |
| ResizeEndTime | 클러스터 스케일 업/다운 종료 시간 |
| NodeAllocationTime | 노드가 할당된 시간 |
| NodeBootTime | 노드가 부팅된 시간 |
| StartTaskStartTime | 태스크가 노드에 할당되고 시작된 시간 |
| StartTaskEndTime | 태스크가 노드에 할당되고 종료된 시간 |
| TotalE2ETimeInSeconds | 노드가 활성 상태였던 총 시간 |

### <a name="amlcomputeinstanceevent-table"></a>AmlComputeInstanceEvent 테이블

| 속성 | 설명 |
|:--- |:--- |
| 형식 | 로그 이벤트의 이름, AmlComputeInstanceEvent |
| TimeGenerated | 로그 항목이 생성된 시간(UTC) |
| Level | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| ResultType | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| CorrelationId | 관련 이벤트 세트를 그룹화하는 데 사용되는 GUID입니다(해당되는 경우). |
| OperationName | 로그 항목과 연결된 작업의 이름 |
| ID | 작업을 수행한 사용자 또는 애플리케이션의 ID입니다. |
| AadTenantId | 작업이 제출된 AAD 테넌트 ID입니다. |
| AmlComputeInstanceName | 로그 항목과 관련된 컴퓨팅 인스턴스의 이름입니다. |

### <a name="amldatalabelevent-table"></a>AmlDataLabelEvent 테이블

| 속성 | 설명 |
|:--- |:--- |
| 형식 | 로그 이벤트의 이름, AmlDataLabelEvent |
| TimeGenerated | 로그 항목이 생성된 시간(UTC) |
| Level | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| ResultType | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| CorrelationId | 관련 이벤트 세트를 그룹화하는 데 사용되는 GUID입니다(해당되는 경우). |
| OperationName | 로그 항목과 연결된 작업의 이름 |
| ID | 작업을 수행한 사용자 또는 애플리케이션의 ID입니다. |
| AadTenantId | 작업이 제출된 AAD 테넌트 ID입니다. |
| AmlProjectId | AML 프로젝트의 고유 ID입니다. |
| AmlProjectName | AML 프로젝트의 이름입니다. |
| AmlLabelNames | 프로젝트에 대해 만들어진 레이블 클래스 이름입니다. |
| AmlDataStoreName | 프로젝트의 데이터가 저장되는 데이터 저장소의 이름입니다. |

### <a name="amldatasetevent-table"></a>AmlDataSetEvent 테이블

| 속성 | 설명 |
|:--- |:--- |
| 형식 | 로그 이벤트의 이름, AmlDataSetEvent |
| TimeGenerated | 로그 항목이 생성된 시간(UTC) |
| Level | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| ResultType | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| AmlWorkspaceId | AML 작업 영역의 GUID 및 고유 ID입니다. |
| OperationName | 로그 항목과 연결된 작업의 이름 |
| ID | 작업을 수행한 사용자 또는 애플리케이션의 ID입니다. |
| AadTenantId | 작업이 제출된 AAD 테넌트 ID입니다. |
| AmlDatasetId | AML 데이터 세트의 ID입니다. |
| AmlDatasetName | AML 데이터 세트의 이름입니다. |

### <a name="amldatastoreevent-table"></a>AmlDataStoreEvent 테이블

| 속성 | 설명 |
|:--- |:--- |
| 형식 | 로그 이벤트의 이름, AmlDataStoreEvent |
| TimeGenerated | 로그 항목이 생성된 시간(UTC) |
| Level | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| ResultType | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| AmlWorkspaceId | AML 작업 영역의 GUID 및 고유 ID입니다. |
| OperationName | 로그 항목과 연결된 작업의 이름 |
| ID | 작업을 수행한 사용자 또는 애플리케이션의 ID입니다. |
| AadTenantId | 작업이 제출된 AAD 테넌트 ID입니다. |
| AmlDatastoreName | AML 데이터 저장소의 이름입니다. |

### <a name="amldeploymentevent-table"></a>AmlDeploymentEvent 테이블

| 속성 | 설명 |
|:--- |:--- |
| 형식 | 로그 이벤트의 이름, AmlDeploymentEvent |
| TimeGenerated | 로그 항목이 생성된 시간(UTC) |
| Level | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| ResultType | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| OperationName | 로그 항목과 연결된 작업의 이름 |
| ID | 작업을 수행한 사용자 또는 애플리케이션의 ID입니다. |
| AadTenantId | 작업이 제출된 AAD 테넌트 ID입니다. |
| AmlServiceName | AML 서비스의 이름입니다. |

### <a name="amlinferencingevent-table"></a>AmlInferencingEvent 테이블

| 속성 | 설명 |
|:--- |:--- |
| 형식 | 로그 이벤트의 이름, AmlInferencingEvent |
| TimeGenerated | 로그 항목이 생성된 시간(UTC) |
| Level | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| ResultType | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| OperationName | 로그 항목과 연결된 작업의 이름 |
| ID | 작업을 수행한 사용자 또는 애플리케이션의 ID입니다. |
| AadTenantId | 작업이 제출된 AAD 테넌트 ID입니다. |
| AmlServiceName | AML 서비스의 이름입니다. |

### <a name="amlmodelsevent-table"></a>AmlModelsEvent 테이블

| 속성 | 설명 |
|:--- |:--- |
| 형식 | 로그 이벤트의 이름, AmlModelsEvent |
| TimeGenerated | 로그 항목이 생성된 시간(UTC) |
| Level | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| ResultType | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| OperationName | 로그 항목과 연결된 작업의 이름 |
| ID | 작업을 수행한 사용자 또는 애플리케이션의 ID입니다. |
| AadTenantId | 작업이 제출된 AAD 테넌트 ID입니다. |
| ResultSignature | 이벤트의 HTTP 상태 코드입니다. 일반적인 값에는 200, 201, 202 등이 포함됩니다. |
| AmlModelName | AML 모델의 이름입니다. |

### <a name="amlpipelineevent-table"></a>AmlPipelineEvent 테이블

| 속성 | 설명 |
|:--- |:--- |
| 형식 | 로그 이벤트의 이름, AmlPipelineEvent |
| TimeGenerated | 로그 항목이 생성된 시간(UTC) |
| Level | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| ResultType | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| AmlWorkspaceId | AML 작업 영역의 GUID 및 고유 ID입니다. |
| AmlWorkspaceId | AML 작업 영역의 이름입니다. |
| OperationName | 로그 항목과 연결된 작업의 이름 |
| ID | 작업을 수행한 사용자 또는 애플리케이션의 ID입니다. |
| AadTenantId | 작업이 제출된 AAD 테넌트 ID입니다. |
| AmlModuleId | 모듈의 GUID 및 고유 ID입니다.|
| AmlModelName | AML 모델의 이름입니다. |
| AmlPipelineId | AML 파이프라인의 ID입니다. |
| AmlParentPipelineId | 부모 AML 파이프라인의 ID입니다(복제의 경우). |
| AmlPipelineDraftId | AML 파이프라인 초안의 ID입니다. |
| AmlPipelineDraftName | AML 파이프라인 초안의 이름입니다. |
| AmlPipelineEndpointId | AML 파이프라인 엔드포인트의 ID입니다. |
| AmlPipelineEndpointName | AML 파이프라인 엔드포인트의 이름입니다. |


### <a name="amlrunevent-table"></a>AmlRunEvent 테이블

| 속성 | 설명 |
|:--- |:--- |
| 형식 | 로그 이벤트의 이름, AmlRunEvent |
| TimeGenerated | 로그 항목이 생성된 시간(UTC) |
| Level | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| ResultType | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| OperationName | 로그 항목과 연결된 작업의 이름 |
| AmlWorkspaceId | AML 작업 영역의 GUID 및 고유 ID입니다. |
| ID | 작업을 수행한 사용자 또는 애플리케이션의 ID입니다. |
| AadTenantId | 작업이 제출된 AAD 테넌트 ID입니다. |
| RunId | 실행의 고유 ID입니다. |

### <a name="amlenvironmentevent--table"></a>AmlEnvironmentEvent 테이블

| 속성 | 설명 |
|:--- |:--- |
| 형식 | 로그 이벤트의 이름, AmlEnvironmentEvent |
| TimeGenerated | 로그 항목이 생성된 시간(UTC) |
| Level | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| OperationName | 로그 항목과 연결된 작업의 이름 |
| ID | 작업을 수행한 사용자 또는 애플리케이션의 ID입니다. |
| AadTenantId | 작업이 제출된 AAD 테넌트 ID입니다. |
| AmlEnvironmentName | AML 환경 구성의 이름입니다. |
| AmlEnvironmentVersion | AML 환경 구성 버전의 이름입니다. |


## <a name="see-also"></a>참조

- Azure Machine Learning 모니터링에 대한 설명은 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
