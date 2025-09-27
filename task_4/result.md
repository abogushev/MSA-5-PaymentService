@startuml
title Платёжный процесс OrchestrPay

start

:Инициирование платежа;
:Создание транзакции (PENDING);

partition Списание и проверки {
:Списание средств с клиента;

fork
:Антифрод-проверка;
if (Результат?) then (Разрешение)
:APPROVED;
else if (Запрет)
:REJECTED;
else if (Ручная проверка)
:MANUAL_REVIEW;
:Ожидание 20 минут;
if (Решение оператора?) then (Разрешение)
:APPROVED;
else (Запрет)
:REJECTED;
endif
endif

fork again
:Compliance проверки;
:Проверка лимитов;
end fork
}

if (Все проверки пройдены?) then (да)
:Перевод контрагенту;
:Статус COMPLETED;

partition Уведомления {
:Уведомление клиента;
:Уведомление маркетплейса;
}

stop
else (нет)
:Возврат средств;
:Статус REFUNDED;

if (Причина - антифрод?) then (да)
:Уведомить безопасность;
else (нет)
:Записать в лог;
endif

:Уведомление об отказе;
stop
endif

@enduml
