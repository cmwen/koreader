# How to Contribute to KOReader

Welcome to the KOReader contributor's guide! This document will walk you through setting up your development environment, building the application, and running tests.

## About the Project

KOReader is a document viewer for e-ink devices, with a frontend written primarily in **Lua**. You don't need to install Lua separately; it's included as part of the project's toolchain.

## Getting Started: Setting Up Your Development Environment

You have two main options for setting up your development environment:

1.  **Using Docker (Recommended for all platforms)**: This is the easiest and most recommended method. It provides a pre-configured environment with all dependencies, ensuring a consistent experience across different operating systems.
2.  **Building Locally**: This method is for developers who prefer to set up the environment directly on their machine. We provide detailed instructions for macOS. For other Linux distributions, please refer to the [Building.md](doc/Building.md) guide.

---

### Option 1: Using the Official Docker Image (Recommended)

1.  **Install Docker**: If you don't have it already, install Docker Desktop for your operating system from the [official Docker website](https://docs.docker.com/get-docker/).

2.  **Clone the Repository**:
    ```bash
    git clone https://github.com/koreader/koreader.git
    cd koreader
    ```

3.  **Run the Docker Container**:
    ```bash
    docker run -v $(pwd):/home/ko/koreader -it koreader/koappimage:latest bash
    ```
    This command starts a `bash` session inside the container and mounts your local `koreader` directory.

4.  **Fetch Third-Party Dependencies**:
    Inside the Docker container, run:
    ```bash
    ./kodev fetch-thirdparty
    ```

You are now ready to build the project! Skip to the "Building and Running the Emulator" section.

---

### Option 2: Setting Up a Local Environment on macOS

If you prefer to work on your local machine, follow these steps to set up the environment on macOS.

#### Step 1: Install Homebrew

Homebrew is a package manager for macOS that simplifies the installation of software. If you don't have it, open your terminal and run the following command:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

#### Step 2: Install Development Tools

Use Homebrew to install the necessary development tools:
```bash
brew install autoconf automake bash binutils cmake coreutils findutils \
    gnu-getopt libtool make meson nasm ninja pkg-config sdl2 util-linux
```

#### Step 3: Update Your PATH

Some of the tools installed by Homebrew are not automatically added to your system's `PATH` to avoid conflicts with built-in macOS utilities. You need to add them to your `PATH` manually. Add the following line to your shell's configuration file (e.g., `~/.zshrc` or `~/.bash_profile`):

```bash
export PATH="$(brew --prefix)/opt/findutils/libexec/gnubin:$(brew --prefix)/opt/gnu-getopt/bin:$(brew --prefix)/opt/make/libexec/gnubin:$(brew --prefix)/opt/util-linux/bin:${PATH}"
```
After adding this line, restart your terminal or run `source ~/.zshrc` (or `source ~/.bash_profile`) for the changes to take effect.

#### Step 4: Install Optional (but Recommended) Tools
These tools are not strictly required for building the project, but they are very useful for development:
```bash
brew install ccache gettext luacheck p7zip
```
- `ccache`: Speeds up recompilation.
- `gettext`: Needed for updating translations.
- `luacheck`: A linter for the Lua codebase.

#### Step 5: Clone the Repository and Fetch Dependencies

Now that you have the tools, you can get the source code:
```bash
git clone https://github.com/koreader/koreader.git
cd koreader
./kodev fetch-thirdparty
```

---

## Building and Running the Emulator

Once your environment is set up (either via Docker or locally), you can build and run the KOReader emulator.

1.  **Build the Emulator**:
    ```bash
    ./kodev build
    ```

2.  **Run the Emulator**:
    ```bash
    ./kodev run
    ```

You can also simulate different e-ink devices using the `-s` flag:
```bash
# Simulate a Kobo Aura One
./kodev run -s=kobo-aura-one

# Simulate a Kindle Paperwhite 3
./kodev run -s=kindle-pw3
```

## Running Tests

KOReader has a suite of unit tests to ensure code quality.

1.  **Activate the Test Environment**:
    ```bash
    ./kodev activate
    ```
    This command sets up the necessary environment variables for running the tests.

2.  **Run the Tests**:
    ```bash
    # Run tests for the base components
    ./kodev test base

    # Run tests for the frontend
    ./kodev test front
    ```

## Submitting Your Changes

When you've made your changes and are ready to contribute them back to the project, please follow the guidelines in our [Collaborating with Git](doc/Collaborating_with_Git.md) document.
