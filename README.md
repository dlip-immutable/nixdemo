# nixdemo


## Setup

- Install Docker

```
docker-compose up -d
docker-compose exec shell sh
```

## Nix Install Basics

```
# Install hello
nix-env -iA nixpkgs.hello
hello
# Uninstall hello
nix-env -e hello
# Install zsh
nix-env -iA nixpkgs.zsh
# Use zsh
exit
docker-compose exec shell zsh
```

## Nix Language

```
# Start REPL

```
