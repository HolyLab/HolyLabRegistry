# HolyLabRegistry
This is a HolyLab private registry to use lab packages according to julia 0.7 Pkg3 design.

# Usage
Clone this repository under DEPOT_PATH/registries. Then, we can use lab private
pakages as if they are registered ones. (Usually, DEPOT_PATH = /home/username/.julia)


# WIP
Still need to add most of our lab packages to this registry.


# To use git protocol in GitHub
(This instruction comes form https://help.github.com/articles/connecting-to-github-with-ssh/)

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
