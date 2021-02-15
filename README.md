# Donk Buildbase

This repository can be used as the root of a Bazel workspace in order to quickly
download and build all released parts of Donk Project.

## Requirements

- Bazel 3.5 or above

## Instructions

Simply clone the repository. The following targets are the most interesting:

- `@donk_transpiler//:main` is the transpiler program. Provide it a transpiled
  AST in binary proto format, like so:

  ```
  $ bazel run @donk_transpiler//:main -- \
      --input_proto /path/to/dme/output.binarypb \
      --output_path /path/to/tgcc
  ```

  This will emit the source code for the project in the directory specified by
  `--output_path`.

- `@donk_interpreter//runner` is a binary which includes the interpreter and the
  emitted source code for a DM environment in C++, and runs the proc `/main()`
  upon execution. It takes no arguments.

- `@donk_transpiler//:coregen` is a binary which emits the basic class, field,
  and proc declarations for the core BYOND API. It takes two arguments,
  `--output_path` as in the main transpiler above, and `--include_prefix`, which
  is prefixed to every self-referenced include directive.

## Generating an AST

In order to provide a BYOND environment in the proper format, clone the `pb2`
branch of the SpacemanDMM repository at
`git@github.com:warriorstar-orion/SpacemanDMM.git`. Then, from within the
working directory of a BYOND environment that compiles cleanly, use the `dm2pb`
script to generate the binary proto, as so:

```
$ cargo run --manifest-path=path_to_SpacemanDMM_repo/src/dm2pb/Cargo.toml \
    --bin=dreammaker2pb
```

The resultant `output.binarypb` file can be fed to the Donk Transpiler above. In
the future, hopefully this step can be reduced to another Bazel command.

## License

Donk Project is Copyright 2021 (c) Warriorstar Orion and released under the MIT
license.