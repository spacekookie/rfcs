- Feature Name: (fill me in with a unique ident, my_awesome_feature)
- Start Date: (fill me in with today's date, YYYY-MM-DD)
- RFC PR: (leave this empty)
- Cargo Issues:
  [rust-lang/cargo#1734](https://github.com/rust-lang/cargo/issues/1734)
  [rust-lang/cargo#1976](https://github.com/rust-lang/cargo/issues/1976)
  [rust-lang/cargo#2127](https://github.com/rust-lang/cargo/pull/2127)
  [rust-lang/cargo#9178](https://github.com/rust-lang/cargo/pull/9178)
- Rustup Issues:
  [rust-lang-nursery/rustup.rs#247](https://github.com/rust-lang-nursery/rustup.rs/issues/247)
  [rust-lang-nursery/rustup.rs#473](https://github.com/rust-lang-nursery/rustup.rs/issues/473)

# Summary
[summary]: #summary

Improve Cargo's integration with the host operating system by using a set
of standard paths for config files, cache files, and executables.  This
provides a more consistent experience to users and enables third-party tools
to integrate with Cargo more easily.


# Motivation
[motivation]: #motivation

Currently Cargo puts all of its files in a directory named `.cargo` in the
user's home directory.  Using this proposal it would use existing standard
directories on the host system.  This enables other tools to integrate with
Cargo, while making the experience to end-users more consistent and in-line
with other tools they are already using.


# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

We are going to introduce three new environment variables, and
deprecating the use of `CARGO_HOME`:

- `CARGO_BIN_DIR`
- `CARGO_CACHE_DIR`
- `CARGO_CONFIG_DIR`

As a default these values will be unset, but allow users to customise Cargo paths
independent of how other tools on their system work.  For most users this will be
irrelevant however.

## *nix specifics

In addition to custom environment variables Cargo will also comply with the [XDG]
specification for application paths which is in active use by many other tools on
MacOS, BSD derivatives, and Linux.

By default these paths are as follows.

- `XDG_CONFIG_HOME = $HOME/.config/cargo`
- `XDG_CACHE_HOME = $HOME/.cache/cargo`
- `XDG_BIN_HOME = $HOME/.bin/cargo`

## Windows specifics

On Windows Cargo will use `$%LOCALAPPDATA%\Cargo\Cache`, which is a standard directory
where applications keep their cache files, and 

Explain the proposal as if it was already included in the language and you were teaching it 
to another Rust programmer. That generally means:

- Introducing new named concepts.
- Explaining the feature largely in terms of examples.
- Explaining how Rust programmers should *think* about the feature, and how it should impact the way they use Rust. It should explain the impact as concretely as possible.
- If applicable, provide sample error messages, deprecation warnings, or migration guidance.
- If applicable, describe the differences between teaching this to existing Rust programmers and new Rust programmers.

For implementation-oriented RFCs (e.g. for compiler internals), this section should focus on how compiler contributors should think about the change, and give examples of its concrete impact. For policy RFCs, this section should provide an example-driven introduction to the policy, and explain its impact in concrete terms.

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

This is the technical portion of the RFC. Explain the design in sufficient detail that:

- Its interaction with other features is clear.
- It is reasonably clear how the feature would be implemented.
- Corner cases are dissected by example.

The section should return to the examples given in the previous section, and explain more fully how the detailed proposal makes those examples work.

# Drawbacks
[drawbacks]: #drawbacks

Why should we *not* do this?

# Rationale and alternatives
[rationale-and-alternatives]: #rationale-and-alternatives

- Why is this design the best in the space of possible designs?
- What other designs have been considered and what is the rationale for not choosing them?
- What is the impact of not doing this?

# Prior art
[prior-art]: #prior-art

Discuss prior art, both the good and the bad, in relation to this proposal.
A few examples of what this can include are:

- For language, library, cargo, tools, and compiler proposals: Does this feature exist in other programming languages and what experience have their community had?
- For community proposals: Is this done by some other community and what were their experiences with it?
- For other teams: What lessons can we learn from what other communities have done here?
- Papers: Are there any published papers or great posts that discuss this? If you have some relevant papers to refer to, this can serve as a more detailed theoretical background.

This section is intended to encourage you as an author to think about the lessons from other languages, provide readers of your RFC with a fuller picture.
If there is no prior art, that is fine - your ideas are interesting to us whether they are brand new or if it is an adaptation from other languages.

Note that while precedent set by other languages is some motivation, it does not on its own motivate an RFC.
Please also take into consideration that rust sometimes intentionally diverges from common language features.

# Unresolved questions
[unresolved-questions]: #unresolved-questions

- What parts of the design do you expect to resolve through the RFC process before this gets merged?
- What parts of the design do you expect to resolve through the implementation of this feature before stabilization?
- What related issues do you consider out of scope for this RFC that could be addressed in the future independently of the solution that comes out of this RFC?
