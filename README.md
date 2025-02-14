# monitoring-cryptocurrency-rates
Script for monitoring cryptocurrency rates
import requests
import time
from twilio.rest import Client

# Настройки Twilio для отправки SMS
account_sid = 'YOUR_ACCOUNT_SID'
auth_token = 'YOUR_AUTH_TOKEN'
client = Client(account_sid, auth_token)

def get_current_price(coin):
    url = f'https://api.coindesk.com/v1/bpi/currentprice/{coin}.json'
    response = requests.get(url)
    if response.status_code == 200:
        data = response.json()
        return float(data['bpi'][coin]['rate_float'])
    else:
        print(f'Ошибка при получении курса {coin}: {response.status_code}')
        return None

def send_sms(message):
    message = client.messages.create(
        body=message,
        from_='YOUR_TWILIO_NUMBER',
        to='YOUR_PHONE_NUMBER'
    )
    print(f'SMS отправлено: {message.sid}')

if __name__ == '__main__':
    while True:
        price = get_current_price('USD')
        if price is not None and price > 50000:
            send_sms(f'Курс Биткойна превысил $50,000! Текущий курс: ${price:.2f}')
        time.sleep(60 * 10)  # Проверять каждые 10 минут
