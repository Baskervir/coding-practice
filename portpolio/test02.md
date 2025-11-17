# 프로젝트: Hips-Tour
포트폴리오에 대한 제목 페이지
+ 포트폴리오 내용과 동일

---

## 목차
+ 포트폴리오 내용과 동일

---

## 프로젝트 개요
+ 시스템 아키텍쳐 설명 및 주요 기술 스택
+ 모듈별 상세 설명 1, 2, 3

---

## 안정적인 데이터 동기화 파이프라인 구축
Hips-Tour 프로젝트의 목표는 TourAPI의 국대 여행지를 사용자에게 제공하는 것입니다.<br>
이 목표를 달성하기 위해 두 가지 주요 제약 사항에 직면했습니다.

### TourAPI의 일일 호출 횟수 제한
TourAPI는 **하루에 1,000회만 호출할 수 있도록 제한**되어 있습니다.<br>
반면 서비스에 필요한 초기 데이터는 최소 3만건 이상의 대규모 입니다.<br>
데이터 1건당 API를 1회 호출하는 방식은 서비스 운영의 시작부터 문제로서 작용합니다.

### 높은 응답 지연
사용자 요청 시마다 실시간으로 외부 API를 호출할 경우, 네트워크 상태 및 외부 서버의 처리 시간에 따라<br>
응답 속도가 불규칙하게 변동될 수 있습니다.<br>
이는 일관된 서비스 품질을 유지하기 어렵게 만들고 사용자 경험을 저하시키는 요인입니다.

---

## Bulk 처리 전략
앞서 정의된 API 호출 수 제한 문제를 해결하기 위해, TourAPI와의 I/O 횟수를 최소화 하는 전략을 생각했습니다.<br>
이 전략의 핵심은 Bulk 처리입니다.

### 설계 원칙 및 고려 사항
#### 처리 단위의 전환: `건(Item)`에서 `페이지(Page)`로
TourAPI가 `pageNo`와 `numOfRows` 파라미터를 통한 페이지네이션을 지원한다는 점을 활용했습니다.<br>
데이터 1건을 처리 단위로 보던 관점에서 `데이터 100건이 포함된 페이지 1개`로 전환하여,<br>
API 호출 1회당 처리하는 데이터 양을 극대화했습니다.

#### 데이터베이스 쓰기 최적화: `JPA 'saveAll()'과 JDBC Batch`
API 호출을 Bulk로 처리했다 하더라도, 데이터베이스에 저장할 때 각 엔티티를 개별적으로 **save()**하는 방식은<br>
여전히 비효율적입니다.<br>
이는 100개의 `INSERT` 쿼리가 각각의 트랜잭션으로 데이터베이스에 전달되어 네트워크 오버헤드와 DB 부하를 발생시키기 때문입니다.
```java
spring:
    jpa:
        properties:
            jdbc:
                batch_size: 100 # 한 번에 모아서 보낼 쿼리 수
            order_inserts: true # INSERT 쿼리끼리 모아서 실행
```
이 설정을 통해, 100개의 `INSERT`쿼리가 데이터베이스로 100번 전송되는 대신,<br>
JDBC 드라이버 버퍼에 모였다가 한 번에 전송됩니다.<br>
결과적으로 **데이터베이스와의 네트워크 왕복을 100회에서 1회로 줄여** 쓰기 성능을 극대화했습니다.<br>
이 설계는 외부 API뿐만 아니라 내부 데이터베이스와의 I/O까지 고려한 최적화 방안이라고 생각합니다.

---

## 핵심 컴포넌트 및 로직
+ `LoadService`: 전체 데이터 적재 프로세스를 총괄하는 서비스 클래스
+ `TourApiClient`: TourAPI의 `areaBasedList`엔드포인트를 페이지 단위로 호출하는 HTTP 클라이언트
+ `PlaceRepository`: `JpaRepository`를 상속받아, `saveAll()`메서드를 통해 **Bulk Insert**를 수행하는 리포지토리

```java
// LoadService.java
@Service
@RequiredArgsConstructor
public class LoadService {
    private final TourApiClient tourApiClient;
    private final PlaceRepository placeRepository;
    
    public void loadAllPlaces() {
        int pageNo = 1;
        while (true) {
            // 1. API I/O 최적화: 1회 호출로 100건 데이터 수신
            TourApiResponse response = tourApiClient.getPlacesByPage(pageNo, 100);
            if (response.isEmpty()) {
                break;  // 더 이상 데이터가 없으면 종료
            }
            
            // 2. DTO를 Entity 리스트로 변환
            List<Place> places = response .toEntities();
            
            // 3. DB I/O 최적화: 1회 호출로 100건 데이터 Batch Insert
            placeRepository.saveAll(places);
            pageNo++;
        }
    }
}
```

