# hermes_webui_silverbullet_llm_wiki_docker

This is a basic "single user" setup procedure for using [Docker Desktop](https://www.docker.com/products/docker-desktop/) 
to locally host [hermes](https://get-hermes.ai/), [hermes-agent](https://hermes-agent.nousresearch.com/), 
[hermes-webui](https://github.com/nesquena/hermes-webui), and [SilverBullet](https://silverbullet.md/) to implement a 
Karpathy-style [LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) with a local 
Ollama-hosted LLM.

The docker compose YAML file is a modification of [docker-compose.three-container.yml](https://github.com/nesquena/hermes-webui/blob/master/docker-compose.three-container.yml) 
from [hermes-webui](https://github.com/nesquena/hermes-webui).

## Security

### Containers

This containerized approach is significantly "locked down" compared to just running hermes-agent directly on your machine, 
especially if you do not enable any messaging or other "cloud" services for your agent. Only one local folder on your 
machine is accessible by the agent, `workspace` which includes the subfolder for wiki content (`workspace/space`) and 
the inbox for the agent (`workspace/raw`), which is read-only for the agent. All other storage for the containers are 
restricted to docker volumes. By default, hermes will prompt for your approval when removing files, but not when creating 
them.

### Encryption, password-protection, and firewalls

None of these web apps are configured for SSL encryption or network (LAN) access. If you want to access them 
from another system on your network you would want to modify this setup to use a web proxy for SSL (https) 
before opening up access to your network. You would also want to password-protect them. Currently, with this
setup, only SilverBullet requires password authentication. So, as provided, this is just for local use by a
single user on a single-user system like your personal workstation or laptop, not a shared server. Make sure 
your system is firewalled to block inbound connections to TCP ports: 3000 (silverbullet), 9119 (hermes-agent), 
8787 (hermes-webui), and 11434 (ollama).

## Setup hermes agent, hermes webui, and silverbullet wiki

Once you have Dock Desktop installed and running, run these commands in your Bash (or GitBash) Terminal:

```bash
mkdir -p ~/workspace/{raw,space}
git clone https://github.com/brianhigh/hermes_webui_silverbullet_llm_wiki_docker.git
cd hermes_webui_silverbullet_llm_wiki_docker
```

Edit `docker-compose.four-container.yml` to replace the SilverBullet admin password.

Then run:

```bash
docker compose -f ./docker-compose.four-container.yml up -d
```

### Test the wiki

Go to http://127.0.0.1:3000/, login, and make sure it's working okay. For example, you could create an 
`eat_that_frog_summary.md` page. The example `initial_prompt.md` provided here references that wiki page.

### Configure Local Models (Ollama)

Go to http://127.0.0.1:9119/config and press the `<> YAML` button.

To use locally-hosted Ollama as your model provider, paste this under "Model Configuration" 
in place of what's already there:

```
model:
  default: gemma4:26b
  api_key: ollama
  base_url: http://host.docker.internal:11434/v1
  provider: auto
```

Edit for your own situation (like a different model). For reference, see: 
https://hermes-agent.nousresearch.com/docs/user-guide/configuring-models

Press the `SAVE` button.

### Testing hermes-webui

Go here: http://localhost:8787/

Enter this into the `CHAT`, or select it from the prompt choices listed:

```
What files are in this workspace?
```

If the results looks right, then you can start using it as intended. For example, 
paste in the contents of `initial_prompt.md` and edit to match your situation
before pressing the `(↑)` button on the right side of the chat input box.

### Ollama model idle timeout (optional)

If you are running Ollama on Windows, you can increase the model idle timeout:

```powershell
[System.Environment]::SetEnvironmentVariable("OLLAMA_KEEP_ALIVE", "10m", "User")
```

This is a persistent setting.

You will need to completely shut down Ollama and restart it for this to take effect.	

Or for Mac/Linux:

```bash
export OLLAMA_KEEP_ALIVE=30m
```

This is not persistent unless you add it to `~/.bashrc`, `~/.bash_profile`, or ~/.zshrc`.

## Issues

The agent's cron jobs may not work properly due to the default server profile used for model access. 
This matters if you want the agent to setup a scheduled task to check the `/workspace/raw` folder periodically.
