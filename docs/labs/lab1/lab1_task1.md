# Лабораторная работа №1. Работа с сокетами
[ ← Вернуться к заданиям](lab_1.md){ .md-button }  
#


## Задание №1
### Текст работы:
Реализовать клиентскую и серверную часть приложения.   
1. Клиент отсылает серверу сообщение «Hello, server». Сообщение должно отразиться на стороне сервера.  
2. Сервер в ответ отсылает клиенту сообщение «Hello, client». Сообщение должно
отобразиться у клиента.

- Обязательно использовать библиотеку socket
- Реализовать с помощью протокола UDP

### Код:

```py title="server.py"
# Server
# UDP 
import socket

# Создаем сокет для UDP
server_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# Устанавливаем адрес и порт для сервера
server_address = ('localhost', 12345)

# Привязываем сокет к адресу и порту
server_socket.bind(server_address)

print("Сервер запущен и ожидает сообщение...")

while True:
    # Получаем данные и адрес клиента
    data, client_address = server_socket.recvfrom(1024)
    
    # Выводим полученное сообщение
    print(f"Получено сообщение от клиента: {data.decode('utf-8')}")

    # Отправляем ответ клиенту
    response_message = "Hello, client"
    server_socket.sendto(response_message.encode('utf-8'), client_address)
```

```py title="client.py"
# Client
import socket

# Создаем сокет для UDP
client_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# Адрес и порт сервера, к которому мы подключаемся
server_address = ('localhost', 12345)

# Сообщение, которое мы отправляем серверу
message = "Hello, server"

# Отправляем сообщение серверу
client_socket.sendto(message.encode('utf-8'), server_address)

# Получаем ответ от сервера
response, _ = client_socket.recvfrom(1024)

# Выводим полученный ответ
print(f"Получено сообщение от сервера: {response.decode('utf-8')}")

# Закрываем сокет клиента
client_socket.close()

```

### Результат:
<image src="../imgs/task1_1.png">
<image src="../imgs/task1_2.png">