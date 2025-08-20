# Jason - OneScript JSON Serialization Library

Jason is a OneScript library for convenient JSON serialization and deserialization of custom script classes. The library is written in Russian and follows OneScript conventions.

**IMPORTANT: Always reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.**

## Working Effectively

### Current Environment (GitHub Actions)
The repository runs in a GitHub Actions environment with OneScript installed via `oscript-library/ovm`:
- **OneScript Version**: 2.0.0-rc.8 (matches packagedef requirement)
- **OPM Version**: 1.5.1 (current and fully functional)
- **OneUnit Version**: 0.2.4 (working test framework)
- **Installation Method**: via `ovm` (OneScript Version Manager), no apt/sudo needed

### Using GitHub Actions (Recommended)
- The repository includes `.github/workflows/copilot-setup-steps.yml` for automated setup
- This workflow uses `otymko/setup-onescript@v1.5` action for proper OneScript installation
- Run from Actions tab: "OneScript Development Environment Setup" workflow
- **ADVANTAGE**: Handles version compatibility and dependency management automatically
- **ADVANTAGE**: Uses updated opm version that supports newer packages

### Manual Local Setup (Alternative)
If you need to set up locally:

#### Prerequisites and Setup
- Install OneScript runtime via `oscript-library/ovm`:
  - Follow ovm installation instructions from oscript-library/ovm repository
  - Install OneScript 2.0.0-rc.8: `ovm install 2.0.0-rc.8`
  - Activate version: `ovm use 2.0.0-rc.8`
  - Verify: `oscript --version` should show "1Script Execution Engine. Version 2.0.0-rc.8"

### Install Dependencies
#### Via GitHub Actions (Recommended)
- The setup workflow automatically installs dependencies via `opm install -l --dev`
- Additionally installs OneUnit globally with `opm install oneunit`
- This includes updated opm and resolves version compatibility issues

#### Manual Installation (if needed)
- Install core dependencies locally with `-l` flag (required for library to work):
  - `opm install -l annotations` -- takes 10-30 seconds. NEVER CANCEL.
  - `opm install -l logos` -- takes 10-30 seconds. NEVER CANCEL. 
  - `opm install -l reflector` (installed automatically with annotations)
  - `opm install -l oneunit` -- for running tests, takes 30-60 seconds. NEVER CANCEL.
- Libraries install to `oscript_modules/` directory locally
- Verify installation: `ls oscript_modules/` should show annotations, logos, reflector directories
- OneUnit executable available at `oscript_modules/bin/oneunit` (no global installation needed)

### Build and Syntax Checking
- Check syntax of individual files: `oscript -check src/Классы/СериализаторJson.os` -- takes ~0.3 seconds
- Check all source files: `find src -name "*.os" -exec oscript -check {} \;` -- takes 1-2 seconds total
- Build: No explicit build step required - OneScript is interpreted

### Testing and Validation
- **Tests work perfectly**: OneUnit 0.2.4 is fully functional
- **JSON serialization and deserialization works**: Library successfully serializes objects to JSON and deserializes them back
- Run tests: `oneunit execute` -- takes ~5 seconds, runs all tests in ./tests directory
- Test output shows: "4 Тестов успешных" (4 successful tests) across 2 test suites
- Individual test validation: Classes can be instantiated, serialized, and deserialized successfully

### Functional Testing Status
- ✅ Class instantiation: `Новый СериализаторJson()` works
- ✅ Method existence: Public methods are accessible  
- ✅ JSON serialization: `Сериализовать(Объект)` method works perfectly
- ✅ JSON deserialization: `ДесериализаторJson` class works for converting JSON back to objects
- ✅ Full test suite: OneUnit runs tests successfully (4 tests across 2 suites)
- ✅ Annotations: `&Сериализуемое` and `&Несериализуемое` annotations work

### Manual Validation Scenarios
After making changes to the library, validate by:
1. **Basic instantiation test**: Create simple test script that instantiates СериализаторJson
2. **Syntax validation**: Run syntax checks on all modified .os files  
3. **Package definition check**: Verify that `packagedef` file is syntactically correct
4. **Dependency verification**: Check dependencies are properly declared in packagedef
5. **Code structure review**: Ensure annotations and class structure follow OneScript conventions
6. **Test execution**: Run `oneunit execute` to ensure all tests pass

**Sample validation script:**
```onescript
#Использовать "/home/runner/work/jason/jason"

Процедура ТестОснов()
    Попытка
        СериализаторJson = Новый СериализаторJson();
        Сообщить("✅ Сериализатор создан успешно");
        
        Объект = Новый Структура("Имя, Возраст", "Тест", 25);
        Результат = СериализаторJson.Сериализовать(Объект);
        Сообщить("✅ Сериализация: " + Результат);
        
        // Десериализация работает с соответствующими типами
        // Подробности использования см. в tests/Десериализация.os
        Сообщить("✅ Библиотека функционирует корректно");
    Исключение
        Сообщить("❌ Ошибка: " + ОписаниеОшибки());
    КонецПопытки;
КонецПроцедуры

ТестОснов();
```

