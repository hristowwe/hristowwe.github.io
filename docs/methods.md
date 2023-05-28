---
id: methods
title: Всички методи в програмата
description: Тук можете да разгледате всички функции. Не забравяйте, че има меню в най-дясно, за да изберете метода, който искате.
slug: /methods
---

Тук можете да разгледате всички функции. Не забравяйте, че има меню в най-дясно, за да изберете метода, който искате.

## ВАЛИДАРАЩИ ФУНКЦИИ

### `boolean checkState(Scanner scanner, Integer state)`

| параметър | значение                                                                 | стойност по подразбиране |
| --------- | ------------------------------------------------------------------------ | ------------------------ |
| `scanner` | инстанция на класа Scanner, използвана за четене на вход от потребителя. | няма                     |
| `state`   | цяло число, представляващо състоянието, което трябва да се провери.      | няма                     |

## Как работи?

В рамките на метода се извежда на стандартния изход съобщение, което показва числото state и пита потребителя дали иска това число да бъде маркирано като финално състояние. Потребителят отговаря с "Y" за да, "N" за не или с друг символ, ако въведе невалиден отговор.

Цикълът се повтаря, докато потребителят не въведе валиден отговор ("Y" или "N"). Ако потребителят въведе "Y", методът връща true, ако въведе "N", методът връща false. Ако потребителят въведе невалиден отговор, се извежда съобщение за невалиден вход и цикълът се повтаря.

Този метод се използва в основния метод main, когато се добавят нови преходи към NFA. След всяко добавяне на преход, потребителят се пита дали следващото състояние трябва да бъде маркирано като финално състояние чрез извикване на checkState(scanner, nextState).

```js
   public static boolean checkState(Scanner scanner, Integer state)
{
    while (true) {
        System.out.print("Do you want this (" + state + ") integer to be the final state? (Y/N): ");
        String answer = scanner.next().trim().toLowerCase();
        scanner.nextLine();
        if (answer.equals("y")) {
            return true;
        } else if (answer.equals("n")) {
            return false;
        } else {
            System.out.println("Invalid input. Please enter 'Y' or 'N'.");
        }
    }
}
```

## ОСНОВНИ МЕТОДИ

### `addTransition(int currentState, char symbol, int nextState)`

Намира се в **NFA.java**

| параметър      | значение                                         | стойност по подразбиране |
| -------------- | ------------------------------------------------ | ------------------------ |
| `currentState` | цяло число, представляващо текущото състояние.   | няма                     |
| `symbol`       | символ, представляващ символа на прехода.        | няма                     |
| `nextState`    | цяло число, представляващо следващото състояние. | няма                     |

## Как работи?

Методът създава нов обект Transition с подадените символ и следващо състояние. След това се използва методът computeIfAbsent на transitions, който взима текущото състояние и връща множеството от преходи за него. Ако няма преходи за текущото състояние, се създава ново празно множество.

След това се проверява дали вече съществува преход със същия символ и следващо състояние в текущото множество от преходи. Това се прави чрез потоциране на текущите преходи и използване на anyMatch, който проверява дали има преход, който има същия символ и следващо състояние.

Ако има дубликат на прехода, се извежда съобщение, че преходът вече съществува. В противен случай преходът се добавя към текущото множество от преходи, firstTransition се задава на false (за да се отбележи, че вече е добавен първи преход) и се извежда съобщение, че преходът е добавен към NFA.

Този метод се използва в основния метод main, когато потребителят въвежда командата "transition" за добавяне на нов преход към NFA.

```js
    public void addTransition(int currentState, char symbol, int nextState) {
        Transition transition = new Transition(symbol, nextState);

        Set<Transition> currentTransitions = transitions.computeIfAbsent(currentState, k -> new HashSet<>());

        boolean isDuplicate = currentTransitions.stream()
            .anyMatch(t -> t.getSymbol() == symbol && t.getNextState() == nextState);

        if (isDuplicate) {
            System.out.println("Transition already exists: " + currentState + " -> " + nextState + " (" + symbol + ")");
        } else {
            currentTransitions.add(transition);
            firstTransition = false;
            System.out.println("Transition added to NFA");
        }
    }
```

### `void setInitialState(int state)`

Намира се в **NFA.java**

| параметър   | значение                                            | стойност по подразбиране |
| ----------- | --------------------------------------------------- | ------------------------ |
| `int state` | цяло число, което представлява началното състояние. | няма                     |

## Как работи?

Този метод просто присвоява подаденото начално състояние на променливата initialState в класа NFA. Това определя началното състояние на автомата.

Този метод се използва в основния метод main, когато потребителят въвежда командата "create" за създаване на нов NFA и задава началното състояние.

```js
  public void setInitialState(int state) {
        initialState = state;
    }
```

### `void addFinalState(int state)`

Намира се в **NFA.java**

| параметър | значение                                            | стойност по подразбиране |
| --------- | --------------------------------------------------- | ------------------------ |
| `state`   | цяло число, което представлява финалното състояние. | няма                     |

