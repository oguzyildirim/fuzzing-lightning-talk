# Fuzzing

### Contents

* [Introduction](#introduction)
* [Usage](#usage)
* [Examples](#examples)

### Introduction

+ Fuzzing is a technique used in software testing to find bugs or vulnerabilities in a program by automatically generating and inputting random or malformed data.

**Fuzz testing is valuable for:**
- Testing the robustness of a software application against different inputs
- Finding vulnerabilities or security weaknesses in software
- Improving software quality by uncovering edge cases and boundary conditions that might not be discovered in manual testing.

In summary, fuzzing is an automated technique for discovering bugs and vulnerabilities in software applications by feeding them with random or malformed input data.

### Usage

Go supports fuzzing in its standard toolchain beginning in Go 1.18. Native Go fuzz tests are supported by [OSS-Fuzz](https://google.github.io/oss-fuzz/getting-started/new-project-guide/go-lang/#native-go-fuzzing-support).

```go
func FuzzReverse(f *testing.F) {
  testcases := []string{"Hello, world", " ", "!12345"}
  for _, tc := range testcases {
    f.Add(tc) // Use f.Add to provide a seed corpus
  }
  f.Fuzz(func (t *testing.T, orig string) {
      rev, err1 := Reverse(orig)
      if err1 != nil {
      return
    }
    doubleRev, err2 := Reverse(rev)
    if err2 != nil {
      return
    }
    if orig != doubleRev {
      t.Errorf("Before: %q, after: %q", orig, doubleRev)
    }
    if utf8.ValidString(orig) && !utf8.ValidString(rev) {
      t.Errorf("Reverse produced invalid UTF-8 string %q", rev)
    }
  })
}
```

### More

- https://security.googleblog.com/2021/03/fuzzing-java-in-oss-fuzz.html
- https://go.dev/doc/tutorial/fuzz
- https://github.com/google/fuzzing
- https://github.com/google/sanitizers
- https://github.com/google/gofuzz
- https://blog.jetbrains.com/go/2022/12/14/understanding-fuzz-testing-in-go/
- https://opensource.com/article/22/1/native-go-fuzz-testing
- https://github.com/golang/go/issues/44551
- https://llvm.org/docs/LibFuzzer.html
- https://lcamtuf.coredump.cx/afl/
- https://aflplus.plus/
- https://github.com/AFLplusplus/AFLplusplus
- https://github.com/google/oss-fuzz
- https://owasp.org/www-community/Fuzzing