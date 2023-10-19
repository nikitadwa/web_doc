# Лабораторная работа №1. Работа с сокетами
[ ← Вернуться к заданиям](lab_1.md){ .md-button }  
#


## Задание №4
### Текст работы:
Реализовать двухпользовательский или многопользовательский чат.   
Реализация многопользовательского часа позволяет получить максимальное количество баллов.  

- Реализовать с помощью протокола TCP.   
- Обязательно использовать библиотеку `threading`.  

Для применения с TCP необходимо запускать клиентские подключения И прием
и отправку сообщений всем юзерам на сервере в потоках. Не забудьте сохранять юзеров,
чтобы потом отправлять им сообщения.

### Код:

```py title="server.py"
# Server

import socket
import threading

# Словарь для хранения подключенных клиентов и их соединений
clients = {}

# Создаем сокет для TCP
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Устанавливаем адрес и порт для сервера
server_address = ('localhost', 8080)

# Привязываем сокет к адресу и порту
server_socket.bind(server_address)

# Ожидаем соединения клиентов
server_socket.listen(5)
print("Сервер ожидает подключения клиентов...")

# Функция для обработки сообщений от клиента
def handle_client(client_socket, client_name):
    while True:
        try:
            # Получаем сообщение от клиента
            message = client_socket.recv(1024).decode('utf-8')
            if not message:
                break
            
            # Отправляем сообщение всем подключенным клиентам, кроме отправителя
            for name, socket in clients.items():
                if name != client_name:
                    socket.send(f"{client_name}: {message}".encode('utf-8'))

        except Exception as e:
            print(f"Ошибка при обработке сообщения от {client_name}: {e}")
            break

    # Удаляем клиента из словаря
    del clients[client_name]
    print(f"{client_name} отключился.")
    client_socket.close()

# Основной цикл сервера
while True:
    # Принимаем входящее соединение
    client_socket, client_address = server_socket.accept()
    print(f"Подключен клиент с адресом: {client_address}")

    # Запрашиваем имя клиента
    client_name = client_socket.recv(1024).decode('utf-8')
    print(f"Клиент {client_name} присоединился к чату.")
    
    # Добавляем клиента и его соединение в словарь
    clients[client_name] = client_socket

    # Запускаем поток для обработки сообщений от клиента
    client_thread = threading.Thread(target=handle_client, args=(client_socket, client_name))
    client_thread.start()
```
```py title="clients.py"
# Client

import socket
import threading

# Функция для чтения сообщений от сервера
def receive_messages(client_socket):
    while True:
        try:
            message = client_socket.recv(1024).decode('utf-8')
            print(message)
        except Exception as e:
            print(f"Ошибка при получении сообщения от сервера: {e}")
            break

# Создаем сокет для TCP
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Адрес и порт сервера, к которому мы подключаемся
server_address = ('localhost', 8080)

# Подключаемся к серверу
client_socket.connect(server_address)

# Вводим имя пользователя
username = input("Введите ваше имя: ")
client_socket.send(username.encode('utf-8'))

# Запускаем поток для приема сообщений от сервера
receive_thread = threading.Thread(target=receive_messages, args=(client_socket,))
receive_thread.start()

# Основной цикл для отправки сообщений
while True:
    message = input()
    if message.lower() == 'exit':
        break
    client_socket.send(message.encode('utf-8'))

# Закрываем сокет клиента
client_socket.close()
```

### Результат:
<image src="../imgs/task4_1.png">
<image src="../imgs/task4_2.png">