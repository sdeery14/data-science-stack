# Poetry for Dependency Management

## Introduction
Poetry is a tool for dependency management and packaging in Python. It allows you to declare the libraries your project depends on and it will manage (install/update) them for you. Poetry offers several benefits over traditional tools like `pip` and `virtualenv`:

- **Simplified Dependency Management**: Poetry automatically resolves and installs dependencies, ensuring that your project has the correct versions of libraries.
- **Environment Isolation**: Poetry creates and manages virtual environments for your projects, keeping dependencies isolated and avoiding conflicts.
- **Consistent and Reproducible Builds**: By using a `pyproject.toml` file, Poetry ensures that your project dependencies are consistent across different environments.
- **Easy Project Setup**: Poetry can quickly set up new projects with a standard structure, including configuration files and dependency management.
- **Development Dependency Management**: Poetry allows you to distinguish between libraries used for development and those used in production by specifying `dev-dependencies` in the `pyproject.toml` file.
- **Publishing Made Easy**: Poetry simplifies the process of packaging and publishing your projects to PyPI.

These features make Poetry a powerful and user-friendly alternative to `pip` and other dependency management tools.

## Installation
To install Poetry, you can use the following command:
```sh
curl -sSL https://install.python-poetry.org | python3 -
```
Or, if you prefer using `pip`:
```sh
pip install poetry
```

## Setting Up a New Project
To create a new project with Poetry, run:
```sh
poetry new my-project
cd my-project
```

## Initializing an Existing Directory
To initialize an existing directory with Poetry, navigate to the directory and run:
```sh
cd my-existing-project
poetry init
```

## Adding Dependencies
To add a dependency, use the `add` command:
```sh
poetry add requests
```
To add a dependency that is only needed for development, use the add command with the --dev flag:
```sh
poetry add --dev pytest
```

## Installing Dependencies
To install all dependencies listed in the `pyproject.toml` file, run:
```sh
poetry install
```

## Using Poetry with VSCode

### Activate the Poetry environment in the terminal

1. **Activate the Environment**:
    In the terminal, navigate to your project directory and activate the Poetry environment:
    ```sh
    poetry shell
    ```
    You should now see the environment is active, indicated by the environment name in parentheses at the beginning of the terminal prompt, e.g., `(my-project) $`.

2. **Run Python files from the terminal**:
    Now that the environment is active, you can run your Python scripts using the `python` command. For example, to run a script named `main.py`, use:
    ```sh
    python main.py
    ```

### Activate the Poetry environment as the Interpreter in VSCode.

1. **Configure VSCode to Use Poetry Environment as Interpreter**:
    - Find the path to the Poetry environment:
      ```sh
      poetry env info --path
      ```
    - Copy the displayed path.
    - In VSCode, open the Command Palette (Ctrl+Shift+P) and select `Python: Select Interpreter`.
    - Click on `Enter interpreter path...` and paste the copied path.

### Activate the Poetry environment in a Jupyter notebook in VSCode

1. **Install Jupyter**:
    Add Jupyter as a dependency:
    ```sh
    poetry add jupyter
    ```

2. **Activate the Environment**:
    In the terminal, activate the Poetry environment:
    ```sh
    poetry shell
    ```

3. **Install the IPython Kernel**:
    Install the IPython kernel for Jupyter:
    ```sh
    ipython kernel install --user --name=my-project
    ```

4. **Select the Kernel in Jupyter**:
    - Start Jupyter Notebook:
      ```sh
      jupyter notebook
      ```
    - In the Jupyter interface, select the kernel named `my-project` for your notebooks.


## Conclusion
Poetry simplifies dependency management and packaging in Python projects. By integrating it with VSCode and Jupyter Notebooks, you can streamline your development workflow.

For more information, visit the [Poetry documentation](https://python-poetry.org/docs/).