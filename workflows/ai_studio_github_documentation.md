---
description: Best practices for documenting Altair AI Studio Python Extensions on GitHub.
---

# Documenting AI Studio Extensions on GitHub

When you publish a custom Altair AI Studio Python Extension to GitHub, it is essential to provide clear documentation so that end-users (data scientists, business analysts) and other developers know how to install and use the operators.

## 1. Standard README.md Structure

Your repository should ALWAYS include a comprehensive `README.md` at the root with the following sections:

### **Title & Description**
State the name of the extension and briefly describe the business value it provides to AI Studio users. 

### **Installation Instructions**
Standard AI Studio users usually do not build extensions from source. You must explicitly instruct them on how to install the pre-compiled `.zip` file:
1. Download the latest `extension_namespace-x.x.x.zip` from the repository's `releases/` folder or the GitHub **Releases** page.
2. Open Altair AI Studio.
3. Open the **"Extensions"** menu and select **"Python Extensions..."** (or navigate to the standard extension manager).
4. Follow the prompts to install a new Python extension from a local ZIP file.
5. Restart AI Studio.

### **Available Operators**
Document each operator provided by your extension. For each operator, include:
- **Display Name:** The name of the operator as it appears in the design canvas.
- **Description:** A short summary of what the operator does.
- **Inputs:** What type of data the operator expects (e.g., `exa` DataFrames).
- **Outputs:** What the operator returns. Be very clear when an operator returns multiple ports, distinguishing between native datasets (`exa`) and Blob FileObjects (`fil` like HTML reports or images).
- **Parameters:** A list of configurable parameters (e.g., column selectors, boolean toggles) and what they control.

### **Development & Build Instructions**
For other developers contributing to the extension, add instructions on how to set up the environment and build the source code:
- The required Conda environment configuration.
- The build command, specifically noting any custom flags required (e.g., using `build-extension --draft-build` to bypass corporate proxy issues).

## 2. Using the `releases/` Folder

Do not expect non-technical users to clone the repository and run the SDK builder. Always track the compiled `.zip` packages in a designated folder like `releases/` (and ensure this is allowed in your `.gitignore`), or attach them directly to GitHub Releases. This guarantees users are downloading a stable, verified build.

## 3. Inline Code Documentation (Docstrings)

AI Studio parses your Python docstrings to dynamically generate the Help Panel UI inside the application. 
Documentation on GitHub should be matched by high-quality inline docstrings:
- Use the `:param [name]: [Description]` format to document parameters.
- Use `:return:` to explicitly define what each output port does. This is critical for users to understand which port to connect to a "Write File" operator versus a processing operator.
