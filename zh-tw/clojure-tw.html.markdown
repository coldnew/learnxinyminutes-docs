---
language: clojure
filename: learnclojure.clj
contributors:
    - ["Adam Bard", "http://adambard.com/"]
translators:
    - ["Yen-Chin, Lee", "https://github.com/coldnew"]
lang: zh-tw
---

Clojure 是執行於 JVM 上的 Lisp 程式語言。Clojure 比 Common Lisp 更強調純[函數式程式設計](https://en.wikipedia.org/wiki/Functional_programming)，並自帶了許多 [STM](https://en.wikipedia.org/wiki/Software_transactional_memory) 工具來處理狀態的變化。

這種組合讓它能更簡單且自動地處理併發問題。

(你需要 Clojure 1.2 或是更新的版本)


```clojure
; 註解 從分號開始

; Clojure 程式碼是由一個一個 形式 (form) 所組成，即由小括號包住的語句，這些語句透過空白符號來進行分隔。

; Clojure 解釋器會把第一個元素當作一個函式或是巨集來使用，剩下的則被認為是參數。

; 檔案的第一行程式碼，我們會使用 `ns` 來開始，這是用來指定當前的命名空間 (namespace)
(ns learnclojure)

; 更多基礎範例:

; str 會使用所有參數來創建一個字串
(str "Hello" " " "World") ; => "Hello World"

; 數學運算就是這樣
(+ 1 1) ; => 2
(- 2 1) ; => 1
(* 1 2) ; => 2
(/ 2 1) ; => 2

; 判斷東西是否相等使用 =
(= 1 1) ; => true
(= 2 1) ; => false

; 邏輯判斷
(not true) ; => false

; 巢狀的形式 (form) 執行起來也應該和你預想的一樣
(+ 1 (- 3 2)) ; = 1 + (3 - 2) => 2

; 型別
;;;;;;;;;;;;;

; Clojure 使用 Java 的物件 (Object) 來描述布林值、字串和數字
; 使用 `class` 可以查看具體的類型
(class 1) ; 整數默認是 java.lang.Long 
(class 1.); 浮點數則是 java.lnag.Double
(class ""); 字串是 java.lang.String ，要記得用雙引號包起來
(class false) ; 布林值則是 java.lang.Boolean
(class nil); `null` 被稱為 nil

; If you want to create a literal list of data, use ' to stop it from
; being evaluated
'(+ 1 2) ; => (+ 1 2)
; (shorthand for (quote (+ 1 2)))

; You can eval a quoted list
(eval '(+ 1 2)) ; => 3

; Collections & Sequences
;;;;;;;;;;;;;;;;;;;

; Lists are linked-list data structures, while Vectors are array-backed.
; Vectors and Lists are java classes too!
(class [1 2 3]); => clojure.lang.PersistentVector
(class '(1 2 3)); => clojure.lang.PersistentList

; A list would be written as just (1 2 3), but we have to quote
; it to stop the reader thinking it's a function.
; Also, (list 1 2 3) is the same as '(1 2 3)

; "Collections" are just groups of data
; Both lists and vectors are collections:
(coll? '(1 2 3)) ; => true
(coll? [1 2 3]) ; => true

; "Sequences" (seqs) are abstract descriptions of lists of data.
; Only lists are seqs.
(seq? '(1 2 3)) ; => true
(seq? [1 2 3]) ; => false

; A seq need only provide an entry when it is accessed.
; So, seqs which can be lazy -- they can define infinite series:
(range 4) ; => (0 1 2 3)
(range) ; => (0 1 2 3 4 ...) (an infinite series)
(take 4 (range)) ;  (0 1 2 3)

; Use cons to add an item to the beginning of a list or vector
(cons 4 [1 2 3]) ; => (4 1 2 3)
(cons 4 '(1 2 3)) ; => (4 1 2 3)

; Conj will add an item to a collection in the most efficient way.
; For lists, they insert at the beginning. For vectors, they insert at the end.
(conj [1 2 3] 4) ; => [1 2 3 4]
(conj '(1 2 3) 4) ; => (4 1 2 3)

; Use concat to add lists or vectors together
(concat [1 2] '(3 4)) ; => (1 2 3 4)

; Use filter, map to interact with collections
(map inc [1 2 3]) ; => (2 3 4)
(filter even? [1 2 3]) ; => (2)

; Use reduce to reduce them
(reduce + [1 2 3 4])
; = (+ (+ (+ 1 2) 3) 4)
; => 10

; Reduce can take an initial-value argument too
(reduce conj [] '(3 2 1))
; = (conj (conj (conj [] 3) 2) 1)
; => [3 2 1]

; Functions
;;;;;;;;;;;;;;;;;;;;;

; Use fn to create new functions. A function always returns
; its last statement.
(fn [] "Hello World") ; => fn

; (You need extra parens to call it)
((fn [] "Hello World")) ; => "Hello World"

; You can create a var using def
(def x 1)
x ; => 1

; Assign a function to a var
(def hello-world (fn [] "Hello World"))
(hello-world) ; => "Hello World"

; You can shorten this process by using defn
(defn hello-world [] "Hello World")

; The [] is the list of arguments for the function.
(defn hello [name]
  (str "Hello " name))
(hello "Steve") ; => "Hello Steve"

; You can also use this shorthand to create functions:
(def hello2 #(str "Hello " %1))
(hello2 "Fanny") ; => "Hello Fanny"

; You can have multi-variadic functions, too
(defn hello3
  ([] "Hello World")
  ([name] (str "Hello " name)))
(hello3 "Jake") ; => "Hello Jake"
(hello3) ; => "Hello World"

; Functions can pack extra arguments up in a seq for you
(defn count-args [& args]
  (str "You passed " (count args) " args: " args))
(count-args 1 2 3) ; => "You passed 3 args: (1 2 3)"

; You can mix regular and packed arguments
(defn hello-count [name & args]
  (str "Hello " name ", you passed " (count args) " extra args"))
(hello-count "Finn" 1 2 3)
; => "Hello Finn, you passed 3 extra args"


; Maps
;;;;;;;;;;

; Hash maps and array maps share an interface. Hash maps have faster lookups
; but don't retain key order.
(class {:a 1 :b 2 :c 3}) ; => clojure.lang.PersistentArrayMap
(class (hash-map :a 1 :b 2 :c 3)) ; => clojure.lang.PersistentHashMap

; Arraymaps will automatically become hashmaps through most operations
; if they get big enough, so you don't need to worry.

; Maps can use any hashable type as a key, but usually keywords are best
; Keywords are like strings with some efficiency bonuses
(class :a) ; => clojure.lang.Keyword

(def stringmap {"a" 1, "b" 2, "c" 3})
stringmap  ; => {"a" 1, "b" 2, "c" 3}

(def keymap {:a 1, :b 2, :c 3})
keymap ; => {:a 1, :c 3, :b 2}

; By the way, commas are always treated as whitespace and do nothing.

; Retrieve a value from a map by calling it as a function
(stringmap "a") ; => 1
(keymap :a) ; => 1

; Keywords can be used to retrieve their value from a map, too!
(:b keymap) ; => 2

; Don't try this with strings.
;("a" stringmap)
; => Exception: java.lang.String cannot be cast to clojure.lang.IFn

; Retrieving a non-present key returns nil
(stringmap "d") ; => nil

; Use assoc to add new keys to hash-maps
(def newkeymap (assoc keymap :d 4))
newkeymap ; => {:a 1, :b 2, :c 3, :d 4}

; But remember, clojure types are immutable!
keymap ; => {:a 1, :b 2, :c 3}

; Use dissoc to remove keys
(dissoc keymap :a :b) ; => {:c 3}

; Sets
;;;;;;

(class #{1 2 3}) ; => clojure.lang.PersistentHashSet
(set [1 2 3 1 2 3 3 2 1 3 2 1]) ; => #{1 2 3}

; Add a member with conj
(conj #{1 2 3} 4) ; => #{1 2 3 4}

; Remove one with disj
(disj #{1 2 3} 1) ; => #{2 3}

; Test for existence by using the set as a function:
(#{1 2 3} 1) ; => 1
(#{1 2 3} 4) ; => nil

; There are more functions in the clojure.sets namespace.

; Useful forms
;;;;;;;;;;;;;;;;;

; Logic constructs in clojure are just macros, and look like
; everything else
(if false "a" "b") ; => "b"
(if false "a") ; => nil

; Use let to create temporary bindings
(let [a 1 b 2]
  (> a b)) ; => false

; Group statements together with do
(do
  (print "Hello")
  "World") ; => "World" (prints "Hello")

; Functions have an implicit do
(defn print-and-say-hello [name]
  (print "Saying hello to " name)
  (str "Hello " name))
(print-and-say-hello "Jeff") ;=> "Hello Jeff" (prints "Saying hello to Jeff")

; So does let
(let [name "Urkel"]
  (print "Saying hello to " name)
  (str "Hello " name)) ; => "Hello Urkel" (prints "Saying hello to Urkel")


; Use the threading macros (-> and ->>) to express transformations of
; data more clearly.

; The "Thread-first" macro (->) inserts into each form the result of
; the previous, as the first argument (second item)
(->  
   {:a 1 :b 2} 
   (assoc :c 3) ;=> (assoc {:a 1 :b 2} :c 3)
   (dissoc :b)) ;=> (dissoc (assoc {:a 1 :b 2} :c 3) :b)

; This expression could be written as:
; (dissoc (assoc {:a 1 :b 2} :c 3) :b)
; and evaluates to {:a 1 :c 3}

; The double arrow does the same thing, but inserts the result of
; each line at the *end* of the form. This is useful for collection
; operations in particular:
(->>
   (range 10)
   (map inc)     ;=> (map inc (range 10)
   (filter odd?) ;=> (filter odd? (map inc (range 10))
   (into []))    ;=> (into [] (filter odd? (map inc (range 10)))
                 ; Result: [1 3 5 7 9]

; 模組
;;;;;;;;;;;;;;;

; 使用 `use` 來導入模組內的所有函式
(use 'clojure.set)

; 然後我們就可以直接使用 `set` 相關的函式了
(intersection #{1 2 3} #{2 3 4}) ; => #{2 3}
(difference #{1 2 3} #{2 3 4}) ; => #{1}

; 你也可以選擇只導入要用到的函式
(use '[clojure.set :only [intersection]])

; 使用 `require` 來導入一個模組
(require 'clojure.string)

; 用 `/` 來呼叫模組內的函式
; 下面是從 `clojure.string` 這個模組裡面呼叫 `blank?` 函式的方法
(clojure.string/blank? "") ; => true

; 載入模組的時候，你也可以給他們較短的別名
(require '[clojure.string :as str])
(str/replace "This is a test." #"[a-o]" str/upper-case) ; => "THIs Is A tEst."
; (#"" 用來表達一個正規表達式)

; 你可以在 `ns` 裡面使用 `:require` 來載入要使用的模組
; 記得這樣使用的話，不需要額外的單引號 `'`
(ns test
  (:require
    [clojure.string :as str]
    [clojure.set :as set]))

; Java
;;;;;;;;;;;;;;;;;

; Java 有大量優秀的函式庫，你肯定會想知道如何在 Clojure 內使用這些 Java 函式庫

; 使用 import 來導入 java 模組
(import java.util.Date)

; 也可以在 ns 定義要導入的模組
(ns test
  (:import java.util.Date
           java.util.Calendar))

; 在類別尾端加入 `.` 的方式來 new 一個 java 物件
(Date.) ; <a date object>

; Use . to call methods. Or, use the ".method" shortcut
; 使用 `.` 來呼叫 java 方法，或者用 `.method` 這種簡化方式
(. (Date.) getTime) ; <a timestamp>
(.getTime (Date.)) ; 和上例同義

; 用 `/` 呼叫靜態方法
(System/currentTimeMillis) ; <a timestamp> (system is always present)

; 使用 `doto` 來更方便的呼叫 (可變的) java類別 
(import java.util.Calendar)
(doto (Calendar/getInstance)
  (.set 2000 1 1 0 0 0)
  .getTime) ; => A Date. set to 2000-01-01 00:00:00

; STM
;;;;;;;;;;;;;;;;;

; Software Transactional Memory is the mechanism clojure uses to handle
; persistent state. There are a few constructs in clojure that use this.

; An atom is the simplest. Pass it an initial value
(def my-atom (atom {}))

; Update an atom with swap!.
; swap! takes a function and calls it with the current value of the atom
; as the first argument, and any trailing arguments as the second
(swap! my-atom assoc :a 1) ; Sets my-atom to the result of (assoc {} :a 1)
(swap! my-atom assoc :b 2) ; Sets my-atom to the result of (assoc {:a 1} :b 2)

; Use '@' to dereference the atom and get the value
my-atom  ;=> Atom<#...> (Returns the Atom object)
@my-atom ; => {:a 1 :b 2}

; Here's a simple counter using an atom
(def counter (atom 0))
(defn inc-counter []
  (swap! counter inc))

(inc-counter)
(inc-counter)
(inc-counter)
(inc-counter)
(inc-counter)

@counter ; => 5

; Other STM constructs are refs and agents.
; Refs: http://clojure.org/refs
; Agents: http://clojure.org/agents
```

### 延伸閱讀

本文肯定無法涵蓋 Clojure 的一切，但是希望足以讓你邁出第一步。

Clojure.org 官網有許多文章:
[http://clojure.org/](http://clojure.org/)

Clojuredocs.org 有核心函式的文檔與範例:
[http://clojuredocs.org/quickref/Clojure%20Core](http://clojuredocs.org/quickref/Clojure%20Core)

4Clojure 是一個很棒的 Clojure/FP 相關技能練習網站:
[http://www.4clojure.com/](http://www.4clojure.com/)

Clojure-doc.org 有許多入門級的文章:
[http://clojure-doc.org/](http://clojure-doc.org/)