## Repository Structure

### Key Files and Directories
```
/home/runner/work/jason/jason/
├── .github/
│   ├── workflows/
│   │   ├── copilot-setup-steps.yml  # GitHub Actions setup workflow
│   │   ├── rebase.yml               # Rebase workflow
│   │   └── test.yml                 # Test workflow (uses autumn-library/workflows)
│   └── copilot-instructions.md      # This file
├── .vscode/                         # VS Code configuration
├── src/Классы/
│   ├── СериализаторJson.os          # Main JSON serializer class
│   ├── ДесериализаторJson.os        # JSON deserializer class
│   ├── АннотацияСериализуемое.os    # Serializable annotation
│   └── АннотацияНесериализуемое.os  # Non-serializable annotation
├── tests/
│   ├── Сериализация.os              # Serialization tests
│   ├── Десериализация.os            # Deserialization tests
│   └── Классы/ТестовыйКласс.os      # Test class with annotations
├── packagedef                        # OneScript package definition
├── .bsl-language-server.json        # BSL language server config
├── opm.ospx                         # OPM package file
└── README.md                        # Brief library description (Russian)
```

### Common Commands and Expected Times
- `oscript --version` -- instant, shows "1Script Execution Engine. Version 2.0.0-rc.8"
- `opm --version` -- instant, shows "1.5.1"
- `ls oscript_modules/` -- instant, lists locally installed packages in oscript_modules/
- `opm install -l <package>` -- 10 seconds to 1 minute depending on dependencies. NEVER CANCEL.
- `oscript -check <file.os>` -- 0.3 seconds per file
- `oneunit execute` -- 5 seconds, runs all tests successfully (4 tests, 2 suites)

## Known Issues and Workarounds

### Package Installation Notes
- packagedef contains method "АдресРепозитория" which requires OPM 1.4.0+ (current 1.5.1 supports this)
- All dependencies install cleanly with current OPM version using `-l` flag for local installation
- Libraries install to `oscript_modules/` directory, executables available in `oscript_modules/bin/`
- No manual workarounds needed

## Development Guidelines

### Code Standards
- All source code in Russian language
- Use OneScript annotations: `&Сериализуемое`, `&Несериализуемое`
- Follow OneScript naming conventions (CamelCase with Russian characters)
- Files must be UTF-8 encoded with BOM (﻿)

### Making Changes
1. Always run syntax check after modifying any .os file
2. Verify that packagedef is still valid
3. **Test functionality**: Run `oscript_modules/bin/oneunit execute` to ensure tests pass
4. **Validate serialization**: Test JSON serialization with updated code
5. Use .bsl-language-server.json for IDE support

### Validation Commands to Run Before Committing
```bash
# Check syntax of main serializer and deserializer
oscript -check src/Классы/СериализаторJson.os
oscript -check src/Классы/ДесериализаторJson.os

# Check syntax of test files  
oscript -check tests/Сериализация.os
oscript -check tests/Десериализация.os
oscript -check tests/Классы/ТестовыйКласс.os

# Verify annotations
oscript -check src/Классы/АннотацияСериализуемое.os
oscript -check src/Классы/АннотацияНесериализуемое.os

# Run full test suite using oneunit
oneunit execute
```

## Important Notes

### Timeout Requirements
- **NEVER CANCEL** opm package installations - can take 1+ minutes depending on dependencies
- Set timeouts of 60+ seconds for package installation commands
- Syntax checking is fast (0.3s per file) and doesn't need special timeouts
- Test execution is fast (5s) and doesn't need special timeouts

### Language and Encoding
- All source code, comments, and variable names in Russian
- Files use Cyrillic characters and must be UTF-8 with BOM
- Package uses Russian method names in packagedef (supported by current OPM)

### Dependencies
- annotations v1.2.0+ (for &Сериализуемое, &Несериализуемое annotations)
- logos v1.7.1+ (for logging)
- collectionos v0.8.2+ (for collection operations)
- reflector v0.7.1+ (for class introspection, installed automatically with annotations)
- OneScript v2.0.0-rc.8 runtime
- oneunit v0.2.4+ (for testing, available in oscript_modules/bin/oneunit or globally)
- asserts v1.5.0+ (for testing assertions, development dependency)

This library provides JSON serialization and deserialization for OneScript custom classes using annotations to control the serialization process. All functionality is working correctly in the current environment.
