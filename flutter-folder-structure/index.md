# Flutter App Architecture

This document provides an overview of the recommended architecture for Flutter applications built using this template. The architecture is designed to promote modularity, maintainability, testability, and scalability, especially for apps with multiple features.

## Project Structure

```
lib/
├── core/
│   ├── theme/
│   ├── localization/
│   ├── configs/
│   ├── constants/
│   ├── api/
│   └── routes/
├── commons/
│   ├── widgets/
│   ├── models/
│   ├── blocs/
│   └── repositories/
├── features/
│   ├── feature_name_1/
│   │   ├── screens/
│   │   ├── widgets/
│   │   ├── blocs/
│   │   ├── models/
│   │   │   └── enums/
│   │   └── repositories/
│   ├── feature_name_2/
│   │   └── ...
├── utils/
│   ├── extensions/
├── main.dart
packages/
```

The project is structured into the following top-level directories:

```
lib/                                # Main application directory
packages/                           # Local Flutter packages (optional, for reusable modules)
```

### `lib/` - Main Application Directory

This directory houses the core source code of your Flutter application. It is further organized into feature-based and core modules.

```
lib/
├── core/                           # Shared, app-wide code
├── commons/                        # Shared commons between features, for communication between features.
├── features/                       # Modular structure for each feature
├── utils/                          # Global utility functions (Use Sparingly!)
└── main.dart                       # App entry point
```

#### `lib/core/` - Core Modules

This directory contains code that is shared across the entire application and is not specific to any single feature. It encapsulates essential functionalities and configurations.

```
lib/core/
├── theme/                      # App theme, styles, colors
├── localization/               # Global localization setup
├── configs/                    # App configuration constants, definitions
├── constants/                  # App-wide constants, definitions
├── api/                        # API related core setup
└── routes/                     # App navigation routes
```

- **`theme/`**: Manages the application's visual theme.

  - `app_theme.dart`: Defines `ThemeData` for different themes (light, dark, etc.).
  - `theme_cubit.dart`: Bloc/Cubit for managing the current app theme state and persistence.
  - `colors.dart`: Defines the app's color palette.
  - `fonts.dart`: Defines custom fonts used in the application.

- **`localization/`**: Handles internationalization and localization of the app.

- **`configs/`**: Holds application-wide configuration settings and definitions.

  - `app_config.dart`: (Example) Could contain configurations loaded from environment variables or configuration files.

- **`constants/`**: Stores application-wide constant values.

  - `theme_constants.dart`: Theme-related constants like spacing values, animation durations, etc.
  - `assets_constants.dart`: Constants for image and other asset paths.

- **`api/`**: Sets up core API client configurations.

  - `api_client.dart`: (Optional) Can contain a base API client setup (e.g., using `Dio` with interceptors for logging, error handling, etc.).
  - `api_endpoints.dart`: Defines base URLs for different APIs and potentially some global endpoint paths.

- **`routes/`**: Defines application-wide navigation routes.
  - `routes.dart`: Defines route names and route generation logic for the app.

#### `lib/commons/` - Commons Modules

This directory contains code that is shared across the entire application and is not specific to any single feature. It encapsulates essential functionalities and configurations, and common UI elements.

```
lib/commons/
├── widgets/                    # Globally reusable widgets
├── models/                     # App-wide models
├── blocs/                      # Global Bloc/Cubits/Controllers
└── repositories/               # Global Data Repositories
```

- **`widgets/`**: Contains globally reusable UI widgets that can be used across different features. These widgets are designed to be generic and not feature-specific.
- **`models/`**: Contains data models that are used across different features of the application.
- **`blocs/`**: Contains globally accessible Bloc, Cubits, or Controllers that manage application-wide state or logic.
- **`repositories/`**: Houses data repositories that are used across multiple features.

#### `lib/features/` - Feature Modules

This directory is the heart of the modular architecture. Each subdirectory within `features/` represents a distinct feature of the application, promoting code isolation and maintainability.

