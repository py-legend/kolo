# Kolo

See everything happening in your running Django app. All without leaving VSCode

![status: alpha](https://img.shields.io/badge/status-alpha-blue) [![vscode downloads](https://img.shields.io/visual-studio-marketplace/i/kolo.kolo)](https://marketplace.visualstudio.com/items?itemName=kolo.kolo) [![vscode last updated](https://img.shields.io/visual-studio-marketplace/last-updated/kolo.kolo)](https://marketplace.visualstudio.com/items?itemName=kolo.kolo) 

[![vscode extension version](https://img.shields.io/visual-studio-marketplace/v/kolo.kolo?label=VSCode%20extension)](https://marketplace.visualstudio.com/items?itemName=kolo.kolo) [![PyPI version](https://img.shields.io/pypi/v/kolo?label=python%20package)](https://pypi.org/project/kolo/)

![supported python versions](https://img.shields.io/pypi/pyversions/kolo) ![supported django versions](https://img.shields.io/pypi/djversions/kolo?label=django)

Kolo is in alpha so there's a decent chance that you will encounter a problem or bug. When you do, please open an issue on this repository and we'll look into it 🙏


![Annotated Kolo screenshot](https://user-images.githubusercontent.com/7718702/120298398-f3d17800-c2c1-11eb-9052-9adbbff0b5f5.png)


<center>
<h3>DjangoCon Announcement video</h3>
<a href="https://www.youtube.com/watch?v=6XR9Y8v7vZ4" alt="Djangocon youtube video"><img width="300px" src="https://user-images.githubusercontent.com/7718702/120330845-204abb80-c2e5-11eb-92b4-51843ec1e9f1.png"></a>

<em>Kolo launched at DjangoCon Europe 2021</em></center>


## Changelog
Kolo consists of a Python package and a VSCode extension:

- [Changelog for Kolo Python package](./python-package-changelog.md)
- [Changelog for Kolo VSCode extension](./vscode-extension-changelog.md)

### New & Noteworthy features
- Much improved support for :sparkles: **exceptions** :sparkles: Jump straight to where the problem occurred and see the all the contextual information to understand _why_ it happened
- Configure Kolo to ignore certain paths (like `/static/`) and include/ignore frames based on file path
- Kolo now automatically selects the latest request served by your Django app, meaning less manual work to get the most relevant context alongside your code
  
     <img width="500px" src="https://user-images.githubusercontent.com/7718702/128250353-f701ddc6-c799-4acc-8cc2-e8defdffb5d0.png" alt="automatically selecting latest invocation"> 
      
      


## Installation
1. Install the python package using `pip install kolo`
2. Add Kolo to the top of your `MIDDLEWARE` in settings.py : `"kolo.middleware.KoloMiddleware"`
3. Make sure DEBUG is True in your settings.py file, then start your local Django server the way you normally would (for example: `python manage.py runserver`)
4. Install the VSCode extension: https://marketplace.visualstudio.com/items?itemName=kolo.kolo
   - Kolo will show up with an icon in the left sidebar in VSCode <img width="40px" src="https://user-images.githubusercontent.com/7718702/120314341-0c965980-c2d3-11eb-9f1d-c3d9bcccd1c9.png">
   - After clicking on the Kolo icon and opening Kolo within VSCode, follow the instructions to login and confirm your email
5. Any new requests that get served by your local, running Django app will now show up in VSCode 🎉
   - Make your Django app serve an HTTP request (for example by going to `localhost:8000`), then return to Kolo in VSCode to inspect it 🔎 🙌

### Minimum requirements
  - VSCode version 1.56 (released April 2021) or newer
  - Python 3.6 or newer
  - Django 2.2 or newer
  - git (Kolo requires git to accurately show line information in VSCode)


### Docker
For an example of using Docker with Kolo, take a look at the [kolo docker demo repo](https://github.com/kolofordjango/kolo-demo-docker) and the different `docker-compose.yml` files contained there

<br>

If you're using Docker for local development, a couple of additional steps are required:

Kolo works by writing request data to a SQLite database from within the Django middleware and then later reading that same request data from the VSCode extension. When Django is running within a Docker container, we need to make sure that the data written by the middleware is written to the right place (where the VSCode extension can then find it.) We can use [Docker Volumes](https://docs.docker.com/storage/volumes/#use-a-volume-with-docker-compose) to accomplish this.



in your `docker-compose.yml` file, add the following based on your operating system:

#### Mac
```yml
volumes:
  - "~/Library/Application\ Support/kolo:/root/.local/share/kolo:rw"
```

#### Linux
```yml
volumes:
  - "~/.local/share/kolo:/root/.local/share/kolo:rw"
```

#### Windows
```yml
volumes:
  - "~\\AppData\\Local\\kolo\\kolo:/root/.local/share/kolo:rw"
```

Within Docker, it's also pretty common to have a generic working directory name such as `/app` or `/code`. If the working directory you're using for your Docker container has a different name to your Django directory (the one that contains the `manage.py` file), then you need to set the `KOLO_PROJECT_NAME` environment variable. Set this to the same name as the directory that contains your project's `manage.py` file. So in the case of the `kolo-demo-docker` project, we set `ENV KOLO_PROJECT_NAME kolo-demo-docker` [in the Dockerfile](https://github.com/kolofordjango/kolo-demo-docker/blob/5512d97d34a5ec8a025c9a2ac7caa39bf8b6e963/Dockerfile#L3-L4).


### Configuration
Kolo only runs when `DEBUG` is set to `True` in your settings.py file. If you would like to disable Kolo when DEBUG is True, you can set the KOLO_DISABLE environment variable: `KOLO_DISABLE=true`

#### Custom Project Name
Kolo distinguishes between the different Django projects you have locally. By default Kolo identifies a project in the following way:
- in Django middleware: the current working directory you start your Django app from (typically this is the directory that contains your `manage.py` file)
- in VSCode: the name of the workspace folder you have open in VSCode

Kolo needs to detect the same project name both in the Django middleware and in VScode, otherwise no data will show up.

You can override these two values:
- The `KOLO_PROJECT_NAME` environment variable will set the project name used by the Kolo django middleware
- The `kolo.projectName` setting in VSCode will set the project name used by the Kolo VSCode extension

If you don't start your Django app using `python manage.py runserver`, set a custom project name via the `KOLO_PROJECT_NAME` environment variable. If you're using Docker to run Django locally, take a look at the Docker section above.

If your VSCode workspace folder doesn't contain the `manage.py` file, you should manually set the `kolo.projectName` setting in VSCode:

<img src="https://user-images.githubusercontent.com/7718702/121227291-4d6a1180-c883-11eb-9e38-d442a8908bda.png" alt="custom project name vscode" width="400px">

`KOLO_PROJECT_NAME` and `kolo.projectName` should have the same value in the same Django project

#### .kolo/config.toml

You can specify request paths to be ignored via the `.kolo/config.toml` file:


```toml
# .kolo/config.toml

[filters]
# Kolo will ignore all requests that include /static/ in their path
ignore_request_paths = ["/static/"]

```

If you would like to explicitly include or ignore certain frames, you can also do that:

```toml
# .kolo/config.toml

[filters]
# Kolo will include all frames from the requests library, since each frame's file path would contain /requests/
include_frames = ["/requests/"]

# Kolo will ignore all frames from internal/utils.py
# This would be useful if utils.py does a lot of function calls that you don't particularly care about
ignore_frames = ["internal/utils.py"]

```


By default, Kolo expects the `.kolo` directory to be at the same folder level as your `manage.py` file, but you can specify a custom location for the `.kolo` directory via the `KOLO_PATH` environment variable

## Usage

In VSCode, Kolo is available via the sidebar menu. Once clicked, Kolo shows the requests that your Django app recently served:

<img width="300px" src="https://user-images.githubusercontent.com/7718702/120450412-555c1a00-c388-11eb-9b1f-5169814924a8.png">

From this view you can start exploring your requests and what happened within them. Almost every item in this list is expandable, clickable, and will let you dig into the details

### Inline annotations



<img width="300px" src="https://user-images.githubusercontent.com/7718702/120464889-55aee200-c395-11eb-8545-b50268fb48d0.png">

<img width="400px" src="https://user-images.githubusercontent.com/7718702/120464844-4891f300-c395-11eb-9f86-180317851bea.png">

To enable the inline code annotations for a specific request, click on the top level item in the list of recent requests. You should see a little confirmation message pop up, and then your code will be annotated with inline function arguments, return values, and local variables.


### Frame visualization

<img width="600px" src="https://user-images.githubusercontent.com/7718702/120466598-28fbca00-c397-11eb-9656-062e99a62736.png">


Click on "Frames" for a specific request, and the frame visualization will open up


Check out https://kolo.app for more feature screenshots ✨



## Privacy
During installation, Kolo confirms your email address. This is the only time any information is transmitted from the Kolo VSCode extension to our servers (only your email address is transmitted). Everything else such as your code, or any runtime data stays on your machine and is not transmitted anywhere.

The data Kolo collects at runtime is stored in a local SQLite database on your machine. This same SQLite database is then accessed by the VSCode extension to power all of Kolo's functionality.



## Support
Running into problems? Kolo is in alpha, so it's quite likely that you will run into a problem. When you do, please open an issue on this repository or email us: support@kolo.app

Looking for assistance? We're here to help get Kolo set up on your codebase: [Schedule Kolo set up](https://calendly.com/wilhelmklopp)

