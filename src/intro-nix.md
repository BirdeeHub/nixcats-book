# Nix
`Nix` is a functional programming language and `Nix-Cli(aka nix)` is a declarative package manager designed to create reproducible and declarative software environments. It works by treating configurations as code, ensuring that every aspect of your environment is consistent, version-controlled, and isolated. One of Nix’s standout features is its ability to manage software dependencies seamlessly while maintaining exact version control.

## Key Concepts of Nix

- **Declarative Configurations:** Environments and configurations are defined in .nix files, making them reproducible.

- **Immutable Packages:** Nix stores packages in a unique, hashed location `/nix/store/$hash-$packagename`, ensuring no conflicts between versions where `$hash` represents the hash id of the package and `$packagename` refers to the name of the package itself. For example, if I do the follwing in my terminal:
    ```shell
    $ nix-shell -p gcc
    ```
    Nix would go and evaluate the package derivation from nixpkgs and store it in `/nix/store/xzfmarrq8x8s4ivpya24rrndqsq2ndiz-gcc-13.3.0`
    > __NOTE:__ `/nix/store` is an immutable file-system which means the user can only access it read-only.

- **Version Locking:** Using tools like `flakes` or `nixpkgs`, you can pin specific versions of packages for consistent builds.

## What is nixpkgs?
`nixpkgs` is a central repository that contains thousands of software packages available in Nix. It acts as the foundation for most Nix-based configurations and is continuously updated to include new packages and fixes. You can view the repo in [GitHub.](https://github.com/NixOS/nixpkgs)

```shell
$ nix-shell -p neovim git
```
This command starts a `bash` shell environment with neovim and git installed, without affecting your system configuration.

## What is flakes?
Flakes are an experimental feature that enhances reproducibility by locking dependencies and configurations in a standardized format. It is intended to replace `default.nix` which you would normally use in a Nix system. This brings a new schema where you can define your packages from the web in `inputs` and build package or a shell for the user. You could say that flakes work similar to the Rust package manager `cargo` or Javascript package manager `npm` which locks your dependencies in a separate file (`Cargo.lock` and `packages-lock.json` respectively), `flake.lock` in this case.

```nix
# flake.nix
{
    description = "An example NixOS configuration";

    inputs = {
        nixpkgs = { url = "github:nixos/nixpkgs/nixos-unstable"; };
        nur = { url = "github:nix-community/NUR"; };
    };

    outputs = inputs: {
        nixosConfigurations = {
            mysystem = inputs.nixpkgs.lib.nixosSystem {
                system = "x86_64-linux";
                modules = [ ./configuration.nix ];
                specialArgs = { inherit inputs; };
            };
        };
    };
}
```

This allow user to use `flakes` to build a system configuration using `nixosConfigurations` defined in the `flake.nix`.

You can learn more about nix and flakes by referring to [resources](./references.md#nix)
