
oh my posh for wsl bash
eval "$(oh-my-posh init bash --config /mnt/c/oh-my-posh_themes/amro.omp.json)"

in wsl, must set the theme at a windows folder


1. switch to root user and install oh my posh using the command:
`curl -s https://ohmyposh.dev/install.sh | bash -s`

2. install a nerd font
Download a nerd font from [Fonts | Oh My Posh](https://ohmyposh.dev/docs/installation/fonts)
And install it in windows. then select any nerd font in the windows terminal for wsl

also run `oh-my-posh font install` and select fira code nerd font there.
**MUST DISABLE ATLAS E NGINE ERENDERING IN TERMINAL SETTINS**

3. keep the fonts in preferrable a windows directory

4. run the eval command to test
5. add the eval command in ~/.bashrc

additional config
- change # to $ for regular config
- make root config add add to root .bashrc
  