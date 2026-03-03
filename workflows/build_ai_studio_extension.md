---
description: Build Altair AI Studio Python Extensions and handle corporate proxy constraints.
---

# Building AI Studio Python Extensions

When building AI Studio extensions using the `altair_aitools` SDK, you may encounter corporate firewall issues, SSL certificate errors, or missing dependencies for remote platforms (like Linux ARM).

Follow these steps to safely build the extension on a Windows machine:

1. **Activate the Environment**: 
   Always ensure you are operating inside the proper conda environment where `altair-aitools` is installed (e.g., `conda activate extension-env`).

2. **Run Draft Build**:
   If the standard `build-extension` fails due to unresolvable packages on different architectures (e.g., `linux-aarch64`), use the `--draft-build` flag. This restricts the build to the host machine's current OS/Architecture.
   
   Command:
   ```powershell
   call C:\ProgramData\miniforge3\Scripts\activate.bat [environment-name] && call build-extension --draft-build [extension_directory_name]
   ```
   
3. **Bypass Conda Hung Status**:
   Using `conda run -n env build-extension ...` can silently hang processing. Always invoke the batch script directly or use a `cmd /c "call ... && call ..."` string locally.
   
4. **Relocate Final Zip**:
   Once the ZIP is compiled (e.g. `extension_namespace-0.0.1.zip`), move it out of the root path to a dedicated `releases/` folder and track it in `.gitignore` using `!releases/*.zip`.