## Как работи?

Този метод просто добавя подаденото финално състояние към множеството finalStates в класа NFA. Това означава, че състоянието е финално и се приема за валиден край на входната дума.

Този метод се използва в основния метод main, когато потребителят въвежда командата "transition" и потвърждава, че дадено състояние трябва да бъде финално.

```js
    public void addFinalState(int state) {
        finalStates.add(state);
    }
```

### `boolean isEmptyLanguage()`

Намира се в **NFA.java**

| параметър | значение | стойност по подразбиране |
| --------- | -------- | ------------------------ |
| `-`       | -        | -                        |

## Как работи?

Този метод извиква вътрешния помощен метод exploreStates(int currentState, Set`<Integer>` reachableStates), който рекурсивно обхожда всички достижими състояния от началното състояние на NFA и ги добавя към множеството reachableStates. След това методът проверява дали някое от тези достижими състояния е финално, като използва операцията Collections.disjoint().

reachableStates: Множество, в което се събират достижимите състояния от началното състояние на NFA.
finalStates: Множество, съдържащо финалните състояния на NFA.
Методът връща резултата от операцията Collections.disjoint(), която връща истина, ако няма общи елементи между reachableStates и finalStates, т.е. някои от достижимите състояния не са финални. Това означава, че езикът, разпознаван от NFA, е празен.

Методът се използва в основния метод main, когато потребителят въвежда командата "empty" и проверява дали NFA има празен език.

```js
    public boolean isEmptyLanguage() {
        Set<Integer> reachableStates = new HashSet<>();
        exploreStates(initialState, reachableStates);
        return Collections.disjoint(reachableStates, finalStates);
    }
```

### `void exploreStates(int currentState, Set<Integer> reachableStates)`

Намира се в **NFA.java**

| параметър         | значение                                                                     | стойност по подразбиране |
| ----------------- | ---------------------------------------------------------------------------- | ------------------------ |
| `currentState`    | Цяло число, представляващо текущото състояние, от което започва обхождането. | няма                     |
| `reachableStates` | Множество, в което се събират достижимите състояния..                        | няма                     |

## Как работи?

В началото на метода текущото състояние се добавя в множеството reachableStates. След това се взимат транзициите от текущото състояние от мапата transitions. Ако има налични транзиции, методът обхожда всяка от тях и проверява дали следващото състояние (което е връзка на транзицията) вече е в reachableStates. Ако не е, то методът се извиква рекурсивно за обхождане на следващото състояние. Това продължава докато не се обходят всички достижими състояния от текущото състояние.

Крайната цел на метода е да намери всички достижими състояния от началното състояние и да ги добави в reachableStates. Това помага при проверката дали езикът на NFA е празен или не, която се извършва в метода isEmptyLanguage().

```js
    private void exploreStates(int currentState, Set<Integer> reachableStates) {
        reachableStates.add(currentState);
        Set<Transition> currentTransitions = transitions.get(currentState);
        if (currentTransitions != null) {
            for (Transition transition : currentTransitions) {
                if (!reachableStates.contains(transition.getNextState())) {
                    exploreStates(transition.getNextState(), reachableStates);
                }
            }
        }
    }
```

### `void printTransitions()`

Намира се в **NFA.java**

| параметър | значение | стойност по подразбиране |
| --------- | -------- | ------------------------ |
| `-`       | -        | -                        |

## Как работи?

Методът printTransitions() се използва за извеждане на всички преходи в недетерминирания автомат (NFA). Той обхожда мапата transitions, която съдържа състоянията като ключове и множества от транзиции като стойности. За всяка транзиция от текущото състояние се извежда информация за текущото състояние, следващото състояние и символа на прехода.

Методът няма параметри.

При изпълнение на метода, преходите се извеждат на стандартния изход със следния формат: "Transition: [текущо състояние] -> [следващо състояние] (символ)".

```js
    public void printTransitions() {
        for (Map.Entry<Integer, Set<Transition>> entry : transitions.entrySet()) {
            int currentState = entry.getKey();
            Set<Transition> currentTransitions = entry.getValue();
            for (Transition transition : currentTransitions) {
                int nextState = transition.getNextState();
                char symbol = transition.getSymbol();
                System.out.println("Transition: " + currentState + " -> " + nextState + " (" + symbol + ")");
            }
        }
    }
```

### `void open(String filename)`

Намира се в **NFAFileManager.java**

| параметър         | значение                                   | стойност по подразбиране |
| ----------------- | ------------------------------------------ | ------------------------ |
| `String filename` | Името на файла, от който ще се зареди NFA. | няма                     |

## Как работи?

Методът open(String filename) се използва за отваряне на файл, който съдържа сериализиран обект от класа NFA (недетерминиран автомат). Той извлича обекта от файла и го добавя към мениджъра на NFA (nfas), като му присвоява уникално идентификационно ID.

