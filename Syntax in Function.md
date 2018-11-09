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

