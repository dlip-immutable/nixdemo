# nixdemo


## Setup

- Install Docker

```sh
docker-compose up -d
docker-compose exec shell sh
```

## Nix Install Basics

```sh
# Install hello
nix-env -iA nixpkgs.hello
hello
# Uninstall hello
nix-env -e hello
```

## Nix Language

```sh
# Start REPL
nix repl

```

## Install Home Manager

```sh
nix-channel --add https://github.com/nix-community/home-manager/archive/master.tar.gz home-manager
nix-channel --update
nix-shell '<home-manager>' -A install
home-manager switch
```

## Basic Programs

```sh
  vi ~/.config/nixpkgs/home.nix
  # Add cowsay package
```

```nix
  home.packages = with pkgs; [
    cowsay
  ];
```

```sh
  home-manager switch
  cowsay hello
```

I have also mounted the the `~/.config/nixpkgs` folder to `nixpkgs` in this repository for convienience

### Modules

Modules are a way to install and configure programs using nix language. The best way to find the available options is to look in the code for `options = {`

#### GIT

https://github.com/nix-community/home-manager/blob/master/modules/programs/git.nix

```nix
  options = {
    programs.git = {
      enable = mkEnableOption "Git";
      package = mkOption {
        type = types.package;
        default = pkgs.git;
        defaultText = literalExpression "pkgs.git";
        description = ''
          Git package to install. Use <varname>pkgs.gitAndTools.gitFull</varname>
          to gain access to <command>git send-email</command> for instance.
        '';
      };
      userName = mkOption {
        type = types.nullOr types.str;
        default = null;
        description = "Default user name to use.";
      };
      userEmail = mkOption {
        type = types.nullOr types.str;
        default = null;
        description = "Default user email to use.";
      };
```

Example config:

```nix
  programs.git = {
    enable = true;

    userEmail = "user@email.com";
    userName = "user";

    extraConfig = {
      init.defaultBranch = "main";
    };
  };
```


#### ZSH

https://github.com/nix-community/home-manager/blob/master/modules/programs/zsh.nix


```nix
  programs.zsh = {
    enable = true;
    enableAutosuggestions = true;
    enableCompletion = true;

    oh-my-zsh = {
      enable = true;
      plugins = [
        "git"
      ];
    };
  };
```