<details>
  <summary>Виж изключенията, които хвърля!</summary>
  <div>
    <div>IOException: Ако възникне грешка при работата с файловата система или при четенето на данните от файла.</div>
     <div>ClassNotFoundException: Ако класът NFA не бъде намерен при десериализацията.</div>
  </div>
</details>

```js
    public void open(String filename) {
        try (ObjectInputStream inputStream = new ObjectInputStream(new FileInputStream(filename))) {
            NFA nfa = (NFA) inputStream.readObject();
            String id = "NFA-" + nextId++;
            nfas.put(id, nfa);
            System.out.println("Opened NFA with ID: " + id);
        } catch (IOException | ClassNotFoundException e) {
            System.out.println("Error opening NFA: " + e.getMessage());
        }
    }
```

Намира се в **NFAFileManager.java**

### `void save(String id, String filename)`

| параметър         | значение                                                      | стойност по подразбиране |
| ----------------- | ------------------------------------------------------------- | ------------------------ |
| `String id`       | Уникалното идентификационно ID на NFA, който ще бъде запазен. | няма                     |
| `String filename` | начална година, от която искаме да започнем извеждането       | няма                     |

## Как работи?

Ако съществува NFA със зададеното ID, методът създава ObjectOutputStream, свързан със файловия поток (FileOutputStream), и записва NFA обекта във файла чрез writeObject метода на ObjectOutputStream. При успешно запазване на NFA, се извежда съобщение за успешно запазване. В случай на грешка при записа, се извежда съобщение за грешка, съдържащо информацията за грешката. Ако не бъде намерен NFA със зададеното ID, се извежда съобщение, че NFA с това ID не е намерен.

```js
    public void save(String id, String filename) {
        NFA nfa = nfas.get(id);
        if (nfa != null) {
            try (ObjectOutputStream outputStream = new ObjectOutputStream(new FileOutputStream(filename))) {
                outputStream.writeObject(nfa);
                System.out.println("NFA saved successfully.");
            } catch (IOException e) {
                System.out.println("Error saving NFA: " + e.getMessage());
            }
        } else {
            System.out.println("NFA with ID " + id + " not found.");
        }
    }
```

### `void list()`

Намира се в **NFAFileManager.java**

| параметър | значение | стойност по подразбиране |
| --------- | -------- | ------------------------ |
| `-`       | -        | няма                     |

## Как работи?

Методът list() се използва за извеждане на списък с наличните NFAs (недетерминирани автомати) в мениджъра на NFA. Той просто преглежда ключовете (идентификационните ID-та) на NFAs в мениджъра и ги извежда на конзолата.

Този метод не приема параметри. При изпълнение на метода, се извежда заглавие "Available NFAs:" и след това се изброяват наличните NFAs, като за всеки NFA се извежда неговото идентификационно ID на нов ред.

```js
    public void list() {
        System.out.println("Available NFAs:");
        for (String id : nfas.keySet()) {
            System.out.println(id);
        }
    }

```

### `void print(String id)`

Намира се в **NFAFileManager.java**

| параметър   | значение                                                         | стойност по подразбиране |
| ----------- | ---------------------------------------------------------------- | ------------------------ |
| `String id` | идентификационното ID на NFA, чиито транзиции искаме да изведем. | няма                     |

## Как работи?

Методът проверява дали съществува NFA със зададеното ID в мениджъра на NFA. Ако NFA със съответното ID съществува, се извиква методът printTransitions() на този NFA, който отговаря за извеждането на транзициите му на конзолата. В противен случай, се извежда съобщение, че NFA с даденото ID не е намерен.

```js
    public void print(String id) {
        NFA nfa = nfas.get(id);
        if (nfa != null) {
            nfa.printTransitions();
        } else {
            System.out.println("NFA with ID " + id + " not found.");
        }
    }    std::cout << "Не бяха намерени никакви бюра по вашите филтри ..." << std::endl;
```

### `void empty(String id)`

Намира се в **NFAFileManager.java**

| параметър   | значение                     | стойност по подразбиране |
| ----------- | ---------------------------- | ------------------------ |
| `String id` | идентификационното ID на NFA | няма                     |

## Как работи?

Методът проверява дали съществува NFA със зададеното ID в мениджъра на NFA. Ако NFA със съответното ID съществува, се извиква методът isEmptyLanguage() на този NFA, който връща булева стойност - true, ако езикът е празен, и false, ако езикът не е празен. Стойността се извежда на конзолата със съобщение "Language is empty: " и след това се изписва резултатът на проверката. В противен случай, ако NFA с даденото ID не е намерен, се извежда съобщение, че NFA със зададеното ID не е намерен.

```js
 public void empty(String id) {
        NFA nfa = nfas.get(id);
        if (nfa != null) {
            boolean isEmpty = nfa.isEmptyLanguage();
            System.out.println("Language is empty: " + isEmpty);
        } else {
            System.out.println("NFA with ID " + id + " not found.");
        }
    }
```
