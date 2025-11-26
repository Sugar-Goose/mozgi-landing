# Test Task

Vue.js 3 додаток з анімаціями та багатомовною підтримкою.

## Технології

- Vue 3.2.13 (Composition API)
- Vue Router 4.6.3
- Vue I18n 9.14.5
- GSAP 3.13.0

## Структура проекту

```
src/
├── components/          # Vue компоненти
│   ├── BurgerMenuComponent.vue
│   ├── LanguageSelectorComponent.vue
│   ├── MainLogoComponent.vue
│   ├── MainTitleComponent.vue
│   ├── NavigationLinkComponent.vue
│   ├── PageTransitionComponent.vue
│   ├── RunningTextComponent.vue
│   └── ShowreelButtonComponent.vue
├── views/              # Сторінки
│   ├── HomePage.vue
│   └── InfoPage.vue
├── locales/            # Переклади
│   ├── en.json
│   └── ua.json
├── router/             # Роутинг
│   └── index.js
├── styles/             # Стилі
│   └── variables.css   # CSS змінні
├── assets/             # Ресурси
│   ├── fonts/         # Шрифти Grtsk Giga
│   ├── language_icon.png
│   └── logo.png
├── App.vue
├── main.js
└── i18n.js
```

## Встановлення

```bash
npm install
npm run serve
```

## Компоненти

### MainLogoComponent
SVG логотип з анімацією заповнення знизу вгору через clip-path.

### MainTitleComponent
Заголовок з паралакс-ефектом при русі миші. Використовує `gsap.quickTo`.

### RunningTextComponent
Бігучій текст з налаштовуваним кутом повороту. Автоматична переініціалізація при зміні розміру вікна та зміні мови.

### ShowreelButtonComponent
Кнопка з текстом по круговій траєкторії. Обертання при наведенні.

### NavigationLinkComponent
Навігаційні посилання з анімацією підкреслення. Підтримка вертикальної та горизонтальної орієнтації. Пiдкреслення також зробив на свiй розсуд, бо в дизайнi не було стейтiв

### LanguageSelectorComponent
Вибір мови з випадаючим меню. Збереження вибору в localStorage.

### BurgerMenuComponent
Мобільне меню з повноекранним оверлеєм та backdrop blur. Довелося придумувати самому, в дизайнi не було

### PageTransitionComponent
Перехід між сторінками.

## Стилізація

CSS змінні в `src/styles/variables.css`:

- Кольори: `--color-background`, `--color-text-primary`, тощо.
- Типографіка: `--font-primary`, `--font-size-base`, тощо.
- Відступи: `--spacing-xs` до `--spacing-xl`
- Z-index: `--z-index-background` до `--z-index-transition`

Breakpoints:
- Desktop: > 1024px
- Tablet: 768px - 1024px
- Mobile: 375px - 768px
- Small: < 375px

## Інтернаціоналізація

Підтримувані мови: English (en), Ukrainian (ua).

Переклади в `src/locales/`. Вибрана мова зберігається в localStorage.

Використання:
```vue
{{ $t('mainTitle.line1') }}
```

## Анімації

Всі анімації через GSAP Timeline. Послідовність на головній сторінці:

1. Фон (1.2s)
2. Бігучій текст (1s)
3. Логотип (0.8s)
4. Вибір мови та бургер-меню (0.7s)
5. Заголовок (0.8s)
6. Кнопка showreel (0.9s)
7. Навігаційні посилання (0.7s)

Pointer-events вимикаються на 1.5s під час анімацій.

## Роутинг

Маршрути:
- `/` - HomePage
- `/info` - InfoPage
- `/info?link=where|what|who` - InfoPage з параметром

Переходи обробляються через `PageTransitionComponent` з анімацією перекриття екрану.

## Особливості

- Всі константи винесені на початок файлів
- Event listeners очищаються в `onUnmounted`
- Використання `will-change` для оптимізації анімацій
- `transform` замість зміни `top/left`
