# План тестирования платёжной системы OrchestrPay

### Основные бизнес-сценарии

| Название | Тип | Компоненты | Предусловия |
|----------|-----|------------|-------------|
| **Successful Payment Flow** | E2E | Payment Service, FraudCheck, Notification, PostgreSQL, Redis | Активный клиент с достаточным балансом, настроенный контрагент |
| **Fraud Automatic Decline** | Интеграционный | Payment Service, FraudCheck Service | Настроенное правило антифрода для отклонения |
| **Manual Review Approval** | E2E | Payment Service, FraudCheck, Camunda, Notification | Правило антифрода, требующее ручной проверки |
| **Manual Review Timeout Auto-Approval** | Интеграционный | Payment Service, FraudCheck, Camunda | Транзакция в статусе MANUAL_REVIEW, таймаут 20 минут |
| **Manual Review Decline** | E2E | Payment Service, FraudCheck, Notification | Транзакция на ручной проверке, оператор отклоняет |

### Компенсационные сценарии

| Название | Тип | Компоненты | Предусловия |
|----------|-----|------------|-------------|
| **Compensation Flow on AML Rejection** | E2E | Payment Service, FraudCheck, AML Service, Notification | Средства списаны, но AML проверка отклонена |
| **Payment Service Failure During Debit** | Интеграционный | Payment Service, PostgreSQL, Redis | Сбой при списании средств |
| **Database Rollback Scenario** | Интеграционный | Payment Service, PostgreSQL | Потеря соединения с БД после списания |

### Тесты отказоустойчивости

| Название | Тип | Компоненты | Предусловия |
|----------|-----|------------|-------------|
| **FraudCheck Service Unavailable** | Интеграционный | Payment Service, FraudCheck, Circuit Breaker | Сервис антифрода возвращает 503 ошибку |
| **Notification Failure Handling** | Интеграционный | Payment Service, Notification Service | Сервис нотификаций недоступен |
| **Redis Cache Recovery** | Интеграционный | Все сервисы, Redis | Сбой Redis с последующим восстановлением |
| **Partial Service Degradation** | Интеграционный | Все сервисы | FraudCheck работает, но Notification недоступен |

### Тесты безопасности и корректности

| Название | Тип | Компоненты | Предусловия |
|----------|-----|------------|-------------|
| **Double Payment Idempotency** | Интеграционный | Payment Service, PostgreSQL | Дублирующийся paymentId в запросе |
| **Data Consistency After Rollback** | Интеграционный | Payment Service, PostgreSQL | Проверка консистентности после компенсации |
| **End-to-End Security Audit** | E2E | Все компоненты, Security Team | Проверка логов на утечки данных |
| **Saga State Recovery** | Интеграционный | Camunda, Payment Service | Восстановление состояния саги после перезапуска |
