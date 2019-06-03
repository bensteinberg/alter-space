# Alterspace

Alterspace is a new kind of reading room: a simple set of controls lets patrons decide on the color and behavior of lights and sounds in the space, adapting conditions for brainstorming, meditation, or quiet study. 


![Photo by Hannah Schoenbaum](photo.png)
Photo by Hannah Schoenbaum


More information about the project is available here: https://alterspace.github.io/
and here: https://lil.law.harvard.edu/projects/alterspace/

This project is a collaboration between Harvard's [Library Innovation Lab](https://lil.law.harvard.edu) and [metaLAB](https://metalab.github.io).

### Before you start, see 
- [List of tools necessary](guides/tools.md)
- [Sound guide](guides/sounds.md)
- [Light guide](guides/lights.md)


### Table of contents:
- [frameworks](Frameworks)
- [Code installation instructions for OS](#install)
- [Code installation instructions for raspberry pis](#raspberry-pi-installation-instructions)

### Frameworks
This project is built using 
- [vue.js](http://vuejs.org/) on the frontend
- [flask](http://flask.pocoo.org/) on the backend   


## Instructions
This project was tested on python 3.5
See [package.json](frontend/package.json) for JS requirements
and [requirements.txt](requirements.txt) for python requirements


### Install
```
pip install pyenv
pyenv install 3.5.4 
pyenv virtualenv 3.5.4 alterspace
pyenv activate alterspace
```

#### Install python dependencies 

```
cp config/config.example.py config/config.py
pip install -r requirements.txt
cd frontend
npm install

```

### Run flask server

```
cd ..
fab run
```
or, to run on a different port:
```
fab run:8000
```

### Tasks
We use Celery to handle all of our light-related tasks, since some tasks might take longer than others and we don't want that to clobber anything else we might want to be doing.
Therefore, we need to start the Celery service in a different terminal session: 
```
fab celery
```

In development, we need to serve both the flask app and vue frontend.  

### Vue/Static assets
To serve files:
```
cd frontend
npm run serve
```
This command runs a live reload server at from http://127.0.0.1:8080/


Remember to build files before adding them to the source code:
```
$ cd frontend
$ npm run build
```

All built front-end files will be in alter-space/dist folder.

Now you should be able to visit http://127.0.0.1:5000/ and see everything working as intended.

Note: some static assets live in the /public folder. On build, these get copied over to the /dist folder
instead of compiled using the webpack configuration.

### Styles
Most styles live in SCSS format in alter-space/frontend/src/assets/css
Some small overrides can live in a <style scoped> fashion in the .vue files


### Working with SVGs
We use the lovely [vue-svgicon](https://github.com/MMF-FE/vue-svgicon#use-generated-icon) for dealing with svgs in Vue.
To use, place all svg assets into frontend/svg-icons.
Run the following command to compile (assets will be placed in frontend/src/components/icons):
```
$ cd ./frontend
$ npm run generate-icons
```

In your Vue component's script tag, import your icon

```python
  import './icons/your-icon';
```

In your vue component's template, place the svg
```html
<svgicon icon="your-icon" width="60" height="60" :original="true" class="btn-default" stroke="0"></svgicon>
```

## Raspberry PI Installation Instructions
- ssh into your pi: https://www.raspberrypi.org/documentation/remote-access/ssh/ (archived at https://perma.cc/RP4P-CYSR)
- set up git:
```
sudo apt install git
git clone https://github.com/harvard-lil/alter-space
cd alter-space
cp config/config.example.py config/config.py
pip3 install -r requirements.txt
```

- install redis
```
sudo apt install redis-server
```

- make sure redis is running
```
ps aux | grep redis
```
you should see something like:
```
$ redis      574  0.2  0.3  29692  3232 ?        Ssl  21:19   0:04 /usr/bin/redis-server 127.0.0.1:6379
```
- In your Raspberry Pi environment, you will be using the [built version](dist/)of the frontend app. Therefore, you need to make sure that your [.env.production](frontend/.env.production) keys are correct.
Specifically, set `VUE_APP_SOUND_LOCAL_URL` to your Pi's IP address, 
make sure .env.production or .env.development point to the right IP and port.
