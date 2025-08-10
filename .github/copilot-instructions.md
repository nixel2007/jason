# Jason - OneScript JSON Serialization Library

Jason is a OneScript library for convenient JSON serialization of custom script classes. The library is written in Russian and follows OneScript conventions.

**IMPORTANT: Always reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.**

## Working Effectively

### Using GitHub Actions (Recommended)
- The repository includes `.github/workflows/copilot-setup-steps.yml` for automated setup
- This workflow uses `otymko/setup-onescript@v1.5` action for proper OneScript installation
- Run from Actions tab: "OneScript Development Environment Setup" workflow
- **ADVANTAGE**: Handles version compatibility and dependency management automatically
- **ADVANTAGE**: Uses updated opm version that supports newer packages

### Manual Local Setup (Alternative)
If you need to set up locally or the GitHub Actions approach fails:

#### Prerequisites and Setup
- Install OneScript runtime:
  - Download: `wget https://github.com/EvilBeaver/OneScript/releases/download/v1.9.3/onescript-engine_1.9.3_all.deb`
  - Install dependencies: `sudo apt-get update && sudo apt --fix-broken install` -- takes 2-5 minutes. NEVER CANCEL.
  - Install OneScript: `sudo dpkg -i onescript-engine_1.9.3_all.deb`
  - Verify: `oscript --version` should show "1Script Execution Engine. Version 1.9.3.14"

### Install Dependencies
#### Via GitHub Actions (Recommended)
- The setup workflow automatically installs dependencies via `opm install -l --dev`
- This includes updated opm and resolves version compatibility issues

#### Manual Installation (if needed)
- Install core dependencies (required for library to work):
  - `sudo opm install annotations` -- takes 30-60 seconds. NEVER CANCEL.
  - `sudo opm install logos` -- takes 10-30 seconds. NEVER CANCEL. 
  - `sudo opm install reflector` (installed automatically with annotations)
- Verify installation: `sudo opm list` should show annotations v1.3.1, logos v1.7.1, reflector v0.7.1

### Build and Syntax Checking
- Check syntax of individual files: `oscript -check src/Классы/СериализаторJson.os` -- takes ~0.3 seconds
- Check all source files: `find src -name "*.os" -exec oscript -check {} \;` -- takes 1-2 seconds total
- Build: No explicit build step required - OneScript is interpreted

### Testing and Validation
- **KNOWN ISSUE**: JSON serialization fails with "System.TypeInitializationException: The type initializer for 'Newtonsoft.Json.JsonWriter' threw an exception"
- **KNOWN ISSUE**: oneunit test runner requires opm v1.4.0+ (current is v1.0.7)
- Object creation works: Classes can be instantiated successfully
- Syntax validation works: `oscript -check tests/Сериализация.os` -- takes ~0.3 seconds
- **WORKAROUND**: Tests cannot currently be executed due to runtime JSON library issues

### Functional Testing Status
- ✅ Class instantiation: `Новый СериализаторJson()` works
- ✅ Method existence: Public methods are accessible  
- ❌ JSON serialization: Fails at OneScript's native JSON writer level
- ❌ Full test suite: Cannot run due to JSON and test runner issues

### Manual Validation Scenarios
After making changes to the library, validate by:
1. **Basic instantiation test**: Create simple test script that instantiates СериализаторJson
2. **Syntax validation**: Run syntax checks on all modified .os files  
3. **Package definition check**: Verify that `packagedef` file is syntactically correct
4. **Dependency verification**: Check dependencies are properly declared in packagedef
5. **Code structure review**: Ensure annotations and class structure follow OneScript conventions

**Sample validation script:**
```onescript
#Использовать "/home/runner/work/jason/jason"

Процедура ТестОснов()
    Попытка
        СериализаторJson = Новый СериализаторJson();
        Сообщить("✅ Сериализатор создан успешно");
    Исключение
        Сообщить("❌ Ошибка создания: " + ОписаниеОшибки());
    КонецПопытки;
КонецПроцедуры

ТестОснов();
```

## Repository Structure

