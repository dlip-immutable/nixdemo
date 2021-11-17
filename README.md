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

## Nix Store

Everything in nix is built in an environment with no mutable inputs such as direct network access or system clock in order to ensure built packages are reproducable. 
The output is written to `/nix/store` and nix-env simply symlinks the correct version to your `~/.nix-profile/bin` folder which was added to your path when you install nix.

```sh
$ which hello
/root/.nix-profile/bin/hello
$ ls -la /root/.nix-profile/bin/hello
lrwxrwxrwx    1 root     root            64 Jan  1  1970 /root/.nix-profile/bin/hello -> /nix/store/ssccr9khjprx3722cqcjikngzffjl4xf-hello-2.10/bin/hello
```

## Nix Language

```sh
# Start REPL
nix repl
```

### Variables

```
nix-repl> foo = "strval"
nix-repl> "$foo"
"$foo"
```

### Lists

```
nix-repl> [ 2 "foo" true (2+3) ]
[ 2 "foo" true 5 ]
```

### Attribute Sets

```
nix-repl> s = { foo = "bar"; a-b = "baz"; "123" = "num"; }
nix-repl> s
{ "123" = "num"; a-b = "baz"; foo = "bar"; }
nix-repl> s.a-b
"baz"
nix-repl> s."123"
"num"
```

### Functions

```
nix-repl> double = x: x*2
nix-repl> double
«lambda»
nix-repl> double 3
6

nix-repl> mul = { a, b ? 2 }: a*b
nix-repl> mul { a = 3; }
6
nix-repl> mul { a = 3; b = 4; }
12
```

Read more [Nix Pills](https://nixos.org/guides/nix-pills/basics-of-language.html)

## Home Manager

Installing programs adhoc as in the above example is not a great improvement over using `apt-get` or `brew`.
In order to ensure you can synchronise configurations across machines your want to be able to commit it to git.
Home Manager makes it easy to install and configure your home directory and programs using a `home.nix` file.

```sh
nix-channel --add https://github.com/nix-community/home-manager/archive/master.tar.gz home-manager
nix-channel --update
nix-shell '<home-manager>' -A install
home-manager switch
```

## Installing Basic Programs

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

#### Creating Files

```
  home.file = {
    ".config/vim/vimrc".text = ''
      set wrap
      set textwidth=79
      set formatoptions=tcqrn1
      set tabstop=2
      set shiftwidth=2
      set softtabstop=2
      set expandtab
      set noshiftround
    '';
  };
```
## Future Topics

- Flakes
- Creating our own packages

