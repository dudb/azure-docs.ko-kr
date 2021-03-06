---
title: "클라우드 무중단 업무 방식 - 데이터베이스 복구 - SQL Database | Microsoft Docs"
description: "Azure SQL 데이터베이스에서 클라우드 무중단 업무 방식 및 데이터베이스 복구를 지원하고 중요 업무용 클라우드 응용 프로그램을 계속해서 실행할 수 있도록 하는 방법을 알아봅니다."
keywords: "무중단 업무 방식, 클라우드 무중단 업무 방식, 데이터베이스 재해 복구, 데이터베이스 복구"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 18e5d3f1-bfe5-4089-b6fd-76988ab29822
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2016
ms.author: carlrab;sashan
translationtype: Human Translation
ms.sourcegitcommit: 9e331f3536bfde96d15ff5bb3a21683f67b25da2
ms.openlocfilehash: d726a886717826368693cda6ca4141c136ea236d


---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Azure SQL 데이터베이스의 비즈니스 연속성 개요
이 개요에서는 Azure SQL 데이터베이스에서 비즈니스 연속성 및 재해 복구를 위해 제공하는 기능에 대해 설명합니다. 데이터 손실을 유발하거나 데이터베이스 및 응용 프로그램을 사용할 수 없게 만들 수 있는 중단 이벤트에서의 복구와 관련된 옵션, 권장 사항 및 자습서도 제공합니다. 토론에서는 사용자 또는 응용 프로그램 오류가 데이터 무결성에 영향을 주거나, Azure 지역에 가동 중단이 발생하거나, 응용 프로그램에 유지 관리가 필요할 때 수행할 작업을 다룹니다. 

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>비즈니스 연속성을 제공하는 데 사용할 수 있는 SQL 데이터베이스 기능
SQL 데이터베이스는 자동화된 백업 및 선택적 데이터베이스 복제를 비롯한 여러 가지 비즈니스 연속성 기능을 제공합니다. 이러한 기능은 최근 트랜잭션에 대한 ERT(예상 복구 시간) 및 잠재적 데이터 손실에 대해 각기 다른 특성을 갖습니다. 이러한 옵션을 이해하면 적절한 옵션을 선택할 수 있습니다. 대부분의 시나리오에서 이러한 옵션을 함께 사용할 수 있습니다. 비즈니스 연속성 계획을 개발할 때는 중단 이벤트 후 응용 프로그램이 완벽하게 복구되기까지 허용되는 최대 시간(RTO(복구 시간 목표))을 이해해야 합니다. 중단 이벤트 후 복구될 때 응용 프로그램이 손실을 허용할 수 있는 최신 데이터 업데이트의 최대 크기(시간 간격)(RPO(복구 지점 목표))도 이해해야 합니다. 

다음 테이블에서 세 가지 가장 일반적인 시나리오에 대한 ERT 및 RPO를 비교합니다.

| 기능 | 기본 계층 | 표준 계층 | 프리미엄 계층 |
| --- | --- | --- | --- |
| 백업에서 특정 시점 복원 |7일 이내의 모든 복원 지점 |35일 이내의 모든 복원 지점 |35일 이내의 모든 복원 지점 |
| 지리적으로 복제된 백업에서 지역 복원 |ERT < 12시간, RPO < 1시간 |ERT < 12시간, RPO < 1시간 |ERT < 12시간, RPO < 1시간 |
| Azure 백업 자격 증명 모음에서 복원 |ERT < 12시간, RPO < 1주 |ERT < 12시간, RPO < 1주 |ERT < 12시간, RPO < 1주 |
| 활성 지역 복제 |ERT < 30초, RPO < 5초 |ERT < 30초, RPO < 5초 |ERT < 30초, RPO < 5초 |

### <a name="use-database-backups-to-recover-a-database"></a>데이터베이스 백업을 사용하여 데이터베이스 복구
SQL 데이터베이스는 데이터 손실로부터 비즈니스를 보호하기 위해 매주 전체 데이터베이스 백업과 매시간 차등 데이터베이스 백업, 그리고 5분 간격으로 트랜잭션 로그 백업을 모두 자동으로 수행합니다. 이러한 백업은 표준 및 프리미엄 서비스 계층의 데이터베이스에 대해서는 35일 동안, 기본 서비스 계층의 데이터베이스에 대해서는 7일 동안 지역 중복 저장소에 저장됩니다. 서비스 계층에 대한 자세한 내용은 [서비스 계층](sql-database-service-tiers.md)을 참조하세요. 서비스 계층에 대한 보존 기간이 비즈니스 요구 사항에 맞지 않으면 [서비스 계층을 변경](sql-database-scale-up.md)하여 보존 기간을 늘릴 수 있습니다. 데이터 센터 가동 중단으로부터 보호하기 위해 전체 및 차등 데이터베이스 백업도 [쌍을 이루는 데이터 센터](../best-practices-availability-paired-regions.md)로 복제됩니다. 자세한 내용은 [자동 데이터베이스 백업](sql-database-automated-backups.md)을 참조하세요.