```
lib/features/
├── feature_name_1/              # Example feature directory
│   ├── screens/                 # Screens specific to the feature
│   ├── widgets/                 # Feature-specific reusable widgets
│   ├── blocs/                   # State management (Cubit/Bloc)
│   ├── models/                  # Data models for the feature
│   │   ├── models.dart          # General import for all models
│   │   └── enums/               # Enums related to the feature
│   └── repositories/            # Handles API calls and data management
│       └── data_repository.dart
├── feature_name_2/              # Another feature module
```

Each feature directory (`feature_name_1`, `feature_name_2`, etc.) follows a consistent internal structure:

- **`screens/`**: Contains Flutter `Screen` widgets for this feature. Each screen typically represents a distinct user interface view within the feature.

- **`widgets/`**: Holds reusable UI widgets that are specific to this feature. These widgets are intended for use within the feature's screens and components.

- **`blocs/`**: Contains Bloc or Cubit classes for state management within the feature. Each Bloc/Cubit is responsible for managing the state and business logic of a specific part of the feature.

- **`models/`**: Defines the data models used by the feature.

  - `models.dart`: Serves as an import aggregator, exporting all models defined within this directory for easier access.
  - `enums/`: (Optional) Contains enums specific to the feature's data models or logic.

- **`repositories/`**: Handles data fetching, caching, and management for the feature.
  - `data_repository.dart`: Implements the data repository interface, abstracting data sources and providing a clean API for the feature's Blocs/Cubits to access data.
  - `data_sources/`: (Optional, can be inside `repository/`) Contains classes responsible for fetching data from different sources (API, local database, etc.).

#### `lib/utils/` - Global Utility Functions

This directory is intended for truly global utility functions and helpers that are used across the application and do not belong to any specific feature or core module. **Use this directory sparingly** to avoid creating a dumping ground for code that could be better placed within features or core modules.

```
lib/utils/
├── datetime_utils.dart         # Helper functions for date and time formatting
├── input_validators.dart       # Validation functions for form inputs
├── extensions/                 # Dart extensions for enhanced functionality
└── internet_connectivity.dart  # Network connectivity checks
```

- `datetime_utils.dart`: Helper functions for formatting and manipulating dates and times.
- `input_validators.dart`: Functions for validating user input in forms (e.g., email validation, password strength checks).
- `extensions/`: Dart extension methods to add functionality to existing classes (e.g., String extensions, BuildContext extensions).

#### `lib/main.dart` - App Entry Point

This file is the starting point of your Flutter application. It typically contains:

- `main()` function: The entry point of the application.
- Initialization: Initializes essential services like Hive, dependency injection, etc.
- `runApp()`: Starts the Flutter application by running the root widget.

## State Management

This architecture primarily utilizes **Bloc** (or Cubit, a simpler variant of Bloc) for state management.

- **Feature-Specific Blocs/Cubits:** Each feature typically has its own Blocs/Cubits located in the `features/[feature_name]/blocs/` directory. These manage the state and business logic specific to that feature.
- **Cross-Feature Cubits/Blocs:** The `commons/blocs/` directory houses Cubits/Blocs that manage state or logic that is shared and accessed across multiple features (e.g., `UserCubit`, `AppSettingsCubit`).

## Data Persistence

**Hive** is used for local data persistence in this template.

- **`core/constants/hive_constants.dart`**: Defines constants related to Hive box names and keys for organized storage.
- Data sources within features or core modules (e.g., `AuthLocalDataSource` in the `auth` feature) handle Hive interactions for local data caching and persistence.

## API Communication

- **`core/api/`**: Provides a foundation for API communication.
  - `api_client.dart`: (Optional) Sets up a base API client (e.g., using `Dio`) with configurations like interceptors.
  - `api_endpoints.dart`: Defines base URLs and potentially some global API endpoint paths as constants.
- Feature-specific repositories in `features/[feature_name]/repositories/` are responsible for making API calls using the configured API client and handling data mapping.

## Utilities

