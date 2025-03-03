
## Create Folder Structure Script

1. Copy the below Dart code and save it as `folder_structure.dart` in the root folder of your Flutter project.

```dart
import 'dart:io';

void main() {
  final currentDir = Directory.current;
  print('Setting up project structure in existing directory: ${currentDir.path}');

  List<String> folders = [
    'lib/core/theme',
    'lib/core/localization',
    'lib/core/configs',
    'lib/core/constants',
    'lib/core/api',
    'lib/core/routes',
    'lib/commons/widgets',
    'lib/commons/models/enums',
    'lib/commons/blocs',
    'lib/commons/repositories',
    'lib/features',
    'lib/utils/extensions',
  ];

  for (var folder in folders) {
    Directory('${currentDir.path}/$folder').createSync(recursive: true);
  }

  print('Project structure setup completed successfully!');
}

```
2. Open a terminal and navigate to the project root.
3. Run the script using:
```sh
dart folder_structure.dart
```

## Create Feature Script

1. Copy the below Dart code and save it as `create_feature.dart` in the root folder of your Flutter project.

```dart
import 'dart:io';

void main(List<String> arguments) {
  String? featureName;

  // Check if feature name is provided as an argument
  if (arguments.isNotEmpty) {
    featureName = sanitizeFeatureName(arguments.first);
  } else {
    stdout.write("Enter the feature name: ");
    featureName = stdin.readLineSync()?.trim();
  }

  // Validate feature name
  if (featureName == null || featureName.isEmpty || !isValidClassName(featureName)) {
    print("❌ Invalid feature name! It must start with a letter and contain only letters, numbers, and underscores.");
    exit(1);
  }

  final featurePath = "lib/features/$featureName";

  // Check if the feature directory already exists
  if (Directory(featurePath).existsSync()) {
    print("⚠️ Feature '$featureName' already exists. Please choose a different name or modify the existing feature.");
    exit(1);
  }

  // Create the feature structure
  createFeatureStructure(featureName);
  print("✅ Feature '$featureName' created successfully!");
}

/// Creates the required feature folder structure
void createFeatureStructure(String featureName) {
  final basePath = "lib/features/$featureName";

  final directories = [
    "$basePath/screens",
    "$basePath/widgets",
    "$basePath/blocs",
    "$basePath/models/enums",
    "$basePath/repositories",
  ];

  for (var dir in directories) {
    Directory(dir).createSync(recursive: true);
  }

  // Create the screen file
  final screenFile = File("$basePath/screens/${featureName}_screen.dart");
  screenFile.writeAsStringSync("""
import 'package:flutter/material.dart';

class ${capitalize(featureName)}Screen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('${capitalize(featureName)}')),
      body: Center(child: Text('Welcome to ${capitalize(featureName)} Screen')),
    );
  }
}
""");

  // Create the repository file
  final repositoryFile = File("$basePath/repositories/${featureName}_repository.dart");
  repositoryFile.writeAsStringSync("""
class ${capitalize(featureName)}Repository {
  // Add your repository methods here
}
""");
}

/// Ensures the feature name follows Dart class naming rules
bool isValidClassName(String name) {
  final classNameRegex = RegExp(r'^[a-zA-Z][a-zA-Z0-9_]*$');
  return classNameRegex.hasMatch(name);
}

/// Capitalizes the first letter of a given string
String capitalize(String s) => s[0].toUpperCase() + s.substring(1);

/// Sanitizes the feature name by removing invalid characters
String sanitizeFeatureName(String name) {
  return name.replaceAll(RegExp(r'[^a-zA-Z0-9_]'), '_');
}
```
2. Open a terminal and navigate to the project root.
3. Run the script using:

```sh
dart scripts/create_feature.dart <feature_name>
```

4. Replace <feature_name> with the actual name of your feature.
