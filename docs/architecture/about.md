# Концепция

## Описание предметной области

Проект представляет собой комплексную систему автоматизированного контент-продакшна и мультиплатформенной дистрибуции, объединяющую:
Производство видеоконтента (короткие и длинные форматы)
Текстового контента (статьи на основе видео)
Мультиплатформенную дистрибуцию (Instagram, VK, YouTube, Telegram, Дзен)
Сбор и анализ обратной связи
Монетизацию через консультации и закрытые каналы
Ниже представлены 5 ключевых UML-диаграмм в формате PlantUMvvv

## Концептуальная модель предметной области (Class Diagram

```puml
@startuml
skinparam classAttributeIconSize 0
skinparam packageStyle rectangle
title Концептуальная модель предметной области "Sigmar2042 Content System"

package "Контент" {
    class Content {
        +id: UUID
        +title: String
        +createdAt: DateTime
        +theme: String
        +status: ContentStatus
        +render()
    }
    
    class ShortVideo {
        +duration: Integer
        +format: VideoFormat
        +sourcePlatform: Platform
        +views: Integer
        +engagement: Float
    }
    
    class LongVideo {
        +duration: Integer
        +chapters: List<Chapter>
        +seoTitle: String
        +description: Text
        +retention: Float
    }
    
    class Article {
        +text: Markdown
        +coverImage: Image
        +zenTags: List<String>
        +wordCount: Integer
    }
    
    class TelegramPost {
        +type: PostType
        +content: Union<Video, Voice, Text, Circle>
        +isExclusive: Boolean
    }
    
    Content <|-- ShortVideo
    Content <|-- LongVideo
    Content <|-- Article
    Content <|-- TelegramPost
    
    LongVideo "1" *-- "many" ShortVideo : собирается из
}

package "Платформы" {
    class Platform {
        <<abstract>>
        +name: String
        +api: API
        +publish(content: Content)
        +getAnalytics(): Stats
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

package "Аудитория и обратная связь" {
    class Audience {
        +totalSize: Integer
        +segments: List<Segment>
        +growthRate: Float
    }
    
    class Feedback {
        +id: UUID
        +author: User
        +source: Platform
        +text: String
        +sentiment: Float
        +processedAt: DateTime
    }
    
    class PainPoint {
        +topic: String
        +frequency: Integer
        +quotes: List<String>
    }
}

package "Автоматизация (Агенты)" {
    class Agent {
        <<abstract>>
        +name: String
        +execute()
    }
    
    class TranscriptionAgent {
        +model: WhisperModel
        +transcribe(video: Video): Text
    }
    
    class EditingAgent {
        +pipeline: MoviePyPipeline
        +convertToHorizontal()
        +addIntroOutro()
        +addSubtitles()
    }
    
    class LLMAgent {
        +model: LLM
        +generateArticle()
        +analyzeFeedback()
        +generateContentPlan()
    }
    
    class FeedbackAgent {
        +collectFromAll()
        +extractPainPoints(): List<PainPoint>
    }
    
    class PublishingAgent {
        +distribute(content: Content, platforms: List<Platform>)
    }
    
    Agent <|-- TranscriptionAgent
    Agent <|-- EditingAgent
    Agent <|-- LLMAgent
    Agent <|-- FeedbackAgent
    Agent <|-- PublishingAgent
}

package "Монетизация" {
    class Monetization {
        +consultationPrice: Money
        +paidChannelAccess: Money
        +zenRevenue: Money
    }
    
    class Consultation {
        +client: User
        +topic: String
        +price: Money
        +status: Status
    }
    
    class PaidChannel {
        +subscribers: List<User>
        +content: List<TelegramPost>
    }
}

class User {
    +id: UUID
    +username: String
    +platforms: List<Platform>
}

Content "1" --> "many" Platform : публикуется на
Feedback "many" --> "1" User : от
FeedbackAgent ..> PainPoint : извлекает
LLMAgent ..> Content : генерирует
Audience "1" *-- "many" User : состоит из
Monetization "1" *-- Consultation
Monetization "1" *-- PaidChannel

enum ContentStatus {
    DRAFT
    PROCESSING
    READY
    PUBLISHED
    ANALYZED
}

enum VideoFormat {
    VERTICAL_9_16
    HORIZONTAL_16_9
    SQUARE_1_1
}

enum PostType {
    VIDEO
    VOICE
    CIRCLE
    TEXT
}
@enduml
```

```puml
@startuml
left to right direction
skinparam classAttributeIconSize 0
skinparam packageStyle rectangle
title Концептуальная модель (упрощенная, pragматичная)

package "Контент" {
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
    
    class TelegramPost {
        +type: PostType
        +isExclusive: Boolean
    }
    
    Content <|-- ShortVideo
    Content <|-- LongVideo
    Content <|-- Article
    Content <|-- TelegramPost
    
    LongVideo "1" *-- "many" ShortVideo : собирается из
}

package "Платформы" {
    class Platform {
        <<abstract>>
        +name: String
        +publish(content: Content)
        +getAnalytics(): Stats
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

package "Хранилище (SQLite)" {
    class SQLiteDB {
        +content: Table
        +publications: Table
        +metrics: Table
        +feedback: Table
        +llm_logs: Table
        +tasks: Table
        +cache: Table
    }
}

package "Агенты (Python)" {
    class BaseAgent {
        +llm: LiteLLMClient
        +db: SQLiteDB
        +ask(prompt, model): String
    }
    
    class TranscriptionAgent {
        +whisper: WhisperModel
        +transcribe(audio): Text
    }
    
    class EditingAgent {
        +moviepy: MoviePy
        +convertToHorizontal()
        +addIntroOutro()
    }
    
    class LLMAgent {
        +generateArticle()
        +analyzeFeedback()
    }
    
    class PublishingAgent {
        +distribute(content, platforms)
    }
    
    class FeedbackAgent {
        +collectFromAll()
        +extractPainPoints()
    }
    
    BaseAgent <|-- TranscriptionAgent
    BaseAgent <|-- EditingAgent
    BaseAgent <|-- LLMAgent
    BaseAgent <|-- PublishingAgent
    BaseAgent <|-- FeedbackAgent
}

package "LLM-провайдеры" {
    class LiteLLMProxy {
        +route(model): Provider
        +fallback(): Provider
    }
    
    class OpenAICloud {
        +gpt4o_mini: Model
        +gpt4o: Model
        +whisper_api: Model
    }
    
    class LocalLLM {
        <<optional>>
        +ollama: Ollama
        +llama32_3b: Model
    }
    
    LiteLLMProxy --> OpenAICloud : основная сила
    LiteLLMProxy --> LocalLLM : опционально
}

package "Монетизация" {
    class Consultation {
        +client: User
        +price: Money
        +status: Status
    }
    
    class PaidChannel {
        +subscribers: List<User>
    }
}

class User {
    +id: UUID
    +username: String
}

Content "1" --> "many" Platform : публикуется на
SQLiteDB "1" *-- "many" Content : хранит
SQLiteDB "1" *-- "many" User : хранит
BaseAgent --> SQLiteDB :读写
BaseAgent --> LiteLLMProxy : запросы к LLM
LiteLLMProxy --> OpenAICloud : 80% запросов
LiteLLMProxy --> LocalLLM : 20% (опционально)

enum ContentStatus {
    DRAFT
    PROCESSING
    PUBLISHED
    ANALYZED
}

enum PostType {
    VIDEO
    VOICE
    CIRCLE
    TEXT
}
@enduml
```