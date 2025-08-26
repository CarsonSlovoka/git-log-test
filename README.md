# Git Log Line Range Testing

## Purpose

This project demonstrates the behavior of the `git log -L` command, which tracks changes to specific line ranges in a file (`main.go`) across commits.

The examples below use two commits ([f68d0984] and [894f74de]) to show how `git log -L` handles different line range scenarios.


Can you identify what possible scenarios might arise?

```sh
git log -L1,3:main.go -L6,10:main.go f68d0984^..f68d0984
```

> [!NOTE]
> If you're not sure, you can take a look at the following tests to better understand the behavior of git log

## Test Cases

### 1. Adding a New File

The `main.go` file at commit [f68d0984] is a newly **added** file,

so the git log at this point will only show one section, and no other sections will appear. (`-L6,10` miss)

**Command:**
```sh
git log -L1,3:main.go -L6,10:main.go f68d0984^..f68d0984
```

**Output:**
```diff
+┊1┊package main
+┊2┊
+┊3┊import "fmt"
```


---

### 2. Ranges in the Same Code Block

Lines 5-7 and 9-10 belong to the same section, so the git log will only show one section. (`-L9,10` miss)

**Command:**
```sh
git log -L5,7:main.go -L9,10:main.go 894f74de^..894f74de
```

**Output:**
```diff
+┊5┊const VERSION = "0.0.0"
+┊6┊
+┊7┊func init() {
```


---

### 3. Adjacent or Overlapping Ranges

When two line ranges are adjacent or overlapping, `git log -L` combines them into a single range and shows all changes within it.

**Command:**
```sh
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

---

### 4. Unchanged Line Ranges

If the specified line range has no changes in the commit, `git log -L` outputs nothing.

**Command:**
```sh
git log -L11,14:main.go 894f74de^..894f74de
```

**Output:**
```
```

Lines 11-14 in `main.go` have no changes in commit [894f74de], so no output is produced.

---


As long as the range specified by -L includes lines with changes, those changes will be displayed.

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

---

### 5. Multiple Non-Overlapping Ranges

When multiple non-overlapping ranges are specified, `git log -L` shows changes for each range separately.

**Command:**
```sh
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

Line 5 and lines 15-16 are distinct ranges with changes in commit [894f74de]. The output shows changes for each range separately, with appropriate `diff` headers.


[f68d0984]: https://github.com/CarsonSlovoka/git-log-test/commit/f68d0984
[894f74de]: https://github.com/CarsonSlovoka/git-log-test/commit/894f74de
