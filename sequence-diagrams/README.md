# Sequence Diagrams

이 폴더는 유즈케이스 다이어그램에 정의된 각 유즈케이스에 대한 시퀀스 다이어그램을 포함합니다. 각 유즈케이스는 정상 흐름과 오류 케이스를 별도의 파일로 분리하여 관리합니다.

## 파일 구조

각 유즈케이스별로 폴더가 생성되어 있으며, 각 폴더 내에는 다음이 포함됩니다:
- **정상 흐름**: `normal-flow.puml` 또는 기능별 파일
- **오류 케이스**: 각 오류 상황별 파일

## 문서 파일

- **README.md**: 이 파일 (폴더 개요 및 사용 가이드)
- **scenarios.md**: 각 유즈케이스의 상세 시나리오 설명
- **SUMMARY.md**: 작성 완료 요약 문서

## 유즈케이스별 시퀀스 다이어그램

### UC1: 주문 하기 (`uc1-place-order/`)

1. **normal-flow.puml**: 정상 흐름 (주문 생성 및 결제)
2. **duplicate-order.puml**: 중복 주문 오류
3. **no-card.puml**: 카드 정보 없음 오류
4. **low-stock.puml**: 재고 부족 오류
5. **payment-already-paid.puml**: 이미 결제됨 오류

### UC2: 주문 내역 조회 (`uc2-view-orders/`)

1. **normal-flow.puml**: 정상 흐름 (주문 있음)
2. **no-orders.puml**: 주문 없음 케이스
3. **auth-failed.puml**: 인증 실패 오류

### UC3: 재고 확인 (`uc3-check-inventory/`)

1. **list-inventory.puml**: 재고 목록 조회
2. **restock-inventory.puml**: 재고 보충 요청 (정상 흐름)
3. **restock-no-menu-item.puml**: 메뉴 아이템 없음 오류

### UC4: 스케줄 조회 (`uc4-view-schedule/`)

1. **list-schedules.puml**: 스케줄 목록 조회

### UC5: 주문 관리 (`uc5-manage-orders/`)

1. **list-orders.puml**: 주문 목록 조회 (정상)
2. **list-orders-no-permission.puml**: 권한 없음 오류
3. **approve-order.puml**: 주문 승인
4. **reject-order.puml**: 주문 거부
5. **start-cooking.puml**: 조리 시작
6. **start-delivery.puml**: 배송 시작
7. **complete-delivery.puml**: 배송 완료

### UC6: 예약 변경 요청 관리 (`uc6-reservation-change/`)

**고객 측:**
1. **create-request-normal.puml**: 변경 요청 생성 (정상 흐름)
2. **create-request-no-permission.puml**: 권한 없음 오류
3. **create-request-deadline-passed.puml**: 변경 기한 지남 오류
4. **create-request-pending-exists.puml**: 처리 중인 요청 존재 오류

**관리자 측:**
5. **admin-list-requests.puml**: 변경 요청 목록 조회
6. **admin-approve-normal.puml**: 변경 요청 승인
7. **admin-reject.puml**: 변경 요청 거부

### UC7: 계정 관리 (`uc7-account-management/`)

1. **list-users.puml**: 사용자 목록 조회
2. **create-employee.puml**: 직원 계정 생성 (정상)
3. **create-employee-duplicate-email.puml**: 이메일 중복 오류
4. **update-approval-status.puml**: 승인 상태 변경
5. **update-user-info.puml**: 사용자 정보 수정

## PNG 이미지 생성

PNG 이미지를 생성하려면:

### Option 1: Online (PlantText)
1. [PlantText](https://www.planttext.com/) 열기
2. `.puml` 파일 내용 복사
3. 붙여넣고 다이어그램 확인
4. PNG로 내보내기

### Option 2: Command Line (PlantUML)
```bash
# 모든 파일 생성
java -jar plantuml.jar sequence-diagrams/**/*.puml

# 특정 폴더만 생성
java -jar plantuml.jar sequence-diagrams/uc1-place-order/*.puml
```

### Option 3: VS Code Extension
1. "PlantUML" 확장 프로그램 설치
2. `.puml` 파일 열기
3. `Alt + D`로 미리보기
4. 우클릭하여 PNG로 내보내기

## 관련 문서

- 유즈케이스 다이어그램: [../usecase-diagram.puml](../usecase-diagram.puml)
- 시스템 아키텍처: [../architecture-diagrams/](../architecture-diagrams/)
- 클래스 다이어그램: [../class-diagrams/](../class-diagrams/)
- 시나리오 상세 설명: [scenarios.md](./scenarios.md)

## 폴더 구조 예시

```
sequence-diagrams/
├── README.md
├── scenarios.md
├── SUMMARY.md
├── uc1-place-order/
│   ├── normal-flow.puml
│   ├── duplicate-order.puml
│   ├── no-card.puml
│   ├── low-stock.puml
│   └── payment-already-paid.puml
├── uc2-view-orders/
│   ├── normal-flow.puml
│   ├── no-orders.puml
│   └── auth-failed.puml
└── ...
```

## 다이어그램 읽기 가이드

1. **정상 흐름**: 각 유즈케이스의 성공적인 실행 시나리오
2. **오류 케이스**: 각 오류 상황별 처리 흐름
3. **분리된 파일**: 각 분기가 독립적인 파일로 관리되어 가독성 향상
4. **번호 매기기**: `autonumber`로 자동 번호 표시
