@startuml
left to right direction
title Use-Case: Контент + AI-коучинг

actor "Sigmar2042\n(наставник)" as Coach
actor "Подписчик\n(зритель)" as Viewer
actor "Клиент\n(платящий)" as Client

rectangle "Система Sigmar2042" {
    
    package "Контент-продакшн" {
        usecase "Снять серию коротких видео" as UC1
        usecase "Загрузить видео в облако" as UC2
        usecase "Автоматическая обработка\n(транскрипция, монтаж, SEO, статья)" as UC3
        usecase "Получить уведомление о готовности" as UC4
        usecase "Опубликовать в IG/VK/YT/Дзен" as UC5
        usecase "Собрать фидбек и выделить боли" as UC6
    }
    
    package "AI-коучинг (Telegram-бот)" {
        usecase "Запустить бота /start" as UC10
        usecase "Заполнить опросную анкету\n(12 вопросов)" as UC11
        usecase "Выбрать тариф\n(Старт/Про/VIP)" as UC12
        usecase "Оплатить подписку" as UC13
        usecase "Получить персональную программу\n(сгенерирована AI)" as UC14
        usecase "Получать ежедневные напоминания\nо тренировке" as UC15
        usecase "Отмечать выполнение тренировки" as UC16
        usecase "Делать еженедельный чек-ин" as UC17
        usecase "Получать AI-корректировку программы" as UC18
        usecase "Задавать вопросы наставнику" as UC19
    }
    
    package "Управление (для наставника)" {
        usecase "Просмотреть список клиентов" as UC30
        usecase "Проанализировать прогресс клиента" as UC31
        usecase "Вмешаться в сложный случай\n(эскалация от AI)" as UC32
        usecase "Получить финансовый отчет" as UC33
    }
}

' Контент-продакшн
Coach --> UC1
Coach --> UC2
Coach --> UC5 : по клику в Telegram
Viewer --> UC5 : смотрит
Viewer --> UC6 : оставляет фидбек
UC2 ..> UC3 : <<trigger>>
UC3 ..> UC4 : <<notify>>
UC4 --> UC5

' AI-коучинг
Viewer --> UC10 : переходит из контента
UC10 --> UC11
UC11 --> UC12
UC12 --> UC13
UC13 --> UC14
UC14 --> UC15
UC15 --> UC16
UC16 --> UC17
UC17 --> UC18

Client --> UC10
Client --> UC16
Client --> UC17
Client --> UC19

' Управление
Coach --> UC30
Coach --> UC31
Coach --> UC32 : когда AI не справляется
Coach --> UC33

' Связи между линиями
UC6 ..> UC11 : <<extend>>\nболи → темы программ
UC19 ..> UC32 : <<extend>>\nэскалация сложных вопросов

note right of UC3
  **Автоматическая обработка:**
  - Whisper small (транскрипция)
  - FFmpeg (удаление тишины)
  - MoviePy (конвертация форматов)
  - GPT-4o-mini (SEO)
  - GPT-4o (статья для Дзен)
  - Pillow (заставки)
end note

note bottom of UC14
  **Генерация программы:**
  GPT-4o на основе анкеты
  создает JSON с расписанием,
  упражнениями, дыханием,
  питанием, восстановлением
end note

note right of UC18
  **AI-корректировка:**
  Каждую неделю LLM анализирует
  чек-ин клиента и корректирует
  программу на следующую неделю
end note

@enduml

@enduml
```