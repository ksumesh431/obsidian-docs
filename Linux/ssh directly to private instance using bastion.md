
You can use SSH tunneling to connect to the private instance through the bastion in a single command.

Here’s the command that will allow you to do that:

`ssh -i private-key.pem -o ProxyCommand="ssh -i private-key.pem -W %h:%p ubuntu@54.162.118.78" ubuntu@172.31.41.48`

### Explanation:

- `-i private-key.pem`: Specifies the private key to use for authentication.
- `-o ProxyCommand="ssh -i private-key.pem -W %h:%p ubuntu@54.162.118.78"`: Tunnels your connection through the bastion host (with its public IP) using the same key.
- `ubuntu@172.31.41.48`: Connects to the private instance using its private IP once tunneled through the bastion.

This command effectively uses the bastion as a jump host, allowing you to SSH directly into the private instance in one step.