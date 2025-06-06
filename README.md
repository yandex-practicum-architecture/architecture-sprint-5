# Проект Ассистента на основе Rasa и React  

## Введение  

Данный проект представляет собой веб-приложение, разработанное с использованием React на клиентской стороне и Rasa на стороне сервера, предназначенное для создания умного ассистента, способного взаимодействовать с пользователями и предоставлять информацию по тематике микросервисной архитектуры. Проект включает в себя несколько этапов, таких как подготовка среды разработки, установка необходимых зависимостей, развертывание приложения и интеграция с моделью Rasa для обработки естественного языка.  

## Изменения в настройках для запуска проекта  

В ходе подготовки и настройки проекта были внесены следующие изменения:  

### 1. **Увеличение числа эпох**:
   В процессе обучения модели было решено увеличить количество эпох до 100 (`epochs: 100`). Это позволило улучшить качество модели и повысить её способность к точному распознаванию и обработке запросов пользователей. Изменения были внесены в файл конфигурации обучения (`config.yml`) или соответствующий скрипт, который отвечает за обучение модели.


Приводим полный файл настроек `config.yml`:

```
language: ru

pipeline:
- name: WhitespaceTokenizer
- name: RegexFeaturizer
- name: LexicalSyntacticFeaturizer
- name: CountVectorsFeaturizer
  analyzer: "char_wb"
  min_ngram: 1
  max_ngram: 2
- name: LanguageModelFeaturizer
  model_name: "bert"
  model_weights: "bert-base-cased"
  cache_dir: null
- name: DIETClassifier
  epochs: 100
  transformer_size: 128
  number_of_transformer_layers: 2
  use_masked_language_model: false
  batch_strategy: "balanced"
  hidden_layers_sizes:
    text: [32]
- name: EntitySynonymMapper
- name: ResponseSelector
  epochs: 100

policies:
- name: MemoizationPolicy
  max_history: 2
- name: RulePolicy
- name: TEDPolicy
  epochs: 100
  constrain_similarities: true
assistant_id: 20250606-233502-relative-castle
```

### 2. **Настройка CORS**:

Для обеспечения корректного взаимодействия между клиентским приложением, разработанным на React, и сервером Rasa, необходимо правильно настроить CORS (Cross-Origin Resource Sharing). Это позволит клиенту выполнять запросы к API сервера, находясь на другом домене или порту.

#### Шаги настройки CORS:

1. **Конфигурация CORS в Rasa**:
   В файле `endpoints.yml`, находящемся в корне нашего Rasa проекта, добавили/отредактировали следующее:

```
action_endpoint:
  url: "http://localhost:5055/webhook"

cors:
  enabled: true			# Включает поддержку CORS
  allow_origin: "*"  	# Разрешены запросы с любых источников
```     

2. **Запуск Rasa с параметрами CORS**:
   При запуске Rasa убедитесь, что вы используете следующий командный флаг:

```
rasa run --enable-api -vv --cors "*"
```

   Это дополнительно подтвердит, что CORS настроен правильно.
   
### 3. **Настройки, касающиеся темы микросервисной архитектуры**:

См. файлы `config.yml', `/data/nlu.yml`, '/data/rules.yml', '/data/stories.yml' в каталоге `configs`.

В проект была добавлена поддержка вопросов по микросервисной архитектуре, 
что позволяет пользователям получать специализированную информацию по этой теме. 
Для эффективного функционирования этой функции внесены изменения в несколько файлов конфигурации:  

- **`config.yml`**: Обновления в конфигурации моделей и параметрах обучения Rasa. В данном файле могут быть указаны параметры, отвечающие за настройку настраиваемых компонентов, таких как пайплайны NLU и политики для взаимодействий.

- **`data/nlu.yml`**: В этом файле добавлены новые намерения и примеры для обработки вопросов о микросервисной архитектуре. Например, добавлено новое намерение `ask_microservices`, которое позволяет идентифицировать запросы пользователей по этой теме.

  Пример изменения:
  ```
  - intent: ask_microservices
    examples: |
      - Что такое микросервисы?
      - Какие преимущества у микросервисной архитектуры?
      - Как организовать микросервисное приложение?
      - Какие риски связаны с микросервисами?
  ```

- **`data/rules.yml`**: В данном файле были добавлены новые правила, позволяющие системе реагировать на намерение `ask_microservices` с соответствующими действиями. Это обеспечивает корректное взаимодействие пользователя с ботом.

  Пример изменения:

```
  - rule: Answer microservices questions
    steps:
      - intent: ask_microservices
      - action: utter_microservices_info
```

- **`data/stories.yml`**: Здесь были добавлены сценарии, иллюстрирующие, как бот должен взаимодействовать с пользователем при возникновении вопросов о микросервисах. Это помогает системе лучше понимать контекст и предоставлять релевантные ответы.

  Пример изменения:

```
  - story: greet and ask microservices
    steps:
      - intent: greet
      - action: utter_greet
      - intent: ask_microservices
      - action: utter_microservices_info
```

Обратите внимание, что после внесения изменений необходимо переобучить модель Rasa, чтобы новые настройки заработали. Для этого выполните команду:  

```
rasa train
```

## Файлы журналов и экраны приложения/журналов/прямых вызовов curl

[logs_done_from_browser_app_microservices.txt](logs_done_from_browser_app_microservices.txt)  

[logs_done_from_browser_app.txt](logs_done_from_browser_app.txt)  

[logs_done.txt](logs_done.txt)  

[api_calls_done.txt](api_calls_done.txt)  

![browser-app.png](images-browser-app/images-browser-app(1).png)  

![browser-app.png](images-browser-app/images-browser-app(2).png)  

![images-rasa-logs-messages-from-browser-1.png](images-rasa-logs-messages-from-browser/images-rasa-logs-messages-from-browser(1).png)  

![images-rasa-logs-messages-from-browser-5.png](images-rasa-logs-messages-from-browser/images-rasa-logs-messages-from-browser(5).png)  

![images-rasa-logs-messages-from-browser-9.png](images-rasa-logs-messages-from-browser/images-rasa-logs-messages-from-browser(9).png)  

![images-rasa-logs-messages-api-calls-2.png](images-rasa-logs-messages-api-calls/images-rasa-logs-messages-api-calls(2).png)  

![images-rasa-logs-messages-api-calls-7.png](images-rasa-logs-messages-api-calls/images-rasa-logs-messages-api-calls(7).png)  

![images-rasa-logs-messages-api-calls-12.png](images-rasa-logs-messages-api-calls/images-rasa-logs-messages-api-calls(12).png)  
