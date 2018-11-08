# Types and Typeclasses


**Type**

类型是每个表达式都有的某种标签，它标明了这一表达式所属的范畴。例如，表达式 True 是 boolean 型，"hello"是个字符串，等等。

可以使用 ghci 来检测表达式的类型。使用 :t 命令后跟任何可用的表达式，即可得到该表达式的类型，先试一下：

    ghci> :t 'a'  
    'a' :: Char  
    ghci> :t True  
    True :: Bool  
    ghci> :t "HELLO!"  
    "HELLO!" :: [Char]  
    ghci> :t (True, 'a')  
    (True, 'a') :: (Bool, Char)  
    ghci> :t 4 == 5  
    4 == 5 :: Bool
    
:t 命令处理一个表达式的输出结果为表达式后跟 :: 及其类型，:: 读作"它的类型为"。凡是明确的类型，其首字母必为大写。'a', 如它的样子，是 Char 类型，易知是个字符 (character)。True 是 Bool 类型，也靠谱。检测 "hello" 得一个 [Char] 这方括号表示一个 List，所以我们可以将其读作"一组字符的 List"。而与 List 不同，每个 Tuple 都是独立的类型，于是 (True,'a') 的类型是 (Bool,Char)，而 ('a','b','c') 的类型为 (Char,Char,Char)。4==5 一定回传 False，所以它的类型为 Bool。
同样，函数也有类型。编写函数时，给它一个明确的类型声明是个好习惯，比较短的函数就不用多此一举了。

    removeNonUppercase :: [Char] -> [Char]  
    removeNonUppercase st = [ c | c <- st, c `elem` ['A'..'Z']]

removeNonUppercase 的类型为 [Char]->[Char]，从它的参数和回传值的类型上可以看出，它将一个字符串映射为另一个字符串。[Char] 与 String 是等价的，但使用 String 会更清晰：removeNonUppercase :: String -> String。编译器会自动检测出它的类型。要是多个参数的函数该怎样？如下便是一个将三个整数相加的简单函数。
addThree :: Int -> Int -> Int -> Int  
addThree x y z = x + y + z
参数之间由 -> 分隔，而与回传值之间并无特殊差异。回传值是最后一项，参数就是前三项。


Int 表示整数。7 可以是 Int，但 7.2 不可以。Int 是有界的，也就是说它由上限和下限。对 32 位的机器而言，上限一般是 2147483647，下限是 -2147483648。
Integer 表示...厄...也是整数，但它是无界的。这就意味着可以用它存放非常非常大的数，我是说非常大。它的效率不如 Int 高。

    factorial :: Integer -> Integer  
    factorial n = product [1..n]
    ghci> factorial 50  
    30414093201713378043612608166064768844377641568960512000000000000

Float 表示单精度的浮点数。

    circumference :: Float -> Float  
    circumference r = 2 * pi * r
    ghci> circumference 4.0  
    25.132742

Double 表示双精度的浮点数。

    circumference' :: Double -> Double  
    circumference' r = 2 * pi * r
    
    ghci> circumference' 4.0  
    25.132741228718345

Bool 表示布尔值，它只有两种值：True 和 False。
Char 表示一个字符。一个字符由单引号括起，一组字符的 List 即为字符串。
Tuple 的类型取决于它的长度及其中项的类型。注意，空 Tuple 同样也是个类型，它只有一种值：()。

**Type variables**

使用到类型变量的函数被称作"多态函数 "，head 函数的类型声明里标明了它可以取任意类型的 List 并回传其中的第一个元素。
在命名上，类型变量使用多个字符是合法的，不过约定俗成，通常都是使用单个字符，如 a, b ,c ,d...

**Typeclasses Intro**
几个基本的 Typeclass：
**Eq** 包含可判断相等性的类型。提供实现的函数是 == 和 /=。所以，只要一个函数有Eq类的类型限制，那么它就必定在定义中用到了 == 和 /=。刚才说了，除函数以外的所有类型都属于 Eq，所以它们都可以判断相等性。
**Ord** 包含可比较大小的类型。除了函数以外，我们目前所谈到的所有类型都属于 Ord 类。Ord 包中包含了<, >, <=, >= 之类用于比较大小的函数。compare 函数取两个 Ord 类中的相同类型的值作参数，回传比较的结果。这个结果是如下三种类型之一：GT, LT, EQ。

    ghci> :t (>)  
    (>) :: (Ord a) => a -> a -> Bool

类型若要成为Ord的成员，必先加入Eq家族。

    ghci> "Abrakadabra" < "Zebra"  
    True  
    ghci> "Abrakadabra" `compare` "Zebra"  
    LT  
    ghci> 5 >= 2  
    True  
    ghci> 5 `compare` 3  
    GT

**Show** 的成员为可用字符串表示的类型。目前为止，除函数以外的所有类型都是 Show 的成员。操作 Show Typeclass，最常用的函数表示 show。它可以取任一Show的成员类型并将其转为字符串。

    ghci> show 3  
    "3"  
    ghci> show 5.334  
    "5.334"  
    ghci> show True  
    "True"

**Read** 是与 Show 相反的 Typeclass。read 函数可以将一个字符串转为 Read 的某成员类型。

    ghci> read "True" || False  
    True  
    ghci> read "8.2" + 3.8  
    12.0  
    ghci> read "5" - 2  
    3  
    ghci> read "[1,2,3,4]" ++ [3]  
    [1,2,3,4,3]
    
    ghci> read "5" :: Int  
    5  
    ghci> read "5" :: Float  
    5.0  
    ghci> (read "5" :: Float) * 4  
    20.0  
    ghci> read "[1,2,3,4]" :: [Int]  
    [1,2,3,4]  
    ghci> read "(3, 'a')" :: (Int, Char)  
    (3, 'a')

编译器可以辨认出大部分表达式的类型，但遇到 read "5" 的时候它就搞不清楚究竟该是 Int 还是 Float 了。只有经过运算，Haskell 才会明确其类型；同时由于 Haskell 是静态的，它还必须得在 编译前搞清楚所有值的类型。

**Enum** 的成员都是连续的类型 -- 也就是可枚举。Enum 类存在的主要好处就在于我们可以在 Range 中用到它的成员类型：每个值都有后继子 (successer) 和前置子 (predecesor)，分别可以通过 succ 函数和 pred 函数得到。该 Typeclass 包含的类型有：(), Bool, Char, Ordering, Int, Integer, Float 和 Double。

    ghci> ['a'..'e']  
    "abcde"  
    ghci> [LT .. GT]  
    [LT,EQ,GT]  
    ghci> [3 .. 5]  
    [3,4,5]  
    ghci> succ 'B'  
    'C'

**Bounded** 的成员都有一个上限和下限。

    ghci> minBound :: Int  
    -2147483648  
    ghci> maxBound :: Char  
    '\1114111'  
    ghci> maxBound :: Bool  
    True  
    ghci> minBound :: Bool  
    False

minBound 和 maxBound 函数很有趣，它们的类型都是 (Bounded a) => a。可以说，它们都是多态常量。
如果其中的项都属于 Bounded Typeclass，那么该 Tuple 也属于 Bounded

    ghci> maxBound :: (Bool, Int, Char)  
    (True,2147483647,'\1114111')

**Num** 是表示数字的 Typeclass，它的成员类型都具有数字的特征。检查一个数字的类型：

    ghci> :t 20  
    20 :: (Num t) => t
    
**Integral** 同样是表示数字的 Typeclass。Num 包含所有的数字：实数和整数。而 Integral 仅包含整数，其中的成员类型有 Int 和 Integer。

**Floating** 仅包含浮点类型：Float 和 Double。