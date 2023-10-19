# Лабораторная работа №1. Работа с сокетами
[ ← Вернуться к заданиям](lab_1.md){ .md-button }  
#


## Задание №5
### Текст работы:
Необходимо написать простой web-сервер для обработки `GET` и `POST` **http**-запросов средствами **Python** и библиотеки `socket`.


### Код:
```py title="server.py"
import socket

grades = {}


class MyHTTPServer:
    def __init__(self, host, port):
        self.host = host
        self.port = port

    def serve_forever(self):
        serv_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

        try:
            serv_sock.bind((self.host, self.port))
            serv_sock.listen()
            print(f"Сервер ожидает подключения на {self.host}:{self.port}")

            while True:
                conn, client_address = serv_sock.accept()
                print(f"Подключен клиент с адресом: {client_address}")
                try:
                    self.serve_client(conn)
                except Exception as e:
                    print('Fail', e)
        finally:
            serv_sock.close()

    def serve_client(self, client):
        try:
            req = self.parse_request(client)
            resp = self.handle_request(req)
            self.send_response(client, resp)
        except ConnectionResetError:
            client = None

        if client:
            client.close()

    def parse_request_line(self, rfile):
        line = rfile.readline()
        line = line.decode('utf-8')
        return line.split()

    def parse_request(self, conn):
        rfile = conn.makefile('rb')
        method, target, ver = self.parse_request_line(rfile)

        request = {'data': {}, 'method': method}
        if '?' in target:
            request['method'] = 'POST'
            values = target.split('?')[1].split('&')
            for value in values:
                a, b = value.split('=')
                request['data'][a] = b

        return request

    def handle_request(self, req):
        if req['method'] == 'POST':
            return self.handle_post(req)
        else:
            return self.handle_get()

    def handle_get(self):
        content_type = 'text/html; charset=utf-8'
        body = '<html><head><style></style></head><body>'
        body += '<form><label>Subject</label><input name="discipline" /><br><br><label>Grade</label><input name="grade"/><br><br><input type="submit"></form>'
        for subject in grades:
            body += f'<div><span>{subject}: {grades[subject]}</span></div>'
        body += '</div></body></html>'
        body = body.encode('utf-8')
        headers = [('Content-Type', content_type),
                   ('Content-Length', len(body))]
        return Response(200, 'OK', headers, body)

    def handle_post(self, request):
        discipline = request['data']['discipline']
        grade = request['data']['grade']

        if discipline not in grades:
            grades[discipline] = []

        grades[discipline].append(grade)

        return self.handle_get()

    def send_response(self, conn, resp):
        rfile = conn.makefile('wb')
        status_line = f'HTTP/1.1 {resp.status} {resp.reason}\r\n'
        rfile.write(status_line.encode('utf-8'))

        if resp.headers:
            for (key, value) in resp.headers:
                header_line = f'{key}: {value}\r\n'
                rfile.write(header_line.encode('utf-8'))

        rfile.write(b'\r\n')

        if resp.body:
            rfile.write(resp.body)

        rfile.flush()
        rfile.close()


class Response:
    def __init__(self, status, reason, headers=None, body=None):
        self.status = status
        self.reason = reason
        self.headers = headers
        self.body = body


if __name__ == '__main__':
    serv = MyHTTPServer('localhost', 8080)
    serv.serve_forever()
```

1. **`MyHTTPServer`** - это класс, который представляет  HTTP-сервер. В его конструкторе инициализируются хост и порт, на которых сервер будет слушать входящие подключения.
2. **`serve_forever`** - это метод, который выполняет основной цикл сервера. Он создает сокет, привязывается к заданному хосту и порту, затем начинает слушать входящие соединения и обрабатывать их.
3. **`serve_client`** - метод для обслуживания клиента. Он принимает сокет клиента в качестве аргумента. Внутри этого метода выполняется обработка запросов клиента.
4. **`parse_request_line`** - метод, который разбирает первую строку HTTP-запроса, полученного от клиента. Он возвращает список слов из этой строки.
5. **`parse_request`** - метод, который разбирает HTTP-запрос, полученный от клиента, и возвращает словарь `request` с информацией о методе запроса (`GET` или `POST`) и данными запроса, если они есть. Если запрос содержит данные, они разбираются и добавляются в словарь `request['data']`.
6. **`handle_request`** - метод, который обрабатывает запрос. В зависимости от метода запроса (GET или POST), он вызывает соответствующий метод обработки.
7. **`handle_get`** - метод для обработки GET-запросов. Он формирует HTML-страницу, включая форму для ввода данных и список оценок, и отправляет эту страницу клиенту в ответе.
8. **`handle_post`** - метод для обработки POST-запросов. Он получает данные из запроса, добавляет оценку в словарь `grades` и затем вызывает `handle_get` для обновления страницы с новым списком оценок.
9. **`send_response`** - метод для отправки ответа клиенту. Он формирует HTTP-ответ с указанным кодом состояния, заголовками и телом ответа, затем отправляет его клиенту.
10. **`Response`** - это класс, который представляет HTTP-ответ. Он содержит код состояния, текстовое описание, заголовки и тело ответа.
11. В основном блоке кода создается экземпляр класса `MyHTTPServer` и запускается метод `serve_forever`, который начинает прослушивание входящих соединений и обработку запросов.


### Результат:


<image src="../imgs/task5_1.png">
<image src="../imgs/task5_2.png">

