# Домашнее задание к занятию «3.2. Ассиметричные криптосистемы» `Пономарев Денис`

## Задача LibreSSL & OpenSSL

### Задача

В этой задаче мы научимся шифровать данные с помощью публичного ключа и расшифровывать с помощью приватного.

Первое, с чего мы должны начать - это сгенерировать ключ. Для этого нужно выбрать алгоритм, размер ключа и passphrase (кодовую фразу).

#### Шаг 1. Создайте закрытый ключ с длиной 2048:

```shell script
openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -aes256 -out private.key
```

Где [`genpkey`](https://www.openssl.org/docs/manmaster/man1/openssl-genpkey.html) - подкоманда, отвечающая за генерацию ключей.

Введите кодовую фразу два раза (обратите внимание, символы не будут отображаться в целях безопасности).

#### Шаг 2. Сгенерируйте открытый ключ на базе закрытого:

```shell script
openssl pkey -pubout -in private.key -out public.key
```

Где [`pkey`](https://www.openssl.org/docs/man1.0.2/man1/pkey.html) - подкоманда, отвечающая за обработку ключей.

#### Шаг 3. Создайте файл message.txt со своей фамилией

#### Шаг 4. Зашифруйте сообщение с помощью публичного ключа:

```shell script
openssl pkeyutl -encrypt -pubin -inkey public.key -in message.txt -out cypher.txt
```

Где [`pkeyutl`](https://www.openssl.org/docs/manmaster/man1/openssl-pkeyutl.html) - подкоманда, отвечающая за низкоуровневые операции с публичными ключами (включая шифрование и расшифрование).

Откройте файл `cypher.txt` в любом текстовом редакторе, удостоверьтесь, что он не читабелен.

#### Шаг 5. Убедитесь, что файл `cyper.txt` расшифровывается:

```shell script
openssl pkeyutl -decrypt -inkey private.key -in cypher.txt -out decrypted.txt
```

### Результат

В качестве результата пришлите:

1. Публичный и приватный ключ (`public.key` и `private.key`)
2. Passphrase (строкой)
3. Зашифрованный файл (`cypher.txt`)

#### Ответ

1. Публичный и приватный ключ ([public.key](<https://github.com/ddponomarev/oib9/blob/main/img/public.key>) и [private.key](<https://github.com/ddponomarev/oib9/blob/main/img/private.key>))
2. Passphrase (`1234`)
3. Зашифрованный файл ([cypher.txt](<https://github.com/ddponomarev/oib9/blob/main/img/cypher.txt>))

![Рисунок 1.](https://github.com/ddponomarev/oib9/blob/main/img/s1.png)

![Рисунок 2.](https://github.com/ddponomarev/oib9/blob/main/img/s2.png)

## Задача GnuPG: подпись сообщения

В этом ДЗ мы научимся подписывать и проверять подпись документов с помощью [GnuPG](https://gnupg.org) - популярной реализации [OpenPGP](https://www.ietf.org/rfc/rfc4880.txt). В частности, она используется в большом количестве Open Source проектов для подписи пакетов с ПО.

#### Шаг 1. Генерация keypair

```shell script
gpg --full-generate-key
```

В диалоговом окне:
1. Выберите тип ключа: `RSA и RSA`
2. Размер ключа: `2048`
3. Срок действия ключа - `0` (не ограничен)

Далее введите:
1. Имя (ваше реальное имя)
2. Email
3. Комментарий оставьте пустым

Для примера (вам не нужно писать `coursar` и `coursar@localhost` - ДЗ будет отправлено на доработку):

![](pic/gpg-generate-keys.png)

После чего вас попросят ввести passphrase для защиты ключа.

#### Шаг 2. Просмотр ключей

```shell script
gpg --list-keys
# либо в коротком формате:
gpg -k
```

Удостоверьтесь, что то, что вы сгенерировали на предыдущем шаге, есть в списке:

![](pic/gpg-list-keys.png)

#### Шаг 3. Подпись сообщения

Создайте текстовый файл с сегодняшней датой в любом формате и сохраните его в файле `message.txt`.

Подпишите его следующей командой (подпись будет создана в виде отдельного файла):

```shell script
gpg --output message.sig --local-user coursar@localhost --detach-sign message.txt
```

Где `coursar@localhost` - это email, который вы указали на первом шаге.

Проверьте, что подпись соответствует сообщению с помощью команды:

```shell script
gpg --verify message.sig message.txt
```

![](pic/gpg-sign-and-verify.png)

#### Шаг 4. Проверка подписи (ложное сообщение)

Создайте текстовый файл `modified.txt` и измените в нём дату на вчерашнюю.

Удостоверьтесь, что проверка подписи теперь не проходит:

```shell script
gpg --verify message.sig modified.txt
```

![](pic/gpg-verify.png)

#### Шаг 5. Экспорт публичного ключа

Для того, чтобы остальные пользователи могли проверять подпись ваших сообщений, вы должны им предоставить ваш публичный ключ.

Для этого нужно его экспортировать с помощью следующей команды:

```shell script
gpg --output public.gpg --armor --export coursar@localhost
```

Где `coursar@localhost` - это email, который вы указали на первом шаге.

По умолчанию, ключ экспортируется в бинарном формате. Опция `--armor` позволяет экспортировать его в текстовом виде.

### Результат

В качестве результата пришлите:
1. Публичный ключ (с шага 5)
2. Файл сообщения и подпись к нему (с шага 3)

#### Ответ

1. Публичный ключ ([public.gpg](<https://github.com/ddponomarev/oib9/blob/main/img/public.gpg>))
2. Файл сообщения и подпись к нему ([файл](<https://github.com/ddponomarev/oib9/blob/main/img/message2.txt>) [подпись](<https://github.com/ddponomarev/oib9/blob/main/img/message.sig>))

![Рисунок 3.](https://github.com/ddponomarev/oib9/blob/main/img/s3.png)
![Рисунок 4.](https://github.com/ddponomarev/oib9/blob/main/img/s4.png)

## Задача GnuPG: decrypt*

Общая логика работы с OpenPGP выглядит следующим образом: с помощью публичного ключа получателя шифруется сообщение (расшифровать его можно только с помощью приватного ключа, который должен быть у вас). Про подпись вы уже знаете из предыдущего ДЗ.

Список ключей:
1. Приватный ключ получателя [`student.key`](assets/student.key)
2. Публичный ключ отправителя [`netology.gpg`](assets/netology.gpg).

Приватный ключ нужен для того, чтобы вы могли расшифровать сообщение.

Сейчас мы эмулируем ситуацию, что приватный ключ каким-то образом попал к вам (а ещё, вот удача, вам стал известен passphrase), поэтому теперь вы можете попытаться расшифровать сообщение.

#### Шаг 1. Импортируйте ключи

Для этого выполните следующую команду:

```shell script
gpg --import netology.gpg
gpg --import student.key
```

<details>
<summary>Passphrase для student.key 😈</summary>

Passphrase: `student`.

<details>
<summary>Неправильная passphrase 😈?</summary>

Такое бывает. Достаточно часто для "стойкости" пароли набирают на другой раскладке (или просто забывают переключить раскладку). Попробуйте `student` в русской раскладке.

Если уж совсем не получилось, то держите скрипт, который всё сделает за вас:

<details>
<summary>Импорт ключа с вводом passphrase</summary>

```shell script
echo ыегвуте | gpg --batch --yes --import student.key
```

</details>

</details>
</details>

#### Шаг 2. Убедитесь, что ключи импортированы

```shell script
gpg --list-keys
```

#### Шаг 3. Расшифруйте сообщение и проверьте подпись

```shell script
gpg --decrypt message.enc
```

Где [`message.enc`](assets/message.enc) - это зашифрованный файл, который вам прислал владелец [`netology.gpg`](assets/netology.gpg).

### Результат

В качестве результата пришлите:
1. Содержимое зашифрованного сообщения (текст)
2. Результат проверки подписи (валидна или нет)

#### Ответ

1. Содержимое зашифрованного сообщения (`1984`)
2. Результат проверки подписи (`подпись валидна`)

![Рисунок 5.](https://github.com/ddponomarev/oib9/blob/main/img/s5.png)
