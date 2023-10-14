 Here are the steps to create a Python virtual environment in Ubuntu WSL:

1. Install virtualenv:
```
sudo apt install python3-venv
```

2. Create the virtual environment:
```
python3 -m venv <venv_name>
```

Replace `<venv_name>` with the name you want to give the virtual environment.

3. Activate the virtual environment:
```
source <venv_name>/bin/activate
```

4. You can then install Python packages inside this virtual environment using `pip`:
```
pip install <package_name>
```

The packages will be installed within the virtual environment, isolated from the system's main Python installation.

5. When you are done working with the virtual environment, deactivate it:
```
deactivate
```

This will take you back to your system's main Python environment.


---

There are a couple of ways to check if you are in a virtual environment in Ubuntu:

1. The most straightforward way is to check the value of the VIRTUAL_ENV environment variable. If it is set, that means you are in a virtual environment. You can check it like this:

    ```
   echo $VIRTUAL_ENV
    ```    

If you are in a virtual environment, it will print the path to that virtual environment. If not, it will print nothing.