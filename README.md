# Fuzzing

### Contents

* [Introduction](#introduction)
* [Usage](#usage)
* [Examples](#examples)

### Introduction

+ In programming and software development, fuzzing or fuzz testing is an automated software testing technique that
  involves providing invalid, unexpected, or random data as inputs to a computer program.
    + Failing built-in code assertions
    + Potential memory leaks

**Fuzz testing is a process of testing APIs with generated data.** The most common forms are:
- **Mutation based fuzzing** which mutates existing data samples (aka the test corpus) to create test data;
- **Generation based fuzzing** which produces new test data based on models of the input.


**Fuzz testing is valuable for:**
- Software that receives inputs from untrusted sources **(security)**;
- Sanity checking the equivalence of two complex algorithms **(correctness)**;
- Verifying the stability of a high-volume API that takes complex inputs **(stability)**,
  e.g. a decompressor, even if all the inputs are trusted.
- 
**Fuzzing is typically used to find the following kinds of bugs:**
- Bugs specific to C/C++ that require the *[sanitizers](https://github.com/google/sanitizers)* to catch:
  - Use-after-free, buffer overflows
  - Uses of uninitialized memory
  - Memory leaks
- Arithmetic bugs:
  - Div-by-zero, int/float overflows, invalid bitwise shifts
- Plain crashes:
  - NULL dereferences, Uncaught exceptions
- Concurrency bugs:
  - Data races, Deadlocks
- Resource usage bugs:
  - Memory exhaustion, hangs or infinite loops, infinite recursion (stack overflows)
- Logical bugs:
  - Discrepancies between two implementations of the same protocol ([example](https://blog.fuzzing-project.org/31-Fuzzing-Math-miscalculations-in-OpenSSLs-BN_mod_exp-CVE-2015-3193.html))
  - Round-trip consistency bugs (e.g. compress the input, decompress back, - compare with the original)
  - Assertion failures

### Usage
Fuzzing is used mostly as an automated technique to expose vulnerabilities in security-critical programs that might be exploited with malicious intent.

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