기본 보존 기간이 응용 프로그램에 대해 충분하지 않을 경우에 데이터베이스에 대한 장기 보존 정책을 구성하여 확장할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요. 

이러한 자동 데이터베이스 백업을 사용하여 다양한 중단 이벤트에서 데이터 센터 내로 및 다른 데이터 센터로 데이터베이스를 복구할 수 있습니다. 자동 데이터베이스 백업을 사용할 경우 예상 복구 시간은 동일한 지역에서 동시에 복구되는 총 데이터베이스 수, 데이터베이스 크기, 트랜잭션 로그 크기 및 네트워크 대역폭에 따라 좌우됩니다. 대부분의 경우 복구 시간은 12시간 미만입니다. 다른 데이터 영역을 복구할 때 잠재적인 데이터 손실은 시간별 차등 데이터베이스 백업의 지역 중복 저장소별로 1시간으로 제한됩니다. 

> [!IMPORTANT]
> 자동화된 백업을 사용하여 복구하려면 SQL Server 참여자 역할의 구성원이거나 구독 소유자여야 합니다. [RBAC: 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)을 참조하세요. Azure 포털, PowerShell 또는 REST API를 사용하여 복구할 수 있습니다. Transact-SQL은 사용할 수 없습니다.
> 
> 

다음 조건의 응용 프로그램의 경우, 자동화된 백업을 비즈니스 연속성 및 복구 메커니즘으로 사용합니다.

* 중요 업무용으로 간주되지 않습니다.
* 연계된 SLA가 없으므로 24시간 이상의 가동 중지가 발생해도 재무적 책임이 없습니다.
* 데이터 변경(시간당 낮은 트랜잭션) 속도가 느리고, 최대 1시간의 데이터 변경 내용 손실은 비교적 허용 가능한 데이터 손실에 해당합니다. 
* 비용에 민감합니다. 

빠른 복구가 필요한 경우 [활성 지역 복제](sql-database-geo-replication-overview.md) (다음에 설명)를 사용합니다. 35일이 지난 데이터를 복구해야 할 경우 Azure Blob 저장소 또는 원하는 다른 위치에 저장된 BACPAC 파일(데이터베이스 스키마 및 관련 데이터를 포함하는 압축된 파일)에 정기적으로 데이터베이스를 보관하는 것이 좋습니다. 트랜잭션이 일관된 데이터베이스 보관 파일을 만드는 방법에 대한 자세한 내용은 [데이터베이스 복사본 만들기](sql-database-copy.md) 및 [데이터베이스 복사본 내보내기](sql-database-export.md)를 참조하세요. 

### <a name="use-active-geo-replication-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>활성 지역 복제를 사용하여 복구 시간을 줄이고 복구와 연결된 데이터 손실 제한
업무 중단이 발생할 경우 데이터베이스 복구를 위해 데이터베이스 백업을 사용하는 것 외에도 [활성 지역 복제](sql-database-geo-replication-overview.md) 를 사용하여 선택한 지역에서 최대 4개의 읽기 가능한 보조 데이터베이스가 유지되도록 데이터베이스를 구성할 수 있습니다. 이러한 보조 데이터베이스는 비동기 복제 메커니즘을 사용하여 주 데이터베이스와 동기화된 상태를 유지합니다. 이 기능은 데이터 센터 가동 중단 또는 응용 프로그램 업그레이드 기간에 업무 중단을 방지하기 위해 사용됩니다. 지리적으로 분산된 사용자에게 읽기 전용 쿼리에 대한 향상된 쿼리 성능을 제공하기 위해 활성 지역 복제를 사용할 수도 있습니다.

