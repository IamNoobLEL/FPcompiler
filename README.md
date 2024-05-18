# Функциональное программирование: Пишем компилятор!

Цель этой лабораторной работы - придумать свой собственный функциональный язык программирования и разработать для него интерпретатор или компилятор.

Вы можете выполнить эту лабораторную работу в группе из 2 или 3 человек (или больше - но это требует одобрения преподавателя):

* 2 человека - компилятор/интерпретатор + примеры программ + краткая документация в README.md (вы можете заменить этот файл своей собственной документацией)
* 3 человека - компилятор/интерпретатор + примеры программ + более подробная документация на GitHub Pages
* 3 и более человек - помимо вышеуказанного, может включать следующее:
  - IDE в браузере
  - Поддержка Jupyter Notebook
  - Трансляция в JavaScript, чтобы можно было выполнять программу в браузере
  - Расширения для VS Code

## Задача

Ваша цель - изобрести и реализовать собственный функциональный язык программирования. Требования:

* Он должен тесно следовать парадигме функционального программирования, на основе либо [лямбда-исчисления](https://en.wikipedia.org/wiki/Lambda_calculus), либо [комбинаторной логики](https://en.wikipedia.org/wiki/Combinatory_logic).
* Он должен быть более или менее универсальным, т.е. реализовывать рекурсию. В идеале - полным по Тьюрингу.
* Как минимум, язык должен позволять запрограммировать функцию для расчета факториала.

> Имейте в виду, что написание парсеров - это утомительная задача, поэтому постарайтесь сделать синтаксис языка как можно проще.

Для вдохновения:

* Изучите [LISP](https://books.ifmo.ru/file/pdf/1918.pdf) - язык программирования с очень простым синтаксисом.
* Комбинаторные парсеры и библиотеку [fparsec](https://www.quanttec.com/fparsec/), если вы хотите реализовать язык с более сложным синтаксисом.
* [Top-Down Parser на F#](https://github.com/fholm/Vaughan).
* Интересный блог пост о [парсинге на F#](https://www.erikschierboom.com/2016/12/10/parsing-text-in-fsharp/).
* Парсинг с использованием инструментов [FsLex и FsYacc](https://realfiction.net/posts/lexing-and-parsing-in-f/) (не рекомендуется).
* Реализация [Scheme в F#](https://github.com/AshleyF/FScheme) - вы можете ознакомиться с этим проектом для вдохновения, но не заимствуйте код оттуда!

## Критерии оценки

* Универсальность
* Примеры программ (включая факториал, но не ограничиваясь им)
* Оригинальность и красота синтаксиса
* Документированность
* Красота реализации

Предпочтительный язык реализации - F#.

В документации явно укажите, какие функции языка вы реализовали:

* [+] Именованные переменные (`let`)
* [+] Рекурсия
* [+] Ленивое вычисление
* [+] Функции
* [+] Замыкания
* [+] Библиотечные функции: ввод-вывод файлов
* [+] Списки / Последовательности
* [+] Библиотечные функции: списки/последовательности

## Репозиторий

Вам необходимо работать над кодом в репозитории GitHub Classroom. После завершения задачи **настоятельно рекомендуется** форкнуть этот код в свои собственные аккаунты GitHub, чтобы он служил вашим портфолио.

## Пошаговая работа

Поскольку проект довольно большой, его нужно делать поэтапно, загружая ваш код в GitHub на каждом этапе:

* Этап 1: Разработка абстрактного синтаксического дерева и парсера для вашего языка + одна примерная программа.
* Этап 2: Разработка интерпретатора/компилятора для вашего языка.
* Этап 3: Написание примеров программ и документации.

> Конечно, вы можете изменять язык на более поздних этапах, если посчитаете это нужным.

## Авторы

Не забудьте упомянуть свою команду в файле README.md, указав также, кто что делал. Также файл README.md должен включать краткое руководство по вашему языку и некоторые короткие примеры кода.

Имя | Роль в проекте
------------------|---------------------
Боев С.С. | Разработка AST, написание отчета
Громов Т.С. | Написание интерпретатора, написание архитектуры языка, обработка ошибок
Хомяков И.А. | Написание интерпретатора, обработка ошибок 

> Если вы используете генеративный ИИ при написании этого кода (ChatGPT, GitHub Copilot и т.п.), вам необходимо упомянуть это здесь, и кратко описать подробности использования генеративного ИИ, а также как это повысило вашу продуктивность. *Использование генеративного ИИ без явного упоминания является нарушением академической этики!*

## Сборка проекта

1. **Сборка проекта**:
   ```sh
   dotnet build
   ```

2. **Запуск проекта**:
   ```sh
   dotnet run <путь к файлу>
   ```

## AST

Синтаксическое дерево (AST) строится путем комбинирования узлов `Expr` в соответствии с синтаксисом языка программирования. Например, операторы и их операнды объединяются в узлы `OPERATOR`, условные выражения представляются узлами `COND`, а функции и их вызовы создают узлы `FUNC_DEF` и `CALL` соответственно.

Каждый узел синтаксического дерева `Expr` может содержать другие узлы `Expr`, что позволяет представлять сложные структуры и выражения. Эта структура данных обеспечивает удобное представление синтаксического анализа и интерпретации кода на данном языке программирования.

### Процесс построения синтаксического дерева

#### Токенизация

Программа принимает исходный код в виде строки и разбивает его на отдельные части, называемые токенами. Этот процесс реализован в функции `tokenize` модуля `Token`. Токены представляют собой минимальные единицы синтаксиса, такие как операторы, числа, строки, идентификаторы и специальные символы.

#### Парсинг 

(Более подробно в разделе [Парсер](#Парсер))

Следующим шагом является анализ последовательности токенов и построение абстрактного синтаксического дерева (AST). Этот процесс реализован в функции `parse` модуля `Parser`. Программа последовательно анализирует каждый токен, сопоставляя его с предопределенными шаблонами, и создает структуры данных `Expr`, представляющие элементы синтаксического дерева.

#### Создание узлов синтаксического дерева

Узлы AST представляются типом `Expr`, который включает различные виды узлов, такие как:

- **SIMPLELIST**: Контейнер для хранения подвыражений, которые нужно вычислить, чтобы получить значение всего выражения. Может содержать как одиночные элементы, такие как числа, так и последовательности операций.

- **SIMPLEARGLIST**: Используется для идентификации аргументов функций, заключенных в фигурные скобки `{}`. Аргументы собираются в одну переменную и помечаются для дальнейшего применения к соответствующим структурным шаблонам.

- **SIMPLE**: Служит для определения ключевых слов и поддерживаемых операторов. Представляет собой метку для распознанного типа, полученного из токена.

На выходе из программы получается дерево синтаксиса, которое представляет структуру введенного выражения.

## Парсер

Парсер программы работает следующим образом:

1. **Парсинг ключевых слов**:
    - Если токен является ключевым словом, таким как `var`, `put`, `def`, `sout`, `if`, `then`, или `else`, он преобразуется в узел типа `SIMPLE`. Этот узел добавляется в список подвыражений, который будет объединен в выражение `SIMPLELIST`.

    ```fsharp
    | Token.ID(expr)::t when (List.tryFind (fun x -> x = expr) key_words).IsSome -> token_parser (Expr.SIMPLE(expr)::acc) t
    ```

2. **Парсинг чисел, строк и логических значений**:
    - Токены, представляющие числа, строки и логические значения, преобразуются в соответствующие узлы типов `NUMBER`, `STRING` и `BOOL`, которые затем добавляются в список подвыражений.

    ```fsharp
    | Token.NUMBER(n)::t -> token_parser (Expr.SIMPLELIST([Expr.NUMBER(n)])::acc) t
    | Token.ID("true")::t -> token_parser (Expr.SIMPLELIST([Expr.BOOL(true)])::acc) t
    | Token.ID("false")::t -> token_parser (Expr.SIMPLELIST([Expr.BOOL(false)])::acc) t
    | Token.STRING(s)::t -> token_parser (Expr.SIMPLELIST([Expr.STRING(s)])::acc) t
    ```

3. **Парсинг идентификаторов**:
    - Идентификаторы, представляющие переменные или функции, преобразуются в узлы типа `ID` и добавляются в список подвыражений.

    ```fsharp
    | Token.ID(id)::t -> token_parser (Expr.SIMPLELIST([Expr.ID(id)])::acc) t
    ```

4. **Парсинг аргументов функций**:
    - Аргументы функций, заключенные в фигурные скобки `{}`, собираются в список и преобразуются в узел типа `SIMPLEARGLIST`.

    ```fsharp
    | Token.LEFT_CURLY::t ->
        let read_args, remaining_part = token_parser [] t
        if List.forall (fun x -> match x with | Expr.SIMPLELIST([Expr.ID(_)]) -> true | _ -> false) read_args 
        then token_parser (Expr.SIMPLEARGLIST(read_args)::acc) remaining_part
        else failwith ("token_parser ERROR: non ids inside function args: " + (sprintf "%A" read_args))
    ```

5. **Парсинг выражений в круглых скобках**:
    - Выражения, заключенные в круглые скобки `()`, анализируются рекурсивно, чтобы собрать все подвыражения в список. Затем список преобразуется в узел типа `SIMPLELIST`, содержащий подвыражения или операторы.

    ```fsharp
    | Token.LEFT_PARENTHESIS::t ->
        let read_exprs, remaining_part = token_parser [] t
        match read_exprs with
        | Expr.SIMPLEOP(op)::t -> token_parser (Expr.SIMPLELIST([Expr.OPERATOR(op, t)])::acc) remaining_part
        | Expr.SIMPLE("var")::Expr.SIMPLELIST([Expr.ID(id)])::Expr.SIMPLELIST(list)::[] -> token_parser (Expr.SIMPLELIST([Expr.VARIABLE(id, Expr.SIMPLELIST(list))])::acc) remaining_part
        | Expr.SIMPLE("put")::Expr.SIMPLELIST([Expr.ID(id)])::Expr.SIMPLELIST(list)::[] -> token_parser (Expr.SIMPLELIST([Expr.SET(id, Expr.SIMPLELIST(list))])::acc) remaining_part
        | Expr.SIMPLE("def")::Expr.SIMPLELIST([Expr.ID(id)])::(Expr.SIMPLEARGLIST(args_list) as args)::(Expr.SIMPLELIST(_) as body)::[] -> 
            token_parser (SIMPLELIST([Expr.FUNC_DEF(id, args, body, Map<string, Expr>[], List.length args_list)])::acc) remaining_part
        | Expr.SIMPLELIST([Expr.ID(id)])::t ->
            if List.forall (fun x -> match x with | Expr.SIMPLELIST(_) -> true | _ -> false) t
            then token_parser (SIMPLELIST([Expr.CALL(id, Expr.SIMPLELIST(t), List.length t)])::acc) remaining_part
            else failwith ("token_parser ERROR: wrong function syntax! Used as parameters for function call: " + (sprintf "%A" t)) 
        | Expr.SIMPLELIST(list)::t -> 
            let rec gather_simple_lists acc = function
            | (Expr.SIMPLELIST(_) as list)::t' -> gather_simple_lists (list::acc) t'
            | [] -> List.rev acc, []
            | waste -> 
                printfn "unmatched expr: %A" waste
                failwith "gather_simple_lists ERROR: misformat expression"

            let lists, _ = gather_simple_lists [] (SIMPLELIST(list)::t)
            token_parser (Expr.SIMPLELIST(lists)::acc) remaining_part
        | Expr.SIMPLE("if")::(Expr.SIMPLELIST(_) as cond)
            ::Expr.SIMPLE("then")::(Expr.SIMPLELIST(_) as expr1)
            ::Expr.SIMPLE("else")::(Expr.SIMPLELIST(_) as expr2)::[] -> 
                token_parser (Expr.SIMPLELIST([Expr.COND(cond, expr1, expr2)])::acc) remaining_part
        | Expr.SIMPLE("if")::(Expr.SIMPLELIST(_) as cond)
            ::Expr.SIMPLE("then")::(Expr.SIMPLELIST(_) as expr1)::[] ->
                token_parser (Expr.SIMPLELIST([Expr.COND(cond, expr1, SIMPLE(""))])::acc) remaining_part
        | Expr.SIMPLE("sout")::(Expr.SIMPLELIST(_) as body)::[] -> token_parser (Expr.SIMPLELIST([Expr.PRINT(body)])::acc) remaining_part
        | (Expr.NUMBER(_) as num_expr)::[] -> token_parser (SIMPLELIST([num_expr])::acc) remaining_part
        | (Expr.STRING(_) as str_expr)::[] -> token_parser (SIMPLELIST([str_expr])::acc) remaining_part
        | waste -> failwith ("token_parser ERROR: wrong parenthesis structure: " + (sprintf "%A" waste))
    ```

6. **Парсинг операторов**:
    - Операторы преобразуются в узлы типа `SIMPLEOP` и добавляются в список подвыражений.

    ```fsharp
    | Token.OPERATOR(op)::t -> token_parser (Expr.SIMPLEOP(op)::acc) t
    ```

### Пример использования парсера

Рассмотрим следующий пример кода:

```plaintext
(var fib1 1)
(var fib2 1)
(def fib {n}
  (if (= n 1) 
    fib1 
    (if (= n 2) 
      fib2 
      (+ (fib (- n 1)) (fib (- n 2)))
    )
  )
)
(sout (fib 7))
```
Этот код определяет две переменные `fib1` и `fib2`, рекурсивную функцию `fib`, которая вычисляет числа Фибоначчи, и выводит результат вызова этой функции с аргументом 7.

## Интерпретатор

Интерпретатор программы работает следующим образом:

1. **Интерпретация SIMPLELIST**:
    - Если необходимо интерпретировать тип `SIMPLELIST`, то все его подвыражения интерпретируются. Этот процесс продолжается до тех пор, пока не будет получено значение, не являющееся типом `SIMPLE`.

    ```fsharp
    | Expr.SIMPLELIST(list) -> 
        let rec eval_lists env = function
            | h::t -> 
                let evaluated_first, new_env = eval_impl env h
                match evaluated_first with
                | Expr.SIMPLE(_) as simple -> eval_lists new_env t
                | _ ->
                    let evaluated, new_env' = eval_impl new_env evaluated_first 
                    match evaluated with
                    | Expr.NUMBER(_) | Expr.STRING(_) | Expr.BOOL(_) -> 
                        if List.length t <> 0 then printfn "eval_simple_lists@ warning# useless members at the end of list"
                        evaluated, new_env'
                    | _ -> eval_lists new_env' t
            | [] ->
                Expr.SIMPLE(""), env
        eval_lists env list
    ```

2. **Интерпретация условного выражения (COND)**:
    - При интерпретации условного выражения сначала вычисляется условие, а затем возвращается соответствующая ветвь выражения без её вычисления.

    ```fsharp
    | Expr.COND(cond, expr1, expr2) ->
        let eval_cond, new_env = eval_impl env cond
        match eval_cond with 
        | Expr.NUMBER(n) -> if Convert.ToBoolean n then (expr1, new_env) else (expr2, new_env)
        | Expr.BOOL(b) -> if b then (expr1, new_env) else (expr2, new_env)
        | waste -> failwith ("eval_impl ERROR: unevaluatable cond expression: " + (sprintf "%A" waste))
    ```

3. **Обращение к именованной переменной**:
    - Если встречается именованная переменная, то программа обращается к переменной в текущем окружении, и значение этой переменной возвращается.

    ```fsharp
    | Expr.ID(id) -> eval_impl env (lookup id env)
    ```

4. **Объявление функции (FUNC_DEF)**:
    - При объявлении функции информация о функции записывается в переменное окружение. В информацию о функции также добавляется текущий контекст (переменные окружения) для поддержки замыканий.

    ```fsharp
    | Expr.FUNC_DEF(id, args, body, _, arity) ->
        Expr.SIMPLE(""), (Map.add id (Expr.FUNC_DEF(id, args, body, env, arity)) env)
    ```

5. **Вызов функции (CALL)**:
    - При вызове функции проверяется соответствие количества аргументов объявленной и вызываемой функции. Затем аргументы функции связываются с параметрами из вызова, и вместе с сохраненным окружением используются для интерпретации тела функции. В этом процессе также учитывается информация о самой функции, что позволяет осуществлять рекурсивные вызовы функций.

    ```fsharp
    | Expr.CALL(id, Expr.SIMPLELIST(args), arity) ->
        let env_function = Map.tryFind id env
        if env_function.IsNone then failwith ("eval_impl ERROR: use of undeclared function " + id)
        else 
            let (Expr.FUNC_DEF(_, Expr.SIMPLEARGLIST(env_args), body, env_env, env_arity)) = env_function.Value
            if arity <> env_arity 
            then failwith ("eval_impl ERROR: function use with different arity: expected " + (sprintf "%A" env_arity) + " got: " + (sprintf "%A" arity))
            else
                let rec add_env_args env = function
                | (Expr.SIMPLELIST([Expr.ID(h1)])::t1), (h2::t2) -> 
                    let eval_h2, new_env =  eval_impl env h2
                    add_env_args (Map.add h1 eval_h2 new_env) (t1, t2)
                | ([], []) -> env
                | waste -> failwith ("eval_impl ERROR: Some serious thing happened during concatenations of maps: " + (sprintf "%A" waste))

                let new_env = add_env_args env (env_args, args) 
                let merged_env = Map.fold (fun acc key value -> Map.add key value acc) env new_env
                let merged_env2 = Map.fold (fun acc key value -> Map.add key value acc) merged_env env_env

                eval_impl merged_env2 body
    ```

6. **Обработка операторов**:
    - Для операторов, взаимодействующих со списками аргументов, сначала происходит оценка и проверка типов элементов списка, а затем непосредственное вычисление значения.

    ```fsharp
    | Expr.OPERATOR(op, t) when (Map.tryFind op numeric_operators).IsSome ->
        let (single_lambda, multiple_lambda) = Map.find op numeric_operators
        let evaluated_list, new_env = eval_args_num eval_impl env [] t
        match List.length evaluated_list with
        | 0 -> failwith "eval_impl ERROR: operator + can't have 0 arguments"
        | 1 -> single_lambda (List.head evaluated_list), new_env
        | _ -> List.reduce (fun x y -> multiple_lambda (x, y)) evaluated_list, new_env
    | Expr.OPERATOR(op, t) when (Map.tryFind op bool_operators).IsSome ->
        let (single_lambda, multiple_lambda) = Map.find op bool_operators
        let evaluated_list, new_env = eval_args_bool eval_impl env [] t
        match List.length evaluated_list with
        | 0 -> failwith "eval_impl ERROR: operator + can't have 0 arguments"
        | 1 -> single_lambda (List.head evaluated_list), new_env
        | _ -> List.reduce (fun x y -> multiple_lambda (x, y)) evaluated_list, new_env
    ```

7. **Простейшие типы**:
    - Простейшие типы, такие как числа, строки и логические переменные, возвращают себя же при оценке.

    ```fsharp
    | Expr.NUMBER(_) as number -> number, env
    | Expr.STRING(_) as string -> string, env
    | Expr.BOOL(_) as boolean -> boolean, env
    ```

8. **Печать результатов (PRINT)**:
    - Узлы `PRINT` вычисляют выражение и выводят его значение.

    ```fsharp
    | Expr.PRINT(body) ->
        let evaludated_body, new_env = eval_impl env body
        match evaludated_body with
        | Expr.SIMPLE(_) -> failwith ("eval_impl.sout ERROR: unevaluatable simple value to sout\n")
        | _ ->
            let evaluated_eval, new_env' = eval_impl new_env evaludated_body
            match evaluated_eval with
            | Expr.NUMBER(num) -> 
                printfn "%f" num
                SIMPLE(""), new_env
            | Expr.STRING(str) -> 
                printfn "\"%s\"" str
                SIMPLE(""), new_env
            | Expr.BOOL(boolean) ->
                if boolean then
                    printfn "true"
                    SIMPLE(""), new_env
                else
                    printfn "false"
                    SIMPLE(""), new_env
            | Expr.ID(id_val) as id ->
                let evaluated_id, new_env'' = eval_impl new_env' id
                printfn "id: %s = %A" id_val evaluated_id
                SIMPLE(""), new_env''
    ```

В соответствии с требованиями языка программирования, интерпретатор возвращает один из подтипов `Expr`. Интерпретатор позволяет анализировать и выполнять код, представленный в структуре дерева синтаксиса.

## Описание функций

### Именованные переменные (let)

**Описание**: 
Функция `let` используется для создания переменных и привязки значений. Это позволяет именовать значения, чтобы затем использовать их в других частях программы.

**Синтаксис**:
```plaintext
(var имя_переменной значение)
```

**Пример**:
```plaintext
(var x 10)
(sout x)
```

**Пояснение**:
В данном примере создается переменная `x` со значением `10`. Затем значение переменной `x` выводится с помощью функции `sout`.

**Соответствующий AST**:
```fsharp
Expr.SIMPLELIST [
    Expr.VARIABLE ("x", Expr.NUMBER 10.0);
    Expr.PRINT (Expr.ID "x")
]
```

### Рекурсия

**Описание**: 
Рекурсия позволяет функции вызывать саму себя. Это полезно для решения задач, которые могут быть разбиты на более мелкие подзадачи того же типа.

**Синтаксис**:
```plaintext
(def имя_функции {аргумент}
  (if (условие)
    (базовый случай)
    (рекурсивный случай)
  )
)
```

**Пример**:
```plaintext
(def fact {n}
  (if (= n 0)
    1
    (* n (fact (- n 1)))
  )
)
(sout (fact 5))
```

**Пояснение**:
В данном примере создается функция `fact`, которая вычисляет факториал числа `n`. Если `n` равно 0, функция возвращает 1 (базовый случай). В противном случае возвращается произведение `n` и вызов `fact` с аргументом `n - 1` (рекурсивный случай).

**Соответствующий AST**:
```fsharp
Expr.SIMPLELIST [
    Expr.FUNC_DEF (
        "fact",
        Expr.SIMPLEARGLIST [Expr.SIMPLELIST [Expr.ID "n"]],
        Expr.SIMPLELIST [
            Expr.COND (
                Expr.OPERATOR ("=", [Expr.ID "n"; Expr.NUMBER 0.0]),
                Expr.NUMBER 1.0,
                Expr.OPERATOR ("*", [
                    Expr.ID "n",
                    Expr.CALL ("fact", Expr.SIMPLELIST [Expr.OPERATOR ("-", [Expr.ID "n"; Expr.NUMBER 1.0])], 1)
                ])
            )
        ],
        Map.empty,
        1
    );
    Expr.PRINT (Expr.CALL ("fact", Expr.SIMPLELIST [Expr.NUMBER 5.0], 1))
]
```

### Ленивое вычисление

**Описание**: 
Ленивое вычисление откладывает выполнение выражения до тех пор, пока оно не потребуется. В данной реализации оно используется неявно при интерпретации некоторых выражений.

### Функции

**Описание**: 
Функции позволяют группировать повторяющиеся блоки кода для многократного использования.

**Синтаксис**:
```plaintext
(def имя_функции {аргумент1 аргумент2 ...}
  тело_функции
)
```

**Пример**:
```plaintext
(def add {x y}
  (+ x y)
)
(sout (add 3 5))
```

**Пояснение**:
В данном примере создается функция `add`, которая принимает два аргумента `x` и `y`, и возвращает их сумму. Затем результат вызова функции `add` с аргументами `3` и `5` выводится с помощью `sout`.

**Соответствующий AST**:
```fsharp
Expr.SIMPLELIST [
    Expr.FUNC_DEF (
        "add",
        Expr.SIMPLEARGLIST [Expr.SIMPLELIST [Expr.ID "x"]; Expr.SIMPLELIST [Expr.ID "y"]],
        Expr.SIMPLELIST [Expr.OPERATOR ("+", [Expr.ID "x"; Expr.ID "y"])],
        Map.empty,
        2
    );
    Expr.PRINT (Expr.CALL ("add", Expr.SIMPLELIST [Expr.NUMBER 3.0; Expr.NUMBER 5.0], 2))
]
```

### Замыкания

**Описание**: 
Замыкания позволяют функции запоминать окружение, в котором они были созданы. Это полезно для создания функций с состоянием.

**Пример**:
```plaintext
(var x 10)
(def inc {y}
  (+ x y)
)
(sout (inc 5))
```

**Пояснение**:
В данном примере переменная `x` находится в окружении функции `inc`. Когда `inc` вызывается с аргументом `5`, она возвращает сумму `x` и `y`.

**Соответствующий AST**:
```fsharp
Expr.SIMPLELIST [
    Expr.VARIABLE ("x", Expr.NUMBER 10.0);
    Expr.FUNC_DEF (
        "inc",
        Expr.SIMPLEARGLIST [Expr.SIMPLELIST [Expr.ID "y"]],
        Expr.SIMPLELIST [Expr.OPERATOR ("+", [Expr.ID "x"; Expr.ID "y"])],
        Map.empty,
        1
    );
    Expr.PRINT (Expr.CALL ("inc", Expr.SIMPLELIST [Expr.NUMBER 5.0], 1))
]
```

### Библиотечные функции: ввод-вывод файлов

**Описание**: 
Библиотечные функции для ввода-вывода файлов позволяют считывать данные из файлов и записывать данные в файлы.

**Пример**:
```plaintext
(def readFile {filename}
  ;; читаем файл и возвращаем его содержимое
)

(def writeFile {filename content}
  ;; записываем содержимое в файл
)
```

### Списки / Последовательности

**Описание**: 
Списки и последовательности используются для хранения и работы с коллекциями элементов.

**Пример**:
```plaintext
(var myList [1 2 3 4 5])
(sout (head myList))
(sout (tail myList))
```

**Пояснение**:
В данном примере создается список `myList`, затем выводятся его первый элемент (`head`) и хвост (все элементы, кроме первого, `tail`).

### Библиотечные функции: списки/последовательности

**Описание**: 
Библиотечные функции для работы со списками и последовательностями включают операции, такие как `map`, `filter`, `reduce`.

**Пример**:
```plaintext
(def map {f lst}
  ;; применяем функцию f к каждому элементу списка lst
)
```
