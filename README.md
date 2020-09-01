# remocolab
remocolab is a Python module to allow remote access to [Google Colaboratory](https://colab.research.google.com/) using SSH or [TurboVNC](https://www.turbovnc.org/).
It also install [VirtualGL](https://www.virtualgl.org/) so that you can run OpenGL programs on a Google Colaboratory machine and see the screen on VNC client.
It secures TurboVNC connection using SSH port forwarding.

- [FAQ](https://github.com/demotomohiro/remocolab/wiki/Frequently-Asked-Questions)

## Requirements
- You can use [Google Colaboratory](https://colab.research.google.com/)
  - That means you need Google acount and a browser that is supported by Google Colaboratory.
- [ngrok](https://ngrok.com/) Tunnel Authtoken
  - You need to sign up for ngrok to get it
- SSH client
- (Optional) [TurboVNC Viewer](https://sourceforge.net/projects/turbovnc/files/) if you use it.

## How to use
1. Create a new notebook on Google Colaboratory
2. Add a code cell and copy & paste one of following codes to the cell

- SSH only:
```python3
!pip install git+https://github.com/sajeevan16/remocolab.git
import remocolab
remocolab.ngrok_token = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
remocolab.setupSSHD()
```

- SSH and TurboVNC:
```python3
!pip install git+https://github.com/sajeevan16/remocolab.git
import remocolab
#remocolab.ngrok_region ="ap"
remocolab.ngrok_token = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
remocolab.setupVNC()

# function ClickConnect(){
# console.log("Working"); 
# document.querySelector("colab-toolbar-button#connect").click() 
# }
# setInterval(ClickConnect,60000)
```

3. (Optional) If you want to run OpenGL applications or any programs that use GPU,
Click "Runtime" -> "Change runtime type" in top menu and change Hardware accelerator to GPU. 
4. Run that cell
5. Then the message that ask you to copy & paste tunnel authtoken of ngrok will appear.
Login to ngrok, click Auth on left side menu, click Copy, return to Google Colaboratory, paste it to the text box under the message and push enter key.
   - ngrok token must be kept secret.
   I understand people hate copy & pasting ngrok token everytime they use remocolab, but I don't know how to skip it without risking a security.
   If you could specify ngrok token to `remocolab.setupSSHD()` or `remocolab.setupVNC()`, you can save ngrok token to a notebook.
   Then, you might forget that your notebook contains it and share the notebook.
6. Select your ngrok region. Select the one closest to your location. For example, if you were in Japan, type jp and push enter key.
   - You can also specify ngrok region to ``remocolab.setupSSHD()`` or ``remocolab.setupVNC()`` in the code like ``remocolab.setupSSHD(ngrok_region = "jp")``.
7. remocolab setup ngrok and SSH server (and desktop environment and TurboVNC server if you run setupVNC). Please wait for it done
   - `remocolab.setupSSHD()` takes about 2 minutes
   - `remocolab.setupVNC()` takes about 5 minutes
8. Then, root and colab user password and ssh command to connect the server will appear.
9. Copy & paste that ssh command to your terminal on your local machine and login to the server using displayed colab user password.
(Even if you just want to use TurboVNC, you need to login using SSH to make SSH port forwarding)

* If you use TurboVNC:
10. Run TurboVNC viewer on your local machine, set server address to ``localhost:1`` and connect.
11. Then, password will be asked. Copy & paste the VNC password displayed in `remocolab.setupVNC()`'s output to your TurboVNC viewer.

When you got error and want to rerun `remocolab.setupVNC()` or `remocolab.setupSSHD()`, you need to do `factory reset runtime` before rerun the command.
As you can run only 1 ngrok process with free ngrok account, running `remocolab.setupVNC/setupSSHD` will fail if there is another instace that already ran remocolab.
In that case, terminate another instance from `Manage sessions` screen.

## How to run OpenGL applications
Put the command to run the OpenGL application after ``vglrun``.
For example, ``vglrun firefox`` runs firefox and you can watch web sites using WebGL with hardware acceleration.

## Arguments of `remocolab.setupSSHD()` and `remocolab.setupVNC()`
- `ngrok_region`
  Specify ngrok region like "us", "eu", "ap". [List of region](https://ngrok.com/docs#global-locations)
- `check_gpu_available`
  When it is `True`, it checks whether GPU is available and show warning in case GPU is not available.

## How to setup public key authentication
By using public key authentication, you can login to ssh server without copy&pasting a password.
If you don't have an SSH key pair, generate it with ssh-keygen:
```console
ssh-keygen -t ecdsa -b 521
```
Add following code after ``remocolab.setupSSHD()`` or ``remocolab.setupVNC()``,
and replace "my public key" in following code with your public key.
```python3
!mkdir /home/colab/.ssh
with open("/home/colab/.ssh/authorized_keys", 'w') as f:
  f.write("my public key")
!chown colab /home/colab/.ssh /home/colab/.ssh/authorized_keys
!chmod 700 /home/colab/.ssh
!chmod 600 /home/colab/.ssh/authorized_keys
!chown -R colab /home/colab/.ssh
```
If you want to login as root, use following code:
```python3
!mkdir /root/.ssh
with open("/root/.ssh/authorized_keys", 'w') as f:
  f.write("my public key")
!chmod 700 /root/.ssh
!chmod 600 /root/.ssh/authorized_keys
```
And replace user name colab in ssh command to root.

## Experimental kaggle support
remocolab in kaggle branch works on [Kaggle](https://www.kaggle.com/).
1. Create a new Notebook with Python language.
2. Set settings to:
   - Internet on
   - Docker to Latest Available
   - GPU on if you use TurboVNC
3. Add a code cell and copy & paste one of following codes to the cell

- SSH only:
```python3
!pip install git+https://github.com/demotomohiro/remocolab.git@kaggle
import remocolab
remocolab.setupSSHD()
```

- SSH and TurboVNC:
```python3
!pip install git+https://github.com/demotomohiro/remocolab.git@kaggle
import remocolab
remocolab.setupVNC()
```

4. Follow instructions from step 4 in above "How to use".
