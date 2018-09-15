- Feature Name: std_path_utlities
- Start Date: 2018-09-15
- RFC PR: (leave this empty)
- Rust Issue: (leave this empty)

# Summary

As part of feedback gathered by the CLI-WG we have concluded that there are several key aspects of path management missing from the Rust stdlib structures. Most of this pre-RFC are based on the summary made by this [comment].

[comment]: https://github.com/rust-lang-nursery/cli-wg/issues/10#issuecomment-407809608

# Motivation

We believe that the addition of these functions will have a positive impact on the Rust ecosystem as a whole, enabling users to write CLI applications more quickly, without having to re-invent the wheel and while also allowing these functions to fit neatly into already existing structures and modules.

# Guide-level explanation

Additions to the `stdlib` are made to the `fs` module, the `path::PathBuf` and `path::Path` constructs.

Firstly, a `fs::normalize` function is added that operates simmilarly to `fs::canonicalize`, except that it doesn't actually touch the filesystem. This means that it wouldn't fail to normalize a path if a directory does not exist on the filesystem.

**Non-destructive `PathBuf::push`**

The `PathBuf` will gain a new, non-destructive `append` function which will operate similarly to the already existing `push`, except never overwriting the buffer, even if the provided path segment is absolute.

```rust
let mut path = PathBuf::from("/usr"); // --> /usr
path.append("/lib");                  // --> /usr/lib
path.push("/etc");                    // --> /etc
```

**Non-destructive `Path::join`**

The `Path` will gain a new function `concat` which operates simmilarly as `PathBuf::append`, always appending provided path segments, even if they were absolute.

```rust
Path::new("/usr").concat("/lib"); // --> /usr/lib
Path::new("/usr").join("/lib");   // --> /lib
```

**`Path::relative_to` and `Path::absolute`**

`Path` would also gain two more functions for computing relative paths to a base one, as well as the absolute base path for any given relative one. These functions would require the current working directory (CWD) to be available. Although in cases where `Path::starts_with` is true, `relative_to` would not require it.

# Reference-level explanation

While `fs::normalize` provides the same functional output as `fs::canonicalize`, it's implementation is quite different, as it can not rely on either `realpath` (for unix) or `CreateFile` (Windows) to do it's job. This means that file path canonicalization needs to be done on it's own, or using some other backend implementation.

The additional `PathBuf` and `Path` functions work similarly in their design that a lot of the code can be shared from `PathBuf::push` and `Path::join` respectively, while those functions can become wrappers around `PathBuf::append` and `Path::concat` with different input sanitisation/ handling.

For `Path::relative_to` and `Path::absolute` the most leg-work is required. Because in cases where `Path::starts_with` for the base-path of `Path::relative_to` is false, the CWD is required to compute the path difference, there should be a generalized way of getting the current working directory. Optionally another function could be added to the `fs` module, that exposes this, however this is entirely optional.

# Drawbacks

Especially the functions `Path::relative_to` and `Path::absolute` require additional utilities to be available in the `fs` module and could add unwanted complexity to it. In case this is a problem, these two functions can be excluded, with a crate potentially implementing them, without having any impact on the other additions made.

For the other functions no drawbacks exist that we are aware of at this time.

# Rationale and alternatives

While all of these functions could be provided via external crates, we believe that the inclusion into `fs`, `PathBuf` and `Path` respectively will yield the best developer experience in the long run, providing common ground between different applications and libraries, that want to use these functions, alike.

Overall we believe the inclusion of these functions in the `stdlib` to have a positive effect on the Rust ecosystem.
