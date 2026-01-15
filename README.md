RussianStocks AI  
===================  
**Автоматизированный прогноз цен акций Мосбиржи на PyTorch + Transformer**

### 1. Что делает проект  
- Загружает ежедневные свечи с MOEX API (все акции Т+2, индекс IMOEX, USD/RUB)  
- Автоматически считает 10+ тех.индикаторов (EMA, MACD, RSI, Bollinger, ATR, Stochastic)  
- Обучает Transformer-регрессор с Optuna-подбором гиперпараметров (d_model, n_heads, lr, layers)  
- Даёт probabilistic-прогноз: mean ± 95 % доверительный интервал (100 MC-Dropout forward-проходов)  
- Отдаёт торговый сигнал «Buy / Hold / Sell» и визуализирует результат интерактивным Plotly-графиком  
- Работает как веб-приложение на Streamlit: бесплатно SBER & GAZP на 1 день, премиум ‑ 500 ₽/мес (YooKassa)  
- Поддерживает GPU-ускорение (AMP) и кэширование (SQLite + LRU + pickle) для минимального времени ответа  

### 2. Главные фичи  
| Функция | Реализация |
|---|---|
| **Данные** | Официальное MOEX REST API, 1-дневные свечи, 365 дней по умолчанию |
| **Фичи** | 14 признаков: цены, объём, волатильность, внешние факторы (MOEX-USD/RUB) |
| **Модель** | Transformer encoder → projection → MSE-loss, обучение AdamW + ReduceLROnPlateau |
| **Гиперпараметры** | Автоподбор Optuna (50 trials, MedianPruner, 4 CPU) |
| **Валидация** | Rolling origin, early-stopping по RMSE, логирование в Sentry |
| **Прогноз** | Monte-Carlo dropout, 100 сэмплов, обратный Robust-scaler, 95 % CI |
| **Визуализация** | Plotly: свечи/линия, наложение прогноза, полосы Боллинджера, MACD |
| **Масштаб** | ThreadPool + LRU-кэш, фоновое обновление данных каждый день 00:00 |
| **Безопасность** | Секреты в Streamlit Secrets, валидация тикеров и e-mail, санитизация regexp |
| **Платежи** | YooKassa (ФЗ-54 чек), Google Sheets как БД подписок, возврат 14 дней |
| **Контейнер** | Dockerfile + requirements.txt → деплой на Streamlit Cloud |

### 3. Структура репозитория  
```
├─ app.py              # Streamlit-интерфейс, логика подписки, форма прогноза  
├─ market_analyzer.py  # Классы MOEXFetcher, DataProcessor, TransformerModel, ModelTrainer  
├─ config.py           # dataclass ModelConfig (все константы и пути)  
├─ requirements.txt    # PyTorch, ta, optuna, plotly, streamlit, yookassa, gspread …  
├─ Dockerfile          # slim-bookworm, python 3.11, uvicorn-like entrypoint  
├─ README.md           # этот файл  
└─ results/            # автосоздаётся: models/, data/, analysis/  
```

### 4. Быстрый старт (локально)  
```bash
git clone https://github.com/yourname/RussianStocks_AI.git  
cd RussianStocks_AI
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt

# запустить интерфейс
streamlit run app.py
```
Откроется http://localhost:8501 – выберите тикер и получите прогноз.

### 5. Переменные окружения / Streamlit Secrets  
```
[sentry]
sentry_dsn = https://...
[yookassa]
shop_id = 123456
secret_key = test_...
[google]
credentials_json = {...}
[smtp]
smtp_server = smtp.gmail.com
smtp_port = 587
smtp_email = ...
smtp_password = ...
[access]
admin_emails = ["you@mail.ru"]
```

### 6. Точность и дисклеймер  
Средняя RMSE на hold-out 2024: 1.8 % от цены закрытия (SBER, 1-day).  
**Важно**: прогнозы не являются индивидуальной инвестиционной рекомендацией (ФЗ-39). Используйте на свой риск.

### 7. Лицензия  
MIT © 2024 Denis Veretennikov

### 8. Контакты  
veretenni@bk.ru | Telegram: @veretennikov
