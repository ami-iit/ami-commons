# Visual Studio Code

In the past multiple developers used [Visual Studio Code](https://code.visualstudio.com/) as a development environment.
Using extensions it is easy to enhance the features of the editor and make it almost a full fledged IDE (some would say better).
The extensions we typically use for development of C/C++ and Python applications are:
 - [C/C++ Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools-extension-pack) for C++ development and integerated CMake;
 - [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) for python development;
 - [Jupyter](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter) for python notebooks;
 - [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) to enhance the git integration.
 - [Remove SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) to connect to remote hosts via SSH.
 - [DevContainers](https://code.visualstudio.com/docs/devcontainers/containers) to easily develop on Docker containers.

In  addition it is suggested to set the following settings:
 - **Files: Auto Save** - > On Focus Change (Allows for autmatic save of files when the focus changes)
 - **Formatting: Format on Save** -> On (Triggers the automatic format of edited file to comply with the common style)
 - **Git: Allow force push** -> On (Permits the use of the force push option in the editor, useful when rebasing but with ATTENTION!)
 - **Suggestions: Inline Suggest** -> On (Automatically suggests intellisense completion while writing, without the need for additional input)
 - **Files: Insert Final New Line** -> On (Adds an EOF newline if you forget to)
 - **Files: Trim trailing whitespaces** -> On (Removes trailing whitespace when saving the file)
