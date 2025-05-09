# Как это работает

:::warning
Эта страница переведена сообществом на русский язык, но нуждается в улучшениях. Если вы хотите принять участие в переводе свяжитесь с [@alexgton](https://t.me/alexgton).
:::

![how it works](/img/localizationProgramGuideline/localization-program.png)

Программа локализации **TownSquare Labs** состоит из нескольких ключевых компонентов. В этой главе будет представлен обзор того, как работает программа, что поможет вам понять ее работу и как эффективно ее использовать.

В этой системе мы интегрируем несколько приложений для бесперебойной работы в качестве единой программы:

- **GitHub**: размещает документацию, синхронизирует документы из репозитория upstream и синхронизирует переводы с определенными ветками.
- **Crowdin**: управляет процессами перевода, включая перевод, корректуру и настройку языковых предпочтений.
- **Системы ИИ**: использует расширенный ИИ для помощи переводчикам, обеспечивая плавный рабочий процесс.
- **Настраиваемый глоссарий**: направляет переводчиков и гарантирует, что ИИ генерирует точные переводы на основе контекста проекта. Пользователи также могут загружать свои глоссарии по мере необходимости.

:::info
Это руководство не будет подробно рассматривать весь процесс, но в нем будут выделены ключевые компоненты, которые делают программу локализации TownSquare Labs уникальной. Вы можете изучить программу более подробно самостоятельно.
:::

## Синхронизация GitHub для документации и переводов

Наш репозиторий использует несколько ветвей для управления документацией и переводами. Ниже приведено подробное объяснение цели и функции каждой специальной ветви:

### Обзор ветвей

- **`dev`**\
  Ветка `dev` запускает GitHub Actions для обработки задач синхронизации. Конфигурации рабочих процессов можно найти в каталоге [**`.github/workflows`**](https://github.com/TownSquareXYZ/ton-docs/tree/dev/.github/workflows):

  - **`sync-fork.yml`**: этот рабочий процесс синхронизирует документацию из вышестоящего репозитория. Он запускается ежедневно в 00:00.
  - **`sync-translations.yml`**: этот рабочий процесс синхронизирует обновленные переводы с соответствующими языковыми ветвями для предварительного просмотра на соответствующих языковых веб-сайтах.

- **`main`**\
  Эта ветвь синхронизируется с вышестоящим репозиторием с помощью действий GitHub, запущенных в ветке `dev`. Также используется для обновления определённых кодов, которые мы хотим предложить оригинальному репозиторию.

- **`l10n_main`**\
  Эта ветвь включает все изменения из ветви `main` и переводы из Crowdin. Все изменения в этой ветке периодически фиксируются в вышестоящем репозитории с помощью новой подветки с именем `l10n_main_[some data]`.

- **`l10n_feat` или `l10n_feat_[specific functions]`**\
  Эта ветка будет включать изменения в коде или документации, связанной с системой перевода. После завершения всего контента изменения в этой ветке будут объединены в `l10_main`.

- **`[lang]_preview`**\
  Эти ветки предназначены для определенных языковых предварительных просмотров, таких как `ko_preview` для корейского и `ja_preview` для японского. Они позволяют нам предварительно просматривать веб-сайт на разных языках.

Поддерживая эти ветки и используя GitHub Actions, мы эффективно управляем синхронизацией нашей документации и обновлений переводов, гарантируя, что наш многоязычный контент всегда будет актуальным.

## Как создать новый проект Crowdin

1. Войдите в свою учетную запись [**Crowdin**](https://accounts.crowdin.com/login).

2. Нажмите `Создать проект` в меню.
   ![Create new project](/img/localizationProgramGuideline/howItWorked/create-new-project.png)

3. Укажите название проекта и целевые языки. Вы можете изменить языки в настройках позже.
   ![Create project setting](/img/localizationProgramGuideline/howItWorked/create-project-setting.png)

4. Перейдите к только что созданному проекту, выберите вкладку "Интеграции", нажмите кнопку `Добавить интеграцию`, найдите GitHub и установите его.
   ![install-github-integration](/img/localizationProgramGuideline/howItWorked/install-github-integration.png)

5. Перед настройкой интеграций GitHub на Crowdin укажите, какие файлы следует загрузить на Crowdin, чтобы избежать загрузки ненужных файлов:

   1. Создайте файл **crowdin.yml** в корне **репозитория GitHub** с базовой конфигурацией:

   ```yml
   project_id: <Your project id>
   preserve_hierarchy: 1
   files:
     - source: <Path of your original files>
       translation: <Path of your translated files>
   ```

   2. Введите правильные значения конфигурации:
      - **project_id**: В проекте Crowdin перейдите на вкладку "Инструменты", выберите API и найдите там **project_id**.
        ![select-api-tool](/img/localizationProgramGuideline/howItWorked/select-api-tool.png)
        ![projectId](/img/localizationProgramGuideline/howItWorked/projectId.png)
      - **preserve_hierarchy**: Поддерживает структуру каталогов GitHub на сервере Crowdin.
      - **source** и **translation**: Укажите пути для файлов, которые нужно загрузить в Crowdin, и место, где должны быть переведенные файлы.

        Обратитесь к [**нашему официальному конфигурационному файлу**](https://github.com/TownSquareXYZ/ton-docs/blob/localization/crowdin.yml) для примера.\
        Более подробную информацию можно найти в [**документации по конфигурации Crowdin**](https://developer.crowdin.com/configuration-file/).

6. Настройте Crowdin для подключения к вашему репозиторию на GitHub:
   1. Нажмите `Добавить репозиторий` и выберите режим `Исходные файлы и файлы перевода`.
      ![select-integration-mode](/img/localizationProgramGuideline/howItWorked/select-integration-mode.png)
   2. Подключите свою учетную запись GitHub и найдите репозиторий, который нужно перевести.
      ![search-repo](/img/localizationProgramGuideline/howItWorked/search-repo.png)
   3. Выберите ветку слева, которая создаст новую ветку, в которой Crowding будет публиковать переводы.
      ![setting-branch](/img/localizationProgramGuideline/howItWorked/setting-branch.png)
   4. Выберите частоту обновления переводов в вашей ветке GitHub. Настройки по умолчанию можно сохранить для других конфигураций, затем нажмите "Сохранить", чтобы включить интеграцию.
      ![frequency-save](/img/localizationProgramGuideline/howItWorked/frequency-save.png)

Дополнительные сведения см. в [**документации по интеграции GitHub**](https://support.crowdin.com/github-integration/).

7. Наконец, вы можете нажать кнопку "Синхронизировать сейчас", чтобы синхронизировать репозиторий и переводы при необходимости.

## Глоссарий

### Что такое глоссарий?

Иногда переводчики ИИ не могут распознать определенные термины, которые не следует переводить. Например, мы не хотим, чтобы «Rust» переводился применительно к языку программирования. Чтобы предотвратить такие ошибки, мы используем глоссарий для руководства переводами.

**Глоссарий** позволяет создавать, хранить и управлять терминологией, относящейся к конкретному проекту, в одном месте, обеспечивая правильный и последовательный перевод терминов.

Вы можете проверить наш [**ton-i18n-glossary**](https://github.com/TownSquareXYZ/ton-i18n-glossary) для справки.
![ton-i18n-glossary](/img/localizationProgramGuideline/howItWorked/ton-i18n-glossary.png)

### Как настроить глоссарий для нового языка?

Большинство платформ перевода поддерживают глоссарии. В Crowdin после настройки глоссария каждый термин отображается как подчеркнутое слово в редакторе. Наведите курсор на термин, чтобы увидеть его перевод, часть речи и определение (если указано).
![github-glossary](/img/localizationProgramGuideline/howItWorked/github-glossary.png)
![crowdin-glossary](/img/localizationProgramGuideline/howItWorked/crowdin-glossary.png)

В DeepL просто загрузите свой глоссарий, и он будет автоматически использоваться во время перевода ИИ.

Мы создали [**программу для глоссария**](https://github.com/TownSquareXYZ/ton-i18n-glossary), которая автоматически загружает обновления.

Чтобы добавить термин в глоссарий:

1. Если английский термин уже есть в глоссарии, найдите соответствующую строку и столбец для языка, который вы хотите перевести, введите перевод и загрузите его.
2. Чтобы загрузить новый глоссарий, клонируйте проект и запустите:

   - `npm i`
   - `npm run generate -- <glossary name you want>`

Повторите шаг 1, чтобы добавить новый термин.

**Просто и эффективно, не так ли?**

## Как воспользоваться преимуществами AI Translation Copilot?

AI Translation Copilot помогает сломать языковые барьеры, имея несколько преимуществ:

- **Повышенная согласованность**: переводы AI основаны на актуальной информации, предоставляя самые точные и актуальные переводы.
- **Скорость и эффективность**: перевод AI выполняется мгновенно, обрабатывая большие объемы контента в режиме реального времени.
- **Надежная масштабируемость**: системы AI постоянно обучаются и совершенствуются, повышая качество перевода с течением времени. С помощью предоставленного глоссария переводы AI можно адаптировать к конкретным потребностям различных репозиториев.

Чтобы использовать AI перевод в Crowdin (мы используем DeepL в нашем проекте):

1. Выберите «Машинный перевод» в меню Crowdin и нажмите "Изменить" в строке DeepL.
   ![select-deepl](/img/localizationProgramGuideline/howItWorked/select-deepl.png)
2. Включите поддержку DeepL и введите ключ API переводчика DeepL.
   > [Как получить ключ API переводчика DeepL](https://www.deepl.com/pro-api?cta=header-pro-api)

![config-crowdin-deepl](/img/localizationProgramGuideline/howItWorked/config-crowdin-deepl.png)

3. В нашей настройке DeepL используется настраиваемый глоссарий. Проверьте [**ton-i18n-glossary**](https://github.com/TownSquareXYZ/ton-i18n-glossary) для получения подробной информации о загрузке глоссария.

4. В репозитории нажмите "Предварительный перевод" и выберите "Машинный перевод".
   ![pre-translation](/img/localizationProgramGuideline/howItWorked/pre-translation.png)

5. Выберите DeepL в качестве движка перевода, выберите целевые языки и выберите файлы для перевода.
   ![pre-translate-config](/img/localizationProgramGuideline/howItWorked/pre-translate-config.png)

Вот и все! Теперь вы можете сделать перерыв и дождаться завершения предварительного перевода.
