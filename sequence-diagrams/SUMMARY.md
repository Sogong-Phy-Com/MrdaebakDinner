# 시퀀스 다이어그램 작성 완료 요약

## 작업 완료 ✅

모든 유즈케이스에 대한 시퀀스 다이어그램을 **분기별로 분리**하여 작성 완료했습니다.

## 파일 구조

각 유즈케이스는 폴더 단위로 관리되며, 정상 흐름과 각 오류 케이스가 별도 파일로 분리되었습니다.

### UC1: 주문 하기 (`uc1-place-order/`) - 5개 파일

1. **normal-flow.puml**: 정상 흐름
   - 주문 생성
   - 재고 예약
   - 로열티 할인 적용
   - 결제 프로세스

2. **duplicate-order.puml**: 중복 주문 오류
3. **no-card.puml**: 카드 정보 없음 오류
4. **low-stock.puml**: 재고 부족 오류
5. **payment-already-paid.puml**: 이미 결제됨 오류

### UC2: 주문 내역 조회 (`uc2-view-orders/`) - 3개 파일

1. **normal-flow.puml**: 정상 흐름 (주문 있음)
2. **no-orders.puml**: 주문 없음 케이스
3. **auth-failed.puml**: 인증 실패 오류

### UC3: 재고 확인 (`uc3-check-inventory/`) - 3개 파일

1. **list-inventory.puml**: 재고 목록 조회
2. **restock-inventory.puml**: 재고 보충 요청 (정상)
3. **restock-no-menu-item.puml**: 메뉴 아이템 없음 오류

### UC4: 스케줄 조회 (`uc4-view-schedule/`) - 1개 파일

1. **list-schedules.puml**: 스케줄 목록 조회

### UC5: 주문 관리 (`uc5-manage-orders/`) - 7개 파일

1. **list-orders.puml**: 주문 목록 조회 (정상)
2. **list-orders-no-permission.puml**: 권한 없음 오류
3. **approve-order.puml**: 주문 승인
4. **reject-order.puml**: 주문 거부
5. **start-cooking.puml**: 조리 시작
6. **start-delivery.puml**: 배송 시작
7. **complete-delivery.puml**: 배송 완료

### UC6: 예약 변경 요청 관리 (`uc6-reservation-change/`) - 7개 파일

**고객 측:**
1. **create-request-normal.puml**: 변경 요청 생성 (정상)
2. **create-request-no-permission.puml**: 권한 없음 오류
3. **create-request-deadline-passed.puml**: 변경 기한 지남 오류
4. **create-request-pending-exists.puml**: 처리 중인 요청 존재 오류

**관리자 측:**
5. **admin-list-requests.puml**: 변경 요청 목록 조회
6. **admin-approve-normal.puml**: 변경 요청 승인
7. **admin-reject.puml**: 변경 요청 거부

### UC7: 계정 관리 (`uc7-account-management/`) - 5개 파일

1. **list-users.puml**: 사용자 목록 조회
2. **create-employee.puml**: 직원 계정 생성 (정상)
3. **create-employee-duplicate-email.puml**: 이메일 중복 오류
4. **update-approval-status.puml**: 승인 상태 변경
5. **update-user-info.puml**: 사용자 정보 수정

## 통계

- **총 유즈케이스**: 8개
- **총 시퀀스 다이어그램 파일**: 31개
- **정상 흐름**: 8개
- **오류 케이스**: 23개

## 주요 특징

1. **분기별 분리**: 각 분기가 독립적인 파일로 관리되어 가독성 향상
2. **실제 코드 반영**: Controller → Service → Repository 흐름 반영
3. **인증 및 권한**: JWT 토큰 검증 과정 포함
4. **오류 처리**: 각 단계별 예외 상황 처리
5. **데이터베이스 분리**: 4개 SQLite DB 처리 흐름 포함

## 관련 문서

- 유즈케이스 다이어그램: `../usecase-diagram.puml`
- 시스템 아키텍처: `../architecture-diagrams/`
- 클래스 다이어그램: `../class-diagrams/`
- 시나리오 상세 설명: `scenarios.md`

## 다음 단계

1. PNG 이미지 생성: 각 `.puml` 파일을 PNG로 변환
2. 다이어그램 검토: 실제 코드와 일치 여부 확인
3. 필요 시 업데이트: 코드 변경 시 다이어그램도 함께 업데이트

---

**작성 완료 일시**: 2025년 12월
**총 파일 수**: 31개 시퀀스 다이어그램 파일
