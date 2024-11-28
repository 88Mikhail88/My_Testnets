

```bash
#!/bin/bash

# Параметры
WALLET_ADDRESS="ADDRESS" # Заменить на свой адрес 
VALIDATOR_ADDRESS="VALIDATOR_ADDRESS" # Заменить на свой адрес
CHAIN_ID="axone-dentrite-1"
FEE_AMOUNT="5000uaxone"
MIN_BALANCE="10000uaxone" # Минимальный баланс для покрытия комиссий
DENOM="uaxone"
PAUSE_BETWEEN_TX=10 # Пауза между транзакциями в секундах

while true; do
    echo "====== Старт цикла: $(date) ======"

    # Проверка баланса
    BALANCE=$(axoned q bank balances "$WALLET_ADDRESS" --output json | jq -r ".balances[] | select(.denom==\"$DENOM\") | .amount")

    if [[ -z "$BALANCE" || "$BALANCE" -lt "${MIN_BALANCE%uaxone}" ]]; then
        echo "Недостаточно средств для покрытия комиссии. Баланс: $BALANCE $DENOM"
        sleep 7200
        continue
    fi

    # Сбор всех ревардов
    echo "Сбор ревардов..."
    axoned tx distribution withdraw-all-rewards --from wallet --chain-id "$CHAIN_ID" --fees "$FEE_AMOUNT" --keyring-backend test -y
    sleep "$PAUSE_BETWEEN_TX"

    # Сбор комиссий и ревардов со своего валидатора
    echo "Сбор комиссий и ревардов с валидатора..."
    axoned tx distribution withdraw-rewards "$VALIDATOR_ADDRESS" --chain-id "$CHAIN_ID" --from wallet --fees "$FEE_AMOUNT" --commission --keyring-backend test -y
    sleep "$PAUSE_BETWEEN_TX"

    # Обновление баланса после сбора наград
    BALANCE=$(axoned q bank balances "$WALLET_ADDRESS" --output json | jq -r ".balances[] | select(.denom==\"$DENOM\") | .amount")

    # Проверка доступного баланса для делегирования
    AMOUNT_TO_DELEGATE=$((BALANCE - ${MIN_BALANCE%uaxone}))
    if [[ "$AMOUNT_TO_DELEGATE" -gt 0 ]]; then
        echo "Делегирование $AMOUNT_TO_DELEGATE uaxone валидатору $VALIDATOR_ADDRESS..."
        axoned tx staking delegate "$VALIDATOR_ADDRESS" "${AMOUNT_TO_DELEGATE}${DENOM}" --chain-id "$CHAIN_ID" --from wallet --fees "$FEE_AMOUNT" --keyring-backend test -y
        sleep "$PAUSE_BETWEEN_TX"
    else
        echo "Недостаточно средств для делегирования после резервирования на комиссию."
    fi

    # Ожидание 2 часа
    echo "Ожидание 2 часа..."
    sleep 7200
done
```
### Объяснение:
Переменные:

- WALLET_ADDRESS: адрес вашего кошелька.
- VALIDATOR_ADDRESS: адрес вашего валидатора.
- CHAIN_ID: идентификатор цепочки Cosmos.
- FEE_AMOUNT: комиссия для транзакций.
- MIN_BALANCE: минимальный баланс, который необходимо оставить для оплаты комиссий.
- DENOM: валюта (uaxone).
- Переменная PAUSE_BETWEEN_TX=10: Указывает паузу в секундах между транзакциями (по умолчанию 10 секунд).
- Добавление sleep: После каждой транзакции добавлен вызов sleep "$PAUSE_BETWEEN_TX" для обеспечения задержки.

Основной процесс:

- Проверяется баланс кошелька.
- Собираются реварды и комиссии.
- После обновления баланса оставшаяся сумма делегируется валидатору, за вычетом минимального остатка на комиссии.
- Скрипт ждет 2 часа и запускается снова.

### Запуск скрипта:

```bash
# Убедись, что jq установлен:
sudo apt-get install jq

# Создайте файл
nano collect_and_delegate.sh # Скопируйте в него отредактированный скрипт

# Сделай скрипт исполняемым:
chmod +x collect_and_delegate.sh

# Запусти скрипт:
./collect_and_delegate.sh
```

### Чтобы открыть сессию в screen и запустить скрипт в фоновом режиме, следуй этим шагам:

Открыть новую сессию screen:

```bash

screen -S new_session # Здесь new_session — это имя сессии. Можно выбрать любое удобное имя.

# Запустить скрипт: Перейди в директорию, где находится скрипт, и запусти его:
./collect_and_delegate.sh

# Отключиться от сессии (не завершая её): Нажмите комбинацию клавиш:
Ctrl + A, затем D # Это выведет из сессии, но она продолжит работать в фоне.

# Как вернуться к сессии:
# Чтобы вернуться к запущенной сессии:
screen -r new_session

# Если есть несколько сессий, можно увидеть их список командой:
screen -ls # Затем присоединиться к нужной по её идентификатору:
screen -r <session_id>

# Завершение сессии:
# Чтобы полностью закрыть сессию (и завершить все процессы внутри неё):

Вернись в сессию командой screen -r.
Завершите скрипт (Ctrl + C) или закройте сессию командой exit.
```
