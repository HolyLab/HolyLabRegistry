# HolyLabRegistry
This is a HolyLab registry to use lab packages according to julia 0.7 Pkg3 design.

# Usage
Clone this repository under DEPOT_PATH/registries. Then, we can use lab private
pakages as if they are registered ones. (Usually, DEPOT_PATH = /home/username/.julia)


# WIP
Still need to add most of our lab packages to this registry.


# To use git protocol in GitHub
This instruction is for Linux users and comes from https://help.github.com/articles/connecting-to-github-with-ssh/.
For windows users, you can get some information at https://gist.github.com/bsara/5c4d90db3016814a3d2fe38d314f9c23

1. Generating a new SSH key at a local machine.
    - Open git bash and paste text below, substituting in your GitHub email address.
    ```
    $ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
    ```

    - When you're prompted to "Enter a file in which to save the key," press Enter. This accepts the default file location.
    ```
    Enter a file in which to save the key (/home/you/.ssh/id_rsa): [Press enter]
    ```

    - At the prompt, type a secure passphrase if you want.
    ```
    Enter passphrase (empty for no passphrase): [Type a passphrase]
    Enter same passphrase again: [Type passphrase again]
    ```

2. Adding your SSH key to the ssh-agent
    - Start the ssh-agent in the background.
    ```
    $ eval "$(ssh-agent -s)"
    Agent pid 59566
    ```

    - Add your SSH private key to the ssh-agent
    ```
    $ ssh-add ~/.ssh/id_rsa
    ```

3. Adding a new SSH key to your GitHub account
    - Copies the contents of the id_rsa.pub file in the local machine to your clipboard
    - Go to GitHub site
    - In the upper-right corner of any page, click your profile photo, then click Settings.
    - In the user settings sidebar, click SSH and GPG keys.
    - Click New SSH key or Add SSH key.
    - In the "Title" field, add a descriptive label for the new key.
    - Paste your copied pulic key into the "Key" field
    - Click Add SSH key.


# For package developer
- How to prepare your package and related repository before register : Generating 'Project.toml' file
    - Initiate your package.
        - Change your working directory to julia package developing directory ex) ~/.julia/dev/
        - Generate a package. Let your package name be 'MyPkg', then
            ```julia
            (v1.0) pkg> generate MyPkg
            ```
        - If you don't have existing package under the developing directory, this will generate a 'MyPkg' directory, 'Project.toml' including UUID and smaple source files.
        - If you already have your package at the location, this will cause an error. In this case, you can generate 'Porject.toml' file at different location temporarily and  copy only the file to your package root.
    - Adding dependent packages.
        - Change your working directory to 'MyPkg' now.
        - Activate and add dependencies. Let those are 'SubPkg1, SugPkg2'.
            ```julia
            (v1.0)  pkg> activate .
            (MyPkg) pkg> add SubPkg1 SubPkg2
            ```
        - This will add the dependent packages under '[dpes]' field in the 'Project.toml' and generate 'Manifest.toml' file. This 'Manifest.toml' file includes specific versions of the dependent packages resolved according to your current environment. (Usually, this file is excluded when you commit your package to a repository - would be convenient if you add this to .gitignore file)
    - Push your package to a repository.

- How to add your package to HolyLabRegistry
  - Make directory including Compat.toml, Deps.toml, Package.toml and Versions.toml under root directory of HolyLabRegistry (It would be easy to begin with just copy related files from other existing directory in the registry)
  - Edit those files appropriately.
    - Deps.toml : include all the dependencies (You can copy some lines from 'Project.toml' file of your package)
    - Package.toml : Package name, UUID, location of the repository
    - Versions.toml : git-tree-sha values according to version numbers.
      You can find this value with git command at the package root directory.
      ```
      $ git cat-file -p v0.1.0
      ```
      Then, You will see the value in the first line.
      If you just want to publish current 'master' branch, try this.
      ```
      $ git cat-file -p master
      ```
      In this case, every time master branch in the repository of the package is updated with new commit, you need to update this git-tree-sha value also in this registry.
  - Add an entry of the package in Registry.toml file. As an example, add a below line if your package name is 'RFFT', directory name is also 'RFFT' and UUID is `3bd9afcd....`
    ```
    3bd9afcd-55df-531a-9b34-dc642dce7b95 = { name = "RFFT", path = "RFFT" }
    ```

- How to make a package be able to access this registry during the travis test
  - Include below lines in the script section of .travis.yml file in the root directory of your package (as an example, let your package name be 'RegisterFit')
  ```
  script:
    - if [[ -a .git/shallow ]]; then git fetch --unshallow; fi
    - julia -e 'using Pkg, LibGit2;
              user_regs = joinpath(DEPOT_PATH[1],"registries");
              mkpath(user_regs);
              all_registries = Dict("General" => "https://github.com/JuliaRegistries/General.git",
                            "HolyLabRegistry" => "https://github.com/HolyLab/HolyLabRegistry.git");
              Base.shred!(LibGit2.CachedCredentials()) do creds
                for (reg, url) in all_registries
                  path = joinpath(user_regs, reg);
                  LibGit2.with(Pkg.GitTools.clone(url, path; header = "registry $reg from $(repr(url))", credentials = creds)) do repo end
                end
              end'
    - julia -e 'using Pkg; Pkg.build(); Pkg.test("RegisterFit"; coverage=false)'
    ```
  - Assign your private ssh key which is paired with a public key in your Github account to the package in the Travis site. (HolyLabRegistry is now open to public. You don't need this step. But, if you are using private packages registered in this registry, you will need this step for accessing those packages)
    - Copies the contents of the private key ('id_rsa' file generated in the 'To use git protocol in GitHub' section - not 'id_rsa.pub') in the local machine to your clipboard.
    - Go to the setup page of the package in the Travis site you want to make to access this registry. (You can get there by choosing the package in your Travis repositories, clicking ‘More options’ button on the upper right corner and selecting ‘setting’ menu.)
    - Assign the private key in the clipboard to the ‘SSH Key’ field.

- See also
    - Creating Registry : https://discourse.julialang.org/t/creating-a-registry/12094