### 결과
+ API 호출 수 99% 감소: 약 30,000회에서 약 300회로 감소
+ DB 트랜잭션 99% 감소: 약 30,000회에서 약 300회로 감소

---

## 결론
일일 API 호출 제한 내에서 하루 만에 모든 초기 데이터를 안정적으로 확보할 수 있게 되었습니다.

---

## 새로운 위기와 극복 과정
Bulk 처리 전략으로 초기 데이터 적재의 가장 큰 제약은 해결했습니다.<br>
그러나 실제 구현 및 테스트 과정에서 시스템의 안정성 문제에 직면했습니다.

### 배치 작업 중단 시 데이터 유실 및 중복 위험
#### 문제점
`LoadService`가 수행하는 전체 데이터 적재 작업은 수백 번의 API 호출과 DB 트랜잭션을 포함하여<br>
상당한 시간이 소요됩니다.<br>
만약 이 작업이 중간에 서버 재시작, 네트워크 단절 등의 예상하지 못한 이유로 중단될 경우,<br>
어떤 데이터까지 처리되었는지 추적할 방법이 없습니다.

### 재시작 시 문제
작업이 처음부터 다시 실행된다면, 이미 적재된 데이터가 중복되는 문제와 API 호출 횟수를 불필요하게 낭비하게 됩니다.

### 수동 처리 한계
마지막으로 성공한 페이지 번호를 개발자가 수동으로 기록하고 관리하는 것은<br>
오류 발생 가능성이 매우 높으며, 운영 환경에서 사용하기에 부적절하다고 판단했습니다.

---

## 설계
이 문제를 해결하기 위해, 작업의 상태를 영속적으로 기록하고 실패 지점부터 자동으로 재시작할 수 있는<br>
**체크포인트** 개념의 설계 방식을 도입했습니다.

### 핵심 컴포넌트 및 책임
+ `SyncLog`(Entity): 데이터 동기화 작업의 상태를 DB에 저장하기 위한 엔티티<br>`areaCode`, `lastSuccessPageNo`등의 필드를 가집니다.
+ `SyncLogRepository`: `SyncLog` 엔티티의 조회 및 저장을 담당하는 JPA 리포지토리
+ `LoadService`: 체크포인트 로직을 포함하도록 수정

### 핵심 로직
1. `LoadService` 작업 시작 시, `SyncLogRepository`를 통해 가장 마지막으로 성공한 페이지 번호(`lastSuccessPageNo`)를 조회합니다.
2. 조회된 `pageNo + 1`부터 페이지 순회를 시작합니다. (기록이 없으면 1부터 시작)
3. 페이지 단위 Bulk 처리가 성공적으로 완료될 때마다, 해당 페이지 번호를 `SyncLog`테이블에 업데이트하여 **체크포인트**를 저장합니다.
4. 이 **체크포인트** 저장 로직은 데이터 저장과 동일한 트랜잭션 내에서 처리되거나, 트랜잭션 분리가 필요할 경우 원자성을 보장하도록 설계하여 데이터 정합성을 유지합니다.

```java
// LoadService.java
public void loadAllPlaces() {
    // 1. 마지막 성공 지점 조회
    int startPage = syncLogRepository.findLastSuccessPage().orElse(1);
    int pageNo = startPage;
    
    while (true) {
        TourApiResponse response = tourApiClient.getPlacesByPage(pageNo, 100);
        if (response.isEmpty()) break;;
        
        List<Places> places = response.toEntities();
        placeRepository.saveAll(places);
        
        // 2. 성공 지점(체크포인트) 기록
        syncLogResporitory.save(new SyncLog(pageNo));
        pageNo++;
    }
}
```

### 결과
체크포인트 로직 도입으로 `LoadService`는 멱등성을 보장하는 안정적인 배치작업으로 개선되었습니다.<br>
이제 작업이 몇 번이고 중단되었다 재시작되더라도 데이터 중복이나 유실 없이<br>
항상 마지막 지점부터 정확하게 작업을 이어갈 수 있게 되었습니다.<br>
이를 통해 수동 개입이 최소화된 초기 데이터 적재 프로세스를 구축했습니다.
