# Dependency direction

Как в родителе. Специфика: bot — **клиент** платформы, а не сервер: он ни к кому не
принимает вызовов (кроме Health), только сам ходит в gateway (HTTP) и streaming (WS).
