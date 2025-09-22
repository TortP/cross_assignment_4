# Демо данные:

## Логин: default

## Пароль: default

Проектирование и Реализация Навигационной Структуры

## 📋 Описание задания

Данный проект реализует комплексную навигационную систему мобильного приложения кофейни "Coffee Park" с использованием React Navigation. Система включает Stack, Tab и Drawer навигацию с передачей параметров между экранами и кастомной стилизацией.

## 🎯 Задание 1: Проектирование навигационной структуры

### Анализ дизайна и типов навигации

Приложение использует **трехуровневую навигационную архитектуру**:

#### 🔐 **AuthStack** - Stack Navigation (Авторизация)

**Файл:** `navigation/AuthStack.jsx`

- **Назначение:** Линейные переходы между экранами авторизации
- **Экраны:** Login → SignUp
- **Связи:** Простой переход между входом и регистрацией

#### 📱 **MainTab** - Tab Navigation (Основные разделы)

**Файл:** `navigation/MainTab.jsx`

- **Назначение:** Быстрый доступ к основным функциям приложения
- **Экраны:** Home, Cart, OrderStatus, Profile
- **Связи:** Горизонтальная навигация между основными разделами

#### 📂 **MainDrawer** - Drawer Navigation (Дополнительные функции)

**Файл:** `navigation/MainDrawer.jsx`

- **Назначение:** Боковое меню с дополнительными экранами
- **Экраны:** Main (содержит MainTab), PaymentMethods, SavedLocations, Settings, About
- **Связи:** Доступ к настройкам и вспомогательным функциям

### Схема навигации:

```
App.jsx
├── AuthStack (неавторизованные пользователи)
│   ├── LoginScreen
│   └── SignUpScreen
└── MainDrawer (авторизованные пользователи)
    ├── Main (MainTab)
    │   ├── HomeScreen
    │   ├── CartScreen
    │   ├── OrderStatusScreen
    │   └── ProfileScreen
    ├── PaymentMethodsScreen
    ├── SavedLocationsScreen
    ├── SettingsScreen
    └── AboutScreen
```

## 🛠️ Задание 2: Реализация навигации

### 1. **AuthStack** - Стек авторизации

**Файл:** `navigation/AuthStack.jsx`

**Особенности реализации:**

- Использует `createStackNavigator` для линейных переходов
- Скрытые заголовки (`headerShown: false`) для кастомного дизайна
- Простая навигация между Login и SignUp

```jsx
import { createStackNavigator } from '@react-navigation/stack';
import { SCREENS } from './ScreenNames';

const Stack = createStackNavigator();

export default function AuthStack() {
  return (
    <Stack.Navigator screenOptions={{ headerShown: false }}>
      <Stack.Screen name={SCREENS.LOGIN} component={LoginScreen} />
      <Stack.Screen name={SCREENS.SIGNUP} component={SignUpScreen} />
    </Stack.Navigator>
  );
}
```

### 2. **MainTab** - Основная табовая навигация

**Файл:** `navigation/MainTab.jsx`

**Особенности реализации:**

- `createBottomTabNavigator` для нижних табов
- Кастомные иконки с анимацией (Ionicons)
- Динамический счетчик товаров в корзине
- Адаптивная стилизация под темы
- Поддержка accessibility для web

**Ключевые компоненты:**

- **View, TouchableOpacity** - структура табов
- **Ionicons** - иконки навигации
- **Animated** - анимации счетчика корзины
- **useSelector** - подключение к Redux состоянию

```jsx
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { Ionicons } from '@expo/vector-icons';
import { Animated } from 'react-native';

const Tab = createBottomTabNavigator();

// Анимированный счетчик корзины
tabBarIcon: ({ color, size }) => {
  if (route.name === SCREENS.CART) {
    return (
      <>
        <Ionicons name="bag-outline" size={size} color={color} />
        {cartItemsCount > 0 && (
          <Animated.View style={badgeStyle}>
            <Animated.Text>{cartItemsCount}</Animated.Text>
          </Animated.View>
        )}
      </>
    );
  }
};
```

### 3. **MainDrawer** - Боковое меню

**Файл:** `navigation/MainDrawer.jsx`

**Особенности реализации:**

- `createDrawerNavigator` для бокового меню
- Содержит MainTab как основной экран
- Тематическая стилизация
- Поддержка accessibility
- Многоязычные labels

**Используемые компоненты:**

- **View** - структура drawer
- **Text** - labels пунктов меню
- **TouchableOpacity** - интерактивные элементы
- **Platform** - платформо-специфичные стили

