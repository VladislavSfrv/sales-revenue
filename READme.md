Проектная работа по JavaScript - Обработка данных продаж
Цель проекта
Рассчитать бонусы для продавцов и метрики эффективности по итогам года на основе данных интернет-магазина.

Формат данных на входе
javascript
const data = {
  customers: [{id: "customer_1", first_name: "Andrey", ...}],
  products: [{sku: "SKU_001", purchase_price: 460.34, sale_price: 699.99, ...}],
  sellers: [{id: "seller_1", first_name: "Alexey", ...}],
  purchase_records: [{
    receipt_id: "receipt_1",
    seller_id: "seller_5",
    customer_id: "customer_1",
    items: [{sku: "SKU_027", discount: 7.68, quantity: 1, sale_price: 919.07}],
    total_amount: 4657.56,
    total_discount: 271.71
  }]
};
Формат результата
javascript
[{
  seller_id: 'seller_1',
  name: 'Alexey Petrov',
  revenue: 123456,      // Общая выручка с учётом скидок
  profit: 12345,        // Прибыль от продаж
  sales_count: 20,      // Количество продаж
  top_products: [       // Топ-10 проданных товаров
    {sku: 'SKU_001', quantity: 12},
  ],
  bonus: 1234,          // Итоговый бонус в рублях
}];
Ключевые функции для реализации
1. calculateSimpleRevenue(purchase, _product)
Расчёт выручки от продажи одного товара с учётом скидки:

Скидка в процентах → десятичное число: discount / 100

Выручка = sale_price × quantity × (1 - discount/100)

2. calculateBonusByProfit(index, total, seller)
Расчёт бонуса по методике:

15% от прибыли — 1 место

10% от прибыли — 2 и 3 места

5% от прибыли — все кроме последнего

0% — последнее место

3. analyzeSalesData(data, options)
Главная функция с шагами:

Проверка входных данных

Проверка опций (наличие calculateRevenue и calculateBonus)

Подготовка статистики по продавцам

Создание индексов для быстрого доступа

Обработка всех чеков и подсчёт метрик

Сортировка продавцов по прибыли

Назначение бонусов

Формирование итогового отчёта

Основные шаги обработки
Подготовка статистики продавцов
javascript
const sellerStats = data.sellers.map(seller => ({
  id: seller.id,
  name: `${seller.first_name} ${seller.last_name}`,
  revenue: 0,
  profit: 0,
  sales_count: 0,
  products_sold: {}  // {sku: quantity}
}));
Обработка чеков
Для каждого чека:

Найти продавца через sellerIndex[record.seller_id]

Увеличить sales_count на 1

Добавить total_amount к revenue

Для каждого товара в чеке:

Найти товар через productIndex[item.sku]

Рассчитать себестоимость: purchase_price × quantity

Рассчитать выручку через calculateRevenue

Рассчитать прибыль: выручка - себестоимость

Добавить к общей прибыли продавца

Учесть количество проданного товара в products_sold

Формирование топ-10 товаров
javascript
seller.top_products = Object.entries(seller.products_sold)
  .map(([sku, quantity]) => ({sku, quantity}))
  .sort((a, b) => b.quantity - a.quantity)
  .slice(0, 10);
  
Проверки
Проверить, что данные не пустые: !data

Проверить, что массивы не пустые: data.sellers.length === 0

Проверить наличие опций и функций расчёта