주 데이터베이스가 예기치 않게 오프라인 상태가 되거나 유지 관리 작업을 위해 오프라인 상태로 전환해야 할 경우 보조 데이터베이스를 빠르게 주 데이터베이스로 승격하고(장애 조치(faiilover)라고도 함) 새로 승격된 주 데이터베이스에 연결하도록 응용 프로그램을 구성할 수 있습니다. 계획된 장애 조치를 사용할 경우 데이터는 손실되지 않습니다. 계획되지 않은 장애 조치를 사용하는 경우 비동기 복제의 특성으로 인해 아주 최근에 발생한 트랜잭션에 대해 약간의 데이터 손실이 발생합니다. 장애 조치(failover) 후, 계획에 따라 또는 데이터 센터가 다시 온라인 상태가 될 때 장애 복구(failback)를 수행할 수 있습니다. 모든 경우에 사용자는 적은 양의 가동 중지 시간을 경험하며 다시 연결해야 합니다. 

> [!IMPORTANT]
> 활성 지역 복제를 사용하려면 SQL Server에서 구독 소유자이거나 관리 권한을 보유해야 합니다. 구독에 대한 권한을 통해 Azure 포털, PowerShell 또는 REST API를 사용하거나, SQL Server 내의 권한을 통해 Transact-SQL을 사용하여 구성 및 장애 조치(failover)를 수행할 수 있습니다.
> 
> 

응용 프로그램이 다음 조건에 맞는 경우 활성 지역 복제를 사용합니다.

* 중요 업무용입니다.
* 24시간 이상의 가동 중지 시간을 허용하지 않는 SLA(서비스 수준 약정)가 있습니다.
* 가동 중지는 재무적 책임을 유발합니다.
* 데이터 변경 속도가 높고 1시간에 해당하는 데이터 손실은 허용할 수 없는 수준입니다.
* 활성 지역 복제를 사용할 때 발생하는 추가적인 비용이 잠재적인 재무적 책임과 연계된 비즈니스 손실보다 낮습니다.

>
> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>사용자 또는 응용 프로그램 오류 발생 이후 데이터베이스 복구
*어느 누구도 완벽하지는 않습니다. 사용자가 실수로 데이터를 삭제할 수도 있고, 의도치 않게 중요한 테이블을 삭제할 수도 있고, 전체 데이터베이스를 삭제할 수도 있습니다. 또는 응용 프로그램에서 결함으로 인해 잘못된 데이터를 적절한 데이터에 덮어쓸 수도 있습니다. 

이러한 시나리오에서 복구 옵션은 다음과 같습니다.

### <a name="perform-a-point-in-time-restore"></a>지정 시간 복원 수행
자동화된 백업을 사용하여 데이터베이스 복사본을 알려진 적절한 지정 시간(데이터베이스 보존 기간 내에 포함되는 시간)으로 복구할 수 있습니다. 데이터베이스가 복원된 후에 원본 데이터베이스를 복원된 데이터베이스로 바꾸거나 복원된 데이터에서 필요한 데이터를 원본 데이터베이스로 복사할 수 있습니다. 데이터베이스가 활성 지역 복제를 사용하는 경우 복원된 복사본에서 필수 데이터를 원본 데이터베이스로 복사하는 것이 좋습니다. 원본 데이터베이스를 복원된 데이터베이스로 바꾸는 경우 활성 지역 복제를 다시 구성하고 다시 동기화해야 합니다(데이터베이스 크기가 클 경우 시간이 오래 걸릴 수 있음). 

