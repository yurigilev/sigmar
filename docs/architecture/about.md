# Концепция

## Описание предметной области

Проект представляет собой комплексную систему автоматизированного контент-продакшна и мультиплатформенной дистрибуции, объединяющую:
Производство видеоконтента (короткие и длинные форматы)
Текстового контента (статьи на основе видео)
Мультиплатформенную дистрибуцию (Instagram, VK, YouTube, Telegram, Дзен)
Сбор и анализ обратной связи
Монетизацию через консультации и закрытые каналы


## Концептуальная модель предметной области (Class Diagram

```puml
@startuml
left to right direction
skinparam classAttributeIconSize 0
skinparam packageStyle rectangle
title Концептуальная модель: Контент-продакшн + AI-коучинг

package "Контент-продакшн" {
    class Content {
        +id: UUID
        +title: String
        +theme: String
        +status: ContentStatus
        +createdAt: DateTime
    }
    
    class ShortVideo {
        +duration: Integer
        +views: Integer
        +engagement: Float
    }
    
    class LongVideo {
        +duration: Integer
        +seoTitle: String
        +retention: Float
    }
    
    class Article {
        +text: Markdown
        +coverImage: Image
        +wordCount: Integer
    }
    
    Content <|-- ShortVideo
    Content <|-- LongVideo
    Content <|-- Article
    
    LongVideo "1" *-- "many" ShortVideo : собирается из
}

package "AI-коучинг (монетизация)" {
    class Client {
        +id: Integer
        +telegramId: Long
        +username: String
        +fullName: String
        +age: Integer
        +heightCm: Integer
        +weightKg: Float
        +healthStatus: HealthStatus
        +goal: TrainingGoal
        +martialLevel: MartialLevel
        +subscriptionTier: Tier
        +subscriptionExpires: DateTime
        +status: ClientStatus
    }
    
    class TrainingProgram {
        +id: Integer
        +clientId: Integer
        +version: Integer
        +title: String
        +durationWeeks: Integer
        +schedule: JSON
        +breathingExercises: JSON
        +isActive: Boolean
    }
    
    class Workout {
        +id: Integer
        +programId: Integer
        +dayNumber: Integer
        +scheduledDate: Date
        +exercises: JSON
        +completed: Boolean
        +difficultyRating: Integer
    }
    
    class CheckIn {
        +id: Integer
        +clientId: Integer
        +weekNumber: Integer
        +weightKg: Float
        +energyLevel: Integer
        +sleepQuality: Integer
        +mood: Integer
        +aiAnalysis: Text
        +aiRecommendations: Text
    }
    
    class Payment {
        +id: Integer
        +clientId: Integer
        +amountRub: Integer
        +tier: Tier
        +provider: PaymentProvider
        +status: PaymentStatus
        +externalId: String
    }
    
    class Conversation {
        +id: Integer
        +clientId: Integer
        +role: MessageRole
        +content: Text
        +createdAt: DateTime
    }
    
    class Reminder {
        +id: Integer
        +clientId: Integer
        +type: ReminderType
        +scheduledAt: DateTime
        +sent: Boolean
    }
    
    Client "1" *-- "many" TrainingProgram : имеет
    Client "1" *-- "many" Payment : оплачивает
    Client "1" *-- "many" CheckIn : делает
    Client "1" *-- "many" Conversation : ведет диалог
    Client "1" *-- "many" Reminder : получает
    TrainingProgram "1" *-- "many" Workout : включает
    TrainingProgram "1" --> "many" CheckIn : корректируется по
}

package "Платформы дистрибуции" {
    class Platform {
        <<abstract>>
        +publish(content)
        +getAnalytics()
    }
    class Instagram
    class VK
    class YouTube
    class Telegram
    class Dzen
    
    Platform <|-- Instagram
    Platform <|-- VK
    Platform <|-- YouTube
    Platform <|-- Telegram
    Platform <|-- Dzen
}

package "Инфраструктура" {
    class SQLiteDB {
        +content: Table
        +publications: Table
        +metrics: Table
        +feedback: Table
        +llm_logs: Table
        +tasks: Table
        +cache: Table
        +clients: Table
        +programs: Table
        +workouts: Table
        +checkins: Table
        +payments: Table
        +conversations: Table
        +reminders: Table
    }
    
    class LiteLLMProxy {
        +route(model): Provider
        +fallback(): Provider
    }
    
    class OpenAICloud {
        +gpt4o_mini: Model
        +gpt4o: Model
        +whisper_api: Model
    }
    
    class TelegramBotAPI {
        +sendMessage()
        +sendPoll()
        +processCallback()
    }
    
    class YooKassaAPI {
        +createPayment()
        +checkStatus()
    }
    
    LiteLLMProxy --> OpenAICloud
}

package "Агенты (Python)" {
    class BaseAgent
    class TranscriptionAgent
    class EditingAgent
    class LLMAgent
    class PublishingAgent
    class FeedbackAgent
    class ProgramGenerator
    class ClientManager
    class ReminderService
    class PaymentService
    
    BaseAgent <|-- TranscriptionAgent
    BaseAgent <|-- EditingAgent
    BaseAgent <|-- LLMAgent
    BaseAgent <|-- PublishingAgent
    BaseAgent <|-- FeedbackAgent
    BaseAgent <|-- ProgramGenerator
    BaseAgent <|-- ClientManager
    BaseAgent <|-- ReminderService
    BaseAgent <|-- PaymentService
}

' Связи
Content "1" --> "many" Platform : публикуется
SQLiteDB "1" *-- Content : хранит
SQLiteDB "1" *-- Client : хранит

BaseAgent --> SQLiteDB : данные
BaseAgent --> LiteLLMProxy : запросы к LLM
ProgramGenerator --> LiteLLMProxy : GPT-4o для программ
PaymentService --> YooKassaAPI : платежи
ClientManager --> TelegramBotAPI : уведомления клиентам

package "Параметры анкеты" {
enum ContentStatus { 
    DRAFT
    PROCESSING
    PUBLISHED 
    ANALYZED 
}
enum HealthStatus { 
    NO_LIMITS
    INJURIES
    CHRONIC
    CARDIO 
}
enum TrainingGoal { 
    STRENGTH
    ENDURANCE
    WEIGHT_LOSS
    MARTIAL
    HEALTH
    BREATHING 
}
enum MartialLevel { 
    NONE
    BEGINNER
    INTERMEDIATE
    ADVANCED 
}
enum Tier { 
    START
    PRO
    VIP 
}
enum ClientStatus { 
    LEAD 
    ACTIVE 
    PAUSED
    CHURNED 
}
enum PaymentStatus { 
    PENDING
    PAID
    FAILED
    REFUNDED 
}
enum PaymentProvider { 
    YOOKASSA 
    TELEGRAM 
}
enum MessageRole { 
    USER
    ASSISTANT
    SYSTEM 
}
enum ReminderType { 
    WORKOUT
    CHECKIN
    PAYMENT 
}
}
@enduml
```