The `lib/utils/` directory provides a place for global utility functions and extensions that are used sparingly and are truly application-wide.

## Key Architectural Principles

- **Feature Modularization:** The application is broken down into independent, self-contained features, improving code organization, maintainability, and team collaboration.
- **Separation of Concerns:** Each layer and module has a clear responsibility (presentation, business logic, data management), promoting cleaner code and testability.
- **State Management with Bloc:** Bloc provides a predictable and reactive way to manage application state, making it easier to handle complex UI interactions and data flows.
- **Local Persistence with Hive:** Hive offers a fast and efficient solution for local data storage, enabling offline capabilities and persistent user settings.
- **Testability:** The architecture is designed to be testable at different levels (unit tests, widget tests, integration tests). Features are isolated, and dependencies are managed to facilitate testing.

## Naming Conventions

Consistent naming conventions are crucial for code readability and maintainability. This template recommends the following conventions:

### Folders

- **lowercase_with_underscores:** Use lowercase with underscores for all folder names.
  - Examples: `data_sources`, `extensions`.

### Files

- **lowercase_with_underscores.dart:** Use lowercase with underscores for all Dart file names, followed by the `.dart` extension.
  - Examples: `app_theme.dart`, `theme_cubit.dart`, `colors.dart`.
- **Feature-specific files:** When naming files within feature folders, aim for descriptive names that clearly indicate the file's purpose within the feature.
  - Examples (within `features/auth/blocs/`): `sign_in_cubit.dart`, `sign_up_cubit.dart`, `auth_bloc.dart`.
  - Examples (within `features/auth/screens/`): `sign_in_screen.dart`, `sign_up_screen.dart`, `profile_screen.dart`.

### Classes, Enums, and Type Aliases

- **PascalCase:** Use PascalCase (also known as UpperCamelCase) for class names, enum names, and type aliases. Start each word with a capital letter and concatenate them without underscores.
  - Examples: `AppTheme`, `AppLocalizationCubit`, `AppConstants`, `DataRepository`, `DateTimeUtils`.

### Variables, Functions, and Methods

- **camelCase:** Use camelCase (also known as lowerCamelCase) for variable names, function names, and method names. Start with a lowercase letter, and capitalize the first letter of subsequent words.
  - Examples: `lightTheme`, `validateEmail`, `formatDateTime`, `isConnected`.
- **Constants:** Use `camelCase` for constants as well, but consider prefixing them with `k` or `_` if they are private constants within a file. For truly global constants (defined in `core/constants/`), use `PascalCase` for the class name that groups them (e.g., `ThemeConstants.defaultSpacing`).
  - Examples: `primaryColor`, `defaultSpacing`, `kDefaultAnimationDuration`, `_apiBaseUrl`.

### Imports

- **Organize Imports:** Keep imports organized and grouped logically (e.g., Flutter imports, package imports, project-internal imports). Use Flutter's automatic import sorting features in your IDE.

### General Guidelines

- **Be Descriptive:** Choose names that are clear, concise, and accurately reflect the purpose of the file, folder, class, or variable.
- **Consistency:** Adhere to these conventions consistently throughout the project.
- **Refactor for Clarity:** Don't hesitate to refactor and rename files or folders if you find better, more descriptive names as your understanding of the project evolves.

By following these naming conventions, you will create a more organized, readable, and maintainable Flutter codebase for projects built upon it.

## Getting Started

1.  **Explore the `lib/features/` directory:** Start by understanding the structure of a feature module and how different components (screens, widgets, blocs, models, repositories) are organized.
2.  **Define your features:** Identify the key features of your application and create corresponding directories in `lib/features/`.
3.  **Implement feature components:** Develop screens, widgets, blocs, models, and repositories within each feature directory, following the established conventions.
4.  **Utilize `core/` modules:** Leverage the core modules for shared functionalities like theme management, localization, and cross-feature communication.

This architecture provides a solid foundation for building robust and scalable Flutter applications. Remember to adapt and extend this template to suit the specific needs and complexity of your project.