## 🔄 Задание 3: Передача данных между экранами

### Реализованные примеры передачи параметров:

#### 1. **ProductsScreen** - Получение категории

**Файл:** `screens/ProductsScreen.jsx`

```jsx
// Получение параметра category из navigation
const ProductsScreen = ({ navigation, route }) => {
  let category = 'Все';
  if (route?.params && typeof route.params.category !== 'undefined') {
    category = route.params.category;
  }

  // Использование категории для фильтрации товаров
  const filteredProducts =
    category === 'Все'
      ? products
      : products.filter(
          (product) =>
            product.category === category || product.categoryId === category
        );
};
```

#### 2. **CartScreen** - Передача данных заказа

**Файл:** `screens/CartScreen.jsx`

```jsx
// Передача данных заказа в OrderStatus
const handleCheckout = async () => {
  // ... логика создания заказа

  navigation.navigate('OrderStatus', {
    orderId: orderResponse.data.id,
    orderData: orderResponse.data,
    isNewOrder: true,
  });
};
```

#### 3. **Categories** - Навигация с параметрами

**Файл:** `components/Categories.jsx`

```jsx
// Передача выбранной категории
const handleCategoryPress = (category) => {
  navigation.navigate(SCREENS.PRODUCTS, {
    category: category.id,
    categoryName: category.name,
  });
};
```

### Типы передаваемых параметров:

| Параметр     | Тип           | Назначение                 | Пример                 |
| ------------ | ------------- | -------------------------- | ---------------------- |
| `category`   | string/number | ID выбранной категории     | `{ category: 1 }`      |
| `orderId`    | string        | ID заказа для отслеживания | `{ orderId: "123" }`   |
| `orderData`  | object        | Полные данные заказа       | `{ orderData: {...} }` |
| `isNewOrder` | boolean       | Флаг нового заказа         | `{ isNewOrder: true }` |

## 🎨 Задание 4: Стилизация навигационных элементов

### Кастомизация заголовков и стилей

#### **Stack Navigator** стилизация:

```jsx
// AppStack.jsx - Единые стили для всех Stack экранов
<Stack.Navigator
  screenOptions={{
    headerShown: true,
    headerTitleAlign: 'center',
    headerStyle: { height: 64 },
    headerTitleStyle: {
      fontSize: 20,
      fontWeight: '500',
      textAlign: 'center',
    },
  }}
>
```

#### **Tab Navigator** кастомизация:

```jsx
// MainTab.jsx - Темизированные табы
screenOptions={{
  tabBarActiveTintColor: theme.brandColor,
  tabBarInactiveTintColor: theme.lightText,
  tabBarStyle: {
    backgroundColor: theme.backgroundColor,
    borderTopColor: theme.borderColor,
    borderTopWidth: 1,
    paddingVertical: 10,
    minHeight: 64,
  },
}}
```

#### **Drawer Navigator** стилизация:

```jsx
// MainDrawer.jsx - Тематическое боковое меню
screenOptions={{
  drawerStyle: {
    backgroundColor: theme.navigationBackgroundColor,
  },
  drawerActiveTintColor: theme.brandColor,
  drawerInactiveTintColor: theme.primaryText,
  drawerLabelStyle: {
    fontSize: 16,
  },
}}
```

### Кастомные элементы навигации:

#### 1. **Анимированный счетчик корзины**

- Использует `Animated.View` для плавных переходов
- Динамически показывает количество товаров
- Поддерживает accessibility labels

#### 2. **Иконки с состояниями**

- Разные иконки для активного/неактивного состояния
- Платформо-специфичные размеры
- Интеграция с темами приложения

#### 3. **Многоязычные labels**

- Использование Redux для переводов
- Динамическое обновление при смене языка
- Fallback на английский язык

## 📱 Задание 5: Адаптивность и тестирование

### Адаптивность по платформам:

#### **iOS специфика:**

- Стандартные iOS жесты для навигации
- Автоматическая интеграция с iOS системной навигацией
- Поддержка Safe Area для iPhone с вырезом

#### **Android особенности:**

- Материал дизайн принципы
- Поддержка аппаратной кнопки "Назад"
- Elevation для карточек и навигации

#### **Web адаптация:**

- Accessibility атрибуты (`aria-hidden: false`)
- Поддержка клавиатурной навигации
- Адаптивные размеры для desktop

### Реализованные жесты:

#### **Drawer Navigation:**

