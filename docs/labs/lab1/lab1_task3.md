# Лабораторная работа №1. Работа с сокетами
[ ← Вернуться к заданиям](lab_1.md){ .md-button }  
#


## Задание №3
### Текст работы:
Реализовать серверную часть приложения.    
1. Клиент подключается к серверу.  
2. В ответ клиент получает http-сообщение, содержащее html-страницу, которую сервер подгружает из файла index.html.  


### Код:

```html title="index.html"
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
<div>
    <h1>Hello, Client!</h1>
</div>
</body>
</html>
```


```py title="server.py"
# Server
import socket

# Создаем сокет для TCP
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Устанавливаем адрес и порт для сервера
server_address = ('localhost', 8080)

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
        # Читаем содержимое файла index.html
        with open("index.html", "r") as file:
            html_content = file.read()

        # Отправляем HTTP-заголовок и HTML-страницу клиенту
        http_response = f"HTTP/1.1 200 OK\nContent-Length: {len(html_content)}\n\n{html_content}"
        client_socket.send(http_response.encode('utf-8'))

    except Exception as e:
        print(f"Ошибка при обработке запроса: {e}")

    finally:
        # Закрываем соединение с клиентом
        client_socket.close()
```


### Результат:
<image src="../imgs/task3_1.png">
<image src="../imgs/task3_2.png">