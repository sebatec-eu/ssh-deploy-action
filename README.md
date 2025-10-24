# SSH Deploy Action

A reusable GitHub Action for SSH-based deployments.

## Usage

### Basic Example

```yaml
steps:
  - uses: sebatec-eu/ssh-deploy-action@v1
    with:
      ssh-key: ${{ secrets.SSH_PRIVATE_KEY }}
      run: "deploy.sh"
```

### Advanced example

`SSH_HOST` and `SSH_ARGS` are used in Makefile

```yaml
steps:
  - name: Deploy to server
    uses: sebatec-eu/ssh-deploy-action@v1
    with:
      ssh-key: ${{ secrets.DEPLOY_KEY }}
      run: "make deploy"
    env:
      SSH_HOST: ${{ vars.SSH_HOST }}
      SSH_ARGS: "-o StrictHostKeyChecking=no"
```

The Makefile looks the following:

```Makefile
SSH_ARGS ?=
SSH_HOST ?=
SCP_DEST ?= $(SSH_HOST):path/on/the/host/

.PHONY: deploy
deploy:
ifeq ($(SSH_HOST),)
	$(error "Undefined SSH_HOST variable")
else
	scp $(SSH_ARGS) $(FCGI_BIN) $(SCP_DEST)
endif
```

### Inputs

| Name    | Description                            | Required |
| ------- | -------------------------------------- | -------- |
| ssh-key | The private SSH key for authentication | yes      |
| run     | The command to execute for deployment  | yes      |

### Outputs

| Name   | Description                          |
| ------ | ------------------------------------ |
| result | The result of the deployment command |

## How to provide ssh key

1. Create a temporary directory and generate a new SSH key:
   ```bash
   cd $(mktemp -d)
   ssh-keygen -t ed25519 -C 'github action sebatec-eu/example-repo' -f id_ed25519
   ```
2. Copy the public key to the server's `.ssh/authorized_keys` file.
3. Set the private key as a GitHub secret:
   ```bash
   cat id_ed25519 | gh secret set DEPLOY_KEY -r sebatec-eu/example-repo
   ```
4. Securely delete the local key files:
   ```bash
   shred id_ed25519* && rm id_ed25519*
   ```

## License

This project is licensed under the MIT License.