- Свайп слева направо для открытия
- Свайп справа налево для закрытия
- Tap за пределами меню для закрытия

#### **Tab Navigation:**

- Touch для переключения табов
- Анимация при смене активного таба
- Haptic feedback на iOS

### Обработка ошибок:

#### **Защита от некорректных параметров:**

```jsx
// ProductsScreen.jsx - Проверка route.params
if (route?.params && typeof route.params.category !== 'undefined') {
  category = route.params.category;
} else if (!route?.params) {
  Alert.alert('Ошибка', 'Параметр category не передан!');
}
```

#### **Fallback навигация:**

```jsx
// Безопасная навигация с проверкой
const safeNavigate = (screenName, params = {}) => {
  try {
    navigation.navigate(screenName, params);
  } catch (error) {
    console.error('Navigation error:', error);
    navigation.navigate('Home'); // Fallback
  }
};
```

## 📁 Модульная структура

### Организация навигационных файлов:

```
navigation/
├── ScreenNames.jsx      # Константы названий экранов
├── AuthStack.jsx        # Стек авторизации
├── AppStack.jsx         # Основной стек приложения
├── MainTab.jsx          # Табовая навигация
└── MainDrawer.jsx       # Drawer навигация
```

### **ScreenNames.jsx** - Константы экранов

```jsx
export const SCREENS = {
  HOME: 'Home',
  CART: 'Cart',
  PRODUCTS: 'Products',
  PROFILE: 'Profile',
  ORDER_STATUS: 'OrderStatus',
  SETTINGS: 'Settings',
  // ... другие экраны
};
```

**Преимущества использования констант:**

- ✅ Избежание опечаток в названиях экранов
- ✅ Централизованное управление роутами
- ✅ Простота рефакторинга
- ✅ TypeScript-дружественный подход

## 🎯 Соответствие требованиям Task 2

### ✅ **Задание 1** - Проектирование структуры

- **Анализ типов навигации:** Stack для авторизации, Tab для основного интерфейса, Drawer для дополнительных функций
- **Определены связи экранов:** Четкая иерархия и переходы между экранами
- **Описаны передаваемые параметры:** category, orderId, orderData, и другие

### ✅ **Задание 2** - Реализация навигации

- **Stack.Navigator:** AuthStack и AppStack для линейных переходов
- **Tab.Navigator:** MainTab для основных разделов приложения
- **Drawer.Navigator:** MainDrawer для дополнительных функций
- **Интеграция компонентов:** Все кастомные компоненты интегрированы в навигацию

### ✅ **Задание 3** - Передача данных

- **navigation.navigate():** Используется для всех переходов с параметрами
- **route.params:** Обработка и валидация во всех экранах
- **Динамический контент:** ProductsScreen фильтрует по категории из параметров

### ✅ **Задание 4** - Стилизация

- **Кастомные заголовки:** Единый стиль для всех Stack экранов
- **Стилизованные табы:** Тематические цвета и анимации
- **Иконки состояний:** Активные/неактивные состояния для всех табов

### ✅ **Задание 5** - Адаптивность

- **Кроссплатформенность:** iOS, Android, Web поддержка
- **Жесты:** Drawer свайпы, Tab touches
- **Обработка ошибок:** Валидация параметров и безопасная навигация

### ✅ **Дополнительные требования**

- **Модульность:** Все навигаторы в отдельных файлах
- **Документация:** Подробные комментарии в коде
- **Константы:** SCREENS для всех названий экранов
- **Чистота кода:** Консистентный стиль и архитектура

## 📸 Скриншоты для документации

### Рекомендуемые скриншоты навигации:

1. **`auth-stack-login.png`** - Экран входа (AuthStack)
2. **`main-tab-navigation.png`** - Нижняя табовая навигация
3. **`drawer-menu-open.png`** - Открытое боковое меню
4. **`stack-navigation-transition.png`** - Переход между экранами Stack
5. **`cart-badge-animation.png`** - Анимированный счетчик корзины
6. **`category-navigation.png`** - Навигация с передачей параметров
7. **`navigation-themes.png`** - Навигация в разных темах

### Как тестировать навигацию:

1. Запустите приложение: `npm start`
2. Протестируйте все типы переходов
3. Проверьте передачу параметров
4. Убедитесь в работе жестов
5. Протестируйте на разных платформах

---

**Проект:** Coffee Park App  
**Навигация:** React Navigation v6  
**Архитектура:** Stack + Tab + Drawer  
**Платформы:** iOS, Android, Web
