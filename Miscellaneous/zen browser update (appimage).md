#zen

run script 
`bash <(curl https://updates.zen-browser.app/appimage.sh)`

Edit .desktop at  ~/.local/share/applications/ZenBrowser.desktop entry and replace the path using:
`:%s/\/home\/kp\/\.local\/share\/AppImage\/ZenBrowser\.AppImage/zen/g`