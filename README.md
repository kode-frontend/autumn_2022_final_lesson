# Поздравляем!
Ты дошел до финального ~~босса~~ задания! \
В его рамках необходимо собрать флоу авторизации перед попаданием на другие разделы

Несколько важных моментов:
- **Данное задание нужно делать на основе ваших предыдущих наработок.** Потому перед началом скопируйте итог выполнения всех прошлых домашних работ в этот репозиторий.
- **Для запросов используем только Mock Server!**
- **По завершению работ необходимо отправить коммит с сообщением done и влить наработки в main.**

## Ссылки
- [Урезанная авторизация в фигме](https://www.figma.com/file/KxWS0hlyfHJNiihnxDpPfU/Skillbox-Auth-Final?node-id=33%3A35654)
- [Stoplight c запросами авторизации](https://kode-education.stoplight.io/docs/kode-bank/YXBpOjI3Nzc0MTYy-skillbox-auth-api)

## Требования к процессу и результату
В первую очередь, необходимо внимательно прочитать задание и продекомпозировать его.
Если возникнут вопросы, не стесняйся задавать их в общий чат.
Примени все полученные знания. В стэке используемых технологий должны быть: styled components, effector, react query, atomic design.

### Результат
- Приложение должно запускаться и корректно работать.
- Необходимо сверстать все ui-элементы самостоятельно, без использования сторонних библиотек.
- После прохождения авторизации, можно перейти в раздел платежей (нужно собрать из предыдущих домашних работ).

### Сроки
Работы принимаем до 5.12.22 09:00:00 UTC +3.

## Требования к авторизации

1. Изначально пользователь попадает на [экран ввода номера](https://www.figma.com/file/KxWS0hlyfHJNiihnxDpPfU/Skillbox-Auth-Final?node-id=181%3A65318) мобильного телефона. Номер телефона должен учитывать маску `+7 (XXX) XXX XX XX`. При неверном формате телефона или его отсутствии должен отображаться снек с ошибкой "Пожалуйста, убедитесь, что вы правильно ввели номер телефона"
2. После успешного ввода и нажатию на кнопку "Войти", клиент должен отправить [запрос](https://kode-education.stoplight.io/docs/kode-bank/b3A6Mjc5MTQ3Mjg-post-api-auth-otp-code) `POST /api/auth/otp_code` на получение OTP кода. Код от тестового сервера всегда будет одинаковым - **"1234"**
    1. Полученные в ответе запроса "otpId" и "otpCode" необходимо сохранить для последующего запроса подтверждения кода и валидации.
    2. При получении ошибки запроса, пользователь должен быть перенаправлен на [экран ошибки](https://www.figma.com/file/KxWS0hlyfHJNiihnxDpPfU/Skillbox-Auth-Final?node-id=77%3A56748). При нажатии на кнопку "Повторить" или крест в левом верхнем углу пользователь должен вернуться на экран ввода телефона (пункт 1)
3. При получении успешного ответа предыдущего запроса `POST /api/auth/otp_code`, пользователь должен перенаправится на экран ввода OTP c вариантом поля для 4-х символов.
    1. При попадании на экран запускается таймер в 3 минуты, по истечению которого пользователь может нажать на кнопку "Выслать код повторно" и заново отправить [запрос](https://kode-education.stoplight.io/docs/kode-bank/b3A6Mjc5MTQ3Mjg-post-api-auth-otp-code) `POST /api/auth/otp_code`.
    2. Если код был введён неверно, поле ввода должно подсвечиваться красным и под ним выводится предупреждающее сообщение "Неверный код. Осталось X попыток". Максимальное количество попыток - 5.
    3. Если пользователь 5 раз неправильно ввёл OTP, ему показывается нативный alert с текстом о завершении сессии и кнопкой "Выход", по нажатию которой происходит перенаправление на начальный экран ввода телефона.
    4. При получении ошибки запроса, пользователь должен быть перенаправлен на [экран ошибки](https://www.figma.com/file/KxWS0hlyfHJNiihnxDpPfU/Skillbox-Auth-Final?node-id=77%3A56748). При нажатии на кнопку "Повторить" или крест в левом верхнем углу пользователь должен вернуться на экран ввода кода (пункт 3.1)
4. После успешного ввода OTP-кода клиент должен [отправить запрос](https://kode-education.stoplight.io/docs/kode-bank/b3A6Mjc3NzQxODE-post-api-auth-confirm) `POST /api/auth/confirm` и показать экран загрузки.
    1. При успешном выполнении запроса необходимо локально сохранить guestToken из ответа при помощи effector.
    2. При получении ошибки запроса, пользователь должен быть перенаправлен на [экран ошибки](https://www.figma.com/file/KxWS0hlyfHJNiihnxDpPfU/Skillbox-Auth-Final?node-id=77%3A56748). При нажатии на кнопку "Повторить" или крест в левом верхнем углу пользователь должен вернуться на экран ввода кода (пункт 3.1)
5. По завершению запроса на получение гостевого токена, пользователь попадает на экран ввода пароля. Паролем должна быть строка длиной больше 5 символов, содержащая только цифры и буквы.
    1. Если введённый пароль будет меньше, необходимо показать снек с текстом "Длина пароля должна быть не менее 5 символов".
    2. Если введённый пароль будет содержать специальные символы, необходимо показать снек с текстом "Пароль может содержать только цифры и буквы".
    3. Пользователь может скрывать и отображать по нажатию на иконку глаза в инпуте.
    4. Пользователь может выйти по нажатию на крест в левом верхнем углу экрана. При нажатии показывается системный alert с предупреждением и кнопками "Отмена", "Выйти". Если пользователь нажимает "Выйти", его переносит в самое начало на экран ввода номера телефона (пункт 1)
6. При успешной валидации пароля и нажатию на кнопку "Войти", клиенту необходимо [отправить запрос](https://kode-education.stoplight.io/docs/kode-bank/b3A6Mjc3NzQxODI-post-api-auth-enter) `POST /api/auth/enter`. Полученные accessToken и refreshToken необходимо сохранить локально при помощи effector.
    1. При получении ошибки запроса пользователь должен быть перенаправлен на [экран ошибки](https://www.figma.com/file/KxWS0hlyfHJNiihnxDpPfU/Skillbox-Auth-Final?node-id=77%3A56748). При нажатии на кнопку "Повторить" или крест в левом верхнем углу пользователь должен вернуться на экран ввода пароля (пункт 5)
7. При успешном получении и сохранении токенов, необходимо перенаправить пользователя на экран "Все готово"
8. По нажатию на кнопку "Продолжить" происходит перенаправление на раздел "Платежи"

## Как проверять ошибки от сервера

Чтобы убедиться в корректном поведении клиента при возникновении ошибок, вы можете в запросе передать заголовок `"Prefer": "code=500, dynamic=true"`. Где в `code` передаётся желаемый HTTP код.

Например:
```tsx
fetch("https://stoplight.io/mocks/kode-education/kode-bank/27774162/api/auth/login", {
  "method": "POST",
  "headers": {
    "Content-Type": "application/json",
    "Prefer": "code=500, dynamic=true"
  },
  "body": "{\"phone\":\"+79115552211\"}"
})
``` 

## Работа с масками инпута

Для создания телефонной маски инпута на экране ввода телефона, можно использовать библиотеки на ваш вкус или из этого списка:
- https://github.com/akinncar/react-native-mask-text
- https://github.com/uNmAnNeR/imaskjs/tree/master/packages/react-native-imask

## Удачи!
