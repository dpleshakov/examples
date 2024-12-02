# Buildifier 7.3.1 AddTables Path Search Issue Examples

This repository demonstrates an issue with the `AddTables` configuration in **Buildifier 7.3.1** when specifying a **relative path** to the `.buildifier-tables.json` file. Specifically, it shows how Buildifier fails to locate the file when run from subdirectories of the workspace.

## Problem Overview

In Buildifier, the `AddTables` field in `.buildifier.json` is used to specify a custom JSON file containing table formatting definitions. The path to this file can either be absolute or relative. However, using a relative path causes issues when running Buildifier from subdirectories of the workspace.

- When you specify a **relative path** in `.buildifier.json`, Buildifier will only be able to find the `.buildifier-tables.json` if it’s located in the current working directory.
- If you run Buildifier from a subdirectory, it fails to locate the file unless the relative path is correctly resolved.

This repository contains two example projects (`example1` and `example2`) that demonstrate this issue.

## Example 1: Default Path Search Behavior

In this example, the `.buildifier.json` file is located in the root of the workspace, and it references a **relative path** to the `.buildifier-tables.json` file.

### Directory Structure

```
example1/
├── .buildifier.json
├── .buildifier-tables.json
├── foo/
│   └── BUILD.bazel
├── BUILD.bazel
└── WORKSPACE
```

### Issue

- The `.buildifier.json` file contains a relative path to `.buildifier-tables.json`:

  ```json
  {
    "AddTables": ".buildifier-tables.json"
  }
  ```

- **When running Buildifier from the root directory**, it finds and uses `.buildifier-tables.json` as expected.
- **When running Buildifier from any subdirectory** (e.g., `cd example1/foo && buildifier`), it **fails** to find `.buildifier-tables.json` due to the relative path search issue.

### How to Reproduce

1. Navigate to the `example1` directory.
2. Run the following command from the root:

   ```bash
   buildifier -r .
   ```

3. Now try running it from a subdirectory (e.g., `example1/foo`):

   ```bash
   cd foo
   buildifier
   ```

You will notice that **Buildifier fails to find** `.buildifier-tables.json` when run from `foo`:
> `buildifier: failed to parse .buildifier-tables.json for -add_tables: open .buildifier-tables.json: The system cannot find the file specified.`

## Example 2: Path Search with Custom Folder

This example mimics the structure of `example1`, but the `.buildifier-tables.json` file is now placed in a subfolder (`bar`).

### Directory Structure

```
example2/
├── .buildifier.json
├── bar/
│   └── .buildifier-tables.json
├── foo/
│   └── BUILD.bazel
├── BUILD.bazel
└── WORKSPACE
```

### Issue

- The `.buildifier.json` file still references the **relative path** to `.buildifier-tables.json`:

  ```json
  {
    "AddTables": "bar/.buildifier-tables.json"
  }
  ```

- **When running Buildifier from the root directory**, it finds and uses `.buildifier-tables.json` in the `bar` folder as expected.
- **When running Buildifier from any subdirectory** (e.g., `cd example2/foo && buildifier`), it **fails** to find `.buildifier-tables.json` due to the relative path search issue.

### How to Reproduce

1. Navigate to the `example2` directory.
2. Run the following command from the root:

   ```bash
   buildifier -r .
   ```

3. Now try running it from the `bar` subdirectory:

   ```bash
   cd bar
   buildifier
   ```

As with **example1**, **Buildifier fails to find** `.buildifier-tables.json` when run from the `bar` subdirectory:
> `buildifier: failed to parse .buildifier-tables.json for -add_tables: open .buildifier-tables.json: The system cannot find the file specified.`
