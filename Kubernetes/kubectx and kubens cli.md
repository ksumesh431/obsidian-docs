
# For managing various contexts and namespaces and switching easily



Throughout the course, you have had to work on several different namespaces in the practice lab environments. In some labs, you also had to switch between several contexts.

While this is excellent for hands-on practice, in a real “live” kubernetes cluster implemented for production, there could be a possibility of often switching between a large number of namespaces and clusters.

This can quickly become and confusing and overwhelming task if you had to rely on kubectl alone.

This is where command line tools such as kubectx and kubens come in to picture.

Reference: [https://github.com/ahmetb/kubectx](https://github.com/ahmetb/kubectx)

Kubectx:

With this tool, you don’t have to make use of lengthy “kubectl config” commands to switch between contexts. This tool is particularly useful to switch context between clusters in a multi-cluster environment.

Installation:

`sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx   sudo ln -s /opt/kubectx/kubectx /usr/local/bin/kubectx`

Syntax:

To list all contexts:

kubectx

To switch to a new context:

kubectx

To switch back to the previous context:

kubectx –

To see the current context:

kubectx -c

Kubens:

This tool allows users to switch between namespaces quickly with a simple command.

Installation:

`sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx   sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens`

Syntax:

To switch to a new namespace:

kubens

To switch back to previous namespace:

kubens –