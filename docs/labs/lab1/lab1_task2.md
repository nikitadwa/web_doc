# Лабораторная работа №1. Работа с сокетами
[ ← Вернуться к заданиям](lab_1.md){ .md-button }  
#


## Задание №2
### Текст работы:
Реализовать клиентскую и серверную часть приложения.   
1. Клиент запрашивает у сервера выполнение математической операции, параметры, которые вводятся с клавиатуры.   
2. Сервер обрабатывает полученные данные и возвращает результат клиенту.  

- Вариант: Поиск площади параллелограмма.
#

### Код:
```py title="server.py"
# Server 
# Поиск площади параллелограмма
import socket

# Создаем сокет для TCP
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Устанавливаем адрес и порт для сервера
server_address = ('localhost', 12345)

# Привязываем сокет к адресу и порту
server_socket.bind(server_address)

# Ожидаем соединения клиента
server_socket.listen(1)
print("Сервер ожидает подключения клиента...")

while True:
    # Принимаем входящее соединение
    client_socket, client_address = server_socket.accept()
    print(f"Подключен клиент с адресом: {client_address}")

    try:
        # Получаем данные от клиента
        data = client_socket.recv(1024).decode('utf-8')
        print(f"Получено сообщение от клиента: {data}")

        # Разбираем параметры, например, стороны a и h
        a, h = map(float, data.split(','))

        # Выполняем операцию для расчета площади параллелограмма
        result = a * h

        # Отправляем результат клиенту
        response_message = f"Площадь параллелограмма: {result}"
        client_socket.send(response_message.encode('utf-8'))

    except Exception as e:
        print(f"Ошибка при обработке запроса: {e}")

    finally:
        # Закрываем соединение с клиентом
        client_socket.close()
```

```py title="client.py"
# Client 
# Поиск площади параллелограмма
import socket

# Создаем сокет для TCP
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Адрес и порт сервера, к которому мы подключаемся
server_address = ('localhost', 12345)

# Подключаемся к серверу
client_socket.connect(server_address)

try:
    # Вводим параметры с клавиатуры (стороны a и h)
    a = float(input("Введите сторону a: "))
    h = float(input("Введите высоту h: "))

    # Отправляем параметры серверу
    message = f"{a},{h}"
    client_socket.send(message.encode('utf-8'))

    # Получаем ответ от сервера
    response = client_socket.recv(1024).decode('utf-8')
    print(f"Ответ от сервера: {response}")

except Exception as e:
    print(f"Ошибка при обмене данными с сервером: {e}")

finally:
    # Закрываем сокет клиента
    client_socket.close()
```



### Результат:

<image src="../imgs/task2_1.png">
<image src="../imgs/task2_2.png">