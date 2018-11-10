# Syntax in Function

**Pattern matching**

    factorial :: (Integral a) => a -> a  
    factorial 0 = 1  
    factorial n = n * factorial (n - 1)

模式的顺序是如此重要：它总是优先匹配最符合的那个，最后才是那个万能的。

若要绑定多个变量(用 _ 也是如此)，我们必须用括号将其括起。

    tell :: (Show a) => [a] -> String  
    tell [] = "The list is empty"  
    tell (x:[]) = "The list has one element: " ++ show x  
    tell (x:y:[]) = "The list has two elements: " ++ show x ++ " and " ++ show y  
    tell (x:y:_) = "This list is long. The first two elements are: " ++ show x ++ " and " ++ show y

length 函数，现在用模式匹配和递归重新实现它：

    length' :: (Num b) => [a] -> b  
    length' [] = 0  
    length' (_:xs) = 1 + length' xs
    
as 模式，就是将一个名字和 @ 置于模式前，可以在按模式分割什么东西时仍保留对其整体的引用。如这个模式 xs@(x:y:ys)，它会匹配出与 x:y:ys 对应的东西，同时你也可以方便地通过 xs 得到整个 List，而不必在函数体中重复 x:y:ys。看下这个 quick and dirty 的例子：

    capital :: String -> String  
    capital "" = "Empty string, whoops!"  
    capital all@(x:xs) = "The first letter of " ++ all ++ " is " ++ [x]
    ghci> capital "Dracula"  
    "The first letter of Dracula is D"

使用 as 模式通常就是为了在较大的模式中保留对整体的引用，从而减少重复性的工作。

**不可以在模式匹配中使用 ++**

**Guards**

模式用来检查一个值是否合适并从中取值，而 guard 则用来检查一个值的某项属性是否为真。咋一听有点像是 if 语句，实际上也正是如此。不过处理多个条件分支时 guard 的可读性要高些，并且与模式匹配契合的很好。

    bmiTell :: (RealFloat a) => a -> String  
    bmiTell bmi  
        | bmi <= 18.5 = "You're underweight, you emo, you!"  
        | bmi <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
        | bmi <= 30.0 = "You're fat! Lose some weight, fatty!"  
        | otherwise   = "You're a whale, congratulations!"
        
guard 由跟在函数名及参数后面的竖线标志，通常他们都是靠右一个缩进排成一列。一个 guard 就是一个布尔表达式，如果为真，就使用其对应的函数体。如果为假，就送去见下一个 guard，如之继续。

最后的那个 guard 往往都是 otherwise，它的定义就是简单一个 otherwise = True ，捕获一切。这与模式很相像，只是模式检查的是匹配，而它们检查的是布尔表达式 。如果一个函数的所有 guard 都没有通过(而且没有提供 otherwise 作万能匹配)，就转入下一模式。这便是 guard 与模式契合的地方。如果始终没有找到合适的 guard 或模式，就会发生一个错误。

当然，guard 可以在含有任意数量参数的函数中使用。省得用户在使用这函数之前每次都自己计算 bmi。我们修改下这个函数，让它取身高体重为我们计算。

    bmiTell :: (RealFloat a) => a -> a -> String  
    bmiTell weight height  
        | weight / height ^ 2 <= 18.5 = "You're underweight, you emo, you!"  
        | weight / height ^ 2 <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
        | weight / height ^ 2 <= 30.0 = "You're fat! Lose some weight, fatty!"  
        | otherwise                 = "You're a whale, congratulations!"
        
guard 也可以塞在一行里面。但这样会丧失可读性，因此是不被鼓励的。即使是较短的函数也是如此，不过出于展示，我们可以这样重写 max'：

    max' :: (Ord a) => a -> a -> a  
    max' a b | a > b = a | otherwise = b

这样的写法根本一点都不容易读。

我们再来试试用 guard 实现我们自己的 compare 函数：

    myCompare :: (Ord a) => a -> a -> Ordering  
    a `myCompare` b  
        | a > b     = GT  
        | a == b    = EQ  
        | otherwise = LT
    ghci> 3 `myCompare` 2  
    GT

**Note**：通过反单引号，我们不仅可以以中缀形式调用函数，也可以在定义函数的时候使用它。有时这样会更易读。

