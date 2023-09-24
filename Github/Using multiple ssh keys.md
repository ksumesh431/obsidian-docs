If you have multiple SSH key pairs and you want to create entries for all of them in your SSH configuration file (`~/.ssh/config`) and use them for different GitHub repositories or hosts, you can define multiple `Host` sections, each with its own `IdentityFile`. Here's how you can do that:

1. **Generate Multiple SSH Key Pairs:**
    
    Generate SSH key pairs for each of your custom keys with different names:

  ``` shell 
ssh-keygen -t rsa -b 4096 -C "email1@example.com" -f ~/.ssh/custom_key1
ssh-keygen -t rsa -b 4096 -C "email2@example.com" -f ~/.ssh/custom_key2
# Generate additional keys as needed


```
    
2. **Create or Edit Your SSH Config File:**
    
    If you don't have an `~/.ssh/config` file, create one. Otherwise, open it for editing:
    
    `touch ~/.ssh/config nano ~/.ssh/config`
    
3. **Specify Each Custom Key for Different Hosts:**
    
    In your `~/.ssh/config` file, specify each custom SSH key for different GitHub hosts or repositories. For example:
    
```
# Custom SSH key for GitHub Host 1
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/custom_key1

# Custom SSH key for GitHub Host 2
Host github.com-yourusername
  HostName github.com
  User git
  IdentityFile ~/.ssh/custom_key2

```
    
 **Save and Exit the SSH Config File.**
    
4. **Test Your SSH Configuration:**
    
    You can test your SSH configuration for each host by running:

    `ssh -T git@github.com ssh -T git@github.com-yourusername`
    
    Each command should use the respective custom SSH key you specified in the `~/.ssh/config` file.

Now, when you run GitHub commands like `git clone`, `git pull`, or `git push`, the SSH configuration specified in the `~/.ssh/config` file will determine which custom SSH key to use based on the `Host` section you provided for the corresponding host or repository.