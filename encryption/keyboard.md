### Randomized Keyboard

Making virtual keyborad resistent to keylogger attack.

**Author**: Vlad Gluhovsky

**License**: The Unlicense (<http://unlicense.org/>)


The keyboard should be in alphabetic order, with randomized offset (position of A). After next key is clicked/pressed, the offset changes randomly. Because of alphabetic order, it will be easy for the human to find the key. But it would be impossible for the keylogger to collect the data, because of the random offset.


Предположим нужно набрать некоторый текст с клавиатуры, но есть подозрение, что на этом компьютере пасётся keylogger. Можно конечно с виртаульной клавиатуры, но всё равно keylogger запомнит mouse events, затем рассчитает grid, и всё. Можно расставить клавиши в случайном порядке, но если keylogger вычислил grid, то простым частотным анализом он сразу же расшифрует текст. Можно было бы менять клавиши в случайном порядке после каждого нажатия -- это уже лучше, но человеку неудобно будет их искать. Моё решение: раскладка виртуальной клавиатуры в алфавитном порядке, но после каждого нажатия меняется offset (положение буквы А).