### Key Files and Directories
```
/home/runner/work/jason/jason/
├── .github/
│   ├── workflows/copilot-setup-steps.yml  # GitHub Actions setup workflow
│   └── copilot-instructions.md            # This file
├── src/Классы/
│   ├── СериализаторJson.os          # Main JSON serializer class
│   ├── АннотацияJsonСвойство.os     # JsonProperty annotation
│   └── АннотацияСериализуемое.os    # Serializable annotation
├── tests/
│   ├── Сериализация.os              # Main test file
│   └── Классы/ТестовыйКласс.os      # Test class with annotations
├── packagedef                        # OneScript package definition
├── .bsl-language-server.json        # BSL language server config
└── README.md                        # Brief library description (Russian)
```

### Common Commands and Expected Times
- `oscript --version` -- instant, shows OneScript version
- `opm --version` -- instant, shows "1.0.7"
- `sudo opm list` -- 1 second, lists installed packages
- `sudo opm install <package>` -- 10 seconds to 5 minutes depending on dependencies. NEVER CANCEL.
- `oscript -check <file.os>` -- 0.3 seconds per file

## Known Issues and Workarounds

### Critical Runtime Issue
- **Problem**: Library fails with "System.TypeInitializationException: The type initializer for 'Newtonsoft.Json.JsonWriter' threw an exception"
- **Impact**: Cannot currently serialize objects to JSON
- **Status**: Unresolved - may require OneScript/Mono version compatibility fixes
- **Workaround**: Only syntax checking and code analysis possible at this time

### Test Runner Compatibility
- **Problem**: oneunit test runner requires opm v1.4.0+, manual install gives v1.0.7
- **Impact**: Cannot run automated tests with manual setup
- **Solution**: Use GitHub Actions workflow which installs updated opm
- **Workaround**: Use syntax checking only: `oscript -check tests/Сериализация.os`

### Package Installation  
- **Problem**: packagedef contains method "АдресРепозитория" not recognized by older opm versions
- **Solution**: GitHub Actions workflow installs updated opm that recognizes this method
- **Workaround**: Install dependencies manually: `sudo opm install annotations logos`

## Development Guidelines

### Code Standards
- All source code in Russian language
- Use OneScript annotations: `&JsonСвойство`, `&Сериализуемое`
- Follow OneScript naming conventions (CamelCase with Russian characters)
- Files must be UTF-8 encoded with BOM (﻿)

### Making Changes
1. Always run syntax check after modifying any .os file
2. Verify that packagedef is still valid
3. **Current limitation**: Cannot test functionality due to runtime issues
4. Focus on syntax correctness and code structure
5. Use .bsl-language-server.json for IDE support

### Validation Commands to Run Before Committing
```bash
# Check syntax of main serializer
oscript -check src/Классы/СериализаторJson.os

# Check syntax of test files  
oscript -check tests/Сериализация.os
oscript -check tests/Классы/ТестовыйКласс.os

# Verify annotations
oscript -check src/Классы/АннотацияJsonСвойство.os
oscript -check src/Классы/АннотацияСериализуемое.os
```

## Important Notes

### Timeout Requirements
- **NEVER CANCEL** apt operations during OneScript installation - can take 5+ minutes
- **NEVER CANCEL** opm package installations - can take 1-5 minutes depending on dependencies
- Set timeouts of 300+ seconds for package installation commands
- Syntax checking is fast (0.3s per file) and doesn't need special timeouts

### Language and Encoding
- All source code, comments, and variable names in Russian
- Files use Cyrillic characters and must be UTF-8 with BOM
- Package uses Russian method names in packagedef (known compatibility issue)

### Dependencies
- annotations v1.3.1+ (for &JsonСвойство, &Сериализуемое annotations)
- logos v1.7.1+ (for logging)
- reflector v0.7.1+ (for class introspection)
- OneScript v1.9.3+ runtime
- Mono v6.8.0+ (installed with OneScript)

This library provides JSON serialization for OneScript custom classes using annotations to control the serialization process. Currently experiencing runtime compatibility issues that prevent full functional testing.