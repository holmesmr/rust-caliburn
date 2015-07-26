# rust-caliburn [![Build Status][ci-build-stat]][ci-link]
A strictly compliant parser for the RFC2812 (IRC Client Protocol) specification,
written in Rust. Currently targets Rust 1.1.

## Notice
rust-caliburn is currently not production ready and should be considered
unstable. It is also not feature complete.

### Performance
rust-caliburn uses [rust-peg][rust-peg] as its parser backend, which implements
PEG but does not currently support memoization - as a result, parts of the
parsing, especially splitting into space-delimited parameters, run slower quickly as parsing becomes more complex (as can be seen in the below example):
```
test tests::line_parser::benchmarks::bench_complex_parse   ... bench:       5,307 ns/iter (+/- 517)
test tests::line_parser::benchmarks::bench_ludicrous_parse ... bench:      16,464 ns/iter (+/- 3,608)
test tests::line_parser::benchmarks::bench_moderate_parse  ... bench:       4,336 ns/iter (+/- 2,489)
test tests::line_parser::benchmarks::bench_simple_parse    ... bench:       1,391 ns/iter (+/- 180)
```

Performance may not be the same in Rust stable (1.1 as of writing), as these
benchmarks were run using `rustc-1.3.0-nightly (82d40cb2b 2015-07-24)`, since
`cargo bench` only currently runs on nightly builds.

## Usage
rust-caliburn is *not* an IRC client library; it does not implement the logic
behind the protocol, it only validates IRC client message lines and parses them
into a machine-readable form. It is intended to be a ready-to-use solution for
IRC servers and clients to use so that they comply with IRC interop standards
without significant work.

### Parsing lines
TODO: Add instructions
There are examples of using the parser in unit tests available `tests/mod.rs`.

## Compliance
rust-caliburn follows the RFC2812 spec as closely as possible for anything but
exceptional circumstances, including:

 * Assuming that prefixes of the form `:localhost` are servers, not users

 * IP addresses are not strictly validated (IPs like `999.999.999.999`, while
      invalid, are accepted)

 * Any command may have no more than 15 parameters

The only deviations from RFC2812 are:

 * No fixed length limit to nicknames - it is technically against
   the RFC2812 specification to accept nicknames longer than 10 chars, but
   the vast majority of IRCds and clients in current use do not follow this
   behaviour to the point where enforcing compliance may affect use of the
   library

## Implementation status

### What is currently implemented

  * Basic parsing (Commands, user strings, server strings, parameters)

### What remains to be done

#### Communication
  * Serializing rfc2812_types::Message back into IRC message strings

#### Input validation specification to be completed for:
  * Channels and channel keys
  * Mask expressions
  * Command-level validation
  * IPv6 addresses

#### Code hygiene
  * Increased unit test coverage
  * Better commenting in PEG spec

#### Decisions to be made
  * Deviation from RFC2812 may be made to allow forward-slash (0x2F) since
    some networks (like Freenode) use this in host cloaks
  * Deviation from RFC2812 may be made to allow : in non-final parameters,
    used in some protocol extensions

[ci-build-stat]: https://travis-ci.org/ceph3us/rust-caliburn.svg?branch=master
[ci-link]: https://travis-ci.org/ceph3us/rust-caliburn
[rust-peg]: https://github.com/kevinmehall/rust-peg
