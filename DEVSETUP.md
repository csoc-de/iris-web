# Development setup

## Prerequisites

### Python 3.9

On Ubuntu systems, use the [deadsnakes PPA](https://launchpad.net/~deadsnakes/+archive/ubuntu/ppa) to install Python 3.9:

```bash
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install python3.9 python3.9-dev python3.9-venv
```

### Docker

On Ubuntu systems, Docker can be installed using the following commands:

```bash
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update

sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo groupadd docker
sudo usermod -aG docker $USER
```

Afterwards, restart your system, so the addition of your user to the `docker` group will take effect.

## Environment variables

Copy the `.env.model` file:

```bash
cp .env.model .env
```

Uncomment the lines setting the variables `IRIS_ADM_PASSWORD`, `IRIS_ADM_API_KEY`, `IRIS_ADM_EMAIL`, `IRIS_ADM_USERNAME` inside the `.env` file.

## Python virtual environment & dependencies

**Note:** on first setup, do not run the following commands inside your editor's integrated terminal, as it might interfere when creating the virtual environment and running the `activate` script, which may cause the requirements installation to fail.

```bash
python3.9 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
cd source
pip install -r requirements.txt
cd ..
```

## Editor setup

Open the repo's root directory in your editor.

### PyCharm

In the upper left corner, click the hamburger menu icon to open the _Main Menu_, go to _Run_ and click _Edit Configurations..._ to open the _Run/Debug Configurations_ dialog. Create a new configuration by clicking the _+_ button in the upper left corner of the dialog. From the dropdown, select _Python_ and fill the form fields:

| Field                 | Value                                          |
| --------------------- | ---------------------------------------------- |
| Name                  | Launch source/run.py                           |
| Interpreter           | Python3.9 `/path/to/iris-web/.venv/bin/python` |
| script                | `/path/to/iris-web/source/run.py`              |
| Working Directory     | `/path/to/iris-web/source`                     |
| Environment variables | `POSTGRES_SERVER=127.0.0.1`                    |
| Paths to ".env" files | `/path/to/iris-web/.env`                       |

Save the configuration by clicking the _OK_ button.

### Visual Studio Code

Go to the _Extensions_ view and search for `ms-python.python` and click _Install_. This extension pack will install the extensions _Pylance_, _Python Debugger_ and _Python Environments_.

Create `.vscode/launch.json` inside the repo's root directory with the following contents:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Launch source/run.py",
      "type": "debugpy",
      "request": "launch",
      "cwd": "${workspaceFolder}/source",
      "program": "${workspaceFolder}/source/run.py",
      "console": "integratedTerminal",
      "env": {
        "POSTGRES_SERVER": "127.0.0.1"
      },
      "envFile": "${workspaceFolder}/.env"
    }
  ]
}
```

## Start the docker container

From the repo's root directory, run:

```bash
docker compose -f docker-compose.dev.yml up db
```

## Launch the application

### PyCharm

In the upper right corner, click the _Debug 'Launch source/run.py'_ button or simply press `Shift` + `F9`.

### Visual Studio Code

Go to the _Run and Debug_ view and press the _Start Debugging_ button or simply press `F5`.

## Open the UI in a browser

After [launching the application](#launch-the-application), it takes a couple of seconds for the UI to be available under http://127.0.0.1:8000/. Use the credentials `IRIS_ADM_USERNAME` and `IRIS_ADM_PASSWORD` from the `.env` file to log in.
