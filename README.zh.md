# 目的

本專案用於測試git log的行為 (`git version 2.43.0`)

你能回答出來，可能會有哪些情況嗎?

```sh
git log -L1,3:main.go -L6,10:main.go f68d0984^..f68d0984
```

> [!NOTE]
> 如果你沒有把握，那麼可以看看以下的測試，那您更瞭解git log的行為


# Test

## 新增測試

[f68d0984] 這個節點的的main.go是新增檔案，所以此時的git log只能有一段，其它的都不會出現


**Command:**
```sh
# git diff f68d0984^..f68d0984
git log -L1,3:main.go -L6,10:main.go f68d0984^..f68d0984
```

**Output:**
```diff
+┊1┊package main
+┊2┊
+┊3┊import "fmt"
```

## 同段測試

[894f74de]

`5-7` 和 `9-10` 都是同樣的一段，所以此時git log也只會出現一段而言(先給的)

**Command:**
```sh
# git diff 894f74de^..894f74de
git log -L5,7:main.go -L9,10:main.go 894f74de^..894f74de
```

**Output:**
```diff
+┊5┊const VERSION = "0.0.0"
+┊6┊
+┊7┊func init() {
```


`5-7` 和 `8-10` 雖然也是同樣一段，但是5-7, 8-10它們併起來，其實就等同5-10, 所以這樣的呈現會是完整的(即兩段的-L都會輸出)

**Command:**
```sh
# git diff 894f74de^..894f74de
git log -L5,7:main.go -L8,10:main.go 894f74de^..894f74de
```

**Output:**
```diff
+┊ 5┊const VERSION = "0.0.0"
+┊ 6┊
+┊ 7┊func init() {
+┊ 8┊
+┊ 9┊}
+┊10┊
```

## 指定的範圍不再異動內

**Command:**
```sh
# git diff 894f74de^..894f74de
git log -L11,14:main.go 894f74de^..894f74de
```

**Output:** 沒有東西，因為11-14並沒有任何的異動
```
```


**Command:**
```sh
git log -L11,16:main.go 894f74de^..894f74de
```

**Output:**
```diff
  5┊11┊func main() {
  6┊12┊        fmt.Println("Test git diff")
  7┊13┊
  8┊14┊        fmt.Println("Hello world")
  9┊15┊
-10┊  ┊        fmt.Println("0123456789")
+  ┊16┊        fmt.Println(VERSION)
```

只要-L指定的範圍內，最後有碰到異動的行，就可以呈現


## 多個範圍


**Command:**
```sh
# git diff 894f74de^..894f74de
git log -L5,5:main.go -L15,16:main.go 894f74de^..894f74de
```

**Output:**
```diff
main.go:5:
+ ┊5┊const VERSION = "0.0.0"

main.go:15:
 9┊15┊
-10┊  ┊        fmt.Println("0123456789")
+  ┊16┊        fmt.Println(VERSION)
```


[f68d0984]: https://github.com/CarsonSlovoka/git-log-test/commit/f68d0984
[894f74de]: https://github.com/CarsonSlovoka/git-log-test/commit/894f74de
