## analyzer_exploration

![coverage][coverage_badge]
[![style: very good analysis][very_good_analysis_badge]][very_good_analysis_link]
[![License: MIT][license_badge]][license_link]

CLI tool that explores your Dart project with the analyzer and prints repository class scaffolds for any class annotated with `@Annotation` under `lib/`. Built with `args`, `cli_completion`, `mason_logger`, and `analyzer`.

---

## Install

From pub (if published):

```sh
dart pub global activate analyzer_exploration
```

From a local path:

```sh
dart pub global activate --source=path <path-to-this-package>
```

Make sure `~/.pub-cache/bin` (or your OS equivalent) is in your PATH.

---

## Usage

Global flags:

- `--version` / `-v`: print the current version
- `--verbose`: enable detailed logging

Subcommands:

- `generate`: scan `lib/` for classes annotated with `@Annotation` and print a repository scaffold for each match
- `completion`: shell completion script support (from `cli_completion`)

Examples:

```sh
# Show version
analyzer_exploration --version

# Generate repository scaffolds (printed to stdout)
analyzer_exploration generate

# Verbose logging
analyzer_exploration --verbose generate
```

Sample annotated class and expected output:

```dart
// lib/annotation.dart
class Annotation {
  const Annotation();
}

// lib/src/data/models.dart
@Annotation()
class MyDataClass {
  String id = 'abc';
}
```

Running:

```sh
analyzer_exploration generate
```

Prints a scaffold similar to:

```dart
class MyDataClassRepository {
  final Map<String, Data> _cache = <String, MyDataClass>{};

  MyDataClass? getById(String id) => _cache[id];

  void save(MyDataClass obj) => _cache[obj.id] = obj;
}
```

Notes:

- The tool analyzes files under `lib/` only.
- Output is printed to stdout; redirect to a file if you want to save it.

---

## How it works

- Uses `AnalysisContextCollection` from `package:analyzer` to resolve Dart units under `lib/`.
- Walks each `CompilationUnit`, finds `ClassDeclaration`s with the `@Annotation` metadata.
- For each match, builds a repository class using `code_builder` and formats with `dart_style`.

Key entry points:

- CLI entry: `bin/analyzer_exploration.dart`
- Command runner: `lib/src/command_runner.dart`
- Generate command: `lib/src/commands/generate_command.dart`
- Marker annotation: `lib/annotation.dart`

---

## Development

Environment:

- Dart SDK: ^3.9.0

Install dependencies:

```sh
dart pub get
```

Run tests:

```sh
dart test
```

Verify code generation metadata is up to date:

```sh
dart test -t version-verify
```

Coverage (optional):

```sh
dart pub global activate coverage 1.15.0
dart test --coverage=coverage
dart pub global run coverage:format_coverage --lcov --in=coverage --out=coverage/lcov.info
```

Open HTML report (requires `genhtml`):

```sh
genhtml coverage/lcov.info -o coverage/
open coverage/index.html
```

---

## License

This project is licensed under the MIT License. See the badge/link below.

---

[coverage_badge]: coverage_badge.svg
[license_badge]: https://img.shields.io/badge/license-MIT-blue.svg
[license_link]: https://opensource.org/licenses/MIT
[very_good_analysis_badge]: https://img.shields.io/badge/style-very_good_analysis-B22C89.svg
[very_good_analysis_link]: https://pub.dev/packages/very_good_analysis
[very_good_cli_link]: https://github.com/VeryGoodOpenSource/very_good_cli