Azure 포털 또는 PowerShell을 사용하여 데이터베이스를 지정 시간으로 복원하기 위한 자세한 단계 및 내용은 [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요. Transact-SQL을 사용하여 복구할 수는 없습니다.

### <a name="restore-a-deleted-database"></a>삭제된 데이터베이스 복원
데이터베이스가 삭제되었으나 논리 서버는 삭제되지 않은 경우 삭제된 데이터베이스를 삭제된 시점으로 복원할 수 있습니다. 이렇게 하면 삭제된 동일한 논리적 SQL Server로 데이터베이스 백업이 복원됩니다. 원래 이름을 사용하여 복원하거나 새 이름 또는 복원된 데이터베이스를 입력할 수 있습니다.

Azure 포털 또는 PowerShell을 사용하여 삭제된 데이터베이스를 복원하는 자세한 단계 및 정보는 [삭제된 데이터베이스 복원](sql-database-recovery-using-backups.md#deleted-database-restore)을 참조하세요. Transact-SQL을 사용하여 복원할 수는 없습니다.

> [!IMPORTANT]
> 논리 서버가 삭제되면 삭제된 데이터베이스를 복구할 수 없습니다. 
> 
> 

### <a name="restore-from-azure-backup-vault"></a>Azure 백업 자격 증명 모음에서 복원
자동화된 백업에 대한 현재 보존 기간 외에 데이터 손실이 발생 하고 장기 보존에 대한 데이터베이스를 구성하는 경우 새 데이터베이스를 Azure 백업 자격 증명 모음에서 매주 백업에서 복원할 수 있습니다. 이 시점에서 원본 데이터베이스를 복원된 데이터베이스로 바꾸거나 복원된 데이터베이스에서 필요한 데이터를 원본 데이터베이스로 복사할 수 있습니다. 이전 버전의 주요 응용 프로그램 업그레이드하기 전에 데이터베이스를 검색해야 할 경우 감사자 또는 법적 순서의 요청을 충족한다면 Azure 백업 자격 증명 모음에 저장된 전체 백업을 사용하여 새 데이터베이스를 만들 수 있습니다.  자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Azure 지역 데이터 센터 가동 중단 상태에서 다른 지역으로 데이터베이스 복구
<!-- Explain this scenario -->

드문 경우지만 Azure 데이터 센터에서 가동 중단이 발생할 수 있습니다. 가동 중단이 발생하면 몇 분 내지 몇 시간 동안 지속될 수 있는 업무 중단이 발생합니다. 

* 한 가지 방법은 데이터 센터 가동 중단이 끝날 때 데이터베이스가 다시 온라인 상태가 될 때까지 기다리는 것입니다. 이러한 방법은 데이터베이스의 오프라인 유지가 가능한 응용 프로그램에 적합합니다. 예를 들어 지속적으로 작업할 필요가 없는 개발 프로젝트 또는 무료 평가판이 있습니다. 데이터 센터가 가동 중단되면 중단이 얼마나 지속될지 알 수 없으므로 이 옵션은 잠시 데이터베이스가 필요 없어도 되는 경우에만 적합합니다.
* 또 다른 방법은 활성 지역 복제를 사용 중인 경우 다른 데이터 영역으로 장애 조치(failover)하거나 지역 중복 데이터베이스 백업(지역 복원)을 사용하여 복구하는 것입니다. 백업에서 복구하는 데는 몇 시간이 걸리지만 장애 조치는 몇 초면 끝납니다.

데이터 센터 가동 중단이 발생할 경우 작업을 수행하는 시기, 복구하는 데 걸리는 시간 및 발생하는 데이터 손실의 양은 위에서 설명한 비즈니스 연속성 기능을 응용 프로그램에서 어떤 방법으로 사용할 것인지에 따라 달라집니다. 실제로 응용 프로그램 요구 사항에 따라 데이터베이스 백업 및 활성 지역 복제를 함께 사용하도록 선택할 수 있습니다. 이러한 비즈니스 지속성 기능을 사용하는 독립 실행형 데이터베이스 및 탄력적 풀에 대한 응용 프로그램 디자인 고려 사항에 대한 자세한 내용은[ 클라우드 재해 복구를 위해 응용 프로그램 디자인](sql-database-designing-cloud-solutions-for-disaster-recovery.md) 및 [탄력적 풀 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)을 참조하세요.

다음 섹션에서는 데이터베이스 백업 또는 활성 지역 복제를 사용하는 복구 단계를 대략적으로 설명합니다. 계획 요구 사항, 복구 후 단계 및 가동 중단을 시뮬레이션하여 재해 복구 훈련을 수행하는 방법을 포함하는 자세한 단계는 [중단 상태에서 SQL 데이터베이스 복구](sql-database-disaster-recovery.md)를 참조하세요.

### <a name="prepare-for-an-outage"></a>가동 중단에 대비
어떤 비즈니스 연속성 기능을 사용하든 관계없이 다음 작업을 수행해야 합니다.

* 서버 수준 방화벽 규칙, 로그인 및 master 데이터베이스 수준 사용 권한을 포함하는 대상 서버를 식별하고 준비합니다.
* 클라이언트 및 클라이언트 응용 프로그램을 새 서버로 리디렉션하는 방법을 결정합니다.
* 감사 설정 및 경고와 같은 다른 종속성을 문서화합니다. 

적절한 계획 및 준비 없이 장애 조치나 복구 이후에 응용 프로그램을 온라인 상태로 전환하면 추가 시간이 소요되고, 바쁜 업무 중 문제 해결이 필요할 수도 있어 비효율적입니다.

### <a name="failover-to-a-geo-replicated-secondary-database"></a>지역에서 복제된 보조 데이터베이스로 장애 조치(failover)
활성 지역 복제를 복구 메커니즘으로 사용하는 경우 [지역에서 복제된 보조 데이터베이스로 강제 장애 조치(failover)를 수행](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database)합니다. 몇 초 안에 보조 데이터베이스는 새로운 주 데이터베이스로 승격되며, 새 트랜잭션을 기록하고 쿼리에 응답할 준비를 갖춥니다. 이때 단 몇 초 간의 데이터 손실이 있을 수 있으나 아직 복제되지도 않은 데이터입니다. 장애 조치 프로세스 자동화에 대한 자세한 내용은 [클라우드 재해 복구를 위해 응용 프로그램 디자인](sql-database-designing-cloud-solutions-for-disaster-recovery.md)을 참조하세요.

> [!NOTE]
> 데이터 센터가 다시 온라인 상태가 되면 원래 주 데이터베이스로 장애 복구(failback)할 수 있습니다(그렇지 않을 수도 있음).
> 
> 

### <a name="perform-a-geo-restore"></a>지역 복원 수행
복구 메커니즘으로 지역 중복 저장소 복제와 자동화된 백업을 함께 사용하는 경우 [지역 복원을 사용하여 데이터베이스 복구를 시작](sql-database-disaster-recovery.md#recover-using-geo-restore)합니다. 일반적으로 복구는 12시간 이내에 수행됩니다. 이때 마지막으로 수행된 매시간 차등 백업의 실행 및 복제를 기준으로 최대 1시간의 데이터 손실이 있을 수 있습니다. 복구가 완료될 때까지 데이터베이스는 어떤 트랜잭션도 기록할 수 없으며 쿼리에 응답할 수 없습니다. 

> [!NOTE]
> 응용 프로그램이 복구된 데이터베이스로 전환하기 전에 데이터 센터가 다시 온라인 상태가 되면 복구를 간단히 취소할 수 있습니다.  
> 
> 

### <a name="perform-post-failover--recovery-tasks"></a>사후 장애 조치(failover)/복구 작업 수행
복구 메커니즘에서 복구한 후에는 사용자 및 응용 프로그램이 다시 실행되기 전에 다음과 같은 추가 작업을 수행해야 합니다.

* 클라이언트 및 클라이언트 응용 프로그램을 새 서버 및 복원된 데이터베이스로 리디렉션
* 사용자가 연결할 수 있도록 적절한 서버 수준 방화벽 규칙이 설정되어 있는지 확인합니다(또는 [데이터베이스 수준 방화벽](sql-database-firewall-configure.md#creating-database-level-firewall-rules)사용).
* 적절한 로그인 및 master 데이터베이스 수준 사용 권한이 설정되었는지 확인합니다(또는 [포함된 사용자](https://msdn.microsoft.com/library/ff929188.aspx)사용).
* 필요에 따라 감사를 구성합니다.
* 필요에 따라 경고를 구성합니다.

## <a name="upgrade-an-application-with-minimal-downtime"></a>최소 가동 중단으로 응용 프로그램 업그레이드
응용 프로그램 업그레이드와 같은 계획된 유지 관리로 인해 응용 프로그램을 오프라인 상태로 전환해야 하는 경우도 있습니다. [응용 프로그램 업그레이드 관리](sql-database-manage-application-rolling-upgrade.md) 에서는 활성 지역 복제를 사용하여 클라우드 응용 프로그램의 롤링 업그레이드를 사용하도록 설정함으로써 업그레이드 기간에 가동 중지 시간을 최소화하고 오류 이벤트에서 복구 경로를 제공하는 방법을 설명합니다. 이 문서에서는 업그레이드 프로세스를 오케스트레이션하는 두 가지 다른 방법을 살펴보고 각 옵션의 이점 및 장단점을 설명합니다.

## <a name="next-steps"></a>다음 단계
독립 실행형 데이터베이스 및 탄력적 풀에 대한 응용 프로그램 디자인 고려 사항에 대한 자세한 내용은[ 클라우드 재해 복구를 위해 응용 프로그램 디자인](sql-database-designing-cloud-solutions-for-disaster-recovery.md) 및 [탄력적 풀 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)을 참조하세요.




<!--HONumber=Dec16_HO3-->


