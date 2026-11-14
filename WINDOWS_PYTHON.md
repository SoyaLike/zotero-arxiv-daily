# Windows x64 环境恢复

使用 CPython 3.11。首先从发布者的 [v0.3.2 Release](https://github.com/abetlen/llama-cpp-python/releases/tag/v0.3.2) 获取 llama_cpp_python-0.3.2-cp311-cp311-win_amd64.whl，并将 $wheel 设为其绝对路径。$runtime 为 CPython 3.11 的绝对路径。

    $env:UV_PYTHON_DOWNLOADS = "never"
    uv sync --locked --python $runtime --no-install-package llama-cpp-python
    $py = Join-Path (Get-Location) ".venv\Scripts\python.exe"
    uv pip install --no-deps --python $py $wheel
    if ($env:CUDA_PATH -and (!(Test-Path (Join-Path $env:CUDA_PATH "bin")) -or !(Test-Path (Join-Path $env:CUDA_PATH "lib")))) { Remove-Item Env:CUDA_PATH }
    if ($env:HIP_PATH -and (!(Test-Path (Join-Path $env:HIP_PATH "bin")) -or !(Test-Path (Join-Path $env:HIP_PATH "lib")))) { Remove-Item Env:HIP_PATH }
    & $py -c "import llama_cpp; assert llama_cpp.__version__ == '0.3.2'"
    uv pip check --python $py
    & $py main.py --help

llm.py 在模块加载时导入 llama_cpp，因此当前 OpenAI 模式也需要安装它。CPU wheel 在 Windows 加载时仍会读取 CUDA_PATH 和 HIP_PATH。上述检查仅在目录失效时移除当前 PowerShell 进程内的变量。恢复时继续使用上述锁定同步命令。

VS Code 可通过 Python: Select Interpreter 选定上述解释器。PyCharm 可在项目解释器设置中选择现有环境。
